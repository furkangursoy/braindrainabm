### An Agent-Based Modelling Approach to Brain Drain

This repository contains the NetLogo source code for the model presented in the following study.

___
_If you find this useful for your research, please consider citing:_

F. Gürsoy, B. Badur. "An Agent-Based Modelling Approach to Brain Drain". _IEEE Transactions on Computational Social Systems._ 2021. (to appear)

Full Text: https://arxiv.org/abs/2103.03234

	@ARTICLE{gursoy2021braindrain,
	  author={F. {Gürsoy} and B. {Badur}},
	  journal={IEEE Transactions on Computational Social Systems}, 
	  title={An Agent-Based Modelling Approach to Brain Drain}, 
	  year={2021},
	  volume={},
	  number={},
	  pages={},
	  doi={10.1109/TCSS.2021.3066074},
	  note={to appear}
	  }
___

## Purpose

Brain drain can be defined as the emigration of highly trained or qualified people from a particular country, which has many undesirable effects for the source country. Purpose of the model is to understand the dynamics of brain drain; and provide an initial version of a simulation-based decision support tool which can be used in discovering future trends for such emigration, and design effective social policies which can reduce, stop, or reverse the brain drain. The model proposes that skilled people would like to emigrate to maximise their utility, yet actual emigration is constrained with barriers, luck, and individuals' social network.

## Entities, state variables, and scales

There exists only a single type of entity, agents, to represent individual persons who are well educated/skilled. Each person is characterised by a number of states: years spent in the workforce (*age*), whether the agent has moved abroad or still in the country (*s*), initial utility representing the agents' utility of moving abroad at the beginning of a simulation (*x*), and the utility of an agent during the course of simulation (*y*) which is calculated based on initial utility and relative attractiveness difference between host and home countries (*G*). The values for *x* and *y* are relative rather than absolute, with positive values indicating that target country provides more utility than the source country. The actual probability of moving abroad consists of a component (*p*) which is a function of utility and number of network-neighbours living abroad (i.e., *s = 0*), and luck of the agent (*luck*).

The source and target countries are not explicitly defined as separate entities. The model assumes only a single source country and a single target country which represents abroad in general. Therefore, there implicitly exists a type of entity other than the individual persons. There are global variables which correspond to the relative or absolute states of the source country. Relative attractiveness difference between host and home countries (*G*) which affects all agent utilities and barriers to move abroad (*threshold*) are such variables. Larger values of *G* indicate that the target country and is more attractive than the sender country in general. Given that *G_t* is its value at *t=t*, *G_0* is its initial value at *t=0* and *G\** is its final value in the long run.

Additional global variables in the model are as follows. How many meaningful connections on average an individual has in the network, which potentially can increase one's chances of moving abroad, is calculated using *linksToNodesRatio*. Network effect coefficient (*networkCoef*) controls the weight of network effects against the utility effect. Luck reduction coefficient (*luckCoef*) determines how much an individual's *luck* is reduced in case of unsuccessful emigration attempt.

The model works at discrete time steps and each time step corresponds to a year. The model is simulated for 50 time steps since the authors believe that a period of 50 years is long enough but not too long for obtaining meaningful insights from a such model as ours.

## Process overview and scheduling

At each time step, the following tasks occur in the order of their appearance here. 

* *age* of all agents are incremented by one.
*  Certain number of agents die, and the same number of agents are born.
*  *G_t*, that is the attractiveness difference between source and target countries, is updated.
*  *y*, that is the utility of moving abroad, of all agents are updated. 
*  *p* values of all agents are updated to reflect the changes in *y*.
*  Agents who satisfy the *p>threshold* condition migrate with a probability equal to *p*luck*. 
*  For individuals who successfully migrated, *s* is set to *0*. For individuals who attempted but failed to migrate, *luck* is updated.

	

## Design Concepts

**Basic Principles.** This model assumes a simple utilitarian approach with barriers and random processes for actually performing the most beneficial action: staying in the country or moving abroad. Network effects also play role in migration by making it more likely for people to move abroad if they have more people in their network who live abroad. Moreover, unsuccessful emigration attempts reduce future chances of emigration. Each unsuccessful attempt uncovers new information indicating further barriers for that agent's emigration. If this is omitted, too many agents who have positive utility eventually emigrate in one of their large number of repetitive attempts over the course of simulation. 

