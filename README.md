# CyberGuard AI: Autonomous Cyber Defense with Reinforcement Learning

A Reinforcement Learning project focused on autonomous cyber defense in simulated healthcare networks.

This project investigates the application of Reinforcement Learning to cybersecurity by training defensive agents to respond to simulated cyber attacks using the `gym-idsgame` environment.

Two approaches are implemented and compared:

- SARSA, using a tabular Q-learning approach.
- Double Deep Q-Network (DDQN), implemented with PyTorch.

The main objective is to investigate how effectively AI agents can learn defensive policies against different attacker strategies in a simulated network environment.

## Project Overview

Healthcare organizations manage highly sensitive information and critical digital infrastructure, making cybersecurity a fundamental requirement.

This project models cyber defense as a sequential decision-making problem. An attacker attempts to compromise the simulated network, while a defender learns which actions should be taken to maximize network survival.

Rather than defining a fixed defensive strategy manually, the defender learns through interaction with the environment and feedback from the reward signal.

The project is based on the `gym-idsgame` simulation environment:

https://github.com/Limmen/gym-idsgame

## Objectives

- Implement SARSA for defensive decision-making.
- Implement Double Deep Q-Network using PyTorch.
- Train the defender against simulated cyber attacks.
- Evaluate the defender against random and maximal attack strategies.
- Compare a tabular Reinforcement Learning approach with a neural approach.
- Analyze reward evolution and training stability.
- Investigate the potential of Reinforcement Learning for autonomous cyber defense.

## Environment

The project uses `gym-idsgame`, a simulation environment designed for studying cyber attack and defense interactions through Reinforcement Learning.

The environment represents a network in which an attacker and a defender interact through a sequence of actions.

The defender observes the current network state and selects a defensive action, while the attacker follows a predefined attack strategy.

Environment repository:

https://github.com/Limmen/gym-idsgame

## Methodology

The defender is the learning agent, while the attacker acts as an external adversarial policy.

The attacker is not trained as part of the main learning process. Instead, predefined attacker agents provided by `gym-idsgame` are used to create different experimental scenarios.

This makes it possible to evaluate the learned defensive policy under different attack behaviors.

## SARSA

SARSA is implemented as a tabular on-policy Reinforcement Learning algorithm.

The observation provided by the environment is flattened into a one-dimensional vector and converted into a tuple so that it can be used as a state representation in the Q-table.

An epsilon-greedy policy is used for action selection:

- With probability epsilon, a random action is selected to encourage exploration.
- Otherwise, the action with the highest Q-value is selected.

The Q-table is updated using the SARSA temporal-difference rule:

Q(s,a) = Q(s,a) + alpha [r + gamma Q(s',a') - Q(s,a)]

The next action is selected using the same epsilon-greedy policy, maintaining the on-policy nature of SARSA.

## Double Deep Q-Network

The second approach uses a Double Deep Q-Network implemented with PyTorch.

Instead of explicitly storing Q-values for every state-action pair, a neural network approximates the Q-function.

The network receives the flattened environment observation as input and produces one Q-value for each available defender action.

The architecture consists of:

- Input layer representing the environment state.
- Two fully connected hidden layers with 128 neurons.
- ReLU activation functions.
- Output layer producing the Q-value for each defender action.

The DDQN implementation uses two networks:

- Online network, responsible for selecting the next action.
- Target network, responsible for evaluating the selected action.

The online network selects the best action in the next state, while the target network evaluates that action. This follows the Double DQN principle and helps reduce Q-value overestimation.

## Experience Replay

The DDQN agent uses an Experience Replay buffer to store previous transitions.

Each transition contains:

- Current state
- Selected action
- Reward
- Next state
- Episode termination status

During training, random batches of previous experiences are sampled from the replay buffer.

This reduces the correlation between consecutive experiences and improves the stability of neural network training.

## Target Network

A separate target network is maintained to provide more stable Q-value targets during training.

