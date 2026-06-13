# DishBrain — Neurons in a Dish Learn to Play Pong

> *In 2022 Kagan et al. grew ~800,000 living neurons on a high-density multi-electrode array and embedded them in a simulated game of Pong; they showed directed learning **within 5 minutes**. This is the landmark experiment that took "wetware computing" from concept to evidence — and the flagship case of this pillar.*
>
> **Difficulty**: Advanced
> **Prerequisites**: [In-Vitro Learning & Plasticity (index)](index.md), [In-Vitro Neuronal Networks](../01_Neuron_Networks_InVitro/Overview.md)
> **Further reading**: [Bio-Electronic Closed-Loop Architectures](../04_Biohybrid_Systems/Overview.md)

---

## 1. Intuition: Give the Neurons a "Body" and a "World"

Neurons cultured in isolation fire spontaneously, but that is just noise-like activity. **DishBrain's core idea is to embed the network in a perception–action loop** — let it "see" the ball's position through electrical stimulation, "move" the paddle through its own firing, and receive feedback depending on whether it hits the ball. Once it has a "body" (the paddle) and a "world" (Pong), the neurons' activity has **consequences**, so they begin to organize to improve those consequences.

Analogy: you cannot judge whether someone can play a ball game by watching their resting EEG; you have to hand them the racket and put them on the court. DishBrain hands the racket to neurons in a dish.

---

## 2. Formal Definition & Theoretical Framework

**DishBrain** is a platform that connects an **in-vitro neuronal network** to a simulated environment through a **High-Density Multi-Electrode Array (HD-MEA)**, forming a **closed-loop perception–action circuit**. Cortical Labs calls such "living neurons + silicon interface" hybrids **Synthetic Biological Intelligence (SBI)**.

Its learning is interpreted via the **Free Energy Principle (FEP)** and **Active Inference**: an agent tends to **minimize the unpredictability of its sensory input**. Formally, variational free energy $F$ upper-bounds "surprise" (negative log-evidence):

$$
F = \mathbb{E}_{q(s)}\big[\ln q(s) - \ln p(o, s)\big] \;\ge\; -\ln p(o)
$$

where $o$ is the observation (sensory stimulation), $s$ the hidden environmental state, and $q(s)$ the network's internal "belief." An agent can lower $F$ in two ways: revise internal beliefs (perception), or **act to change observations** (action). DishBrain's design constructs feedback exactly along these lines — see §4.2.

---

## 3. History & Motivation

Whether in-vitro neurons can "learn" had long been confined to stimulus–response plasticity (e.g., LTP/LTD). The real turning point was **giving the network an embodied, goal-directed task**:

- Platform & team: Cortical Labs (Melbourne) with Monash and others.
- Landmark paper: **Kagan et al. (2022), *Neuron***, whose title states "in vitro neurons learn and exhibit sentience when embodied in a simulated game-world."
- Scale: ~**800,000** neurons (mouse cortical + human iPSC-derived) growing on an HD-MEA with thousands–tens of thousands of electrodes.

The motivation is twofold: for neuroscience, it provides a **controllable closed-loop platform** to study learning/plasticity; for computing, it explores using living neural tissue as an **adaptive computing substrate** — the core thesis of wetware computing.

---

## 4. Experimental Mechanism

### 4.1 The Closed Loop

<div class="diagram">
<svg viewBox="0 0 720 260" xmlns="http://www.w3.org/2000/svg" font-family="Georgia, serif">
  <rect x="60" y="95" width="170" height="70" rx="8" fill="var(--dia-accent-soft, rgba(196,103,58,.10))" stroke="var(--dia-accent, #c4673a)" stroke-width="1.5"/>
  <text x="145" y="124" text-anchor="middle" font-size="14" fill="var(--dia-accent, #c4673a)" font-weight="bold">neurons @ HD-MEA</text>
  <text x="145" y="144" text-anchor="middle" font-size="11" fill="var(--dia-stroke-soft, #6b5f4e)">~800k cells</text>
  <rect x="490" y="95" width="170" height="70" rx="8" fill="none" stroke="var(--dia-blue, #2c4d6e)" stroke-width="1.5"/>
  <text x="575" y="124" text-anchor="middle" font-size="14" fill="var(--dia-blue, #2c4d6e)" font-weight="bold">simulated Pong</text>
  <text x="575" y="144" text-anchor="middle" font-size="11" fill="var(--dia-stroke-soft, #6b5f4e)">ball + paddle</text>
  <path d="M490,110 C380,40 250,40 158,92" fill="none" stroke="var(--dia-green, #4a6741)" stroke-width="1.5" marker-end="url(#d)"/>
  <text x="324" y="40" text-anchor="middle" font-size="12" fill="var(--dia-green, #4a6741)">sense: stimulation encodes ball position</text>
  <path d="M230,150 C340,220 460,220 560,166" fill="none" stroke="var(--dia-accent, #c4673a)" stroke-width="1.5" marker-end="url(#e)"/>
  <text x="396" y="232" text-anchor="middle" font-size="12" fill="var(--dia-accent, #c4673a)">act: read firing → move paddle</text>
  <text x="360" y="190" text-anchor="middle" font-size="11" fill="var(--dia-stroke-soft, #6b5f4e)">hit → predictable feedback; miss → random stim (high "surprise")</text>
  <defs>
    <marker id="d" markerWidth="9" markerHeight="9" refX="5" refY="4.5" orient="auto"><path d="M0,0 L9,4.5 L0,9 z" fill="var(--dia-green, #4a6741)"/></marker>
    <marker id="e" markerWidth="9" markerHeight="9" refX="5" refY="4.5" orient="auto"><path d="M0,0 L9,4.5 L0,9 z" fill="var(--dia-accent, #c4673a)"/></marker>
  </defs>
