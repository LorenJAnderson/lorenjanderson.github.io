---
layout: page
permalink: /notes/sect01/
---
&ensp;
# 1. Environments

This section contains notes on environments. 

## 1.1 Environment API

## 1.2 Environment Showcase

### Gymnasium 
### ATARI 
The Arcade Learning Environment (ALE) was published in 2013 and offers a standardized interface to hundreds of ATARI 2600 games. From my own point of view, the success of the ATARI suite is in part due to the exciting nature of each game (i.e. compare these envrionments to the others described in this section). While there is no definitive repertoire of games for training, the 
research community has honed in on a set of 57 games for algorithmic benchmarking purposes. This set of games is quite diverse and has seen its fair share of continual and substantial research effort. 

Traditionally, the ATARI suite has been a testbed for algorithms with discrete action spaces: the controller has the two axes and a button to press for the action space of size 18: 

$$\{down, middle,up\} x \{left, middle, up\} x \{button unpressed, button pressed\}.$$

Recently, a continuous version of the environment has been created that has teh action space of 

$$[0, 1] \times [-\pi, \pi] \times [0, 1]$$

that maps polar coordinates in the unit circle to joystick positions and maps the last dimesion to pressing the button. A threshold parameter $\tau$ determines the mapping from polar coordinates and last dimension to joystick location and fire status [TODO - paper didn't seem to discuss threshold effect on fire]

#### Modifications

Some modifications were made to the initial environments to promote faster and easier learning as we will now discuss. Due to the number of possible environment additions, a standardization study was conducted in 2018 [Machado2018] that provided guidelines for modifications. Since many (and perhaps most) of papers choose to follow the guildlines in the paper, we dicuss the guidelines in detail below. We also discuss some of the choices that were not discussed in the survey. Many of these modifications are available as Gymnasium environment wrappers that can be placed on top of an already existing Gym.Env object for ease of modularity. 

**Lives Lost**: ATARI environments usually allow lives to be lost before the game restarts. Terminating the game after a single life lost reduces the amount of learning required to ascertain which behaviors lose lives at the cost of extra domain knowledge of the game hard-coded into the environment. The recommendation is to only terminate the environment on all lives lost instead of single (of many) lives lost to reduce the amount of domain knowledge.

**Frame Pooling**: Some objects are only displayed every other frame for computational reasons [Mnih2015]. From two successive frames, the maximum value of each pixel is taken to overcome this issue. There is no explicit recommendation [Machado 2018], but the experiments DO incorporate frame pooling. Another technique of color averaging (averaging two consecutive frames) is discussed but not adopted for DRL trials. 

**Full Action Space**: Some ATARI games do not need all 18 actions and can instead increase learning speed by using a reduced action space. Despite the speedup, the experiments did not incorporate the extra gameknowledge and used the full action space [Machado2018]. 

**Frame Skipping**: The 60FPS of ATARI yields little difference between consecutive frames. Instead of providing actions on every frame, a common practice is to employ frame skipping by which actions are only taken once every $k$ frames; experimentation showed that $k=5$ is good for ATARI, although there wasn't an explicit recommendation [Machado2018]. (Discuss exploration)

**Sticky Actions**: The ATARI console is deterministic, and some stochasticity prevents agents from memorizing trajectories. A major contribution of [Machado2018] is to randomly repeat the previous action for one frame instead of the new action with parameter $$\zeta$$. If the action is not repeated, the new action is taken during the next decision point. For example, when frame skipping is also present, the new action is taken immediately in the frame when the new action is not repeated until the next decision point. Sticky actions are recommended, and the value $$\zeta=0.25$$ is used in the experiments. 

**Frame Stacking**: To determine velocities of objects, knowledge from multiple close-in-time frames is required. Frame stacking wasn't recommended or deemed necessary since memory can be added with a neural network architecture such as an LSTM; however, frame stacking is perfectly fine to use, especially when the neural network is fairly simple and doesn't incorporate memory (e.g an OTS implementation). 

**Reward Normalization**: While discussed but not recommended, most games incorporate reward scaling through e.g. the sign of the reward or dividing by first non-zero reward. 

**Resizing**: A 210x160 frame is larger than necessary; although not performed in the study [Machado2018], other sources have reduced the screen to an 84x84 screen for computational ease. 

**Grayscaling**: Similarly, although not performed, other sources have reduced RGB to a single channel such as through grayscaling. 

Using the current Gymnasium code, the 'raw' environment is as follows: 

```
gym.make(<environment_name>NoFrameskip-v4)
```

It appears that the full action space False by default. To add available preprocessing steps, 


```
gym.make(<environment_name>NoFrameskip-v4,
	frameskip=5,
	full_action_space=True,
	repeat_action_probability=0.25,
	)
```

Frame pooling isn't...Lives lost aren't explicitly discussed, but it is a best practice and should be turned off by default. 


### MuJoCo 

```
Test
```

#### References 

[Mnih2015] Mnih, Volodymyr, et al. "Human-level control through deep reinforcement learning." nature 518.7540 (2015): 529-533.

[Farebrother2024] Farebrother, Jesse, and Pablo Samuel Castro. "CALE: Continuous arcade learning environment." Advances in Neural Information Processing Systems 37 (2024): 134927-134946.

[Macado2018] Machado, Marlos C., et al. "Revisiting the arcade learning environment: Evaluation protocols and open problems for general agents." Journal of Artificial Intelligence Research 61 (2018): 523-562.
