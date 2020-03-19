# Transmit Video Frames from Windows to Ubuntu w/ ROS 2

We assume you have already installed ROS 2 on Ubuntu. In this document, we only show you:
1. How to setup ROS 2 environment and build ROS 2 packages on Windows 10.
2. How to use OpenCV along with ROS 2 to send/receive video frames on local Windows 10 PC.
3. How to send video frames from Windows 10 to Ubuntu.
   
If you also want to learn how to install ROS 2 on Ubuntu, please check [this](https://index.ros.org/doc/ros2/Installation/Dashing/Linux-Install-Debians/).

## Overall steps
- Install Microsoft Visual Studio 2019 Community.
- Install ROS 2 Dashing and all the dependencies on Windows 10.
- Leverage new **Windows Terminal** for better user experience.
- Clone this package to your workspace.
- Build this package and function verification.

My steps are sorted according to http://wiki.ros.org/Installation/Windows and https://index.ros.org/doc/ros2/Installation/Dashing/Windows-Install-Binary/

---

## Install Microsoft Visual Studio 2019 Community

VS2019 official website: https://docs.microsoft.com/en-us/cpp/build/vscpp-step-0-installation?view=vs-2019

When you install VS2019, make sure to include "**Desktop development with C++**" workload.

---

## Install ROS 2 Dashing and all the dependencies on Windows 10

Install [Choco](https://chocolatey.org/install):
  - Open **cmd** with privilege (Run as Administrator)
  - Copy the following command line and then paste it on cmd:
  
    ```
    @"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
    ```

Install Python:
 - Please install ```python-3.7.5```, other versions are not compatible with ROS 2.
    ```
    choco install -y python --version 3.7.5
    ```

Install Git:

 - Paste the following command line into privileged cmd:
    ```
    choco upgrade git -y
    ```

Install ROS 2:

 - Follow below commands on privileged cmd to install ROS 2 and its dependencies:
    ```
    mkdir c:\opt\chocolatey
    set ChocolateyInstall=c:\opt\chocolatey
    choco source add -n=ros-win -s="https://roswin.azurewebsites.net/api/v2" --priority=1
    choco upgrade ros-dashing-desktop -y --execution-timeout=0 --pre
    ```
    The above ROS 2 binary packages will be installed into ```c:\opt\ros\```. It's not recommended to use this package since there are lots of problems I've met. So below binary packages are what I actually use:
 - Please download the binary packages from github: https://github.com/ros2/ros2/releases/tag/release-dashing-20191213 and then extract it into ```c:\dev\```

Verify ROS 2 function:

- Open a new terminal with VS2019 development environment (i.e. ```x64 Native Tools Command Prompt for VS 2019``` in the Start Menu)
- Load ROS 2 development environment:
    ```
    # Load ROS 2 environment
    call c:\dev\ros2-dashing\setup.bat
    ```
- Type the following command to start **talker**:
    ```
    ros2 run demo_nodes_cpp talker
    ```
- Open a new terminal with both VS2019 and ROS 2 environment, and then type the following command to start **listener**:
    ```
    ros2 run demo_nodes_cpp listener
    ```
- The ROS 2 is installed successfully if listener receives "Hello World".
 
---

## Leverage new **Windows Terminal** for better user experience
It's recommended to use Windows Terminal to manage multiple terminals with ROS development environment. Please download it from Microsoft store: https://www.microsoft.com/store/apps/9n0dx20hk701

You can copy my settings in [resource/profiles.json](https://github.com/QQting/ros2_image_tools/blob/dashing/resource/profiles.json) and replace current settings. My configuration saves your time typing so many commands in order to load VS2019 environment and ROS environment.
Please make sure the paths filled in profiles.json are all correct. The paths should be the same if all the softwares are installed in default place.

![image](https://github.com/QQting/ros2_image_tools/blob/dashing/resource/windows_terminal_settings.png)
---
## Clone this package to your workspace

Open a new terminal with ROS 2 development environment, and then do the followings:
```
# Get source
mkdir c:\dev\ros2_image_ws\src
cd c:\dev\ros2_image_ws\src
git clone https://github.com/QQting/ros2_image_tools.git
```

Please set correct path to your video file or camera device in ```ros2_image_tools\src\cam2image.cpp```. In my experience, I've tested 'highway1.mp4' from this site http://rpg.ifi.uzh.ch/E2VID.html and also tested the webcam on my laptop.

To specify the file path, please modify [src\cam2image.cpp](https://github.com/QQting/image_tools/blob/9d361bebd1052d2ce11700d3b6af8da456b2cbf3/src/cam2image.cpp#L157):
```
# For video file:
cap.open("C:\\dev\\resource\\highway1.mp4");

# For camera device:
cap.open(0);  // open camera 'device 0'
```

---

## Build this package and function verification

Build the source after you modified:
```
# Build source
cd c:\dev\ros2_image_ws
colcon build --merge-install --event-handlers console_cohesion+ --cmake-args -DCMAKE_BUILD_TYPE=Release

# Load this package environment
call install\local_setup.bat

# Publish image with Best-effort QoS
ros2 run my_tools cam2image -r 0

# Subscribe image with Best-effort QoS
ros2 run my_tools showimage -r 0
```

Now you can clone this package into Ubuntu, and then use below command to build package on Ubuntu:
```
colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release
```

After this, try to run ```showimage``` on Ubuntu and ```cam2image``` on Windows, you should see the result as below pictures:
```
# On Windows
ros2 run my_tools cam2image -r 0
```
![image](https://github.com/QQting/ros2_image_tools/blob/dashing/resource/image_sender.png)
```
# On Ubuntu
ros2 run my_tools showimage -r 0
```
![image](https://github.com/QQting/ros2_image_tools/blob/dashing/resource/image_receiver.png)
