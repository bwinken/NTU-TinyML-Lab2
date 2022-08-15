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
4. Required packages are in requirements.txt. 
```
$ git config --global user.name <your name> 
$ git config --global user.email <your email>
```
We need to clone some packages from git, so need to set the configuration.

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


### Setup
Since the package is for STM32F746NG, we need to do some modification on 'setup.sh' to download the right BSPs. Also, several header files need to include the support functions to STM32H747I.

You cna run modified '747setup.sh' to setup the building environment or write files on yourself.
```
$ bash ./747setup.sh
```


### Run Inference

In inference stage, we use the Mbed CLI2 which uses Ninja as a build system, and CMake to generate the build environment.
```
$ bash ./test_performance.sh <SEED> DISCO_H747I GCC_ARM
```
The random seed could be any integer greater than 0. After building, the console would show the compiling details like this:

![](https://i.imgur.com/pDBBJrL.png)







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

