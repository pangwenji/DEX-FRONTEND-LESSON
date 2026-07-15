# 第 3 课：创建池与管理流动性

> 建议课时：60～90 分钟｜对应页面：`/liquidity`、`/positions`

## 学习目标

- 理解做市、价格区间、费率档位和流动性头寸；
- 完成“检查池 → 创建池 → 授权代币 → 添加流动性”的流程；
- 正确处理多笔交易的顺序与状态；
- 理解头寸为何常用 NFT 表示。

## 核心概念

### 集中流动性

做市者将资金配置在一个价格区间内。价格处于区间时资金参与交易并赚取手续费；价格离开区间后头寸不再活跃，并逐渐偏向其中一种资产。

```text
选择 Token A/B + Fee
        ↓
查询池是否存在 ──否──→ 创建并初始化池
        │是                    │
        └──────────┬───────────┘
                   ↓
          授权 Token A 与 B
                   ↓
        设置价格区间和投入数量
                   ↓
            Mint 头寸 NFT
```

### 价格与 Tick

协议通常用离散 tick 表示价格。UI 输入的价格需要转换到合法 tick，且上下界必须满足 `tickLower < tickUpper`。费率档位通常决定 tick spacing。

## 代码导读

| 文件 | 作用 |
| --- | --- |
| `src/app/liquidity/page.tsx` | 流动性页面 |
| `src/components/pools/CreatePoolModal.tsx` | 创建与初始化池 |
| `src/components/pools/AddLiquidity.tsx` | 数量、区间、授权与添加 |
| `src/components/pools/LiquidityManager.tsx` | 业务步骤编排 |
| `src/hooks/useCreatePool.ts` | 创建池写链逻辑 |
| `src/hooks/usePositions.ts` | 查询用户头寸 |
| `src/app/positions/page.tsx` | 头寸列表 |

## 实操任务

1. 使用上一课的两个代币地址，选择合适费率。
2. 调用 `/api/pools/check` 检查池是否存在。
3. 若不存在，创建池并设置初始价格。
4. 分别检查两种代币 allowance；不足时先授权。
5. 设置价格区间和投入数量，添加流动性。
6. 打开 `/positions`，确认新的头寸与交易记录。

## 交易状态机

多步写链不要只用一个布尔值表示状态，建议显式建模：

```ts
type Step =
  | 'idle'
  | 'checking-pool'
  | 'creating-pool'
  | 'approving-token0'
  | 'approving-token1'
  | 'adding-liquidity'
  | 'success'
  | 'error'
```

每一步都应等待交易回执成功后再进入下一步；失败时保留上下文，让用户能安全重试。

## 风险提示

- 初始价格输入反向会造成错误定价，甚至被套利。
- `approve` 的 spender 必须是实际转移代币的合约。
- 无限授权交互简单，但风险更高；教学中应解释精确授权与无限授权的取舍。
- 添加流动性不是保本操作，存在无常损失和智能合约风险。

## 课堂检查点

- 为什么添加两种代币可能需要两次授权？
- 当前价格离开区间后，头寸会发生什么？
- 创建池成功后，为什么仍可能无法添加流动性？

## 课后作业

为多步交易增加步骤条、失败重试与交易浏览器链接；在提交前展示预计投入数量、价格区间和授权对象。

下一课：[实现代币交换](../lesson1.4/README.md)。
