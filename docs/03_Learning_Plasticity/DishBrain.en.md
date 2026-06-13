# DishBrain — Neurons in a Dish Learn to Play Pong

> *In 2022 Kagan et al. grew ~800,000 living neurons on a high-density multi-electrode array and embedded them in a simulated game of Pong; they showed directed learning **within 5 minutes**. This is the landmark experiment that took "wetware computing" from concept to evidence — and the flagship case of this pillar.*
>
> **Difficulty**: Advanced　·　**Reading time**: ~13 min
> **Prerequisites**: [In-Vitro Learning & Plasticity (index)](index.md), [In-Vitro Neuronal Networks](../01_Neuron_Networks_InVitro/Overview.md)
> **Further reading**: [Bio-Electronic Closed-Loop Architectures](../04_Biohybrid_Systems/Overview.md)

---

The two previous articles showed that the mind can be understood in layers ([Marr's three levels](/notes/cognitive-science/00_Foundations/Marr_Levels)), and that neurons can be grown from stem cells ([iPSC](/notes/biosubstrate/01_Stem_Cell_Biology/iPSC)). Now comes the boldest step — **can those living neurons actually *compute* something?** The 2022 DishBrain experiment gave an answer that turned heads worldwide: yes, and it learned to play Pong in five minutes. This article dissects what it really did, why it worked, and how to interpret it soberly.

## 1. Intuition: Give the Neurons a "Body" and a "World"

First correct a common impression. Neurons cultured in isolation are not quiet — they fire spontaneously — but that is more like meaningless background noise. Watching that activity alone, you cannot tell whether the cells "can" do anything.

**DishBrain's core idea is to embed the network in a perception–action loop:** let it "see" where the ball is through electrical stimulation, "swing" the paddle through its own firing, and receive feedback depending on whether it hits the ball. Once it has a "body" (the paddle) and a "world" (the game of Pong), every firing event has **consequences** — and the neurons begin to self-organize to improve those consequences.

An analogy: you would not judge whether someone can play a ball game from their resting EEG; you have to put the racket in their hand and send them onto the court. That is exactly what DishBrain does — it hands the racket to neurons in a dish.

## 2. Formal Definition & Theoretical Framework

Tighten the intuition. **DishBrain** is a platform that connects an **in-vitro neuronal network** to a simulated environment through a **High-Density Multi-Electrode Array (HD-MEA)**, forming a **closed-loop perception–action circuit**. Cortical Labs calls such "living neurons + silicon interface" hybrids **Synthetic Biological Intelligence (SBI)**.

For "why it learns," the authors borrow the **Free Energy Principle (FEP)** and **Active Inference**. In one line, the theory says: **an agent tends to minimize the "unpredictability" of its own sensory input.** Formally, variational free energy $F$ is an upper bound on "surprise" (the negative log-evidence $-\ln p(o)$):

$$
F = \mathbb{E}_{q(s)}\big[\ln q(s) - \ln p(o, s)\big] \;\ge\; -\ln p(o)
$$

Here $o$ is the observation (sensory stimulation), $s$ the hidden environmental state, and $q(s)$ the network's internal "belief" about the world. There are two ways to lower $F$: revise internal beliefs to better predict the world (**perception**), or **act to change the observation itself** (**action**). DishBrain's entire design is built around "let the network lower its surprise by acting" — which leads to its cleverest move.

## 3. History & Motivation

Whether in-vitro neurons can "learn" had long been confined to stimulus–response plasticity (e.g., [LTP/LTD](/notes/neuroscience/02_Cellular_Molecular/LTP_LTD): repeated stimulation strengthens or weakens synapses). But that is not yet "learning to do a task" in the intuitive sense. The real turning point was giving the network an **embodied, goal-directed task** and seeing whether it self-organizes toward the goal.

- **Platform & team**: Cortical Labs (Melbourne) with Monash and others.
- **Landmark paper**: **Kagan et al. (2022), in *Neuron***, with a strikingly direct title — "in vitro neurons learn and exhibit sentience when embodied in a simulated game-world."
- **Scale**: ~**800,000** neurons (mouse cortical + human iPSC-derived), growing on an HD-MEA with thousands to tens of thousands of electrodes.

Its significance runs both ways: for **neuroscience**, it provides a precisely controllable closed-loop platform to study learning and plasticity; for **computing**, it concretely explores using living neural tissue as an **adaptive computing substrate** — the core thesis of wetware computing.

## 4. Experimental Mechanism

With theory and background in place, let us see how the system actually runs.

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

The loop itself is just two wires: one "feeds" the world into the neurons (sense), one "reads" the neurons' intent out into an action (act). The hard part is not the wiring but **how feedback is delivered** — and this is where FEP does the heavy lifting.

### 4.2 Key Design: Reward via "Predictability"

Conventional reinforcement learning has to design a reward signal. DishBrain has none; instead it uses the FEP insight above to shape feedback like this:

- **Hitting the ball (good outcome)** → deliver a burst of **structured, predictable** stimulation (low surprise).
- **Missing the ball (bad outcome)** → deliver **completely random, unpredictable** stimulation (high surprise).

Because the network "by nature" wants to minimize the unpredictability of its sensory input, it spontaneously adjusts toward "hit more, get less random stimulation" — and this **looks like learning**. The authors report that, compared to controls with no feedback, the cultured network showed a measurable improvement within about **5 minutes** of real-time play. Note the elegance: the reward signal is not an external score but is written directly into "how predictable the sensory input is."

### 4.3 The "Sentience" Wording Controversy

The paper's title uses the word "sentience," which sparked wide debate. To be clear: the authors explicitly adopt the narrow **"responsiveness/sensitivity"** sense under Seth and colleagues' definition — functional sensitivity and adaptation to environmental information — **not** subjective consciousness, and not the capacity to feel pain. Reading the word as "conscious" both **overstates the result** and **misguides ethical judgment**. It is also precisely why the experiment pushed the question of [the sentience and ethics of in-vitro systems](../06_Ethics_Frontiers/Overview.md) to the foreground for the first time in a concrete way.

## 5. Worked Example: What Actually Happens in One Game of Pong

Bring the mechanism down to frame-by-frame and it becomes concrete:

1. **Sensory encoding**: the ball's horizontal position $x$ maps to a stimulation site on a region of the MEA; the ball's distance / vertical information is encoded by stimulation frequency. The neurons thus "feel" where the ball is.
2. **Action readout**: the array is split into two "motor regions"; the difference in their firing activity sets whether the paddle moves up or down, and how fast.
3. **Closed-loop update**: the paddle moves → the ball is caught or missed → the system sends back "predictable/random" stimulation accordingly → that stimulation in turn shapes the network's next moment of activity. Repeat.
4. **How "learning" is measured**: track average rally length and hit rate over time, then compare against "no-feedback / random-feedback" controls. Only when the experimental group significantly beats the controls do you have evidence of learning.

Step 4 matters most — it separates "appears to learn" from "actually learns," which is also the pitfall to watch below.

## 6. Common Pitfalls

This topic is easily distorted by headlines; several common misreadings must be named:

> **Pitfall 1: reading "sentience" as "consciousness."** The paper means functional responsiveness, unrelated to subjective experience. Conflating them leads people to both overrate the result and misjudge the ethics.
>
> **Pitfall 2: thinking this is a "trained neural-network model."** It is not. This is **living** neurons self-organizing in real time: no backpropagation, no explicit loss function; learning comes from synaptic plasticity + closed-loop feedback.
>
> **Pitfall 3: taking "learns in 5 minutes" as crushing reinforcement learning.** It demonstrates "an in-vitro network can be shaped by a closed loop," not "performance beyond algorithms." Comparisons of performance with artificial RL must be very cautious.
>
> **Pitfall 4: neglecting controls and statistics.** Spontaneous activity drifts on its own; "appears to be improving" must be checked against no-feedback / random-feedback controls with rigorous statistics, or self-deception is easy.

## 8. Related Concepts

- **Prerequisite**: [In-Vitro Neuronal Networks](../01_Neuron_Networks_InVitro/Overview.md) — DishBrain's "hardware" is this cultured network on the MEA.
- **Next**: [Bio-Electronic Closed-Loop Architectures](../04_Biohybrid_Systems/Overview.md) — DishBrain is a canonical example of a biohybrid closed loop.
- **Cross-pillar (neuroscience)**: [Synaptic Plasticity: LTP and LTD](/notes/neuroscience/02_Cellular_Molecular/LTP_LTD) — the cellular basis of its learning.
- **Cross-pillar (cognitive science)**: [Reasoning & Decision / Predictive Processing](/notes/cognitive-science/04_Reasoning_Decision/index) — free energy / active inference is the same predictive-processing framework instantiated in living tissue.
- **Cross-pillar (stem cells)**: [Induced Pluripotent Stem Cells (iPSC)](/notes/biosubstrate/01_Stem_Cell_Biology/iPSC) — human DishBrain neurons come from iPSC differentiation.

## References

1. Kagan, B. J., et al. (2022). *In vitro neurons learn and exhibit sentience when embodied in a simulated game-world*. Neuron, 110(23), 3952–3969.e8.
2. Friston, K. (2010). *The free-energy principle: a unified brain theory?*. Nature Reviews Neuroscience, 11(2), 127–138.
3. Cortical Labs (2022). *Synthetic Biological Intelligence (SBI) — the DishBrain platform*. (Company technical overview.)
4. Smirnova, L., et al. (2023). *Organoid intelligence (OI): the new frontier in biocomputing and intelligence-in-a-dish*. Frontiers in Science, 1, 1017235. (Adjacent direction: organoid intelligence.)
