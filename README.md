# Multiple-access-with-MuJoCo-robots
Communication environments between 1 BS and several gymnasium entities. <br>
Author: Septimia Sarbu, ICON, CWC, University of Oulu <br>
Created on 16.11.2023 <br>
The notebooks implement basic communication environments between one base station (BS) and one or several user equipments (UEs). The UEs are instantiated as gymnasium environments (CartPole and MuJoCo) https://gymnasium.farama.org/ . To simulate real-time parallel communication, the operation of the BS and UEs is implemented using the Multiprocessing feature of python. <br>

In **CartPole_simple_comm_1BS_1UE_github.ipynb** and **Mujoco_simple_comm_1BS_1UE_github.ipynb**: the communication takes place between 1 BS and 1 UE. At one time point, the UE (either the CartPole or the MuJoCo gym environment) sends its state to the BS, at the next time point the BS samples an action from the uniform distribution and sends it to the UE. Then, the UE executes the received actions and, at the following time point sends the new state to the BS. Perfect reception is assumed at the BS. A UE state vector is counted as an SDU (service data unit). The communication round ends when a given number of SDUs have been sent and received. <br>
