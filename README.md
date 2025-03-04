# Multiple-access-with-MuJoCo-robots
Communication environments between 1 BS and several gymnasium entities. <br>
Author: Septimia Sarbu, ICON, CWC, University of Oulu <br>
Created on 16.11.2023 <br>
The notebooks implement basic communication environments between one base station (BS) and one or several user equipments (UEs). The UEs are instantiated as gymnasium environments (CartPole and MuJoCo) [Gymnasium](https://gymnasium.farama.org/) . To simulate real-time parallel communication, the operation of the BS and UEs is implemented using the Multiprocessing feature of python. <br>

# Overview
In **CartPole_simple_comm_1BS_1UE_github.ipynb** and **Mujoco_simple_comm_1BS_1UE_github.ipynb**: the communication takes place between 1 BS and 1 UE. At one time point, the UE (either the CartPole or the MuJoCo gym environment) sends its state to the BS, at the next time point the BS samples an action from the uniform distribution and sends it to the UE. Then, the UE executes the received actions and, at the following time point sends the new state to the BS. Perfect reception is assumed at the BS. A UE state vector is counted as an SDU (service data unit). The communication round ends when a given number of SDUs have been sent and received. Instead of the action sampled from the uniform distribution, the user would sample the action from a policy learned a priori. <br>

In **CartPole_simple_comm_1BS_manyUEs_contention_free_github.ipynb** and **Mujoco_simple_comm_1BS_manyUEs_contention_free_github.ipynb**: the communication takes place between 1 BS and sevaral UEs. The number of UEs is given by the user in the main() function found in the notebooks. A basic version of the contention free protocol facilitates the communication between the BS and the UEs. A UE state vector is counted as an SDU (service data unit). With the block error rate probability (pTBLER) given by the user in the main() function found in the notebooks, corrupted reception is assumed at the BS. The communication round ends when a given number of SDUs have been sent and received. <br>

When the UE (either the CartPole or the MuJoCo gym environment) has SDUs to send, it sends a service request ('SR') to the BS and waits to receive a service grant ('SG'). If the BS receives multiple 'SR' at the same time point, it randomly select one UE to which to transmit the 'SG'. Upon reception of the 'SG', the selected UE sends its state to the BS and waits for an action from the BS. If the SDU is received without errors, then the BS samples an action from the uniform distribution and sends it to the UE.  If it is received without errors, then the BS sends an acknowledgement message ('ACK') to the UE. If it is received with error, with probability given by pTBLER, then the BS sends a non-acknowledgement message ('NACK') to the UE. Upon reception of 'NACK', the UE retransmits its state. When a UE receives an action from the BS, it executes the received action and, at the following time point, sends a new 'SR' message to the BS. Instead of the action sampled from the uniform distribution, the user would sample the action from a policy learned a priori. <br>

# Expected outputs
These notebooks provide a parallel communication framework between 1 BS and several UEs (CartPole or MuJoCo robots). When running these notebooks, in log files, the user can track the signalling messages exhanged between the UEs and the BS and can visualized the data received by the BS. While the communication is running, these two functionalities can be monitored for each time step. As a result, the user can track how often and which UEs access the channel and how much data the BS is receiving, as well as the type of data received.  

# Getting started
These notebooks provided in this repository have been tested on a remote server running Red Hat Enterprise Linux release 8.9, python 3.7.13, GCC 7.5.0 and displayed for editing in a web browser under Windows 10 Enterprise.

In order to open, run and edit the notebook in a web browser, the user may follow the following instructions:
1. In Windows, download the notebook files from the above list
2. In PowerShell, copy them onto the remote server, using the password for authentication
 ```bash
PS C:\> scp \absolute\path\to\your\files\*ipynb username@remoteserver:/absolute/path/to/destination\.
```
or using the authentication certificate
 ```bash
PS C:\> scp -i \absolute\path\to\your\authentication_certificate \absolute\path\to\your\files\*ipynb username@remoteserver:/absolute/path/to/destination\.
```

3. Create a SSH tunnel to the remote server, to be able to display the notebooks on the user's local station for editing. Firstly, open a Linux emulator on Windows, such as Windows Subsystem for Linux (WSL)
```bash
ssh -i /absolute/path/to/your/authentication_certificate -4 -L port_number_on_local_machine:localhost:port_number_on_server -vvvvv username@remoteserver -N
```

4. On the remote server running a Linux distribution, launch a python virtual environment in the desired directory
```bash
[username@remoteserver]$ conda activate /absolute/path/to/your/conda/installation/miniconda3/envs/MyEnv
```
5. After activation, in that environment, launch jupyter without browser features, running on a specified port. This port number needs to match the one used in making the SSH tunnel from above, that is **port_number_on_server**.
```bash
(MyEnv)[username@remoteserver]$ jupyter notebook --no-browser --port=port_number_on_server
```
6. On the local station, launch a web browser and open the content found at **port_number_on_local_machine**, used in making the SSH tunnel from above. 
```bash
localhost:port_number_on_local_machine
```

# Baselines
The contention-free communication protocol with random scheduling acts as the baseline for the above scenarios. The user may modify the provided source code, to replace the baseline and add their own solution to the protocol emergence challenge.

# Usage
The notebooks contain the source code for the above scenarios. The source code can be modified directly by openning, running and editing the notebooks in a web application. To ensure real-time synchronization, the communication network of 1 BS and several UEs (CartPole or MuJoCo robots) is simulated using the Multiprocessing feature of python. The entry point is the main() function illustrated below. The BS and UEs exchange messages and data via the Queue data structure. The BS and the UEs represent concurrent processes, which, once they are launched, will communicate through the contention-free protocol with random scheduling, implemented in the functions runUE() and runBS(). The user may modify these function to replace the baseline and add their own solution to the protocol emergence challenge.
```python
# entry point
if __name__ == '__main__':
        
    # create the UE and the BS
    tSDUs = 20 + 1 # SDUs to transmit from each UE to BS, then the episode ends
    tEND = 20 + 1
    
    nUEs = 2
    
    # create the shared queues
    UCM = [Queue() for i in range(nUEs)]
    DCM = [Queue() for i in range(nUEs)]
    
    ch = [Queue() for i in range(nUEs)]

    # start the UE
    UE_processes = [Process(target=runUE, args=(UCM,DCM,ch,tSDUs,UEModel,i,)) for i in range(nUEs)]
    for UE_process in UE_processes:
        UE_process.start()
    
    # start the BS
    seedBS = 230
    BS_process = Process(target=runBS, args=(UCM,DCM,ch,BSModel,nUEs,tEND,seedBS,))
    BS_process.start()
        
    BS_process.join()
    
    # wait for all processes to finish
    for UE_process in UE_processes:
        UE_process.join()

    print("All processes have terminated successfully")
```

# How to contribute
1. **Implementing new problems:** extending the notebooks by changing the type of the communication messages and the network topology
2. **Implementing new solutions:** propose new communication protocols for the above problems 
# References
1. [Gynmasium environment](https://gymnasium.farama.org)
2. [How to make your custom Gymnasium environment](https://gymnasium.farama.org/tutorials/gymnasium_basics/environment_creation)
3. [Python Multiprocessing tutorials](https://superfastpython.com/learning-paths/)
4. [How to run Jupyter notebooks](https://docs.jupyter.org/en/latest/running.html)
5. [Markdown language summarized](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

# License
This project is licensed unde the MIT license - see the License file on the right 



