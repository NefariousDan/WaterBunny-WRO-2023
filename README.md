Engineering materials
====

This repository contains engineering materials of a self-driven vehicle's model participating in the WRO Future Engineers competition in the season 2023.

## Content

* `team-photos` contains 2 photos of the team (an official one and one funny photo with all team members).
* `robot-photos` contains 6 photos of the vehicle (from every side, from top and bottom).
* `video` contains the video.md file with the link to a video where driving demonstration exists.
* `schemes` contains one or several schematic diagrams in form of JPEG, PNG or PDF of the electromechanical components illustrating all the elements (electronic components and motors) used in the vehicle and how they connect to each other.
* `src` contains code of control software for all components which were programmed to participate in the competition.
* `assets` various files (mostly images) that were used in creating this repository.

## Introduction

This repository consists of 3 main parts: `hardware`, `software` and `module description`. 

| Part          | Description   |
| ------------- |-------------: |
| Hardware      | Detailed explanation of all the physical parts of the robot, including sensors, cameras, motors |
| Software      | Detailed explanation of all the steps to reproduce our solution, including library requirements, operational systems, IDEs, etc. |
| Module description | Descriptions of python scripts with their purposes and algorithms  |


## Hardware




## Software

### On Raspberry PI (Server)

- Our robot uses Raspian OS 32 bits, officially supplied operating system by Raspberry Pi Foundation. To install the operating system, you must have a microSD. First install the Raspberry Pi Imager and then choose the `Raspbian OS 32 bits (Recommended)` option from the list (NOTE: All the data on the microSD will be formatted and deleted. Don't forget to backup all important files)

- After the Raspian is installed, we must enable *SSH*, *VNC* and *Legacy Camera* support using the `sudo raspi-config` command from the terminal. Apply the changes and reboot.

- In order to connect to Raspberry Pi via SSH, we must first know its IPv4 address. Make sure that the Rapsberry Pi and your PC are connected to the same network, and then type `hostname -I` command into the terminal. Remember this IP address. Next, open the terminal on your PC and type `ssh [username]@[ipv4address]`. It will ask for the final confirmation, type 'yes'.

- Install all necessary libraries and dependancies by first upgrading pip (`pip3 install --upgrade pip`) and then running this command:

```
sudo apt-get update && sudo apt-get upgrade
pip install opencv-python       
pip install -U vidgear[core]
```

### On PC (Client)

- Install the libraries (On Windows):
  
  ```
  py -m pip install pip --upgrade
  pip install opencv-python
  pip install -U vidgear[core]
  pip install dxcam
  ```     

  Install Visual Studio Code. Then install the `Remote - SSH` extension.

- ![image](https://github.com/DanC54/WaterBunny-WRO-2023/assets/59985928/f659d4cc-ee8c-46ec-910b-5b5135142366). 

  After that choose the `Connect to Host` option...
- ![image](https://github.com/DanC54/WaterBunny-WRO-2023/assets/59985928/9aea72c2-2ae2-4f8e-8e27-8c01cf7388fe) 

  ...and enter the `ssh [username]@[Raspberry Pi's IPv4 address]` into the pop-up menu. 

- ![image](https://github.com/DanC54/WaterBunny-WRO-2023/assets/59985928/7b30b04a-409b-4310-9647-297294f88405)



- Make sure that the `TCP port 5454` is forwarded. You probably don't have to worry about it, as it almost always is. In case it isn't forwarded, check [this guide](https://www.noip.com/support/knowledgebase/general-port-forwarding-guide).


## Module description

| Part          | Description   |  Server/Client     |
| ------------- |-------------: |:-----------:  |
| `GPIORobot.py`    |  Main library used for accessing Raspberry Camera and sending frames to client (PC) via `Netgear` framework. |  Server-side   |
| `RobotApiLab.py`  |  A file containing class object of our robot. It is used for controlling servo and DC motors.   |   Server-side
| `Qualify.py`      | Code for riding 3 laps around the game mat *wihout* any obstacles. |   Server-side  |
| `Final.py`     | Code for riding 3 laps around the game mat *with* green and red obstacles.  |  Server-side   |
| `Hawkeye.py`    |  A python file used to receive processed images from Raspberry PI to Client          |    Client-side   |



### Server-side

- `GPIORobot.py`: Contains `__work_f()` and `__send_frame()` functions that run on separate threads when `GPIORobot()` class is initialized. Initializes `Netgear` framework and sends frame via it.
- `RobotAPILab.py`: Runs `Servo.write()` function as a separate thread in order to avoid using `time.sleep()` function, allowing us to avoid overheating the L298 motor driver or DC motors.
- `Qualify.py`: Contains `black_line_left()` and `black_line_right()` functions to constantly search for walls and drive away from them.
- `Final.py`: Same as above, except the function `detect_box()` was added to determine the steering angle when the camera sees red and green obstacles. 


### Client-side

- `Hawkeye.py`: Connects to the `Netgear` framework in a `receive=True` mode. Replace the `address="192.168.x.xxx"` line with your *Client's* IPv4 address (You can get this by typing `ipconfig` into Windows terminal).

You will have to modify the following code:

```
client = NetGear (
    address="192.168.x.xxx",
    port="5454",
    protocol="tcp",
    pattern=1,
    receive_mode=True,
    logging=True,
    **options
)
```

