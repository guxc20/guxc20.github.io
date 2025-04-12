---
title: Solidity
date: 2025-02-11 18:45:30
tags: [Solidity,Web3]
categories: Web3
---

## 数据类型
Solidity 是一种静态类型语言，这意味着需要指定每个变量（状态和本地）的类型。 Solidity 提供了几种基本类型，例如：

+ `bool`（布尔值）
+ `uint`, `uint256`, `uint32`（无符号整数）
+ `int`, `int256`, `int32`（有符号整数）
+ `address`（以太坊地址）

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract Primitives {
    bool public boo = true;

    uint8 public u8 = 1;
    uint256 public u256 = 456;
    uint256 public u = 123; // uint is an alias for uint256
    
    int8 public i8 = -1;
    int256 public i256 = 456;
    int256 public i = -123; // int is same as int256// minimum and maximum of intint256 public minInt = type(int256).min;
    int256 public maxInt = type(int256).max;

    address public addr = 0xCA35b7d915458EF540aDe6068dFe2F44E8fa733c;
}
```



在 Solidity 中，`bytes`数据类型用于表示字节序列。字节可用于存储二进制数据，例如文件内容或加密哈希值。

Solidity 中有两种类型的字节数组：

+ **固定大小字节数组**`bytes1`：这些数组用到声明`bytes32`。数字表示数组的长度（以字节为单位）。例如，`bytes1`是长度为 1 的字节数组，`bytes2`是长度为 2 的字节数组，依此类推，直到`bytes32`。
+ **动态大小的字节数组**：这些数组用 声明。此类型与其他语言中`bytes`的类似。`byte[]`

以下是如何使用字节数组的示例：



```plain
// Declare a fixed-size byte array
bytes1 a = 0xb5; // [10110101]
bytes1 b = 0x56; // [01010110]

// Declare a bytes10 array
bytes10 d = 0x68656c6c6f776f726c64; // "helloworld" in hexadecimal

// Declare a bytes32 array
bytes32 e = 0x68656c6c6f776f726c6420202020202020202020202020202020202020202020; // "helloworld" padded with spaces in hexadecimal

// Declare a dynamically-sized byte array
bytes memory c = "Hello, world!";
```

## 常量
常量是无法修改的变量。常量的名称应全部用大写字母命名，并用下划线分隔单词。

```plain
// SPDX-License-Identifier: MITpragma solidity ^0.8.24;

contract Constants {
    // coding convention to uppercase constant variablesaddress public constant MY_ADDRESS =
        0x777788889999AaAAbBbbCcccddDdeeeEfFFfCcCc;
    uint256 public constant MY_UINT = 123;
}
```

## immutable
声明为 的变量比`immutable`声明为 的变量限制更少`constant`。不可变变量可以在构造时赋值。该值可以在部署之前的任何时间更改，然后变为永久值。

另一个限制是，不可变变量只能分配给在创建后不可能执行的内部表达式。这排除了除构造函数之外的所有修饰符定义和函数。

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract Immutable {
    // coding convention to uppercase constant variables address public immutable MY_ADDRESS;
    uint256 public immutable MY_UINT;

    constructor(uint256 _myUint) {
        MY_ADDRESS = msg.sender;
        MY_UINT = _myUint;
    }
}
```

## 变量
Solidity 中有 3 种类型的变量：

1. 状态变量
    - 在函数外部声明
    - 存储在区块链上
2. 局部变量
    - 在函数内部声明
    - 未存储在区块链上
3. 全局变量
    - 提供有关区块链的信息

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract Variables {
    // State variables are stored on the blockchain.
    string public text = "Hello";
    uint256 public num = 123;

    function doSomething() public {
        // Local variables are not saved to the blockchain.
        uint256 i = 456;

        // Here are some global variables
        uint256 timestamp = block.timestamp; // Current block timestamp
        address sender = msg.sender; // address of the caller
    }
}
```

## 数组
数组可以具有编译时固定大小，也可以具有动态大小。

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract Array {
    // Several ways to initialize an array
    uint256[] public arr;
    uint256[] public arr2 = [1, 2, 3];
    // Fixed sized array, all elements initialize to 0
    uint256[10] public myFixedSizeArr;

    function get(uint256 i) public view returns (uint256) {
        return arr[i];
    }

    // Solidity can return the entire array.
    // But this function should be avoided for
    // arrays that can grow indefinitely in length.
    function getArr() public view returns (uint256[] memory) {
        return arr;
    }

    function push(uint256 i) public {
        // Append to array
        // This will increase the array length by 1.
        arr.push(i);
    }

    function pop() public {
        // Remove last element from array
        // This will decrease the array length by 1
        arr.pop();
    }

    function getLength() public view returns (uint256) {
        return arr.length;
    }

    function remove(uint256 index) public {
        // Delete does not change the array length.
        // It resets the value at index to it's default value,
        // in this case 0
        delete arr[index];
    }

    function examples() external pure {
        // create array in memory, only fixed size can be created
        uint256[] memory a = new uint256[](5);
    }
}
```

