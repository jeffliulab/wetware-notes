# DishBrain — 培养皿里的神经元学会打 Pong

> *2022 年 Kagan 等人把约 80 万个活体神经元养在高密度多电极阵列上，接入仿真版乒乓球游戏 Pong；它们在**5 分钟内**就表现出有方向的学习。这是"湿件计算"从概念走向实证的标志性实验，也是本板块的旗舰案例。*
>
> **难度**：Advanced
> **前置知识**：[体外学习与可塑性导论](index.md)、[体外神经元网络](../01_Neuron_Networks_InVitro/Overview.md)
> **后续阅读**：[生物-电子闭环架构](../04_Biohybrid_Systems/Overview.md)

---

## 1. 直觉：给神经元一个"身体"和一个"世界"

孤立培养的神经元会自发放电，但那只是噪声般的活动。**DishBrain 的核心想法是：把神经网络嵌进一个感知–行动闭环**——让它通过电刺激"看到"球的位置，通过自己的放电"挥动"球拍，再根据击中与否得到反馈。一旦有了"身体"（球拍）和"世界"（Pong），神经元的活动就有了**后果**，于是它们开始组织起来去改善后果。

类比：你不会通过看一个人的脑电静息态来判断他会不会打球；你得把球拍递给他、让他下场打。DishBrain 就是把球拍递给了培养皿里的神经元。

---

## 2. 形式化定义与理论框架

**DishBrain** 是一个把**体外神经元网络 (in-vitro neuronal network)** 通过**高密度多电极阵列 (High-Density Multi-Electrode Array, HD-MEA)** 接入仿真环境、形成**闭环感知–行动回路**的平台。Cortical Labs 将这类"活体神经元 + 硅基接口"的混合系统称为**合成生物智能 (Synthetic Biological Intelligence, SBI)**。

其学习被作者用**自由能原理 (Free Energy Principle, FEP)** 与**主动推断 (Active Inference)** 来解释：智能体倾向于**最小化感官输入的不可预测性**。形式上，变分自由能 $F$ 是"意外"（负对数证据）的上界：

$$
F = \mathbb{E}_{q(s)}\big[\ln q(s) - \ln p(o, s)\big] \;\ge\; -\ln p(o)
$$

其中 $o$ 是观测（感官刺激），$s$ 是环境隐状态，$q(s)$ 是网络的内部"信念"。智能体可以通过两条途径降低 $F$：调整内部信念（感知），或**采取行动改变观测**（行动）。DishBrain 的实验设计正是据此构造反馈——见 §4.2。

---

## 3. 历史与动机

体外神经元能否"学习"，长期停留在"刺激–反应可塑性"层面（如 LTP/LTD）。真正的转折是**给网络一个具身的、有目标的任务**：

- 平台与团队：Cortical Labs（墨尔本）联合 Monash 等机构。
- 标志性论文：**Kagan et al. (2022), *Neuron***，标题直言"in vitro neurons learn and exhibit sentience when embodied in a simulated game-world"。
- 规模：约 **800,000** 个神经元（小鼠皮层来源 + 人 iPSC 衍生），生长在含数千–数万电极的 HD-MEA 上。

动机有双重意义：对神经科学，它提供了研究学习/可塑性的**可控闭环平台**；对计算，它探索把活体神经组织当作**自适应计算基质**的可能——这正是 wetware computing 的核心命题。

---

## 4. 实验机制

### 4.1 闭环回路

<div class="diagram">
<svg viewBox="0 0 720 260" xmlns="http://www.w3.org/2000/svg" font-family="Georgia, serif">
  <!-- neurons on MEA -->
  <rect x="60" y="95" width="170" height="70" rx="8" fill="var(--dia-accent-soft, rgba(196,103,58,.10))" stroke="var(--dia-accent, #c4673a)" stroke-width="1.5"/>
  <text x="145" y="124" text-anchor="middle" font-size="14" fill="var(--dia-accent, #c4673a)" font-weight="bold">神经元 @ HD-MEA</text>
  <text x="145" y="144" text-anchor="middle" font-size="11" fill="var(--dia-stroke-soft, #6b5f4e)">~800k 细胞</text>
  <!-- game world -->
  <rect x="490" y="95" width="170" height="70" rx="8" fill="none" stroke="var(--dia-blue, #2c4d6e)" stroke-width="1.5"/>
  <text x="575" y="124" text-anchor="middle" font-size="14" fill="var(--dia-blue, #2c4d6e)" font-weight="bold">仿真 Pong</text>
  <text x="575" y="144" text-anchor="middle" font-size="11" fill="var(--dia-stroke-soft, #6b5f4e)">球 + 球拍</text>
  <!-- sense (stim) top arrow: world -> neurons -->
  <path d="M490,110 C380,40 250,40 158,92" fill="none" stroke="var(--dia-green, #4a6741)" stroke-width="1.5" marker-end="url(#d)"/>
  <text x="324" y="40" text-anchor="middle" font-size="12" fill="var(--dia-green, #4a6741)">感知：电刺激编码球位置</text>
  <!-- act (record) bottom arrow: neurons -> world -->
  <path d="M230,150 C340,220 460,220 560,166" fill="none" stroke="var(--dia-accent, #c4673a)" stroke-width="1.5" marker-end="url(#e)"/>
  <text x="396" y="232" text-anchor="middle" font-size="12" fill="var(--dia-accent, #c4673a)">行动：读放电 → 移动球拍</text>
  <!-- feedback note -->
  <text x="360" y="190" text-anchor="middle" font-size="11" fill="var(--dia-stroke-soft, #6b5f4e)">击中→可预测反馈；漏接→随机刺激（高"意外"）</text>
  <defs>
    <marker id="d" markerWidth="9" markerHeight="9" refX="5" refY="4.5" orient="auto"><path d="M0,0 L9,4.5 L0,9 z" fill="var(--dia-green, #4a6741)"/></marker>
    <marker id="e" markerWidth="9" markerHeight="9" refX="5" refY="4.5" orient="auto"><path d="M0,0 L9,4.5 L0,9 z" fill="var(--dia-accent, #c4673a)"/></marker>
  </defs>
