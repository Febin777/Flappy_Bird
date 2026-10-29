# CyberFlap RL (v2.4)
> Autonomous Reinforcement Learning Flappy Bird agent powered by Tabular Q-Learning over a Discretized Markov Decision Process (MDP).

CyberFlap RL is an interactive, browser-based reinforcement learning simulator designed to demonstrate fundamental Reinforcement Learning (RL) concepts in real time. Rather than relying on hardcoded heuristics or pixel-based deep neural networks, the agent discretizes continuous game physics into structured state vectors and converges on an optimal policy via the Bellman Equation.

---

## Key Features

- **Real-Time Tabular Q-Learning:** Visualizes state-action lookups, dynamic policy shifts, and updates to $Q(s, a)$ on the fly.
- **Discretized State Space:** Projects continuous pixel positions into finite, bucketed state representations.
- **Interactive Telemetry Dashboard:**
  - **Live Q-Table:** Real-time inspectability of the most recently visited state buckets.
  - **Sensors & Decision Feed:** Direct readout of relative distances, velocity, and state vector hashes.
  - **Reward Analytics:** Donut chart breakdown tracking survival ticks, pipe clearances, and terminal collisions.
  - **Score Progression:** Episode-by-episode performance tracking with rolling moving averages.
- **Dual Pilot Modes:** Toggle seamlessly between Human Pilot mode and AI Agent autonomous control.
- **Training Acceleration:** Multi-speed execution controls up to `TURBO` mode for rapid policy training.
- **Model Persistence:** Import and export trained Q-tables via JSON, or load pre-trained weights instantly.

---

## Reinforcement Learning Architecture

### 1. Markov Decision Process (MDP)

The game environment is modeled as an MDP $(S, A, P, R, \gamma)$:

- **State Space ($S$):** Continuous game physics are discretized into a 3-element state bucket tuple:
  $$\text{State Vector} = \left[ \Delta X_{\text{pipe}},\, \Delta Y_{\text{gap}},\, V \right]$$
  - $\Delta X_{\text{pipe}}$: Bucketed horizontal distance to the upcoming obstacle.
  - $\Delta Y_{\text{gap}}$: Bucketed vertical offset from the bird to the center of the gap.
  - $V$: Vertical velocity of the bird.
  - State representations are stored as hashed strings (e.g., `24_-25_7`) for $O(1)$ table lookups.

- **Action Space ($A$):** Binary discrete action selection:
  - $a_0 = \text{WAIT}$ (allow gravity to pull the agent downward)
  - $a_1 = \text{FLAP / JUMP}$ (apply upward impulse)

- **Reward Function ($R$):**
  - **$+1$ per tick:** Survival incentive for staying airborne.
  - **$+15$ per pipe:** Bonus for clearing a pipe gate.
  - **$-1000$ on collision:** Severe terminal penalty for pipe, ground, or ceiling impact.

---

### 2. Tabular Q-Learning & Bellman Update

The agent optimizes its action-value function $Q(s, a)$ using temporal difference learning:

$$Q(s, a) \leftarrow Q(s, a) + \alpha \left[ r + \gamma \max_{a'} Q(s', a') - Q(s, a) \right]$$

#### Default Hyperparameters:
| Parameter | Symbol | Value | Role |
| :--- | :---: | :---: | :--- |
| **Learning Rate** | $\alpha$ | `0.70` | Governs the step size of value updates. |
| **Discount Factor** | $\gamma$ | `0.95` | Weights future prospective returns over immediate rewards. |
| **Exploration Rate** | $\epsilon$ | `0.05` | Balances exploration (random jumps) vs. exploitation (optimal policy). |

---