## Mapping
mapping(keyType => valueType)

可以`keyType`是任何内置值类型、字节、字符串、枚举或任何协定。但是，**不允许**使用其他用户定义或复杂类型，例如映射、结构或数组类型。

`valueType`可以是任何类型，包括另一个映射或数组。

映射只能有一个数据位置`storage`。

它们不能用作公开可见的合约函数的参数。这些限制对于包含映射的数组和结构体也适用。

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract Mapping {
    // Mapping from address to uint
    mapping(address => uint256) public myMap;

    function get(address _addr) public view returns (uint256) {
        // Mapping always returns a value.
        // If the value was never set, it will return the default value.
        return myMap[_addr];
    }

    function set(address _addr, uint256 _i) public {
        // Update the value at this address
        myMap[_addr] = _i;
    }

    function remove(address _addr) public {
        // Reset the value to the default value.
        delete myMap[_addr];
    }
}

contract NestedMapping {
    // Nested mapping (mapping from address to another mapping)
    mapping(address => mapping(uint256 => bool)) public nested;

    function get(address _addr1, uint256 _i) public view returns (bool) {
        // You can get values from a nested mapping
        // even when it is not initialized
        return nested[_addr1][_i];
    }

    function set(address _addr1, uint256 _i, bool _boo) public {
        nested[_addr1][_i] = _boo;
    }

    function remove(address _addr1, uint256 _i) public {
        delete nested[_addr1][_i];
    }
}// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract Mapping {
    // Mapping from address to uint
    mapping(address => uint256) public myMap;

    function get(address _addr) public view returns (uint256) {
        // Mapping always returns a value.
        // If the value was never set, it will return the default value.
        return myMap[_addr];
    }

    function set(address _addr, uint256 _i) public {
        // Update the value at this address
        myMap[_addr] = _i;
    }

    function remove(address _addr) public {
        // Reset the value to the default value.
        delete myMap[_addr];
    }
}

contract NestedMapping {
    // Nested mapping (mapping from address to another mapping)
    mapping(address => mapping(uint256 => bool)) public nested;

    function get(address _addr1, uint256 _i) public view returns (bool) {
        // You can get values from a nested mapping
        // even when it is not initialized
        return nested[_addr1][_i];
    }

    function set(address _addr1, uint256 _i, bool _boo) public {
        nested[_addr1][_i] = _boo;
    }

    function remove(address _addr1, uint256 _i) public {
        delete nested[_addr1][_i];
    }
}
```



## 可见性
Solidity 有两种类型的函数调用：外部函数和内部函数。派生合约可以无法访问内部函数。这导致函数的可见性有四种类型

external 外部函数是合约接口的一部分，这意味着它们可以从其他合约和通过交易调用。外部函数 f 不能在内部调用（即 f() 不起作用，但 this.f() 起作用）。

public 公共函数是合约接口的一部分，可以内部调用，也可以通过消息调用。

internal 内部函数只能从当前合约或从其派生的合约内部访问。它们不能从外部访问。可以采用内部类型的参数，如映射或存储引用。

private 私有函数类似于内部函数，但它们在派生合约中不可见。

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract BaseContract {
    // External function
    // Can be called from other contracts and via transactions
    // Cannot be called internally
    function externalFunc() external pure returns (string memory) {
        return "External function called";
    }

    // Public function
    // Can be called internally or via message calls
    function publicFunc() public pure returns (string memory) {
        return "Public function called";
    }

    // Internal function
    // Can only be accessed from within the current contract or contracts deriving from it
    // Cannot be accessed externally
    function internalFunc() internal pure returns (string memory) {
        return "Internal function called";
    }

    // Private function
    // Can only be accessed from within the current contract
    // Cannot be accessed from derived contracts or externally
    function privateFunc() private pure returns (string memory) {
        return "Private function called";
    }

    function testFuncs() public view returns (string memory, string memory) {
        // Call the public and internal functions
        // Call the external function using "this"
        return (publicFunc(), this.externalFunc());
    }
}

contract DerivedContract is BaseContract {
    function callBaseFuncs() public view returns (string memory, string memory) {
        // Can call the public and internal functions of the base contract
        return (publicFunc(), internalFunc());
    }
}
```

