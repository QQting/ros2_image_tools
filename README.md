# image_tools
Modified from https://github.com/ros2/demos/tree/master/image_tools

## For Windows 10

Open a new terminal with VS2019 developing environment:
```
# Load ROS 2 environment
call c:\opt\ros\dashing\x64\setup.bat

# Get source
mkdir c:\dev\ros2_image_ws\src
cd c:\dev\ros2_image_ws\src
git clone https://github.com/QQting/image_tools.git
```

Please set correct path to your camera device or a video file in ```src/cam2image.cpp```. If you want to transfer video file, you can download the MP4 file from this site http://rpg.ifi.uzh.ch/E2VID.html

Please modify [src\cam2image.cpp](https://github.com/QQting/image_tools/blob/9d361bebd1052d2ce11700d3b6af8da456b2cbf3/src/cam2image.cpp#L157):
```
# For video file:
cap.open("C:\\dev\\resource\\highway1.mp4");

# For camera device:
cap.open(0);  // open camera 'device 0'
```

Build the source after you modified:
```
# Build source
cd c:\dev\ros2_image_ws
colcon build --merge-install --event-handlers console_cohesion+

# Load this package environment
call install\local_setup.bat

# Publish image
ros2 run image_tools cam2image

# Subscribe image
ros2 run image_tools showimage
```
