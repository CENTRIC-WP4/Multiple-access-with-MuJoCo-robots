# Multiple-access-with-MuJoCo-robots
Communication environments between 1 BS and several gymnasium entities. <br>
Author: Septimia Sarbu, ICON, CWC, University of Oulu <br>
Created on 16.11.2023 <br>
The notebooks implement basic communication environments between one base station (BS) and one or several user equipments (UEs). The UEs are instantiated as gymnasium environments (CartPole and MuJoCo) https://gymnasium.farama.org/ . To simulate real-time parallel communication, the operation of the BS and UEs is implemented using the Multiprocessing feature of python. <br>

# Overview
In **CartPole_simple_comm_1BS_1UE_github.ipynb** and **Mujoco_simple_comm_1BS_1UE_github.ipynb**: the communication takes place between 1 BS and 1 UE. At one time point, the UE (either the CartPole or the MuJoCo gym environment) sends its state to the BS, at the next time point the BS samples an action from the uniform distribution and sends it to the UE. Then, the UE executes the received actions and, at the following time point sends the new state to the BS. Perfect reception is assumed at the BS. A UE state vector is counted as an SDU (service data unit). The communication round ends when a given number of SDUs have been sent and received. Instead of the action sampled from the uniform distribution, the user would sample the action from a policy learned a priori. <br>

In **CartPole_simple_comm_1BS_manyUEs_contention_free_github.ipynb** and **Mujoco_simple_comm_1BS_manyUEs_contention_free_github.ipynb**: the communication takes place between 1 BS and sevaral UEs. The number of UEs is given by the user in the main() function found in the notebooks. A basic version of the contention free protocol facilitates the communication between the BS and the UEs. A UE state vector is counted as an SDU (service data unit). With the block error rate probability (pTBLER) given by the user in the main() function found in the notebooks, corrupted reception is assumed at the BS. The communication round ends when a given number of SDUs have been sent and received. <br>

When the UE (either the CartPole or the MuJoCo gym environment) has SDUs to send, it sends a service request ('SR') to the BS and waits to receive a service grant ('SG'). If the BS receives multiple 'SR' at the same time point, it randomly select one UE to which to transmit the 'SG'. Upon reception of the 'SG', the selected UE sends its state to the BS and waits for an action from the BS. If the SDU is received without errors, then the BS samples an action from the uniform distribution and sends it to the UE. If it is received with error, with probability given by pTBLER, then the BS sends a non-acknowledgemet message ('NACK') to the UE. Upon reception of 'NACK', the UE retransmits its state. When a UE receives an action from the BS, it executes the received action and, at the following time point, sends a new 'SR' message to the BS. Instead of the action sampled from the uniform distribution, the user would sample the action from a policy learned a priori. <br>

# Expected outputs

# Getting started

# Baselines

# Usage

# How to contribute

# References
1. [Gynmasium environment] (https://gymnasium.farama.org/)
2. [How to make your custom Gymnasium environment] (https://gymnasium.farama.org/tutorials/gymnasium_basics/environment_creation/)
# License
This project is licensed unde the MIT license - see the License file on the right 



