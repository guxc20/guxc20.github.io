---
title: Mock Test
date: 2025-03-17 13:55:39
tags: [Golang, Test]
categories: Golang
---


Mock 是一种测试技术，用于模拟程序中的外部依赖（如数据库、API、第三方服务等），以便在单元测试中：

隔离被测函数的逻辑。有时候没办法连接数据库，但是又要保证代码能力，可以使用。



## 案例
有一个函数叫AddSystemLevel，其中有对数据库查询的几个函数，然后当我想要对AddSystemLevel进行测试时，可以利用mock test

```plain
// 原始未解耦版本（直接调用数据库）
func AddSystemLevel(nodes []Node) []Node {
    // 直接调用数据库方法
    systemProjectMapping, err := GetAllSystemNames() // 查数据库
    maxId, err := GetMaxNodeID()                     // 查数据库
    
    // ...其他逻辑
    return updatedNodes
}

// 数据库方法
func GetAllSystemNames() (map[string]string, error) {
    // 真实数据库操作
    var result map[string]string
    err := DB.SQL("SELECT ...").Find(&result)
    return result, err
}

```

## 步骤
### 定义接口
```plain
// 定义依赖接口
type NodeService interface {
    GetAllSystemNames() (map[string]string, error)
}
```

### 原来接口改造
```plain
// 生产环境真实实现
type DefaultNodeService struct{}

func (d *DefaultNodeService) GetAllSystemNames() (map[string]string, error) {
    // 真实数据库操作
    var result map[string]string
    err := DB.SQL("SELECT ...").Find(&result)
    return result, err
}

// 改造成依赖注入版本
func AddSystemLevel(nodes []Node, service NodeService) []Node { // 新增接口参数
    systemProjectMapping, err := service.GetAllSystemNames() // 通过接口调用
    
    // ...其他逻辑保持不变
    return updatedNodes
}

```

新建mock_node_service.go 文件

```plain
// mock_node_service.go
type MockNodeService struct {
    mock.Mock
}

func (m *MockNodeService) GetAllSystemNames() (map[string]string, error) {
    args := m.Called()
    return args.Get(0).(map[string]string), args.Error(1)
}


```

调用AddSystemLevel的方法处

```plain
     func main() {
         service := &DefaultNodeService{}	//添加空的service
         nodes := AddSystemLevel(initialNodes, service)
         // ...
     }
     
```

### 测试文件中使用
```plain
package main

import (
    "testing"
    "github.com/stretchr/testify/assert"
    "github.com/stretchr/testify/mock"
)
// 都可以让ai生成
func TestAddSystemLevel(t *testing.T) {
    // 创建 Mock 对象
    mockService := new(MockNodeService)
    
    // 定义 Mock 行为（On 表示期望的调用参数，Return 设置返回值）
    mockService.On("GetAllSystemNames").Return(map[string]string{
        "proj1": "system1"
    }, nil)

     // 准备测试数据
    nodes := []Node{
        {Id: 1, Name: "proj1", Cate: "project"},
    }
    
    // 调用被测函数（需将依赖注入到 AddSystemLevel 中）
    // 假设 AddSystemLevel 接收一个 NodeService 接口参数：
    result := AddSystemLevel(nodes, mockService) // 依赖注入示例

    // 验证结果和 Mock 调用
    assert.Len(t, result, 2)
    mockService.AssertExpectations(t) // 检查所有期望是否被调用
}

```