## 函数
函数可以在合约内部和外部定义。函数以类型化参数作为输入。

要写入或更新状态变量，您需要发送交易

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

// Free function defined outside of a contract
// Always has implicit internal visibility
function freeFunction(uint a, uint b) pure returns (uint) {
    return a + b;
}

contract Simple {
    uint sum;

    // Function defined inside a contract
    // Takes two uint parameters as input
    function taker(uint a, uint b) public {
        sum = a + b;
    }

    // Function that returns multiple values
    // The names of return variables can be omitted
    function arithmetic(uint a, uint b)
        public
        pure
        returns (uint, uint)
    {
        return (a + b, a * b);
    }

    // Function that uses an early return
    // Must provide return values together with the return statement
    function earlyReturn(uint a, uint b)
        public
        pure
        returns (uint sum, uint product)
    {
        if (a == 0 || b == 0) {
            return (0, 0);
        }

        sum = a + b;
        product = a * b;
    }
}
```

## Getter函数
`public`编译你的合约会自动为所有状态变量创建 getter 函数。

Getter 函数可以声明`view`或`pure`。

`View`函数承诺它们不会修改状态。

`Pure`函数声明不会更改或读取任何状态变量。具体来说，应该可以`pure`在编译时仅根据其输入和来评估函数`msg.data`，而无需了解当前区块链状态。

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract ViewAndPureExample {
    uint public stateVar = 10;

    // View function
    // Can read state variables but cannot modify them
    function viewFunc() public view returns (uint) {
        return stateVar;
    }

    // Pure function
    // Cannot read or modify state variables
    function pureFunc(uint x, uint y) public pure returns (uint) {
        return x + y;
    }
}
```

## 枚举
Solidity 支持枚举（enum），它们对于跟踪状态很有用。枚举是创建用户定义类型的一种方法。

选项由从 开始的后续无符号整数值表示`0`。

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract Game {
    // Enum representing game status
    enum Status {
        NotStarted,
        InProgress,
        GameOver,
        Won
    }

    // Default value is the first element listed in
    // definition of the type, in this case "NotStarted"
    Status public status;

    // Returns uint
    // NotStarted - 0
    // InProgress - 1
    // GameOver   - 2
    // Won        - 3
    function getStatus() public view returns (Status) {
        return status;
    }

    // Update status by passing uint into input
    function setStatus(Status _status) public {
        status = _status;
    }

    // You can update to a specific enum like this
    function win() public {
        status = Status.Won;
    }

    // delete resets the enum to its first value, 0
    function reset() public {
        delete status;
    }
}
```

## Struct
结构体是可以对多个变量进行分组的自定义类型。

结构体可以在合约之外声明并导入到另一个合约中。

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract Tasks {
    // Define a new struct type 'Task' with three fields.
    struct Task {
        string title;
        string description;
        bool done;
    }

    // Declare a dynamic array 'tasks' of 'Task' structs.
    Task[] public tasks;

    // Add a new task to the 'tasks' array.
    function addTask(string calldata _title, string calldata _description) public {
        // Create a new temporary 'Task' object in memory.
        Task memory newTask;
        newTask.title = _title;
        newTask.description = _description;
        newTask.done = false;

        // Add the 'newTask' into the 'tasks' array.
        tasks.push(newTask);
    }

    // Mark a task as done.
    function markTaskAsDone(uint256 _index) public {
        // Get a reference to the task in the 'tasks' array using the '_index'.
        Task storage task = tasks[_index];
        task.done = true;
    }

    // Get the details of a task.
    function getTask(uint256 _index) public view returns (string memory title, string memory description, bool done) {
        Task storage task = tasks[_index];
        return (task.title, task.description, task.done);
    }

    // Update the title of a task.
    function updateTaskTitle(uint256 _index, string calldata _newTitle) public {
        Task storage task = tasks[_index];
        task.title = _newTitle; // Set the new title of the task
    }

    // Update the description of a task.
    function updateTaskDescription(uint256 _index, string calldata _newDescription) public {
        Task storage task = tasks[_index];
        task.description = _newDescription;
    }

    // Delete the task from the 'tasks' array using the '_index'.
    // Note: The 'delete' keyword in Solidity doesn't actually delete the task from the 'tasks' array,
    // it just sets the task at the given index to its initial default state (i.e., an empty string ("") for 'title' and 'description', and 'false' for 'done').
    function deleteTask(uint256 _index) public {
        delete tasks[_index];
    }
}
```