The target network is updated using soft updates rather than being replaced abruptly.

This allows the target values to change gradually and helps reduce oscillations during training.

## Attack Strategies

Two attacker strategies available in `gym-idsgame` are considered.

### Random Attack

The `RandomAttackBotAgent` is used to simulate a stochastic attacker.

The attacker does not consistently select the strongest possible action, creating a variable and less predictable attack pattern.

This scenario evaluates the ability of the defender to react to stochastic attack behavior.

### Maximal Attack

The `AttackMaximalValueBotAgent` is used to simulate a stronger adversarial strategy.

In this scenario, the attacker selects actions according to a maximal-value strategy, creating a more challenging environment for the defensive agent.

The DDQN implementation allows the attack scenario to be changed through the `do_MaximalAttack` parameter.

Setting `do_MaximalAttack = False` uses the random attacker.

Setting `do_MaximalAttack = True` uses the maximal-value attacker.

## Reward Design

In addition to the baseline reward provided by `gym-idsgame`, a custom reward shaping strategy is used during training.

The custom reward introduces:

- A negative penalty when the defender is hacked.
- A positive reward when the episode terminates without the defender being compromised.
- A small survival bonus at each step.

The purpose is to encourage the agent to prioritize network survival while strongly penalizing successful attacks.

The same general reward-shaping strategy is used for SARSA and DDQN training.

## Evaluation

The trained agents are evaluated using a greedy policy.

During evaluation, epsilon-greedy exploration is disabled and the defender always selects the action with the highest estimated Q-value.

The main evaluation metrics are:

- Win Rate
- Average Reward
- Reward per Episode
- SARSA Temporal-Difference Error
- DDQN Training Loss
- Moving Average Reward

An episode is considered a defensive success when it terminates without the network being compromised.

## Results

The experiments produced the following representative results:

| Algorithm | Attack Strategy | Win Rate | Average Reward |
|---|---|---:|---:|
| SARSA | Random Attack | ~60% | Negative |
| DDQN | Maximal Attack | ~62% | ~-0.47 |
| DDQN | Random Attack | ~65% | ~-0.49 |

The results indicate that both approaches are capable of learning non-trivial defensive behavior.

In the performed experiments, DDQN achieved a slightly higher win rate than SARSA and produced a more favorable average reward.

The DDQN results suggest that the neural approach can learn a more effective defensive policy in this environment.

However, the environment is stochastic, meaning that results can vary between different executions depending on random seeds, network initialization, exploration and attacker behavior.

Therefore, the reported values should be considered representative experimental results rather than fixed benchmarks.

## Training Analysis

The notebook generates plots to analyze the behavior of the learning algorithms.

The analysis includes:

- SARSA reward per episode.
- SARSA Temporal-Difference error.
- DDQN reward per episode.
- DDQN training loss.
- SARSA vs DDQN reward comparison.
- Moving-average reward comparison.
- Moving-average training error comparison.

These visualizations are generated directly by the notebook using Matplotlib.

## Technologies

- Python
- PyTorch
- NumPy
- Matplotlib
- Gym
- gym-idsgame
- Google Colab
- Reinforcement Learning
- Deep Reinforcement Learning

## Installation

The project is designed to run primarily in Google Colab, but the required Python dependencies can also be installed in a local environment.

Clone the repository:

    git clone https://github.com/andrealuigipala-del/CyberGuard-AI-Autonomous-Cyber-Defense-with-Reinforcement-Learning.git

Move into the project directory:

    cd CyberGuard-AI-Autonomous-Cyber-Defense-with-Reinforcement-Learning

Install the dependencies:

    pip install -r requirements.txt

The `requirements.txt` file contains the external Python packages required by the project:

    gym==0.26.2
    numpy==1.23.5
    torch
    matplotlib

These dependencies correspond to the main external libraries imported by the notebook:

