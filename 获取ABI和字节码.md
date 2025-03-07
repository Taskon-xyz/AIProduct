# 如何获取合约的 ABI 和字节码

在将合约提供给第三方部署时，您需要提供合约的 ABI（应用程序二进制接口）和字节码。本文档将详细说明如何从源代码中获取这些信息。

## 目录

- [什么是 ABI 和字节码](#什么是-abi-和字节码)
- [使用 Hardhat 获取](#使用-hardhat-获取)
- [使用 Truffle 获取](#使用-truffle-获取)
- [使用 Remix 获取](#使用-remix-获取)
- [创建合约 JSON 文件](#创建合约-json-文件)
- [扁平化合约代码](#扁平化合约代码)

## 什么是 ABI 和字节码

- **ABI (Application Binary Interface)**: 定义了如何与合约交互的接口，包括函数签名、事件定义等。它是一个 JSON 格式的数组，描述了合约的所有公开函数和事件。

- **字节码 (Bytecode)**: 是编译后的合约代码，可以直接部署到区块链上。它是一个十六进制字符串，表示 EVM（以太坊虚拟机）可以执行的指令。

## 使用 Hardhat 获取

如果您使用 Hardhat 开发框架，可以通过以下步骤获取 ABI 和字节码：

### 1. 编译合约

```bash
npx hardhat compile
```

### 2. 创建导出脚本

创建一个名为 `scripts/export-abi.js` 的文件：

```javascript
const fs = require('fs');
const path = require('path');

async function main() {
  // 获取编译后的合约信息
  const AirdropFactory = await ethers.getContractFactory("AirdropFactory");
  const AirdropPool = await ethers.getContractFactory("AirdropPool");
  const ERC1967Proxy = await ethers.getContractFactory("ERC1967Proxy");

  // 创建输出目录
  const outputDir = path.join(__dirname, '../abi-export');
  if (!fs.existsSync(outputDir)) {
    fs.mkdirSync(outputDir);
  }

  // 导出 AirdropFactory
  const factoryJson = {
    abi: AirdropFactory.interface.format('json'),
    bytecode: AirdropFactory.bytecode
  };
  fs.writeFileSync(
    path.join(outputDir, 'AirdropFactory.json'),
    JSON.stringify(factoryJson, null, 2)
  );
  console.log('AirdropFactory ABI 和字节码已导出');

  // 导出 AirdropPool
  const poolJson = {
    abi: AirdropPool.interface.format('json'),
    bytecode: AirdropPool.bytecode
  };
  fs.writeFileSync(
    path.join(outputDir, 'AirdropPool.json'),
    JSON.stringify(poolJson, null, 2)
  );
  console.log('AirdropPool ABI 和字节码已导出');

  // 导出 ERC1967Proxy
  const proxyJson = {
    abi: ERC1967Proxy.interface.format('json'),
    bytecode: ERC1967Proxy.bytecode
  };
  fs.writeFileSync(
    path.join(outputDir, 'ERC1967Proxy.json'),
    JSON.stringify(proxyJson, null, 2)
  );
  console.log('ERC1967Proxy ABI 和字节码已导出');
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

### 3. 运行导出脚本

```bash
npx hardhat run scripts/export-abi.js
```

这将在 `abi-export` 目录下创建三个 JSON 文件，包含各个合约的 ABI 和字节码。

## 使用 Truffle 获取

如果您使用 Truffle 开发框架，可以通过以下步骤获取 ABI 和字节码：

### 1. 编译合约

```bash
truffle compile
```

### 2. 创建导出脚本

创建一个名为 `scripts/export-abi.js` 的文件：

```javascript
const fs = require('fs');
const path = require('path');

// 导入编译后的合约 JSON 文件
const AirdropFactory = require('../build/contracts/AirdropFactory.json');
const AirdropPool = require('../build/contracts/AirdropPool.json');
const ERC1967Proxy = require('../build/contracts/ERC1967Proxy.json');

// 创建输出目录
const outputDir = path.join(__dirname, '../abi-export');
if (!fs.existsSync(outputDir)) {
  fs.mkdirSync(outputDir);
}

// 导出 AirdropFactory
const factoryJson = {
  abi: AirdropFactory.abi,
  bytecode: AirdropFactory.bytecode
};
fs.writeFileSync(
  path.join(outputDir, 'AirdropFactory.json'),
  JSON.stringify(factoryJson, null, 2)
);
console.log('AirdropFactory ABI 和字节码已导出');

// 导出 AirdropPool
const poolJson = {
  abi: AirdropPool.abi,
  bytecode: AirdropPool.bytecode
};
fs.writeFileSync(
  path.join(outputDir, 'AirdropPool.json'),
  JSON.stringify(poolJson, null, 2)
);
console.log('AirdropPool ABI 和字节码已导出');

// 导出 ERC1967Proxy
const proxyJson = {
  abi: ERC1967Proxy.abi,
  bytecode: ERC1967Proxy.bytecode
};
fs.writeFileSync(
  path.join(outputDir, 'ERC1967Proxy.json'),
  JSON.stringify(proxyJson, null, 2)
);
console.log('ERC1967Proxy ABI 和字节码已导出');
```

### 3. 运行导出脚本

```bash
node scripts/export-abi.js
```

## 使用 Remix 获取

如果您使用 Remix IDE 开发合约，可以通过以下步骤获取 ABI 和字节码：

### 1. 编译合约

- 在 Remix 中打开您的合约文件
- 切换到 "Solidity Compiler" 选项卡
- 选择正确的编译器版本（0.8.28）
- 点击 "Compile" 按钮

### 2. 获取 ABI

- 编译成功后，点击 "Compilation Details" 按钮
- 在弹出的窗口中，找到 "ABI" 部分
- 点击复制图标或 "Copy" 按钮
- 将复制的 ABI 保存到一个 JSON 文件中

### 3. 获取字节码

- 在同一个 "Compilation Details" 窗口中
- 找到 "Bytecode" 部分
- 展开 "object" 字段
- 复制其中的十六进制字符串（不包括 "0x" 前缀）
- 将复制的字节码保存到同一个 JSON 文件中

## 创建合约 JSON 文件

为了让第三方能够使用您的合约，您需要为每个合约创建一个 JSON 文件，格式如下：

```json
{
  "abi": [...],  // 合约的 ABI 数组
  "bytecode": "0x..." // 合约的字节码，以 0x 开头
}
```

例如，`AirdropFactory.json` 文件的内容应该类似于：

```json
{
  "abi": [
    {
      "inputs": [],
      "stateMutability": "nonpayable",
      "type": "constructor"
    },
    {
      "inputs": [
        {
          "internalType": "uint256",
          "name": "feeRate",
          "type": "uint256"
        }
      ],
      "name": "InvalidFeeRate",
      "type": "error"
    },
    // ... 更多 ABI 条目
  ],
  "bytecode": "0x608060405234801561001057600080fd5b5061001a3361001f565b61002d565b600080546001600160a01b031916905561144f806100..."
}
```

## 扁平化合约代码

如果您希望第三方能够在区块链浏览器上验证合约，您需要提供扁平化的合约代码。扁平化是指将所有导入的合约内容合并到一个文件中。

### 使用 Hardhat 扁平化合约

1. 安装 hardhat-flatten 插件：

```bash
npm install --save-dev hardhat-flatten
```

2. 在 `hardhat.config.js` 中添加插件：

```javascript
require("hardhat-flatten");
```

3. 运行扁平化命令：

```bash
npx hardhat flatten contracts/AirdropFactory.sol > flat_AirdropFactory.sol
npx hardhat flatten contracts/AirdropPool.sol > flat_AirdropPool.sol
```

4. 修复扁平化文件中的许可证声明重复问题：
   - 打开扁平化后的文件
   - 保留第一个 `// SPDX-License-Identifier: ...` 行
   - 删除所有其他的许可证声明行

### 使用 Truffle 扁平化合约

1. 安装 truffle-flattener：

```bash
npm install --save-dev truffle-flattener
```

2. 运行扁平化命令：

```bash
npx truffle-flattener contracts/AirdropFactory.sol > flat_AirdropFactory.sol
npx truffle-flattener contracts/AirdropPool.sol > flat_AirdropPool.sol
```

3. 同样需要修复许可证声明重复问题。

## 总结

通过以上步骤，您可以获取合约的 ABI 和字节码，并创建适合第三方部署的 JSON 文件。如果需要在区块链浏览器上验证合约，还可以提供扁平化的合约代码。

将这些文件提供给第三方后，他们可以按照《别人部署.md》中的步骤部署您的合约，而无需访问原始源代码。 