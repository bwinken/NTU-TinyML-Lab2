# LAB 2: Deploy NN onto STM32
## Description
In LAB2, we deploy a pretrained image recognition model onto STM32H747. The input of the system is binarized CIFAR-10 image, and the model would predict the label of the image. 

## Environment Setup
- mbed-tools==7.55.0
- ninja==1.10.0
- gcc-arm-none-eabi==9.2.1

1. Install [mbed-tools](https://os.mbed.com/docs/mbed-os/v6.15/build-tools/install-and-set-up.html) by pip :
   
   ```
    pip install mbed-tools
    ```

2. Install [GCC_ARM](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/downloads) by :

    ```
    sudo apt-get install gcc-arm-none-eabi
    ```

3. Install [Ninja](https://installati.one/ubuntu/20.04/ninja-build/) by :

    ```
    sudo apt-get install ninja-build
    ``` 

### Setting git config 
1. Required packages are in requirements.txt. 

    ```
    git config --global user.name <your name> 
    git config --global user.email <your email>
    ```
We need to clone some packages from git, so need to set the configuration.

### Download Package
1. Downloaded files from github : 

    ```
    git clone https://github.com/bwinken/NTU-TinyML-Lab2.git
    ```
    ```
    cd NTU-TinyML-Lab2/LAB2
    ```


### Setup
Since the package is for STM32F746NG, we need to do some modification on 'setup.sh' to download the right BSPs. Also, several header files need to include the support functions to STM32H747I.
1. Add executable permission
   ```
   chmod u+x clean.sh
   ```
   ```
   chmod u+x generate_cmake_files.sh
   ```
2. You cna run copy `747setup.sh` in `NTU-TinyML-Lab2/LAB2` to setup the building environment or write `setup.sh` file on yourself.

   ```
   bash ./747setup.sh
   ```

### Modify files
- `main.cpp`          (in ./image_recognition)
- `display_util.cc`   (in ./image_recognition/stm32f746_discovery)
- `image_provider.cc` (in ./image_recognition/stm32f746_discovery)
1. `main.cpp (Line21)` 
    ```
    #include "mbed-os/targets/TARGET_STM/TARGET_STM32H7/STM32Cube_FW/STM32H7xx_HAL_Driver/stm32h7xx_hal.h"
    ```
2. `display_util.cc (Line23) `
    ```
    #include  "BSP/Drivers/BSP/STM32H747I-Discovery/stm32h747i_discovery_lcd.h"
    ```
3. `display_util.cc (Line27-30) `
   ```
   Change variable : hDcmiHandler ->hdcmi_discovery`
   ```
4. `image_provider.cc (Line21)` 
    
    ```
    #include "BSP/Drivers/BSP/STM32H747I-Discovery/stm32h747i_discovery_camera.h"
    ```

### Run Inference
1. In inference stage, we use the Mbed CLI2 which uses Ninja as a build system, and CMake to generate the build environment. To run a test, execute
`$ ./test_performance.sh <SEED> <TARGET> <TOOLCHAIN>`,
where the `<SEED>` is any integer >= 0, `<TARGET>` is an mbed-enabled device such as `DISCO_H747I`, and `<TOOLCHAIN>` is `GCC_ARM` or `ARM`. Running the command with the same seed will test the same set of images.

   ```
   bash ./test_performance.sh 0 DISCO_H747I GCC_ARM
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
    <img src=https://user-images.githubusercontent.com/61946472/191679155-3b0eee9d-a531-4f59-828c-fab1c67160dc.png>
 </p>
2. After passing through the whole model, it would compute a total number of ticks and time.
<p align="center">
    <img src=https://user-images.githubusercontent.com/61946472/191679217-60a5067e-880d-4d70-b8c0-1ab549414911.png>
</p>
3. After all 50 images are tested, it would show the accuracy of the model.
<p align="center">
    <img src=https://user-images.githubusercontent.com/61946472/191679783-d5eca440-3839-475a-8af5-5db969606514.png>
</p>
