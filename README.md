# LAB 2: Deploy NN onto STM32
## Description
In LAB2, we deploy a pretrained image recognition model onto STM32H747. The input of the system is binarized CIFAR-10 image, and the model would predict the label of the image. 

## Environment Setup
- mbed-tools==7.55.0
- ninja==1.10.0
- gcc-arm-none-eabi==9.2.1

1. Install [mbed-tools](https://os.mbed.com/docs/mbed-os/v6.15/build-tools/install-and-set-up.html) by pip :
   
   ```
    $ pip install mbed-tools
    ```

2. Install [GCC_ARM](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/downloads) by :

    ```
    $ sudo apt-get install gcc-arm-none-eabi
    ```

3. Install [Ninja](https://installati.one/ubuntu/20.04/ninja-build/) by :

    ```
    $ sudo apt-get install ninja-build
    ``` 

### Setting git config 
1. Required packages are in requirements.txt. 

    ```
    $ git config --global user.name <your name> 
    $ git config --global user.email <your email>
    ```
We need to clone some packages from git, so need to set the configuration.

### Download Package
1. Create folder named `Lab2`

    ```
    $ mkdir Lab2
    $ cd Lab2
    ```
2. Downloaded files from github : 

    ```
    $ git clone https://github.com/ARM-software/ML-examples.git
    ```
    ```
    $ cd ML-examples/tflm-cmsisnn-mbed-image-recognition
    ```


### Setup
Since the package is for STM32F746NG, we need to do some modification on 'setup.sh' to download the right BSPs. Also, several header files need to include the support functions to STM32H747I.

1. You cna run modified '747setup.sh' to setup the building environment or write files on yourself.

   ```
   $ bash ./747setup.sh
   ```

### Modify files
- `main.cpp`          (in ./image_recognition)
- `display_util.cc`   (in ./image_recognition/stm32f747_discovery)
- `image_provider.cc` (in ./image_recognition/stm32f747_discovery)
1. `main.cpp` 

    ```
    (Line21) #include "mbed-os/targets/TARGET_STM/TARGET_STM32H7/STM32Cube_FW/STM32H7xx_HAL_Driver/stm32h7xx_hal.h"
    ```
2. `display_util.cc`

    ```
    (Line27-30) Change variable : hDcmiHandler ->hdcmi_discovery`
    (Line23)  #include  "BSP/Drivers/BSP/STM32H747I-Discovery/stm32h747i_discovery_lcd.h"
    ```
3. `image_provider.cc` 
    
    ```
    (Line21) #include "BSP/Drivers/BSP/STM32H747I-Discovery/stm32h747i_discovery_camera.h"
    ```

### Run Inference

1. In inference stage, we use the Mbed CLI2 which uses Ninja as a build system, and CMake to generate the build environment.

   ```
   $ bash ./test_performance.sh <SEED> DISCO_H747I GCC_ARM
   ```
The random seed could be any integer greater than 0. After building, the console would show the compiling details like this:

<p align="center">
    <img src=https://i.imgur.com/pDBBJrL.png>
</p>


### Copy the binary file onto board

This should result in a binary file `mbed-tflm-image-recognition.bin` at the following path:\
`cmake_build/DISCO_H747I/release/GCC_ARM/mbed-tflm-image-recognition.bin`
    
Copy the binary file onto the board, and download Tera-Term to see the performance. Inference stage contains three parts:

1. For every iteration, it will show the number of ticks used in each layer.
<p align="center">
    <img src=https://i.imgur.com/F7boGv8.png>
</p>
2. After passing through the whole model, it would compute a total number of ticks and time.
<p align="center">
    <img src=https://i.imgur.com/WawCR45.png>
</p>
3. After all 50 images are tested, it would show the accuracy of the model.
<p align="center">
    <img src=https://i.imgur.com/gUMKICs.png>
</p>




