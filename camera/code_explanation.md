### This is an explanation of the code used for capturing images with different shutter speeds and gains.

#### Dependcies:
* Python
* Opencv
* Pyueye
* skimage

#### Installation:
* Python on Windows:[Link](https://www.howtogeek.com/197947/how-to-install-python-on-windows/)
* Python on Ubuntu or MacOSX:[Link](https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-local-programming-environment-on-macos)
* Install pip: 
  * `curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py` 
  * `python get-pip.py --user`
  * Behind a proxy `python get-pip.py --proxy <your proxy here>`
* `pip install scikit-image`
* `pip install pyueye`
* OpenCV:[Link](https://docs.opencv.org/3.2.0/d5/de5/tutorial_py_setup_in_windows.html)

##### Import Libraries
~~~
from pyueye import ueye
import ctypes
import time
import numpy as np
import os
~~~

##### Create the arrays which will hold the values of gain and exposure time.
Here as we are using 30fps in the cars we are using 30 fps setting for the camera and thus the exposure time can be set to a max of 32ms.
~~~
gain_arr=np.arange(120,420,60)[::-1]
b=gain_arr
for i in range(len(gain_arr)-1):
    gain_arr=np.append(gain_arr,b)
    
exposure_arr=np.arange(3,33,7)
exposure_arr=np.repeat(exposure_arr,len(b))
~~~
Here we are using a stride of 7ms for the exposure time and stride of 60 for gains.

This will give arrays like this:
~~~
exposure_arr = array([ 3,  3,  3,  3,  3, 10, 10, 10, 10, 10, 17, 17, 17, 17, 17, 24, 24,24, 24, 24, 31, 31, 31, 31, 31])
gain_arr = array([360, 300, 240, 180, 120, 360, 300, 240, 180, 120, 360, 300, 240,180, 120, 360, 300, 240, 180, 120, 360, 300, 240, 180, 120])
~~~
##### Function to click the pictures in a loop
~~~
def clickPics(exposure_arr,gain_arr,k):
    start=time.time()
    hcam = ueye.HIDS(0)
    pccmem = ueye.c_mem_p()
    memID = ueye.c_int()
    hWnd = ctypes.c_voidp()
    ueye.is_InitCamera(hcam, hWnd)
    ueye.is_SetDisplayMode(hcam, 0)
    sensorinfo = ueye.SENSORINFO()
    ueye.is_GetSensorInfo(hcam, sensorinfo)
    ueye.is_AllocImageMem(hcam, sensorinfo.nMaxWidth, sensorinfo.nMaxHeight,24, pccmem, memID)
    ueye.is_SetImageMem(hcam, pccmem, memID)
    ueye.is_SetDisplayPos(hcam, 100, 100)

    newpath=r'../bitmapImages/test'+str(k)+'/'  # please enter the folder path here 
    if not os.path.exists(newpath):
        os.makedirs(newpath)

    for i in range(len(exposure_arr)):

        CamProp_Exposure = ueye.double()
        CamProp_Exposure.value = exposure_arr[i]     # exposure value in ms ; max=32 if FPS=30
        nRet = ueye.is_Exposure (hcam, ueye.IS_EXPOSURE_CMD_SET_EXPOSURE, CamProp_Exposure, 8)                        

        CamProp_ExposureReal = ueye.double()
        nRet = ueye.is_Exposure (hcam, ueye.IS_EXPOSURE_CMD_GET_EXPOSURE, CamProp_ExposureReal, 8)     

        CamProp_Gamma = ueye.int()
        CamProp_Gamma.value = gain_arr[i]
        ueye.is_Gamma(hcam, ueye.IS_GAMMA_CMD_SET, CamProp_Gamma, ueye.sizeof(CamProp_Gamma)) 
        nret = ueye.is_FreezeVideo(hcam, ueye.IS_WAIT)


        FileParams = ueye.IMAGE_FILE_PARAMS()
        FileParams.pwchFileName =newpath+ str(exposure_arr[i])+"_"+str(gain_arr[i])+".bmp"
        #print(newpath+ str(exposure_arr[i])+"_"+str(gain_arr[i])+".bmp")
        FileParams.nFileType = ueye.IS_IMG_BMP
        FileParams.ppcImageMem = None
        FileParams.pnImageID = None


        nret = ueye.is_ImageFile(hcam, ueye.IS_IMAGE_FILE_CMD_SAVE, FileParams, ueye.sizeof(FileParams))

    end=time.time()
    print(end-start)
    ueye.is_FreeImageMem(hcam, pccmem, memID)
    ueye.is_ExitCamera(hcam)
    
    
~~~    
#### This function will save images in a folder structure like this:

```
bitmapImages
│      
│
└───test0
│   │   3_120.bmp
│   │   10_120.bmp
|   |   ...
│   
└───test1
    │   3_120.bmp
    │   10_120.bmp
    |   ...
    ...
```

So each test[i] folder will contain images with almost the same scene taken with different parameters of the camera.

The naming of the files in each of the test[i] folder is `<exposure_time>_<gain>.bmp`.(`.bmp` stands for bitmap images)

Argument `k` in the above function `clickPics()` is the test number. (for k=0 it will store in `test0`, for k=23 it will store in `test23` folder)

##### The final loop for clicking pictures
~~~
ranges=100
for i in range(ranges):
  clickPics(exposure_arr,gain_arr,i)
~~~

This will click the images and save them in the folder.