## 构造函数Constructor
`constructor`是在创建合约时声明的可选函数。

如果没有`constructor`，则契约将假定默认构造函数，相当于`constructor() {}`。

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

// Simple contract with a constructor
contract SimpleContract {
    uint256 public data;

    constructor(uint256 _data) {
        data = _data; // Initialize 'data' with the value passed to the constructor
    }
}

// Contract with a constructor and a function to change the data
contract ChangeableContract {
    uint256 public data;

    constructor(uint256 _data) {
        data = _data; // Initialize 'data' with the value passed to the constructor
    }

    function setData(uint256 _data) public {
        data = _data; // Change 'data' to the new value passed to the function
    }
}

// Base contract A
contract A {
    string public name;

    constructor(string memory _name) {
        name = _name; // Initialize 'name' with the value passed to the constructor
    }
}

// Contract B inherits from A and has its own data
contract B is A {
    uint256 public data;

    constructor(string memory _name, uint256 _data) A(_name) {
        data = _data; // Initialize 'data' with the value passed to the constructor
    }
}

// Parent constructors are always called in the order of inheritance
// regardless of the order of parent contracts listed in the
// constructor of the child contract.

// Order of constructors called:
// 1. A
// 2. B
// 3. C
contract C is A, B {
    constructor(string memory _name, uint256 _data) A(_name) B(_name, _data) {}
}
```

## 数据位置data location
数据位置有三种：`memory`、`storage`和`calldata`。这些关键字用于指定数据的位置。

+ `storage`是存储状态变量的位置。区块链上的永久数据。
+ `memory`用于保存临时值。内存变量的生存期仅限于外部函数调用。
+ `calldata`是存储函数参数的不可修改、非持久的位置（行为类似于内存）。

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract DataLocations {
    uint256[] public numbers; // 'numbers' is a state variable and is stored in 'storage'

    // 'arr' is a function argument and is stored in 'calldata'
    function addToNumbers(uint256[] calldata arr) public {
        for (uint256 i = 0; i < arr.length; i++) {
            numbers.push(arr[i]); // 'arr[i]' is read from 'calldata'
        }
    }

    // 'x' is a local variable and is stored in 'memory'
    function calculateSum(uint256[] calldata arr) public pure returns (uint256) {
        uint256 sum = 0; // 'sum' is a local variable and is stored in 'memory'
        for (uint256 i = 0; i < arr.length; i++) {
            sum += arr[i]; // 'arr[i]' is read from 'calldata'
        }
        return sum;
    }
}
```

## Try/Catch
可以使用 try/catch 语句完美地捕获外部调用中的失败。

该`try`关键字后面必须跟有外部函数调用。

Solidity 还支持各种`catch`块来处理不同类型的异常：

+ `catch Error(string memory reason)`赶上失败`revert()`和`require()`
+ `catch Panic(uint256 panicCode){...}`捕捉失败的恐慌错误，例如失败`assert`（）、除以零、算术溢出等。
+ `catch (bytes memory reason){...}`捕获与任何其他子句不匹配的错误签名。结果，声明的变量返回低级错误数据。
+ `catch {...}`如果您对错误数据不感兴趣，可以使用。

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

// External contract used for try / catch examples
contract ExternalContract {
    address public owner;

    constructor(address _owner) {
        require(_owner != address(0), "Invalid address");
        assert(_owner != 0x0000000000000000000000000000000000000001);
        owner = _owner;
    }

    function performAction(uint256 x) public pure returns (string memory) {
        require(x != 0, "Require failed");
        return "Action performed";
    }
}

