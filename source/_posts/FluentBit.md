---
title: FluentBit
date: 2023-12-10 18:40:40
tags: [中间件, fluentbit]
categories: 中间件
---

# 安装
```json
brew install fluent-bit
```
# 快速启动
```
//启动fluent-bit 输入 dummy（假数据），输出 标准输出
 fluent-bit -i dummy -o stdout -f 1
 
 //使用配置文件启动
 fluent-bit -c main.conf
```
## docker启动
```yaml
docker run -d -v /root/gxc/fluent-conf:/config --network host fluent/fluent-bit -c /config/main.conf
```
```yaml
[SERVICE]
    Flush     5
    Daemon    off
    Log_Level trace

[INPUT]
    Name        kafka
    Brokers     10.10.124.28:9092
    Topics      ni-device-topic
    Tag         ni-device

[OUTPUT]
    Name  stdout
    Format json
    Match *

[OUTPUT]
    Name            influxdb
    Match           *
    Host            10.10.124.28
    Port            8086
    Database        ni
    Sequence_Tag    off
    Retry_Limit     False
```
# 概念

- Input 输入，主要使用各种插件
- Parser 解析器，将数据从非结构化数据转换为结构化数据，比如转成json
- Filter过滤器，主要使用各种插件，正则匹配数据
- Buffer缓冲，
- Routing路由，通过tag和Match实现
- 输出，主要使用各种插件
# 实战案例
结合golang代码，这里是以mqtt作为输入的demo
```
func main() {
  broker := "localhost:1883"
  // clientID 是 MQTT 客户端的标识符，用于在 MQTT 代理中唯一标识一个客户端
  clientID := "mqtt-demo"
  topic := "mytopic"

  opts := MQTT.NewClientOptions().AddBroker(broker).SetClientID(clientID)
  client := MQTT.NewClient(opts)

  if token := client.Connect(); token.Wait() && token.Error() != nil {
    fmt.Println("Failed to connect to MQTT broker:", token.Error())
    os.Exit(1)
  }

  // 发布消息
  test:=map[string]string{"name":"ccc","sex":"man"}
  testJson,_:=json.Marshal(test)
  token := client.Publish(topic, 0, false, string(testJson))
  //token.Wait()

  if token.Error() != nil {
    fmt.Println("Failed to publish message:", token.Error())
  } else {
    fmt.Println("Message published successfully")
  }

  // 等待接收消息
  time.Sleep(3 * time.Second)

  // 断开连接
  client.Disconnect(250)
}
```
## 输出到标准输出
fluentbit 配置文件main.conf 如下
```
[SERVICE]
    Flush     5
    Daemon    off
    Log_Level trace

[INPUT]
    Name  mqtt
    Tag    data
    Listen 0.0.0.0
    Port   1883

[FILTER]
    Name         record_modifier
    Match        data
    Record       message   $json_parser($.message)

[OUTPUT]
    Name  stdout
    Match data
```
默认情况下，Fluent Bit 的 MQTT 输入插件期望接收的消息是 JSON 格式。这里使用 Fluent Bit 的 record_modifier 过滤器插件，将 JSON 格式的消息转换为适合 Fluent Bit 的记录格式。执行flubit
```
guxc@guxuchengdeMacBook-Pro fluentbit_test % pwd
/Users/guxc/fluentbit_test
guxc@guxuchengdeMacBook-Pro fluentbit_test % fluent-bit -c main.conf
```
得到fluentbit输出日志如下
```
[2023/07/02 20:24:21] [ info] [sp] stream processor started
[2023/07/02 20:24:29] [debug] [input:mqtt:mqtt.0] [fd=40] new TCP connection
[2023/07/02 20:24:29] [trace] [input:mqtt:mqtt.0 at /tmp/fluent-bit-20230624-5236-igw8zp/fluent-bit-2.1.6/plugins/in_mqtt/mqtt_conn.c:61] [fd=40] read()=23 bytes
[2023/07/02 20:24:29] [trace] [input:mqtt:mqtt.0 at /tmp/fluent-bit-20230624-5236-igw8zp/fluent-bit-2.1.6/plugins/in_mqtt/mqtt_prot.c:236] [fd=40] CMD CONNECT (connack=4 bytes)
[2023/07/02 20:24:29] [trace] [input:mqtt:mqtt.0 at /tmp/fluent-bit-20230624-5236-igw8zp/fluent-bit-2.1.6/plugins/in_mqtt/mqtt_conn.c:61] [fd=40] read()=37 bytes
[2023/07/02 20:24:29] [debug] [input chunk] update output instances with new chunk size diff=89, records=1, input=mqtt.0
[2023/07/02 20:24:29] [trace] [input:mqtt:mqtt.0 at /tmp/fluent-bit-20230624-5236-igw8zp/fluent-bit-2.1.6/plugins/in_mqtt/mqtt_prot.c:312] [fd=40] CMD PUBLISH
[2023/07/02 20:24:29] [debug] [input:mqtt:mqtt.0] [fd=40] connection closed
[2023/07/02 20:24:31] [debug] [task] created task=0x600000fec000 id=0 OK
[2023/07/02 20:24:31] [debug] [output:stdout:stdout.0] task_id=0 assigned to thread #0
[0] data: [[1688300669.236867000, {}], {"topic"=>"mytopic", "name"=>"aaa", "sex"=>"man", "message"=>"$json_parser($.message)"}]
[2023/07/02 20:24:31] [debug] [out flush] cb_destroy coro_id=0
[2023/07/02 20:24:31] [debug] [task] destroy task=0x600000fec000 (task_id=0)
```
## 输出到influxdb
当fluentbit配置如下时，将输出到influxdb中
```
[SERVICE]
    Flush     5
    Daemon    off
    Log_Level trace

[INPUT]
    Name  mqtt
    Tag    flubit
    Listen 0.0.0.0
    Port   1883

[FILTER]
    Name         record_modifier
    Match        flubit
    Record       message   $json_parser($.message)

[OUTPUT]
    Name          influxdb
    Match         *
    Host          127.0.0.1
    Port          8086
    Bucket        gxc-b
    Org           gxc-o
    Sequence_Tag  _seq
    HTTP_Token    oFFqyn1rcXp5Bsx3x62seWGZ3xUtcoyMtxZVS3LhZ_w9K2xwt9as3JG5vbwdB_1pN11Tt4vt1IR12Yujof9iBQ==
```
根据配置，数据将被写入到 InfluxDB 数据库中名为 gxc-b 的桶，该桶属于名为 gxc-o 的组织。这里没有明确指定测量（Measurement）名称。在这种情况下，默认情况下，Fluent Bit 通常会使用输入数据的标签（Tag）作为测量名称。
查看influxdb插入的数据执行查询语句,得到结果
```
> select * from flubit

Interactive Table View (press q to exit mode, shift+up/down to navigate tables):
Name: flubit
┏━━━━┳━━━━━━━━━━━━━━━━┳━━━┳━━━━━━━━━━━━━┳━━━┳━━━┳━━━━━┓  
┃ index ┃               time              ┃ _seq ┃         message         ┃  name ┃ sex ┃   topic  ┃   
┣━━━━╋━━━━━━━━━━━━━━━━╋━━━╋━━━━━━━━━━━━━╋━━━╋━━━╋━━━━━┫  
┃      1┃   1688306144020832000.0000000000┃0     ┃$json_parser($.message)  ┃ aaa   ┃man  ┃ mytopic  ┃   
┣━━━━┻━━━━━━━━━━━━━━━━┻━━━┻━━━━━━━━━━━━━┻━━━┻━━━┻━━━━━┫  
┃                                                                                 7 Columns, 1 Rows, Page 1/1┃
┃                                                                                    Table 1/1, Statement 1/1┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```
fluentbit的日志如下
```
[2023/07/02 22:02:47] [ info] [sp] stream processor started
[2023/07/02 22:02:51] [debug] [input:mqtt:mqtt.0] [fd=31] new TCP connection
[2023/07/02 22:02:51] [trace] [input:mqtt:mqtt.0 at /tmp/fluent-bit-20230624-5236-igw8zp/fluent-bit-2.1.6/plugins/in_mqtt/mqtt_conn.c:61] [fd=31] read()=23 bytes
[2023/07/02 22:02:51] [trace] [input:mqtt:mqtt.0 at /tmp/fluent-bit-20230624-5236-igw8zp/fluent-bit-2.1.6/plugins/in_mqtt/mqtt_prot.c:236] [fd=31] CMD CONNECT (connack=4 bytes)
[2023/07/02 22:02:51] [trace] [input:mqtt:mqtt.0 at /tmp/fluent-bit-20230624-5236-igw8zp/fluent-bit-2.1.6/plugins/in_mqtt/mqtt_conn.c:61] [fd=31] read()=37 bytes
[2023/07/02 22:02:51] [debug] [input chunk] update output instances with new chunk size diff=89, records=1, input=mqtt.0
[2023/07/02 22:02:51] [trace] [input:mqtt:mqtt.0 at /tmp/fluent-bit-20230624-5236-igw8zp/fluent-bit-2.1.6/plugins/in_mqtt/mqtt_prot.c:312] [fd=31] CMD PUBLISH
[2023/07/02 22:02:52] [debug] [task] created task=0x600001568000 id=0 OK
[2023/07/02 22:02:52] [debug] [upstream] KA connection #32 to 127.0.0.1:8086 is connected
[2023/07/02 22:02:52] [debug] [http_client] not using http_proxy for header
[2023/07/02 22:02:52] [debug] [output:influxdb:influxdb.0] http_do=0 OK
[2023/07/02 22:02:52] [debug] [upstream] KA connection #32 to 127.0.0.1:8086 is now available
[2023/07/02 22:02:52] [debug] [out flush] cb_destroy coro_id=0
[2023/07/02 22:02:52] [debug] [task] destroy task=0x600001568000 (task_id=0)
[2023/07/02 22:02:54] [trace] [input:mqtt:mqtt.0 at /tmp/fluent-bit-20230624-5236-igw8zp/fluent-bit-2.1.6/plugins/in_mqtt/mqtt_conn.c:61] [fd=31] read()=2 bytes
[2023/07/02 22:02:54] [trace] [input:mqtt:mqtt.0 at /tmp/fluent-bit-20230624-5236-igw8zp/fluent-bit-2.1.6/plugins/in_mqtt/mqtt_prot.c:437] [fd=31] CMD DISCONNECT
```
## 输出到InfluxDB，且表名（measurement）可选
从上一个案例可以看到Influxdb会将输入的tag作为表名，但如果要根据不同输入来存入不同表，如何配置
比如有这样的json
```
//想要aaa存一个表，ccc存一个表
{
  "name":"aaa",
  "age":12,
  "sex":"man",
}

{
  "name":"ccc",
  "age":11,
  "sex":"woman",
}
```
解决方法，增加Filter，变更tag
```
[SERVICE]
    Flush     5
    Daemon    off
    Log_Level trace

[INPUT]
    Name  mqtt
    Tag    flubit
    Listen 0.0.0.0
    Port   1883

# 将原tag flubit转成json中输入的name
[FILTER]
    Name        rewrite_tag
    Match       flubit
    Rule        $name ^(.*)$ $name false

[OUTPUT]
    Name  stdout
    Format json
    Match *

[OUTPUT]
    Name          influxdb
    Match         *
    Host          127.0.0.1
    Port          8086
    Bucket        gxc-b
    Org           gxc-o
    Sequence_Tag  _seq
```
执行完后查看influxdb
```
//可以看到measurements里增加了ccc表
Name: measurements
┏━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━┓  
┃ index ┃                         name                         ┃   
┣━━━━╋━━━━━━━━━━━━━━━━━━━━━━━━━━━┫  
┃      1┃boltdb_reads_total                                   ┃    
┃      2┃boltdb_writes_total                                  ┃    
┃      3┃ccc                                                  ┃    
┣━━━━┻━━━━━━━━━━━━━━━━━━━━━━━━━━━┫  
┃                                    2 Columns, 128 Rows, Page 1/10┃
┃                                          Table 1/1, Statement 1/1┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛

//查询数据，可以看到增加了一条数据
> select * from "ccc"


Name: ccc
┏━━━━┳━━━━━━━━━━━━━━━━┳━━━┳━━━┳━━━┳━━━━━┓  
┃ index ┃               time              ┃ _seq ┃ name ┃ sex ┃   topic  ┃   
┣━━━━╋━━━━━━━━━━━━━━━━╋━━━╋━━━╋━━━╋━━━━━┫  
┃      1┃   1688471230458330880.0000000000┃0     ┃ccc   ┃man  ┃ mytopic  ┃   
┣━━━━┻━━━━━━━━━━━━━━━━┻━━━┻━━━┻━━━┻━━━━━┫  
┃                                                     6 Columns, 1 Rows, Page 1/1┃
┃                                                        Table 1/1, Statement 1/1┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

### 使用name作为influxdb表名
```yaml
    [INPUT]
        Name            mqtt
        Tag             test_device
        Listen          0.0.0.0
        Port            30086
    [FILTER]
        Name            rewrite_tag
        Match           test_device
        Rule            $name ^(.*)$ $name false
        Emitter_Name    re_emitted
        Emitter_Storage.type    filesystem
        Emitter_Mem_Buf_Limit   100M
    [OUTPUT]
      Name            influxdb
      Match           *
      Host            10.10.14.30
      Port            30086
      Database        devices
      Sequence_Tag    off
      Retry_Limit     False
      Tag_Keys        name topic
```
# 文档
[中文文档](https://hulining.gitbook.io/fluentbit/)
[英文文档](https://docs.fluentbit.io/manual/)
