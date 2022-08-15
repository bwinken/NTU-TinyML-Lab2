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

### Git Pack Download
Downloaded file would be stored in folder named `Lab2`

```
$ mkdir Lab2
$ cd Lab2
```
```
$ git clone https://github.com/ARM-software/ML-examples.git
```
```
$ cd ML-examples/tflm-cmsisnn-mbed-image-recognition![image](https://user-images.githubusercontent.com/61946472/184610428-0d3f5d29-8f8c-4791-87ab-bb769d478d96.png)
```


## Build on PC
With this command, some necessary libraries and tools will be downloaded. Then test file along with all of its dependencies will be built. Makefile has instructed the C++ compiler to build the code and create a binary, which it will then run. The result should be like this:
    
```
$ make -f tensorflow/lite/micro/tools/make/Makefile test_hello_world_test
```
![image](https://user-images.githubusercontent.com/61946472/184302778-aa919659-94e0-4ce2-9385-87acc595cee3.png)



## Deploy on STM32H747
Mbed requires source files to be structured in a certain way. The TensorFlow Lite for Microcontrollers Makefile knows how to do this for us, and can generate a directory suitable for Mbed. The building process should be like this:
```
$ make -f tensorflow/lite/micro/tools/make/Makefile TARGET=mbed OPTIMIZED_KERNEL_DIR=cmsis_nn generate_hello_world_mbed_project
```
<!--
![](https://i.imgur.com/tENEHNF.png)
-->
The directory below contains all of the example’s dependencies structured in the correct way for Mbed to be able to build it.
```
$ cd tensorflow/lite/micro/tools/make/gen/mbed_cortex-m4_default/prj/hello_world/mbed
```


### Setting up Mbed
To get started, use the following command to specify to Mbed that the current directory is the root of an Mbed project:

```
$ mbed config root .
```
Next, instruct Mbed to download the dependencies and prepare to build:

```
$ mbed deploy
```
<!--
### Modify Mbed Configuration

By default, Mbed will build the project using C++ 98. However, TensorFlow Lite requires C++ 11. Run the following Python snippet to modify the Mbed configuration files so that it uses C++ 11. You should put `modify.py` in `tensorflow/lite/micro/tools/make/gen/mbed_cortex-m4_default/prj/hello_world/mbed` and enter the command:

`$ python3 modify.py`
-->

### Modify header files

Replication arm_math.h and cmsis_gcc.h to correct folder.

```
$ cd ~/Lab1/tensorflow/
```

```
$ cp tensorflow/lite/micro/tools/make/downloads/cmsis/CMSIS/DSP/Include/arm_math.h  tensorflow/lite/micro/tools/make/gen/mbed_cortex m4_default/prj/hello_world/mbed/mbed-os/cmsis/TARGET_CORTEX_M/arm_math.h
```
```
$ cp tensorflow/lite/micro/tools/make/downloads/cmsis/CMSIS/Core/Include/cmsis_gcc.h  tensorflow/lite/micro/tools/make/gen/mbed_cortex-m4_default/prj/hello_world/mbed/mbed-os/cmsis/TARGET_CORTEX_M/cmsis_gcc.h
```


### Compile 
```
$ cd tensorflow/lite/micro/tools/make/gen/mbed_cortex-m4_default/prj/hello_world/mbed
```
1. To compile, run:
    ```
    $ mbed toolchain GCC_ARM
    ```
    ```
    $ mbed target DISCO_H747I
    ```
    ```
    $ mbed compile -c
    ```
2. This will produce a file named `mbed.bin` in `~/Lab1/tensorflow/tensorflow/lite/micro/tools/make/gen/mbed_cortex-m4_default/prj/hello_world/mbed/BUILD/DISCO_H747I/GCC_ARM/`. To flash it to the board, copy the file to the volume mounted as a USB drive. For instance:\
```
$ cp /BUILD/DISCO_H747I/GCC_ARM/mbed.bin /media/<USER>/<BOARD_NAME>/
```
3. Download Tera Term and Connect with STM32 to see the performance.

![image](https://user-images.githubusercontent.com/61946472/184304013-0ff3c1b5-54e8-48c6-80d4-f906618e994b.png)


## TODO: Cosine predictor
1. Find `train_hello_world_model.ipynb` in `~/Lab1/tensorflow/tensorflow/lite/micro/examples/hello_world/train`
2. modify code to cosine predictor, and replace model.cc
3. Replace `hello_world_test.cc`provide in our Lab1 Github: 
4. Run the following command to bulid:
```
$ make -f tensorflow/lite/micro/tools/make/Makefile test_hello_world_test
```
![image](https://user-images.githubusercontent.com/61946472/184305590-2018128a-3bb1-477e-8605-4c4e1c370fc0.png)


## (Optional) Control LCD on STM32H747I
1. Put `BSP` in
    `~/Lab1/tensorflow/tensorflow/lite/micro/tools/make/gen/mbed_cortex-m4_default/prj/hello_world/mbed/`
    
2. Put `LCD_DISCO_F746NG.cpp` and `LCD_DISCO_F746NG.h`in
    `~/Lab1/tensorflow/tensorflow/lite/micro/tools/make/gen/mbed_cortex-m4_default/prj/hello_world/mbed/tensorflow/lite/micro/examples/hello_world/`

3. Replace `output_handler.cc` in
     `~/Lab1/tensorflow/tensorflow/lite/micro/tools/make/gen/mbed_cortex-m4_default/prj/hello_world/mbed/tensorflow/lite/micro/examples/hello_world/output_handler.cc`


![DEMO (1)](https://user-images.githubusercontent.com/61946472/184305251-4078c8db-0e00-40ec-99aa-c12b2b47af31.gif)

