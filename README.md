# GelSight Tactile Sensor Demo - Dextrob version
*This project is adapted from GelSightInc [tactiledemo](https://github.com/gelsightinc/tactiledemo) and [sdkdemo](https://github.com/gelsightinc/sdkdemo) with additional implementation of ROS integration, customized projects and documentation files.* <br/>

This project demonstrates how to grab images from the GelSight tactile sensor, push them through the GelSightSDK to convert to 3D and then convert the 3D data
into OpenCV CV_32F format. 

## Prerequisites

This demo assumes you have several third-party packages installed. Here are the packages and basic installation instructions.

 - Ximea API: Download the Ximea XPI from [XIMEA Linux Software](https://www.ximea.com/support/wiki/apis/XIMEA_Linux_Software_Package) and install according to the instructions. It will install the Ximea libraries to /usr/lib and the headers to /usr/include/m3api. <br/>
Note: the current camera info can be found from [here](https://www.ximea.com/en/products/usb-31-gen-1-with-sony-cmos-xic/mc031cg-sy), which use `./install` instead of `./install -pcie` <br/>
check `export LD_LIBRARY_PATH=/opt/XIMEA/lib:$LD_LIBRARY_PATH` in `~/.bashrc`
 - OpenCV: Download and use CMake to build OpenCV. [install opencv on Ubuntu](https://docs.opencv.org/master/d7/d9f/tutorial_linux_install.html) After make install, the libraries will be installed to /usr/local/lib. <br/> *The original repo suggests opencv_world: "modify the CMake settings to build opencv_world for convenince."?* 
 - GelSightSdk: Get the latest version of GelSightSdk (C++ code to initiate sensor and retrieve data) for Linux from support@gelsight.com. In our case, the licenses are obtained through emailing the vendor directly and the licenses are system-dependent (Ubuntu 18/20) with expiry dates. <br/>
The software expects to find the license file `gelsight_64.bin` in `$GS_SDK/lib`. Therefore, after obtaining the license file, define the GS_SDK environment variable to specify the location of the GelSightSdk folder on your machine, i.e set the following in `~/.bashrc`
```
export GS_SDK=/home/ruihan/Desktop/GelSight/GelSightSdk20210113_Ubuntu18
export LD_LIBRARY_PATH=$GS_SDK/lib:$LD_LIBRARY_PATH
```

## Testing the camera
Run the xiCamTool software by either 
 - Double clicking the xiCamTool icon that is installed at the desktop by default
 - Running `/opt/XIMEA/bin/xiCamTool` in a new terminal

You can test the camera by running xiCamTool. The basic settings are:
 - Basic -> Auto exposure: unchecked
 - Basic -> Exposure: 2 ms (between 1 and 3 ms depending on the camera/lens)
 - Trigger, Device I/O ->  Device IO Setup... -> GPO1: Exposure active 
 - Performance -> Control FPS: Frame Rate Limit
 - Performance -> Frame rate: 80 FPS

The `Exposure active` settings for GPO (General Purpose Output) triggers the LEDs using the camera strobe. When the exposure is active, the strobe is HIGH, turning the lights on. It is normal for the lights to flicker with the camera. 

You should be able to click the triangle in the upper left corner of xiCamTool and see a live image at 80 FPS with the lights on, similar to the following in xiCamTool <br/>
![xiCamTool_sampleImg](docs/xiCamTool_sampleImg.jpg)

## Checklist/tips for debugging
* If you encounter the following issue is when running xiCOP in /opt/XIMEA/bin: <br/>
 `[MC050MG-SY-UB/CIMAU1633011] Kernel setting usbcore.usbfs_memory_mb is too low.` <br/>
Execute the following command: <>
`sudo tee /sys/module/usbcore/parameters/usbfs_memory_mb >/dev/null <<<0`
```

## Known issue
* If you run `ruihan@Precision-M4800:~/package/examples/xiSample$ ./xiSample` or any abovementioned command in terminal, the following errors may occur, which is currently ignored as long as xiCamTool returns reasonale images. (The log level can also be set in xiCamTool)
```
...
xiAPI: xiAPI error: Expected XI_OK 
```

## Building the demo
Download the software: <br/>
 - http://www.gelsight.com/downloads/GelSightSdk20210113.Ubuntu18.tar.gz (for Ubuntu18)
- http://www.gelsight.com/downloads/GelSightSdk20210114.U20.tar.gz (for Ubuntu20)
Download the license: <br/>
 - http://www.gelsight.com/downloads/AStar_gelsight_64.bin (for Ubuntu18) (currently expires on Jan 14, 2022)
 - http://www.gelsight.com/downloads/AStar_Ub20_gelsight_64.bin (for Ubuntu20)
On linux, run the following: (take Ubuntu 18 filenames as an example)
```
sudo mkdir -m 777 /etc/gelsight

cp AStar_gelsight_64.bin /etc/gelsight/gelsight_64.bin

tar -xzf GelSightSdk20210113.Ubuntu18.tar.gz

git clone https://github.com/gelsightinc/sdkdemo.git sdkdemo

cd sdkdemo

export GS_SDK <path to GelSightSdk20210113.Ubuntu18>

mkdir build

cd build

cmake ..; make

./demo/demo
```