</svg>
</div>
<p class="figure-caption">Figure 1 — The DishBrain loop: stimulation "feeds" the ball position to the neurons (sense), motor-region firing moves the paddle (act), and feedback distinguishes win/lose via predictable vs unpredictable stimulation.</p>

### 4.2 Key Design: Reward via "Predictability"

DishBrain has no conventional reward signal; instead it uses the FEP insight to construct feedback:

- **Hitting the ball (good outcome)** → deliver **structured, predictable** stimulation (low surprise).
- **Missing the ball (bad outcome)** → deliver **unpredictable random** stimulation (high surprise).

Because the network "wants" to minimize the unpredictability of its sensory input, it self-organizes toward "hitting more" — and thus appears to **learn**. The authors report performance gains relative to controls within about **5 minutes** of real-time gameplay.

### 4.3 The "Sentience" Wording Controversy

The paper's title uses "sentience." The authors are explicit that this is the narrow **responsiveness/sensitivity** sense (functional sensitivity and adaptation to environmental information) under Seth and colleagues' definition — **not** subjective consciousness or the capacity for suffering. The wording sparked wide debate and pushed [sentience and ethics of in-vitro systems](../06_Ethics_Frontiers/Overview.md) to the foreground.

---

## 5. Worked Example: What Happens in One Game of Pong

1. **Sensory encoding**: the ball's $x$ position maps to a stimulation site on a region of the MEA, with $y$/distance encoded by stimulation frequency → the neurons "feel" where the ball is.
2. **Action readout**: the array is split into two "motor regions"; the difference in their firing activity sets the paddle's up/down velocity.
3. **Closed-loop update**: the paddle moves → the ball bounces or is missed → predictable/random stimulation is sent back accordingly → influencing the network's next activity.
4. **Measuring learning**: track average rally length and hit rate over time per "session," compared to controls (no feedback); improvement is evidence of learning.

---

## 6. Common Pitfalls

> **Pitfall 1: reading "sentience" as "consciousness."** The paper means functional responsiveness, not subjective experience. Conflating them both overstates the result and inflames the ethics debate.
>
> **Pitfall 2: thinking this is a "trained neural-network model."** This is **living** neurons self-organizing in real time — no backpropagation, no explicit loss; learning comes from plasticity + closed-loop feedback.
>
> **Pitfall 3: taking "learns in 5 minutes" as beating RL.** It shows in-vitro networks can be shaped by closed-loop feedback, not that they outperform algorithms; comparisons with artificial RL must be very cautious.
>
> **Pitfall 4: neglecting controls and statistics.** "Appears to learn" must be checked against no-feedback/random-feedback controls with rigorous statistics, or spontaneous activity drift can mislead.

---

## 8. Related Concepts

- **Prerequisite**: [In-Vitro Neuronal Networks](../01_Neuron_Networks_InVitro/Overview.md) — DishBrain's "hardware" is the cultured network on the MEA.
- **Next**: [Bio-Electronic Closed-Loop Architectures](../04_Biohybrid_Systems/Overview.md) — DishBrain is a canonical biohybrid closed loop.
- **Cross-pillar (neuroscience)**: [Synaptic Plasticity: LTP and LTD](/notes/neuroscience/02_Cellular_Molecular/LTP_LTD) — the cellular basis of learning.
- **Cross-pillar (cognitive science)**: [Bayesian Cognition / Predictive Processing](/notes/cognitive-science/04_Reasoning_Decision/index) — free energy / active inference is the same predictive-processing framework instantiated in living tissue.
- **Cross-pillar (stem cells)**: [Induced Pluripotent Stem Cells (iPSC)](/notes/biosubstrate/01_Stem_Cell_Biology/iPSC) — human DishBrain neurons come from iPSC differentiation.

---

## References

1. Kagan, B. J., et al. (2022). *In vitro neurons learn and exhibit sentience when embodied in a simulated game-world*. Neuron, 110(23), 3952–3969.e8.
2. Friston, K. (2010). *The free-energy principle: a unified brain theory?*. Nature Reviews Neuroscience, 11(2), 127–138.
3. Cortical Labs (2022). *Synthetic Biological Intelligence (SBI) — the DishBrain platform*. (Company technical overview.)
4. Smirnova, L., et al. (2023). *Organoid intelligence (OI): the new frontier in biocomputing and intelligence-in-a-dish*. Frontiers in Science, 1, 1017235. (Adjacent direction: organoid intelligence.)
