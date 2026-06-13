# DishBrain — 培养皿里的神经元学会打 Pong

> *2022 年 Kagan 等人把约 80 万个活体神经元养在高密度多电极阵列上，接入仿真版乒乓球游戏 Pong；它们在**5 分钟内**就表现出有方向的学习。这是"湿件计算"从概念走向实证的标志性实验，也是本板块的旗舰案例。*
>
> **难度**：Advanced　·　**阅读时间**：约 13 分钟
> **前置知识**：[体外学习与可塑性导论](index.md)、[体外神经元网络](../01_Neuron_Networks_InVitro/Overview.md)
> **后续阅读**：[生物-电子闭环架构](../04_Biohybrid_Systems/Overview.md)

---

前面两篇我们看到：心智可以分层理解（[Marr 三层次](/notes/cognitive-science/00_Foundations/Marr_Levels)），神经元可以从干细胞造出来（[iPSC](/notes/biosubstrate/01_Stem_Cell_Biology/iPSC)）。现在到了最大胆的一步——**能不能让这些活体神经元，真的"算"点什么？** 2022 年的 DishBrain 实验给了一个让全世界侧目的回答：能，而且它在 5 分钟里就学会了打 Pong。这篇就拆解它到底做了什么、为什么能成、以及该怎么冷静地理解它。

## 1. 直觉：给神经元一个"身体"和一个"世界"

先纠正一个常见印象。孤立培养的神经元并不安静，它们会自发放电——但那更像没有意义的噪声背景音。光看这种活动，你无从判断这团细胞"会不会"做事。

**DishBrain 的核心想法，是把神经网络塞进一个感知–行动闭环：** 让它通过电刺激"看到"球在哪，通过自己的放电"挥动"球拍，再根据有没有接到球得到反馈。一旦它有了"身体"（球拍）和"世界"（Pong 游戏），它的每一次放电就有了**后果**——于是神经元开始自组织地去改善这个后果。

打个比方：你不会靠看一个人静坐时的脑电图，来判断他会不会打球；你得把球拍递到他手里、让他真的下场。DishBrain 做的，就是把球拍递给了培养皿里的神经元。

## 2. 形式化定义与理论框架

把直觉收紧。**DishBrain** 是一个平台：它把**体外神经元网络 (in-vitro neuronal network)**，通过**高密度多电极阵列 (High-Density Multi-Electrode Array, HD-MEA)** 接入一个仿真环境，构成**闭环的感知–行动回路**。Cortical Labs 把这类"活体神经元 + 硅基接口"的混合系统称为**合成生物智能 (Synthetic Biological Intelligence, SBI)**。

它"为什么会学"，作者借用了**自由能原理 (Free Energy Principle, FEP)** 与**主动推断 (Active Inference)** 来解释。一句话概括这个理论：**智能体倾向于最小化自己感官输入的"不可预测性"。** 形式上，变分自由能 $F$ 是"意外"（即负对数证据 $-\ln p(o)$）的一个上界：

$$
F = \mathbb{E}_{q(s)}\big[\ln q(s) - \ln p(o, s)\big] \;\ge\; -\ln p(o)
$$

这里 $o$ 是观测（感官刺激），$s$ 是环境的隐状态，$q(s)$ 是网络内部对世界的"信念"。降低 $F$ 有两条路：要么调整内部信念去更好地预测世界（**感知**），要么**采取行动去改变观测本身**（**行动**）。DishBrain 的整个实验设计，正是围绕"让网络通过行动来降低意外"来构造的——这就引出了它最巧妙的一招。

## 3. 历史与动机

"体外神经元能不能学习"，长期停留在"刺激–反应"式的可塑性层面（比如 [LTP/LTD](/notes/neuroscience/02_Cellular_Molecular/LTP_LTD)：反复刺激让突触变强或变弱）。但那还不是我们直觉里的"学会做一件事"。真正的转折，是给网络一个**具身的、有目标的任务**，看它会不会朝目标自我组织。

- **平台与团队**：Cortical Labs（墨尔本）联合 Monash 等机构。
- **标志性论文**：**Kagan et al. (2022), 发表于 *Neuron***，标题直白得惊人——"in vitro neurons learn and exhibit sentience when embodied in a simulated game-world"。
- **规模**：约 **80 万** 个神经元（小鼠皮层来源 + 人 iPSC 衍生），生长在含上千到上万电极的 HD-MEA 上。