**Emergence.** Numbers of agents in the source and target countries emerge from the value of *G* which represents the general socioeconomic status difference between source and target countries, and *threshold* which indicates the degree of the barriers against moving abroad. Then, essentially, there are two control mechanisms available for policy-makers, either increase the barriers of moving abroad (which might result in undesirable side effects depending on the nature of the policies) or design policies towards a better socioeconomic environment.

**Sensing.** Individuals have complete information on *G*, *threshold*, and the status (*s*) of their network-neighbours.

**Interaction.** An individual's chance of moving abroad increases as they have more network-neighbours, which can be considered as an implicit form of interaction.

**Stochasticity.** Actual emigration is a simple stochastic process based on the probability of emigration (*p\*luck*) of each agent, constrained by the specified *threshold* value.

**Observation.** A plot is used for observing the number of agents staying in the country over the course of simulation. In addition, two simple monitors are used to display the number of individuals at home country and abroad. A visual display is also included which displays the links between the agents who are represented with different colours based on whether they live in home country or abroad.

## Initialisation
Initially, *1000* agents are created. All *s* values are initiated as *1* (i.e., all agents are born in the home country). All agents have the same initial *luck* value of *1*. Their *age* values are randomly assigned between *0* and *30*, assuming that an agent stays in the workforce for 30 years. Finally, all agents have *x* values uniformly distributed between *-1* and *1*, which means that some have better utility in moving abroad and others in staying in the country.

## Input Data
The model does not use input from external models or data files.

## Submodels

Due to initialisation of 1000 agents aged uniformly in *0-30* range, around 33 people would have *age* value of *30*. In order to ensure the initial assumption that an agent stays in the workforce for around 30 years, 33 agents who have the greatest age are selected and removed from the model (i.e., death of agents). In case of more than enough number of agents having the same age, the agents are randomly selected among them. For birth of agents; 33 agents are duplicated, creating 33 agents with the same state values. However, we initialise all state values and network links from scratch; therefore, there is effectively no duplication. (In the earlier versions, duplication is used to carry some attributes of the parent agents to the newborn agents. In this version, we do not require such state value preservation. However, we chose to keep it this way in the code so that future versions make modifications on it to achieve such value preservation more easily.). *luck*, *age*,  *x* are assigned values same as the original initialisation. For each newborn agent, an appropriate number of links are created based on the selected network type. The expected value of the number of links in the network does not change over time. 

Given that *n* is the length of the simulation, the below equation shows the formula for how *G* gradually attains the value of *G*** based on its initial value at *t=0* that is *G_0*, its value in the long run that is *G\**, and the current time step *t*. The decay parameter *d* is set as *-0.1* in this study.

*G_t = G\*  - (G\* - G_0) \* e^(d\*t)*

*y* values are updated according to the following equation.

*y_t = x + G_t*


*p* values are updated based on the following equation, given that *NA* is the set of network-neighbours who already live abroad. The equation consists of two components: utility effects and network effects. Utility values are scaled and shifted by respective parameters *scale* and *shift* and used in a logistic function to produce utility effects. Network effects are calculated via the natural logarithm operation. A coefficient (*normCoef*) is utilised to balance range differences between utility effects and network effects. Initial experiments during model development showed that when *scale*, *slide*, and *normCoef* are set values of *1.5*, *4*, and *0.05* respectively, realistic and meaningful *p* values were obtained. Hence, those parameter values are employed in this study. 

*p = e^{scale\*y-shift) / (e^(scale\*y-shift)+1) + normCoef \* networkCoef \* ln(1+|NA|)*



The emigration process is shown in below algorithm. It also demonstrates how the values of *s* and *luck* are updated.

**if** *p > threshold*
	**then** [ **if** *p \* luck > random(0,1)*
		**then** s = 0*
	**else**
		*luck = luck \* luckCoef* ]
**else**
	**continue;**
