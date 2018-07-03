# Installing Tensorflow with a GPU on Windows.

## Prequisites (we will install all of these):
* NVIDIA GPU on you system.
* Anaconda with python(3.x)
* CUDA Tool Kit
* CuDNN

## Please follow the instructions in the same order as in this text.

## Installing CUDA Tool Kit:
* Download the latest version from [here](https://developer.nvidia.com/cuda-toolkit-archive):
* After it has been downloaded open the `.exe` file and follow the onsceen instructions.
* It will check for the system compatibility. 
* Then agree to the license agreement.
* Then click on Express-> Next.(This may take some time to install and your screen may flicker at the end of the installation,but don't worry)

## Adding Environment Variables.
* This has to be done before proceeding forward and is an **important** step.
* Go to `Start` and search for `Environment Variables`.
* Click on `edit environment variables`.
* Click on `Path` and then click on `edit`.

* Then add the following paths:
  - C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.0\bin
  - C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.0\libnvvp
  - C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.0\extras\CUPTI\libx64
  
If the `edit variables` line is just a single line then add `;` at the end of each of the 3 paths above and type them in a single line.

**NOTE:** Check the version number before adding the path.If you version is 9.2 please change it in the above paths by replacing the `v9.0` with `v9.2`

## Download CuDNN:
* Go to this [site](https://developer.nvidia.com/rdp/cudnn-download) and download the the CuDNN file for the version of CUDA you downloaded in the previous step.
* You may have to create a profile with NVIDIA first before downloading.
* Extract the zip file contents to someplace in the computer you wish.
* For eg: I extracted it at `C:\cudnn-9.0-windows7-x64-v7.1`
* Go to that folder->cuda->bin and copy the address(filepath) of that file.
  * for example:`C:\cudnn-9.0-windows7-x64-v7.1\cuda\bin`
* Add the path to the `environment variables` as done before.

## Update the GPU Driver:
* First check for the GPU in your system.
* Go to `Start`->`NVIDIA Control Panel`->`System Information`. In the Items list check for the GPU name. For me it is `Quadro M4000`.
* Now go to this [site](http://www.nvidia.de/Download/index.aspx). Fill in the details of the GPU name and download the the driver.
* Run the `.exe` file.

## Installing Python 3.5/3.6 in Anaconda:
* Go to this [site](https://www.anaconda.com/download/) and download the Anaconda for python v3.5 or 3.6.
*Check the `add python to your PATH` option during the install.

## Install Tensorflow:
* Open an Anaconda Prompt window and type the following command.

  `pip install --ignore-installed --upgrade tensorflow-gpu`
* If you are under a firewall,you may need to proxy.

  `pip install --ignore-installed --upgrade tensorflow-gpu --proxy <your proxy name>`

## Test your Installation:
* Open another Anaconda prompt shell.
* Type `python`.
* You may want to run the prompt as an **Admin** if you are getting `PermissionError: [WinError 5] Access is denied` as an error.
~~~
import tensorflow as tf
hello=tf.constant('Hello,Tensorflow')
sess=tf.Session()
print(sess.run(hello))
~~~

* You should get some information on the terminal about your GPU like this:
![Output1](https://github.com/nsidn98/General-Coding-/blob/master/images/Capture.PNG)

* The output should be `Hello,Tensorflow`

* If you type `sess = tf.Session(config=tf.ConfigProto(log_device_placement=True))`, you should get something like this:

![Output2](https://github.com/nsidn98/General-Coding-/blob/master/images/Capture2.PNG)



## Common Errors:
1:If you get this error:`ImportError: Could not find 'cudart64_90.dll'. TensorFlow requires that this DLL be installed in a directory that is named in your %PATH% environment variable. Download and install CUDA 9.0 from this URL: https://developer.nvidia.com/cuda-toolkit` Then check whether the file `cudart64_90.dll` is in `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.0\bin` and you have entered the path properly in the `environment variables`.

2:`PermissionError: [WinError 5] Access is denied`: Check if you are running the prompt as an **Admin**