contract MainContract {
    event Log(string message);
    event LogBytes(bytes data);

    ExternalContract public externalContract;

    constructor() {
        // This ExternalContract is used for example of try catch with external call
        externalContract = new ExternalContract(msg.sender);
    }

    // Example of try / catch with external call
    // handleExternalCall(0) => Log("External call failed")
    // handleExternalCall(1) => Log("Action performed")
    function handleExternalCall(uint256 _i) public {
        try externalContract.performAction(_i) returns (string memory result) {
            emit Log(result);
        } catch {
            emit Log("External call failed");
        }
    }

    // Example of try / catch with contract creation
    // handleNewContract(0x0000000000000000000000000000000000000000) => Log("Invalid address")
    // handleNewContract(0x0000000000000000000000000000000000000001) => LogBytes("")
    // handleNewContract(0x0000000000000000000000000000000000000002) => Log("ExternalContract created")
    function handleNewContract(address _owner) public {
        try new ExternalContract(_owner) returns (ExternalContract _externalContract) {
            // you can use variable _externalContract here
            emit Log("ExternalContract created");
        } catch Error(string memory reason) {
            // catch failing revert() and require()
            emit Log(reason);
        } catch (bytes memory reason) {
            // catch failing assert()
            emit LogBytes(reason);
        }
    }
}
```

## 错误
Solidity 中的错误提供了一种方便且节省 gas 的方式来解释操作失败的原因。它们可以在合约内部和外部定义（包括接口和库）。

它们必须与`revert`语句一起使用，这会导致当前调用中的所有更改都被恢复，并将错误数据传回给调用者。

错误实例只能使用`revert`语句来创建。

使用自定义错误的方法示例：

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

// Error for insufficient balance for transfer. Needed `required` but only
// `available` available.
// @param available balance available.
// @param required requested amount to transfer.
error InsufficientBalance(uint256 available, uint256 required);

// Error for invalid recipient address
error InvalidRecipient(address recipient);

// Error for transfer amount being zero
error ZeroTransfer();

contract MyToken {
    mapping(address => uint) balance;

    function transfer(address to, uint256 amount) public {
        if (amount > balance[msg.sender]) {
            revert InsufficientBalance({
                available: balance[msg.sender],
                required: amount
            });
        }
//address(0) 代表零地址（zero address），即 0x0000000000000000000000000000000000000000。
//这个地址通常被用作特殊标识，表示一个无效的地址或者未初始化的地址。
        if (to == address(0)) {
            revert InvalidRecipient(to);
        }

        if (amount == 0) {
            revert ZeroTransfer();
        }

        balance[msg.sender] -= amount;
        balance[to] += amount;
    }
    // ...
}
```

## 验证和断言
+ `require(bool condition)`：如果条件为假，则中止执行并恢复状态改变（用作外部组件中的错误）
+ `require(bool condition, string memory message)`：如果条件为假，则中止执行并恢复状态更改。还提供错误消息。
+ `revert()`：中止执行并恢复状态改变
+ `revert(string memory message)`：中止执行并恢复状态更改，并提供解释性字符串
+ `assert(bool condition)`：如果条件为假，则中止执行并恢复状态改变（用于内部错误）

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract MyContract {
    mapping(address => uint) public balance;

    function deposit(uint amount) public {
        // Use require to validate inputs from external calls
        require(amount > 0, "Deposit amount must be greater than 0");

        balance[msg.sender] += amount;
    }

    function withdraw(uint amount) public {
        // Use require to validate inputs from external calls
        require(amount <= balance[msg.sender], "Insufficient balance");

        balance[msg.sender] -= amount;
    }

    function transfer(address to, uint amount) public {
        // Use revert to abort execution and revert state changes
        if (to == address(0)) {
            revert("Cannot transfer to zero address");
        }

        // Use require to validate inputs from external calls
        require(amount <= balance[msg.sender], "Insufficient balance");

        balance[msg.sender] -= amount;
        balance[to] += amount;

        // Use assert for internal errors that should never happen
        assert(balance[msg.sender] + balance[to] == amount);
    }
}
```

## 函数修饰符
修饰符可以用来以声明的方式改变函数的行为。

例如，您可以使用修饰符在执行函数之前自动检查条件。

与函数一样，修饰符可以被覆盖，但只有当它们被标记virtual时才可以。

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract MyContract {
    address public owner;
    uint256 public balance;
    bool private _currentlyExecuting;

    constructor() {
        owner = msg.sender;
        _currentlyExecuting = false;
        balance = 0;
    }

    // Modifier to check if the caller is the owner of the contract
    modifier onlyOwner {
        require(msg.sender == owner, "Caller is not the owner");
        _;
    }

    // Modifier to check if multiple parameters are valid
    modifier validParams(address _addr, uint256 _amount) {
        require(_addr != address(0), "Invalid address");
        require(_amount > 0, "Amount must be greater than zero");
        _;
    }

    // Modifier to prevent reentrancy attacks
    modifier nonReentrant {
        // Check if the function is currently being executed
        require(!_currentlyExecuting, "Reentrant call");

        // Set the flag to true to signal that the function is being executed
        _currentlyExecuting = true;

        // Execute the rest of the function
        _;

        // Reset the flag to false after the function has finished executing
        _currentlyExecuting = false;
    }

    // Function to deposit funds into the contract
    function depositFunds(uint256 _amount) public onlyOwner {
        balance += _amount;
    }

    // Function that can only be called by the owner and is protected against reentrancy attacks
    function sensitiveFunction(address _to, uint256 _amount) public onlyOwner nonReentrant validParams(_to, _amount) {
        // Transfer the specified amount to the specified address
        balance -= _amount;
        // This is just an example, in a real contract you would use the transfer function of the address
    }
}
```

