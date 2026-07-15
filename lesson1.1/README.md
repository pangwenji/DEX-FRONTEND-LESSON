# 第 1 课：部署自己的 ERC-20 代币

> 建议课时：45～60 分钟｜对应页面：`/release`

## 学习目标

完成本课后，你将能够：

- 说明钱包、RPC、链 ID 与合约地址在 DApp 中的关系；
- 使用 Wagmi 连接 Sepolia 钱包；
- 从前端部署 ERC-20 合约并等待交易确认；
- 将新代币添加到钱包，并为下一课准备代币对。

## 课前准备

1. 安装 Node.js 20+、MetaMask，并切换到 Sepolia。
2. 准备少量 Sepolia ETH 作为 Gas。
3. 进入 `lesson1.1/code`，执行 `npm install && npm run dev`。
4. 检查 `.env` 中的 RPC 与 WalletConnect 配置；不要提交密钥。

## 核心概念

### 从点击按钮到链上状态

```text
填写代币参数 → 钱包签名 → 广播部署交易 → 等待区块确认
             → 获得合约地址 → 钱包导入代币 → 查询余额
```

- **交易哈希**只表示交易已广播，不代表成功。
- **交易回执**中的 `status` 才能确认执行结果。
- **合约地址**由部署交易产生，是后续授权、建池和交换的代币标识。
- ERC-20 的显示数量需要结合 `decimals`；链上保存的是整数。

## 代码导读

| 文件 | 作用 | 授课关注点 |
| --- | --- | --- |
| `src/lib/wagmi.ts` | 链与钱包配置 | chain、transport、connector |
| `src/lib/constants.ts` | 网络和业务常量 | 地址必须与当前网络匹配 |
| `src/lib/release-contract.ts` | 部署字节码与 ABI | constructor 参数编码 |
| `src/app/release/page.tsx` | 部署页面 | 表单校验、写链、确认与错误状态 |
| `src/components/NetworkChecker.tsx` | 网络检查 | 错误网络时阻止写操作 |

## 实操任务

### 任务 1：连接钱包

启动项目后连接 MetaMask。记录当前账户和 chain ID，尝试切换到其他网络，观察页面提示。

### 任务 2：部署代币

输入名称、符号和初始供应量，例如 `Course Token`、`CTK`、`1000000`。发起交易后分别观察：等待签名、等待确认、部署成功和失败状态。

### 任务 3：验证结果

1. 在区块浏览器中查看部署交易；
2. 记录新代币合约地址；
3. 将代币添加到 MetaMask；
4. 核对钱包余额与初始供应量；
5. 再部署一种代币，作为下一课的交易对。

## 关键代码思路

```ts
const hash = await deployContract({ abi, bytecode, args })
const receipt = await waitForTransactionReceipt({ hash })

if (receipt.status !== 'success' || !receipt.contractAddress) {
  throw new Error('合约部署失败')
}
```

页面应区分 `isPending`（等待钱包）、`isConfirming`（等待上链）和 `isSuccess`，避免用户重复提交。

## 常见问题

- **钱包没有弹窗**：检查钱包是否锁定、页面是否已授权连接。
- **insufficient funds**：测试 ETH 不足以支付 Gas。
- **合约部署成功但余额为 0**：检查初始供应量接收者和 decimals 换算。
- **导入代币失败**：确认网络与合约地址一致，并检查地址格式。

## 课堂检查点

- 为什么不能只看到交易哈希就提示“部署成功”？
- `100` 个 18 位精度代币在链上的整数是多少？
- 合约地址和钱包地址分别代表什么？

## 课后作业

为部署表单补充名称、符号、供应量校验，并在成功区域增加区块浏览器链接。提交两种代币的地址和交易哈希。

## 下一课

带上本课部署的两个 ERC-20 地址，进入[第 2 课：流动性池](../lesson1.2/README.md)。