它的意义是双向的：对**神经科学**，它提供了一个研究学习与可塑性的、可精确控制的闭环平台；对**计算**，它真刀真枪地探索"把活体神经组织当作自适应计算基质"的可能——而这，正是湿件计算的核心命题。

## 4. 实验机制

理论和背景都铺好了，我们来看这套系统具体是怎么转起来的。

### 4.1 闭环回路

<div class="diagram">
<svg viewBox="0 0 720 260" xmlns="http://www.w3.org/2000/svg" font-family="Georgia, serif">
  <rect x="60" y="95" width="170" height="70" rx="8" fill="var(--dia-accent-soft, rgba(196,103,58,.10))" stroke="var(--dia-accent, #c4673a)" stroke-width="1.5"/>
  <text x="145" y="124" text-anchor="middle" font-size="14" fill="var(--dia-accent, #c4673a)" font-weight="bold">神经元 @ HD-MEA</text>
  <text x="145" y="144" text-anchor="middle" font-size="11" fill="var(--dia-stroke-soft, #6b5f4e)">~800k 细胞</text>
  <rect x="490" y="95" width="170" height="70" rx="8" fill="none" stroke="var(--dia-blue, #2c4d6e)" stroke-width="1.5"/>
  <text x="575" y="124" text-anchor="middle" font-size="14" fill="var(--dia-blue, #2c4d6e)" font-weight="bold">仿真 Pong</text>
  <text x="575" y="144" text-anchor="middle" font-size="11" fill="var(--dia-stroke-soft, #6b5f4e)">球 + 球拍</text>
  <path d="M490,110 C380,40 250,40 158,92" fill="none" stroke="var(--dia-green, #4a6741)" stroke-width="1.5" marker-end="url(#d)"/>
  <text x="324" y="40" text-anchor="middle" font-size="12" fill="var(--dia-green, #4a6741)">感知：电刺激编码球位置</text>
  <path d="M230,150 C340,220 460,220 560,166" fill="none" stroke="var(--dia-accent, #c4673a)" stroke-width="1.5" marker-end="url(#e)"/>
  <text x="396" y="232" text-anchor="middle" font-size="12" fill="var(--dia-accent, #c4673a)">行动：读放电 → 移动球拍</text>
  <text x="360" y="190" text-anchor="middle" font-size="11" fill="var(--dia-stroke-soft, #6b5f4e)">击中→可预测反馈；漏接→随机刺激（高"意外"）</text>
  <defs>
    <marker id="d" markerWidth="9" markerHeight="9" refX="5" refY="4.5" orient="auto"><path d="M0,0 L9,4.5 L0,9 z" fill="var(--dia-green, #4a6741)"/></marker>
    <marker id="e" markerWidth="9" markerHeight="9" refX="5" refY="4.5" orient="auto"><path d="M0,0 L9,4.5 L0,9 z" fill="var(--dia-accent, #c4673a)"/></marker>
  </defs>
</svg>
</div>
<p class="figure-caption">Figure 1 — DishBrain 闭环：电刺激把球位置"喂"给神经元（感知），读取运动区放电来移动球拍（行动），反馈以可预测/不可预测刺激区分输赢。</p>

回路本身只有两根线：一根把世界"喂"进神经元（感知），一根把神经元的意图"读"出来变成动作（行动）。难点不在接线，而在**反馈怎么给**——这正是 FEP 大显身手的地方。

### 4.2 关键设计：用"可预测性"当奖惩

传统强化学习要设计一个奖励信号。DishBrain 没有，它转而利用前面那条 FEP 的洞见，把反馈做成这样：

- **击中球（好结果）** → 给一段**结构化、可预测**的刺激（低意外）。
- **漏接球（坏结果）** → 给一段**完全随机、不可预测**的刺激（高意外）。

因为网络"本性"就想最小化感官输入的不可预测性，它会自发地朝着"多击中、少挨随机刺激"的方向调整自己——这看上去，就**像在学习**。作者报告，相比不接反馈的对照组，培养网络在**约 5 分钟**的实时游戏内，表现就出现了可测量的提升。注意这里的精妙：奖惩信号不是外加的分数，而是直接写进了"感官输入可不可预测"这件事本身。