## 事件Events
当执行某些操作时，EVM 会发出事件。这些事件在合约代码中使用`event`关键字定义，并且可以使用关键字发出`emit`。

您最多可以添加索引到三个参数的属性。这些参数被添加到称为“主题”的特殊数据结构中

主题允许您搜索事件。没有索引属性的参数存储在日志的数据部分中

```plain
pragma solidity ^0.8.0;

contract MyContract {
    // Define events
    event Transaction(address indexed sender, address indexed receiver, uint256 amount);
    event UserRegistered(address indexed user, string name);
    event UserUpdated(address indexed user, string name);
    event ContractPaused();
    event ContractResumed();

    // Struct to hold user data
    struct User {
        string name;
    }

    // Mapping from address to User data
    mapping(address => User) public users;

    // Variable to track whether the contract is paused
    bool public paused = false;

    // Function to transfer funds
    function transferFunds(address receiver, uint256 amount) public {
        (bool success, ) = receiver.call{value: amount}("");
        require(success, "Transfer failed.");

        // Emit the event to log the transaction
        emit Transaction(msg.sender, receiver, amount);
    }

    // Function to register a new user
    function registerUser(string memory name) public {
        users[msg.sender] = User(name);
        emit UserRegistered(msg.sender, name);
    }

    // Function to update a user's information
    function updateUser(string memory name) public {
        users[msg.sender].name = name;
        emit UserUpdated(msg.sender, name);
    }

    // Function to pause the contract
    function pauseContract() public {
        paused = true;
        emit ContractPaused();
    }

    // Function to resume the contract
    function resumeContract() public {
        paused = false;
        emit ContractResumed();
    }
}
```

## 以太单位
以太币是以太坊区块链的原生加密货币。它用于支付网络上的交易费用和计算服务。

Ether 可以分割成更小的单位，最小单位是`wei`。

``文字数字可以带后缀wei，`gwei`或`ether`来指定以太的子面额。没有后缀（`wei`、`gwei`、`ether`）的以太数字被认为是`wei`。

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract EtherUnits {
    uint256 public oneWei = 1 wei;
    // 1 wei is equal to 1
    bool public isOneWei = 1 wei == 1;

    unit256 public oneGwei = 1 gwei;
    // 1 gwei is equal to 1e9 wei
    bool public isOneGwei = 1 gwei == 1e9;

    uint256 public oneEther = 1 ether;
    // 1 ether is equal to 10^18 wei
    bool public isOneEther = 1 ether == 1e18;
}
```



## 应付账款
Solidity 中的关键字`payable`用于指示函数可以接收 Ether。如果您想在函数中接收 Ether，则需要这样做。

如果接收合约没有接收功能，则会引发异常`receive() external payable {...}`

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract PayableExample {
    // Payable address can receive Ether
    address payable public recipient;

    // Payable constructor can receive Ether
    constructor(address payable _recipient) {
        recipient = _recipient;
    }

    // Function to deposit Ether into this contract.
    // Call this function along with some Ether.
    function deposit() public payable {}

    // Function to withdraw all Ether from this contract.
    // Notice how this function below does not need payable keyword.
    // It has been defined in the global recipient variable.
    function withdraw() public {
        // get the amount of Ether stored in this contract
        uint256 amount = address(this).balance;

        //向目标地址 recipient 发送 amount 以太币（Ether）。
        (bool success,) = recipient.call{value: amount}("");
        require(success, "Failed to send Ether");
    }

    // Function to transfer Ether from this contract to another address
    function transfer(address payable _to, uint256 _amount) public {
        // Note that "_to" is declared as payable
        (bool success,) = _to.call{value: _amount}("");
        require(success, "Failed to send Ether");
    }

    // This function is called when Ether is sent without data
    receive() external payable {
        deposit();
    }
}
```