</svg>
</div>
<p class="figure-caption">Figure 1 — DishBrain 闭环：电刺激把球位置"喂"给神经元（感知），读取运动区放电来移动球拍（行动），反馈以可预测/不可预测刺激区分输赢。</p>

### 4.2 关键设计：用"可预测性"做奖惩

DishBrain 没有传统意义的奖励信号，而是利用 FEP 的洞见构造反馈：

- **击中球（好结果）** → 给予**结构化、可预测**的刺激（低意外）。
- **漏接球（坏结果）** → 给予**不可预测的随机**刺激（高意外）。

因为网络"想要"最小化感官输入的不可预测性，它会自组织地朝"多击中"的方向调整——于是看起来像在**学习**。作者报告，相对对照组，培养网络在**约 5 分钟**实时游戏内即显现出表现提升。

### 4.3 关于"sentience"的措辞争议

论文标题用了 "sentience"。作者明确这是 Seth 等人定义下的**狭义可感性/响应性**（对环境信息的功能性敏感与适应），**不是**主观意识或痛觉。这一用词引发了广泛讨论，也把 [体外系统的感受性与伦理](../06_Ethics_Frontiers/Overview.md) 推到台前。

---

## 5. Worked Example：一局 Pong 里发生了什么

1. **感知编码**：球的 $x$ 位置映射到 MEA 上某区电极的刺激位点，$y$/距离用刺激频率编码 → 神经元"感觉到"球在哪。
2. **行动读出**：把电极阵列分成两块"运动区"，两区放电活动之差决定球拍上/下移动速度。
3. **闭环更新**：球拍移动 → 球反弹或漏接 → 据结果回送可预测/随机刺激 → 影响网络下一步活动。
4. **学习的度量**：统计每"回合"的平均击球数 (rally length)、命中率随时间的变化，与（不接反馈的）对照组比较；提升即学习证据。

---

## 6. Common Pitfalls

> **易错点 1：把 "sentience" 读成"有意识"。** 论文指的是功能性响应性，不涉及主观体验。混淆会同时夸大成果与伦理争议。
>
> **易错点 2：以为这是"训练好的神经网络模型"。** 这是**活体**神经元的实时自组织，没有反向传播、没有显式损失；学习来自可塑性 + 闭环反馈。
>
> **易错点 3：把"5 分钟学会"当成超越 RL。** 它展示的是体外网络可被闭环塑形，而非性能超过算法；与人工 RL 的对比要非常谨慎。
>
> **易错点 4：忽视对照与统计。** "看起来在学"必须对照无反馈/随机反馈组，并做严格统计，否则易被自发活动漂移误导。

---

## 8. Related Concepts

- **前置**：[体外神经元网络](../01_Neuron_Networks_InVitro/Overview.md) — DishBrain 的"硬件"就是 MEA 上的培养网络。
- **后续**：[生物-电子闭环架构](../04_Biohybrid_Systems/Overview.md) — DishBrain 是生物混合闭环的典型实例。
- **跨板块（神经科学）**：[突触可塑性 LTP 与 LTD](/notes/neuroscience/02_Cellular_Molecular/LTP_LTD) — 学习的细胞基础。
- **跨板块（认知科学）**：[贝叶斯认知 / 预测处理](/notes/cognitive-science/04_Reasoning_Decision/index) — 自由能/主动推断是同一套预测处理框架在活体上的体现。
- **跨板块（干细胞）**：[诱导多能干细胞 (iPSC)](/notes/biosubstrate/01_Stem_Cell_Biology/iPSC) — 人源 DishBrain 神经元正来自 iPSC 分化。

---

## References

1. Kagan, B. J., et al. (2022). *In vitro neurons learn and exhibit sentience when embodied in a simulated game-world*. Neuron, 110(23), 3952–3969.e8.
2. Friston, K. (2010). *The free-energy principle: a unified brain theory?*. Nature Reviews Neuroscience, 11(2), 127–138.
3. Cortical Labs (2022). *Synthetic Biological Intelligence (SBI) — DishBrain platform*. （公司技术说明）
4. Smirnova, L., et al. (2023). *Organoid intelligence (OI): the new frontier in biocomputing and intelligence-in-a-dish*. Frontiers in Science, 1, 1017235. （相邻方向：类器官智能）