### 4.3 关于 "sentience" 的措辞争议

论文标题用了 "sentience"（常译"感知力/有感"）一词，引发了广泛讨论。必须讲清楚：作者明确采用的是 Seth 等人定义下的**狭义"响应性/可感性"**——即对环境信息的功能性敏感与适应，**而不是**主观意识，也不是能感到痛苦。把这个词读成"有意识"，会同时**夸大成果**和**误导伦理判断**。也正因如此，它把 [体外系统的感受性与伦理](../06_Ethics_Frontiers/Overview.md) 这个问题，第一次实打实地推到了台前。

## 5. Worked Example：一局 Pong 里到底发生了什么

把机制落到一帧一帧，你会看得更具体：

1. **感知编码**：球的水平位置 $x$，被映射到 MEA 上某个区域的刺激位点；球的距离 / 纵向信息，用刺激频率来编码。于是神经元"感觉到"了球在哪。
2. **行动读出**：把电极阵列划成两块"运动区"，两区放电活动的差，决定球拍向上还是向下、移动多快。
3. **闭环更新**：球拍移动 → 球被接住或漏掉 → 系统据结果回送"可预测/随机"刺激 → 这段刺激又影响网络下一刻的活动。如此循环。
4. **怎么算"学会了"**：统计每个回合的平均击球次数（rally length）、命中率随时间的变化，再和"不接反馈 / 随机反馈"的对照组比。只有当实验组显著优于对照，才算拿到学习的证据。

第 4 步尤其重要——它把"看起来在学"和"真的在学"区分开了，这也是下面要警惕的坑。

## 6. Common Pitfalls

这是个容易被媒体标题带偏的题目，几个常见误读必须点破：

> **易错点 1：把 "sentience" 读成"有意识"。** 论文指的是功能性响应性，与主观体验无关。混淆它，会让人既高估成果、又错判伦理。
>
> **易错点 2：以为这是一个"训练好的神经网络模型"。** 不是。这是**活体**神经元的实时自组织：没有反向传播、没有显式损失函数，学习来自突触可塑性 + 闭环反馈。
>
> **易错点 3：把"5 分钟学会"当成碾压强化学习。** 它证明的是"体外网络可以被闭环塑形"，而不是"性能超过算法"。与人工 RL 比性能，要非常谨慎。
>
> **易错点 4：忽视对照与统计。** 自发活动本身会漂移，"看起来在变好"必须对照无反馈 / 随机反馈组并做严格统计，否则极易自我欺骗。

## 8. Related Concepts

- **前置**：[体外神经元网络](../01_Neuron_Networks_InVitro/Overview.md) — DishBrain 的"硬件"，就是 MEA 上的这张培养网络。
- **后续**：[生物-电子闭环架构](../04_Biohybrid_Systems/Overview.md) — DishBrain 是生物混合闭环系统的一个典型实例。
- **跨板块（神经科学）**：[突触可塑性 LTP 与 LTD](/notes/neuroscience/02_Cellular_Molecular/LTP_LTD) — 它学习的细胞级基础。
- **跨板块（认知科学）**：[推理与决策 / 预测处理](/notes/cognitive-science/04_Reasoning_Decision/index) — 自由能 / 主动推断，正是同一套预测处理框架在活体组织上的体现。
- **跨板块（干细胞）**：[诱导多能干细胞 (iPSC)](/notes/biosubstrate/01_Stem_Cell_Biology/iPSC) — 人源 DishBrain 的神经元，正来自 iPSC 分化。

## References

1. Kagan, B. J., et al. (2022). *In vitro neurons learn and exhibit sentience when embodied in a simulated game-world*. Neuron, 110(23), 3952–3969.e8.
2. Friston, K. (2010). *The free-energy principle: a unified brain theory?*. Nature Reviews Neuroscience, 11(2), 127–138.
3. Cortical Labs (2022). *Synthetic Biological Intelligence (SBI) — the DishBrain platform*.（公司技术说明）
4. Smirnova, L., et al. (2023). *Organoid intelligence (OI): the new frontier in biocomputing and intelligence-in-a-dish*. Frontiers in Science, 1, 1017235.（相邻方向：类器官智能）
