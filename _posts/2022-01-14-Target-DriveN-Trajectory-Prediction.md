---
title: Target-driveN Trajectory Prediction(TNT)
date: 2022-01-15 18:12:00 +0800
categories: [Paper]
tags: [Review, Deep Learning, Trajectory Prediction, Multimodal Prediction]
seo:
  date_modified: 2022-01-15 19:15:26 +0800


---

***link***: https://arxiv.org/pdf/2008.08294.pdf

> 1. ABSTRACT

​		Predicting the future behavior of moving agents is essential for real world applications. <mark>It is challenging as the intent of the agent and the corresponding behavior is unknown and intrinsically multimodal</mark>. Our key insight is that for prediction within a moderate time horizon, the future modes can be effectively captured by a set of target states. This leads to our target-driven trajectory prediction (TNT) framework. TNT has three stages which are trained end-to-end. 

1. It first **predicts an agent’s potential target states T steps into the future**, by encoding its interactions with the environment and the other agents. 

2. TNT then **generates trajectory state sequences conditioned** on targets. 

3. A final stage **estimates trajectory likelihoods and a final compact set of trajectory predictions is selected**. 

   This is in contrast to previous work which models agent intents as latent variables, and relies on test-time sampling to generate diverse trajectories. We benchmark TNT on trajectory prediction of vehicles and pedestrians, where we outperform state-of-theart on Argoverse Forecasting, INTERACTION, Stanford Drone and an in-house Pedestrian-at-Intersection dataset.

> 1. INTRODUCTION

![image-20220115175212334](/assets/img/commons/image-20220115175212334.png)



> <del>2. RELATED WORK</del>

> <del>3. FORMULATION</del>

> 4. Target-DriveN Trajectory Prediction

![image-20220115175945290](/assets/img/commons/image-20220115175945290.png)

**4.1 Scene context encoding**

Modeling scene context is a first step in trajectory prediction so as to capture agent-road and agentagent interactions. TNT can use any suitable context encoder: when the HD map is available, we use a state-of-the-art hierarchical graph neural network <mark>VectorNet (https://arxiv.org/pdf/2005.04259.pdf) </mark>to encode the context. Specifically, polylines are used to abstract the HD map elements cP (lanes, traffic signs) and agent trajectories sP ; a subgraph network is applied to encode each polyline, which contains a variable number of vectors; then a global graph is used to model the interactions between polylines. The output is a global context feature x for each modeled agent. If scene context is only available in the form of top-down imagery, a ConvNet is used as the context encoder.



**4.2 Target prediction**

 		Targets τ are defined as the locations (x, y) an agent is likely to be at a fixed time horizon T. In the first target prediction stage, we aim to <mark>provide a distribution of future targets of an agent p</mark>. We model the potential future targets via a set of N discrete, quantized locations with continuous offsets.

![image-20220115185608094](/assets/img/commons/image-20220115185608094.png)

​	The trainable functions <mark>f(·) and ν(·) are implemented with a 2-layer multilayer perceptron (MLP)</mark>, with target coordinates (xk , yk ) and the scene context feature x as inputs. They predict a discrete distribution over target locations and their most likely offsets. The loss function for training this stage is given by:

![image-20220115185727125](/assets/img/commons/image-20220115185727125.png)



![image-20220115185800491](/assets/img/commons/image-20220115185800491.png)



**4.3 Target-conditioned motion estimation**

​		In the second stage, we model the likelihood of a trajectory given a target with a generalized normal distribution. This <mark>makes two assumptions</mark>，

* future time steps are conditionally independent, which makes our model computationally efficient by avoiding sequential predictions.
* Second, we are making strong but reasonable assumption that the distribution of the trajectories is unimodal (normal) given the target.

![image-20220115190023388](/assets/img/commons/image-20220115190023388.png)



**4.4 Trajectory scoring and selection**

​		Our final stage estimates the likelihood of full future trajectories sF . <mark>This differs from the second stage, which decomposes over time steps and targets, and from the first stage which only has knowledge of targets, but not full trajectories</mark>:

![image-20220115190210576](/assets/img/commons/image-20220115190210576.png)

​	where g(·) is modeled as a 2-layer MLP. The loss term for training this stage is the cross entropy between the predicted scores and ground truth scores,

![image-20220115190320019](/assets/img/commons/image-20220115190320019.png)



**4.5 Training and inference details**

![image-20220115190415429](/assets/img/commons/image-20220115190415429.png)



> 5. Experiments

**5.1 Datasets**

* Argoverse forecasting dataset

* INTERACTION dataset

* In-house Pedestrian-at-Intersection dataset (PAID) 

* Stanford Drone dataset (SDD)

  

**5.2 Implementation Details**

![image-20220115190649392](/assets/img/commons/image-20220115190649392.png)



**5.3 Ablation study**

![image-20220115190723425](/assets/img/commons/image-20220115190723425.png)

![image-20220115190745941](/assets/img/commons/image-20220115190745941.png)

**5.4 Comparison with state-of-the-art**

![image-20220115190827650](/assets/img/commons/image-20220115190827650.png)



> 6. Conclusion

![image-20220115190854025](/assets/img/commons/image-20220115190854025.png)



***related paper:***

| Paper                                                        | link                                 |
| ------------------------------------------------------------ | ------------------------------------ |
| DenseTNT: End-to-end Trajectory Prediction from Dense Goal Sets | https://arxiv.org/pdf/2108.09640.pdf |
| VectorNet: Encoding HD Maps and Agent Dynamics from Vectorized Representation | https://arxiv.org/pdf/2005.04259.pdf |
| ...                                                          |                                      |