## fallback回退
`fallback`在下列情况下会激活该功能：

+ 调用合约时不存在任何函数
+ 没有提供数据，也没有接收以太函数

一个合约最多可以`fallback`声明一个函数，使用以下任一方式：

+ `fallback() external payable { ... }`
+ `fallback (bytes calldata input) external payable returns (bytes memory output) { ... }`

如果`fallback`使用 function 代替 function `receive`，它将只有 2300 gas 津贴，以限制 gas 密集型操作。

请注意，`function`声明函数时不需要关键字`fallback`。所有后备函数都必须具有`external`可见性。

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

// Example 1: Receiving Ether
contract EtherReceiver {
    uint public totalReceived;

    // This fallback function is used to keep track of the total Ether received by the contract.
    // It's triggered when Ether is sent to the contract's address in a transaction without data.
    fallback() external payable {
        totalReceived += msg.value;
    }
}

// Example 2: Proxy Pattern
contract Proxy {
    address public implementation;

    constructor(address _implementation) {
        implementation = _implementation;
    }

    // This fallback function is used to implement the proxy pattern.
    // Assuming implementation variable has `delegatecall` function.
    // It catches any calls that don't match any of the proxy's functions and forwards them to the implementation contract.
    // It's triggered when a function that doesn't exist in the proxy contract is called.
    fallback() external payable {
        (bool success, ) = implementation.delegatecall(msg.data);
        require(success);
    }
}

// Example 3: Default Functionality
contract DefaultFunctionality {
    // Event to emit when the fallback function is triggered
    event FallbackTriggered(string message);

    // This fallback function provides default functionality when no function is specified or the specified function doesn't exist.
    // It's triggered when the contract is called without specifying any function, or if the function specified doesn't exist in the contract.
    fallback() external {
        // Emit an event when the fallback function is triggered
        emit FallbackTriggered("Fallback function was called.");
    }
}

// Example 4: Fallback with bytes type input and output
contract FallbackWithInputOutput {
    // Event to emit when the fallback function is triggered
    event FallbackTriggered(string message, bytes input, bytes output);

    // This fallback function takes an input, performs some operation, and returns an output.
    // It's triggered when the contract is called without specifying any function, or if the function specified doesn't exist in the contract.
    fallback(bytes calldata input) external payable returns (bytes memory) {
        // Perform some operation with the input
        bytes memory output = new bytes(input.length);
        for (uint i = 0; i < input.length; i++) {
            output[i] = input[i];
        }

        // Emit an event when the fallback function is triggered
        emit FallbackTriggered("Fallback function was called.", input, output);

        return output;
    }
}
```



## 交易Ehter
以太币交易包括两个主要部分：发送和接收以太币。

回想一下，交易以太币的方法围绕`payable`关键字展开。

### 发送以太币
+ `transfer`/ `send`（由于 2300 gas 限制允许潜在的重新进入攻击，因此不推荐）
+ `sendValue` [开放Zeppelin](https://docs.openzeppelin.com/contracts/2.x/api/utils#Address-sendValue-address-payable-uint256-)
+ `call`

推荐的方法是`call`与可重入保护搭配使用。

使用方法发送以太币的简单示例`call`：

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract SendEther {

    address public sender;
    uint256 public amount;

    mapping(address => uint256) public balances;

    function sendViaCall(address payable _to) public payable {

        // Remember to update the balance before interactions prevent reentrancy attacks!
        // Follow Checks-Effects-Interactions pattern
        balances[msg.sender] -= amount;
        balances[to] += amount;

        // Call returns a boolean value indicating success or failure.
        // This is the current recommended method to use.
        (bool sent, bytes memory data) = _to.call{value: msg.value}("");
        require(sent, "Failed to send Ether");
    }
}
```


## 参考
[https://www.levelup.xyz/solidity](https://www.levelup.xyz/solidity)

