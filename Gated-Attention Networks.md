## Gated-Attention Architectures for Task-Oriented Language

Paper link: [here](https://arxiv.org/pdf/1706.07230.pdf) \
Code link: here

*Overview*

**What?** *Task Oriented Language grounding* : Extractin semantic meaningful representation of langugage and map it ot visual elements and actions in the environment. The paper proposes combined Gated Attention mechanism and learns a poliy to execute the language instructions and execute them using standard reinforement and imitation learning methods. 

**Why**
Challenges involved in Task Oriented Language grounding:
- agent has to learn to recognize object in raw pixel input
- overcome cases where the object might be occluded or outside field of view
- ground (or map) each concept of instruction in visual elements or actions 
- reason bout pragmatic of language based on the objects 

**How?**

> To overcome these challenges, the paper proposes an architectture that comprises of "state processing module" that creates a joint representation and the images observed by the agent and a poicy learner to predict the optimal action the agent has to take in that timestep.

 Gated-Attention **multimodal fusion** mechanism, which is based on multiplicative
interactions between both modalities.

1) Tha authors propose an end-to-end trainable architecture that handles mapping of language instructions to raw visual pixel-based inpt for task-oriented language grounding without no linguistic or perceptual knowlege
2) Gated-Attention mechanism for multi-modal fusion. It outforms the baseline method of concatenating the representations. 

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e8855a07-de61-4125-86aa-dacd2fa0b2d0/Untitled.png](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e8855a07-de61-4125-86aa-dacd2fa0b2d0/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210203%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210203T223239Z&X-Amz-Expires=86400&X-Amz-Signature=15de50afac8bb94d09b3aa56996898b9e8985cc6680b79bb05dce81dd83b550c&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

*Problem Formulation*

1) Consider an agent interating with the episodic environment $E$
2) In beginning of each episode, the agent receives a natural language instruction ($L$) which indicates a description of the target, a visual object in the environement.
3) At each time stamp, the agent receives a raw pixel-level image of the first-person view of the environment and perform action $a_t$
4) The episode terminates whenever the agent reaches any object or the number of time steps exceed the maximum episode length

Let  $s_t = (I_t,L)$ denote the state at each time stamp. The objective is to learn an optimal policy $\pi(a_t|s_t)$, which maps the observed state to actions. In this paper, tha authors use two policy learning approches
(1) Imitation learning
(2) Reinforcement learning

*Proposed Architecture*
Two main cocntrictutions from the paper

1) **State Processing Module**:  It consists of 
- Convolutional network to process the image
- Gated recurrent unit to process the instructions
- Gated- Attention units to combine the image and language representations

 Gated- Attention Units : the instruction embeddings are passed throgh a fully-connected linear later with a sigmoid activation. The outpt layer,  

1) Consider an agent interating with the episodic environment $E$
2) In beginning of each episode, the agent receives a natural language instruction ($L$) which indicates a description of the target, a visual object in the environement.
3) At each time stamp, the agent receives a raw pixel-level image of the first-person view of the environment and perform action $a_t$
4) The episode terminates whenever the agent reaches any object or the number of time steps exceed the maximum episode length

Let  $s_t = (I_t,L)$ denote the state at each time stamp. The objective is to learn an optimal policy $\pi(a_t|s_t)$, which maps the observed state to actions. In this paper, tha authors use two policy learning approches
(1) Imitation learning
(2) Reinforcement learning

*Proposed Architecture*
Two main cocntrictutions from the paper

1. **State Processing Module**:  It consists of 
- - Convolutional network to process the image
- - Gated recurrent unit to process the instructions
- - Gated- Attention units to combine the image and language representations

**Gated- Attention Units** : the instruction embeddings are passed throgh a fully-connected linear later with a sigmoid activation. The outpt layer, *d,* is equal to the number of feature maps in the outpur of the convolutional network. The output of this linear layer is called the attention vector which is exanded to *H x W* matrix. The matrix is multiplied element wise with the outpt of the convolutional network. 

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/673313be-ac6e-47f0-8169-c8a82f6d9f23/Untitled.png](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/673313be-ac6e-47f0-8169-c8a82f6d9f23/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210203%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210203T223253Z&X-Amz-Expires=86400&X-Amz-Signature=e52ea0b45d65fa3f169da6d9b45d531ab0e250a15bc5e8b37ea8d27c2632fbd5&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

2. **Policy Learning Module:** For reinforcement learnign, the authors use the *Asynchronous Advantage Actor-Critic (A3C) algorithm* which uses a deep neural network to learn the policy and value functions and runs multiple parallel threads to update the network parameters

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e7af28a8-74bd-4a64-9cfb-0a3b833e8880/Untitled.png](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e7af28a8-74bd-4a64-9cfb-0a3b833e8880/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210203%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210203T223321Z&X-Amz-Expires=86400&X-Amz-Signature=6c4cf54b2837207271e0dc94c40127e563777476f69a5d919b8b5550161e655a&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

*Evaluation Criterion*

The authors use two scenarios for evaluation:
(1) **Multitask Generalization** (MT), where the agent is evaluated on unseen maps with instructions in the train set. Unseen maps comprise on unseen combinations on objects placed at randomized locations
(2) **Zero-shot Generalization** (ZSL), where the agent is evaluated on unseen test instructions. This scenario tests whether the agent can generalize to new combinations of attribute-object pairs.