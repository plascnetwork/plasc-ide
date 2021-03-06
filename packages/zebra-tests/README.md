## Zebra-Tests

`zebra-tests` is a tool to test Solidity smart contracts. It works underneath Tron IDE plugin "Solidity Unit Testing" which is used to write and run test cases for a contract. Tests are written in Solidity itself. `zebra-tests` can be used as CLI and a library too.

### Installation
As a dev dependency:

`npm install --save-dev zebra-tests`

As a global NPM module to use as CLI:

`npm -g install zebra-tests`

### Test structure
zebra-tests provides and injects a built-in assert library for testing purpose.

#### Assert library

| Available functions  | Supported types |
| ------------- | ------------- |
| `Assert.ok()`  | `bool`  |
| `Assert.equal()`  | `uint`, `int`, `bool`, `address`, `bytes32`, `string`  |
| `Assert.notEqual()` | `uint`, `int`, `bool`, `address`, `bytes32`, `string`  |
| `Assert.greaterThan()` | `uint`, `int` |
| `Assert.lesserThan()` | `uint`, `int` |

#### Available special functions:
Apart from above, library provides some special functions as:

* `beforeEach()` - runs before each test
* `beforeAll()` - runs before all tests
* `afterEach()` - runs after each test
* `afterAll()` - runs after all tests


### How to use

#### As command line interface

* To run all files inside `examples` directory
```
$ zebra-tests examples/
``` 
* To run single test file named `simple_storage_test.sol` inside `examples` directory
```
$ zebra-tests examples/simple_storage_test.sol
```
**NOTE:** zebra-tests will assume that name of tests file ends with `"_test.sol"`. e.g `simple_storage_test.sol`


**Example:**
Consider for a simple storage contract named `simple_storage.sol`:

```Javascript
pragma solidity >=0.4.22 <0.7.0;

contract SimpleStorage {
  uint public storedData;

  constructor() public {
    storedData = 100;
  }

  function set(uint x) public {
    storedData = x;
  }

  function get() public view returns (uint retVal) {
    return storedData;
  }
}
```

test file `simple_storage_test.sol` can be as:


```Javascript
pragma solidity >=0.4.22 <0.7.0;
import "zebra_tests.sol"; // injected by zebra-tests
import "./simple_storage.sol";

contract MyTest {
  SimpleStorage foo;

  function beforeEach() public {
    foo = new SimpleStorage();
  }

  function initialValueShouldBe100() public returns (bool) {
    return Assert.equal(foo.get(), 100, "initial value is not correct");
  }

  function valueIsSet200() public returns (bool) {
    foo.set(200);
    return Assert.equal(foo.get(), 200, "value is not 200");
  }

  function valueIsNotSet200() public returns (bool) {
    return Assert.notEqual(foo.get(), 200, "value is 200");
  }
}
```

Running `simple_storage_test.sol` file will output as:

```
	???  MyTest
[19:15:02] payload method is  eth_gasPrice
[19:15:02] payload method is  eth_sendTransaction
[19:15:02] payload method is  eth_getTransactionReceipt
[19:15:02] payload method is  eth_gasPrice
[19:15:02] payload method is  eth_sendTransaction
[19:15:02] payload method is  eth_getTransactionReceipt
	???  Initial value should be100
[19:15:02] payload method is  eth_gasPrice
[19:15:02] payload method is  eth_sendTransaction
[19:15:02] payload method is  eth_getTransactionReceipt
[19:15:02] payload method is  eth_gasPrice
[19:15:02] payload method is  eth_sendTransaction
[19:15:02] payload method is  eth_getTransactionReceipt
	???  Value is set200
[19:15:02] payload method is  eth_gasPrice
[19:15:02] payload method is  eth_sendTransaction
[19:15:02] payload method is  eth_getTransactionReceipt
[19:15:02] payload method is  eth_gasPrice
[19:15:02] payload method is  eth_sendTransaction
[19:15:02] payload method is  eth_getTransactionReceipt
	???  Value is not set200
```

:point_right: zebra-test can also be used for continuous integration testing. See example [Su Squares contract](https://github.com/su-squares/ethereum-contract/tree/e542f37d4f8f6c7b07d90a6554424268384a4186) and [Travis build](https://travis-ci.org/su-squares/ethereum-contract/builds/446186067) 

#### As a Library for development 

Import library:
```Javascript
const ZebraTests = require('zebra-tests');
```

Run a single test object:
```Javascript
ZebraTests.runTest (testName: string, testObject: any, contractDetails: CompiledContract, fileAST: AstNode, opts: Options, testCallback: TestCbInterface, resultsCallback: ResultCbInterface)
```
<em>Params:-</em>
1. `testName` - Name of the test
2. `testObject` -  Web3 1.0 contract instance of the test
3. `contractDetails` - Contract details
4. `fileAST` - AST of test file
5. `opts` - Custom options
6. `testCallback(object)` -  Called each time there is a test event. 3 possible type of objects:
* `{ type: 'contract', value: '<TestName>', filename: '<test_filename.sol>' }`
* `{ type: 'testPass', value: '<name of testing function>', time: <time taken>, context: '<TestName>'}`
* `{ type: 'testFailure', value: '<name of testing function>', time: <time taken>, context: '<TestName>', errMsg: '<message in the Assert>' }`

7. `resultsCallback(object)` - Callback with test results
* `passingNum` - number of passing tests
* `failureNum` - number of failing tests
* `timePassed` - time it took for all the tests to run (in seconds)

Running a set of tests given the sourcecode:
```Javascript
ZebraTests.runTestSources(contractSources: SrcIfc, versionUrl: string, usingWorker: boolean, testCallback: Function, resultCallback: Function, finalCallback: any, importFileCb: Function, opts: Options);
```
<em>Params:-</em>
1. `contractSources` - Contract sources
2. `versionUrl` - URL of solc compiler to load
3. `usingWorker` - Set `true` if compiler should be loaded using web-worker
4. `testCallback(object)` - Called each time there is a test event. 3 possible type of objects:
* `{ type: 'contract', value: '<TestName>', filename: '<test_filename.sol>' }`
* `{ type: 'testPass', value: '<name of testing function>', time: <time taken>, context: '<TestName>'}`
* `{ type: 'testFailure', value: '<name of testing function>', time: <time taken>, context: '<TestName>', errMsg: '<message in the Assert>' }`

5. `resultCallback(err, object)` -  Callback with test results
* `passingNum` - number of passing tests
* `failureNum` - number of failing tests
* `timePassed` - time it took for all the tests to run (in seconds)

6. `finalCallback(err)` - called when all tests finish running.
7. `importCb(url, cb)` - Callback to resolve imported files
8. `opts` - Custom options


## Contribute

Please feel free to open an issue or a pull request. 

In case you want to add a code, do have a look to our contribution guidelnes [here](https://github.com/TRON-US/zebra/blob/master/CONTRIBUTING.md). Reach us in [Gitter](https://gitter.im/tronide/community) in case of any queries.


