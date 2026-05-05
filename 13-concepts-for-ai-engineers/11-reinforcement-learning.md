# 11. What is Reinforcement Learning?

**Reinforcement Learning (RL)** is a machine learning paradigm where an **agent** learns to behave in an **environment** by taking **actions** and receiving **rewards**, with the goal of maximizing total reward over time. Unlike supervised learning, there are no labeled examples — only feedback from interaction.

> Supervised learning learns from a teacher. RL learns from consequences.

---

## The Core Loop

```
       ┌────────────────────────┐
       │       Environment      │
       └────────────┬───────────┘
                    │ state, reward
                    ▼
              ┌───────────┐
              │   Agent   │
              └─────┬─────┘
                    │ action
                    ▼
       ┌────────────────────────┐
       │       Environment      │
       └────────────────────────┘
```

At each step `t`:
1. Agent observes **state** `s_t`.
2. Agent picks an **action** `a_t` according to its **policy** π.
3. Environment returns **reward** `r_t` and the new state `s_{t+1}`.
4. Agent updates π to make high-reward trajectories more likely.

---

## Key Vocabulary

| Term | Meaning |
|------|---------|
| **Agent** | The learner / decision maker |
| **Environment** | What the agent acts on |
| **State (s)** | Current situation |
| **Action (a)** | What the agent can do |
| **Reward (r)** | Scalar feedback signal |
| **Policy (π)** | Strategy: state → action |
| **Value (V)** | Expected total future reward from a state |
| **Q-value (Q)** | Expected reward of (state, action) pair |
| **Episode** | One run from start to terminal state |
| **Return** | Discounted sum of future rewards |
| **Discount (γ)** | How much we value future vs immediate reward |

---

## Exploration vs Exploitation

The fundamental tradeoff:
- **Exploit** — choose the action you currently believe is best.
- **Explore** — try something new to discover better actions.

Strategies: ε-greedy, softmax, Upper Confidence Bound (UCB), entropy bonuses.

---

## Algorithm Families

### Value-based
Learn Q-values; pick the action with the highest Q.
- **Q-learning** — classic table-based.
- **DQN (Deep Q-Network)** — neural net approximator. Atari-level breakthroughs.

### Policy-based
Directly learn the policy π that maps states to actions.
- **REINFORCE** — vanilla policy gradient.
- **PPO (Proximal Policy Optimization)** — workhorse of modern RL; stable, scalable.
- **TRPO** — earlier, more conservative cousin of PPO.

### Actor-Critic
Combine both: an **actor** chooses actions, a **critic** estimates value.
- **A2C / A3C, SAC, DDPG**.

### Model-based
Learn a model of the environment, plan inside it.
- **AlphaZero, MuZero** — search + learned model.
- More sample-efficient; harder to scale.

---

## Where RL Shines

- **Games** — AlphaGo, AlphaZero, OpenAI Five (Dota), StarCraft.
- **Robotics** — locomotion, manipulation.
- **Control** — data-center cooling, chip placement.
- **Recommendation** — long-term engagement optimization.
- **LLM alignment** — RLHF.

---

## RL in LLMs (the part most engineers care about)

### RLHF — Reinforcement Learning from Human Feedback

The technique that turned raw next-token predictors into helpful assistants.

```
1. Pretrained LLM
2. Collect human preference pairs: response A vs B
3. Train a reward model on those preferences
4. Use PPO (or DPO) to fine-tune the LLM to maximize the reward model's score
```

### DPO — Direct Preference Optimization

A simpler, more stable alternative to RLHF that skips the explicit reward model.

### RLAIF — Reinforcement Learning from AI Feedback

Use a stronger AI judge instead of (or alongside) humans for preference labels. Cheaper at scale; foundation of Constitutional AI.

### RL with Verifiable Rewards

The 2024–25 breakthrough behind reasoning models (o1, o3, R1, Claude reasoning).

- Tasks where correctness is **verifiable**: math, code, formal proofs.
- Reward is automatic (does the test pass? is the answer correct?).
- Lets RL scale beyond expensive human labels.
- Produces models that *think longer* before answering.

---

## A Simple Mental Picture

A toddler learns to walk: tries something, falls, gets up, tries differently. No teacher hands them the right motor sequence — they bootstrap from reward (staying upright) and consequence (falling). RL formalizes this.

---

## Caveats and Hard Parts

- **Reward hacking** — agents satisfy the literal metric in unintended ways.
- **Sparse rewards** — feedback only at the end of long episodes.
- **Credit assignment** — which action was actually responsible for the reward?
- **Sample inefficiency** — millions of trials for non-trivial tasks.
- **Distribution shift** — policy trained in sim falls apart in reality.
- **Stability** — RL is famously fiddly to tune.

---

## Further Reading

- Sutton & Barto — *Reinforcement Learning: An Introduction* (the bible)
- OpenAI Spinning Up (free, hands-on)
- Anthropic / OpenAI / DeepMind blogs on RLHF and reasoning
- DeepSeek-R1 paper for RL on verifiable rewards in LLMs