- `gym` provides the Reinforcement Learning environment interface.
- `numpy` is used for numerical computation and state manipulation.
- `torch` is used to implement and train the DDQN neural network.
- `matplotlib` is used for training and evaluation visualizations.

The `gym-idsgame` environment is cloned directly by the notebook because the project relies on its repository implementation and internal modules.

During initialization, the notebook clones:

https://github.com/Limmen/gym-idsgame

The notebook also applies a small compatibility fix to `random_attack_bot_agent.py` before importing the corresponding attacker agent.

## Running the Project

Open the notebook:

`Reinforcement_learning_final_project.ipynb`

and execute the cells sequentially.

The notebook is divided into two main sections.

### SARSA

The SARSA section includes:

- Environment initialization.
- Q-table creation.
- Epsilon-greedy action selection.
- SARSA training.
- Policy extraction.
- Greedy evaluation.
- Reward analysis.
- Temporal-Difference error analysis.

### DDQN

The DDQN section includes:

- Environment initialization.
- Neural network definition.
- Online and target networks.
- Replay Buffer.
- Epsilon-greedy action selection.
- Double DQN training.
- Random Attack evaluation.
- Maximal Attack evaluation.
- Reward analysis.
- Training loss analysis.

## Google Colab

The project was developed and tested primarily in Google Colab.

A reference notebook for configuring the `gym-idsgame` environment is available here:

https://colab.research.google.com/drive/15nTl0CSLuSaO_4rQg3eLQXrtfE6qvjYW?usp=sharing

The original project notebook is also available through Google Colab.

## Repository Structure

The repository is intentionally kept simple, with the notebook representing the main project deliverable.

    CyberGuard-AI-Autonomous-Cyber-Defense-with-Reinforcement-Learning/
    ├── Reinforcement_learning_final_project.ipynb
    ├── README.md
    └── requirements.txt

The notebook contains the complete implementation, including environment setup, SARSA, DDQN, training, evaluation and visualization.

## Future Improvements

Several improvements could make the system more robust and suitable for further research.

Potential future developments include:

- Hyperparameter optimization.
- Evaluation over multiple random seeds.
- Statistical comparison over multiple independent runs.
- Training against multiple attacker strategies.
- Adaptive adversarial training.
- Curriculum learning.
- Prioritized Experience Replay.
- Dueling DQN architectures.
- More complex network environments.
- Evaluation against previously unseen attack strategies.
- Self-play between attacker and defender.
- Multi-agent Reinforcement Learning.

A particularly interesting direction would be adversarial training in which the defender is exposed to a continuously changing set of attacker strategies during training.

This could improve the robustness and generalization capabilities of the learned defensive policy.

## Learning Outcomes

The project provides practical experience in:

- Reinforcement Learning.
- SARSA.
- Deep Q-Learning.
- Double DQN.
- PyTorch.
- Neural Network Design.
- Experience Replay.
- Target Networks.
- Epsilon-Greedy Exploration.
- Reward Shaping.
- Adversarial Environments.
- Cybersecurity Simulation.
- Model Evaluation.
- Training Analysis.

## Cybersecurity Context

This project explores the application of Artificial Intelligence to autonomous cyber defense in a controlled simulation environment.

The defender must continuously observe the network state, select appropriate defensive actions and adapt its behavior based on the consequences of previous decisions.

This makes Reinforcement Learning a natural framework for studying adaptive cybersecurity strategies.

The project represents an experimental step toward AI-driven cyber defense systems capable of learning defensive policies rather than relying exclusively on static, manually defined rules.

## Disclaimer

This project is intended for academic, educational and research purposes.

All experiments are performed in a simulated environment using `gym-idsgame`.

The implementation does not target real healthcare infrastructure, patient data or real-world systems.

## Author

Developed as an AI Engineering and Reinforcement Learning project focused on the intersection of:

**Artificial Intelligence | Reinforcement Learning | Cybersecurity | Healthcare**
