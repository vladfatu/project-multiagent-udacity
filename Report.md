# Report

### Learning Algorithm

To solve this environment, I used DDPG. I started from Udacity's [agent](https://github.com/udacity/deep-reinforcement-learning/blob/master/ddpg-pendulum/ddpg_agent.py) for the pendulum Gym environment based on this [paper](https://arxiv.org/pdf/1509.02971.pdf) by Google DeepMind. 
\
\
The pendulum Gym environment has a single agent while the Tennis environment I was trying to solve has 2 agents that are playing tennis against each other. So I had to adjust the code from Udacity to support multiple agents. 
\
\
At first, I tried to solve the problem for only one of the agents. I wanted to see if I can train an agent to send the ball over the net the first time(knowing it will not return). This was pretty slow since half of the time the ball was in the other agent's side and since that agent was not being trained, the episode would just end. Still, I did get some results with this and it helped me tune the hyperprameters.

After this, I tried to solve the environment for both agents so I added a MultiAgentController helper class. Here I keep a reference to a global Replay Buffer and the 2 agents. Even though they share the replay buffer, the agents are completely independent, each has their own actor and critic network.


- #### Network architecture

There are 2 agents and each contains 2 networks, an Actor and a Critic.
- Actor:
The input to the Actor network has 24 dimensions(3 stacked observation vectors of size 8) and contains the position and velocity of the ball and racket. This is followed by 2 hidden Linear Layers of size 128 activated by a RELU activation function. The output has a size of 2 and is activated by a tanh activation function.
- Critic:
The input to the Critic network has the same 24 dimensions as the Actor network. This is followed by a hidden Linear Layer of size 128 activated by a RELU activation function. Next, there is another hidden Linear Layer of size 130(128 from the previous layer + 2, the action provided by the Actor network). This layer is also activated by a relu activation function. Finally, the output has a size of 1.

- #### Hyperparameters


<strong>BUFFER_SIZE = 1e5</strong> (this is size of the replay buffer used to store experiences)\
<strong>BATCH_SIZE = 128</strong> (this is the number of random samples chosen from the replay buffer when training)\
\
<strong>LR_ACTOR = 1e-3</strong> (actor network learning rate)\
<strong>LR_CRITIC = 1e-3</strong> (critic network learning rate)\
<strong>GAMMA = 0.99</strong> (discount factor)\
\
<strong>TAU = 1e-2</strong> (factor used when updating the target network)\
<strong>WEIGHT_DECAY = 0</strong> (L2 weight decay)


### Results

I was able to solve the environment in 882 episodes.

<img width="540" alt="Training Graph" src="https://github.com/vladfatu/project-multiagent-udacity/assets/1000350/30f9d1a1-24b6-4b5e-9319-5018ca1f2acd">

### Future Improvements

- #### Give the critic access to state observations from both agents.
In the current implementation the 2 agents are completely independent from each other. They do have a common replay buffer but they only have access to their own state observation. [This paper](https://proceedings.neurips.cc/paper_files/paper/2017/file/68a9750337a418a86fe06c1991a1d64c-Paper.pdf) suggests that we could get better results if we allow the critic access to both state observations.  

- #### Add noise to the observation space instead of the action space.
The current implementation adds Ornsetein-Uhlenbeck noise to actions predicted by the network(the output). It is possible to get better results if noise was added to the observation(the input of the network).

- #### Add noise with decay.
It is possible that noise is helpful at the begining of training, but not after the agent is starting to send the ball over the net succesfully. I should try to implement a decay factor for noise to diminish it over time. 

- #### Prioritized experience replay!
Another improvement would be to choose better experiences from the replay buffer. This is even more relevant here than other environments as in the begining of training we have very few examples of succesful actions and there is a big chance that when we sample the replay buffer we will not retrieve any. The full description for this method can be found in [this research paper](https://arxiv.org/abs/1511.05952).
