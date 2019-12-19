## An Installation Guide for Caffe_train
This guide leads us to install [caffe_train](https://github.com/CMU-Perceptual-Computing-Lab/caffe_train). We are going to have a GPU accelerated Caffe on Ubunto 18.04 and we will use Python 3.6.
We are also considering some probable errors and their solutions. The main sources are [Caffe_train](https://github.com/CMU-Perceptual-Computing-Lab/caffe_train), [Caffe Installation](http://caffe.berkeleyvision.org/installation.html) and [Caffe Ubunto Installation](http://caffe.berkeleyvision.org/install_apt.html).
<br />

##### Prepare your Linux
Prepare your Linux like:
```bash
sudo apt-get install linux-headers-$(uname -r)
sudo apt update 
```	
<br />

##### Install GPU driver
Install the last GPU driver compatible with your system.
```bash
apt install nvidia-driver-390
```
<br />

##### Install CUDA and other Caffe dependencies
It's better to install CUDA and other Caffe dependencies with one command of:
```bash
sudo apt build-dep caffe-cuda
```
<br />

or you can install CUDA independently that's not recomended.  
Navigate to the directory containing CUDA Debian files that is /home/ghazal/workspace/deeplearningprerequisites for me.
```bash
aria2c "https://developer.nvidia.com/compute/cuda/10.1/Prod/local_installers/cuda-repo-ubuntu1804-10-1-local-10.1.168-418.67_1.0-1_amd64.deb" 
sudo dpkg -i cuda-repo-ubuntu1804-10-1-local-10.1.168-418.67_1.0-1_amd64.deb 
sudo apt-key add /var/cuda-repo-10-1-local-10.1.168-418.67/7fa2af80.pub 
sudo apt-get install cuda    
```

You could see [here](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).
<br />

##### Clone Caffe_train
Clone or download [Caffe_train](https://github.com/CMU-Perceptual-Computing-Lab/caffe_train) to an appropriate directory.
```bash
cd /home/ghazal/workspace
git clone git@github.com:CMU-Perceptual-Computing-Lab/caffe_train.git
```
<br />

##### Install pyCaffe requirements
Install needed packages like:
```bash
sudo -H pip3 install -r /home/ghazal/workspace/caffe_train/python/requirements.txt
```
<br />

##### Install cuDNN 
Install cuDNN for the best speed.  
Login to Nvidia and click download [here](https://developer.nvidia.com/rdp/cudnn-download) and dowmload the last version debian files.  
Navigate to the directory containing cuDNN Debian files that for me is /home/ghazal/workspace/deeplearningprerequisites.
```bash
sudo dpkg -i libcudnn7*
cp -r /usr/src/cudnn_samples_v7/ /home/ghazal/workspace/deeplearningprerequisites 
cd  /home/ghazal/workspace/deeplearningprerequisites/cudnn_samples_v7/mnistCUDNN
make clean && CUDA_PATH=/usr make
./mnistCUDNN
```

You could see [here](https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html).
<br />

##### Install Protobuf 
Get assured that the newest Protobuf is correctly installed.
```bash
apt install libprotobuf-dev
```
<br />

##### Make  
Make a copy of Makefile.config.example and save it as Makefile.config. Make needed changes to Makefile.config and then:
```bash
cd /home/ghazal/workspace/caffe_train
make clean make all
```
<br />

###### Probable Error A 
Caffe installation error with CUDNN V >= 6.0 that seems like:

> CXX src/caffe/data_transformer.cpp  
In file included from ./include/caffe/util/device_alternate.hpp:40:0,  
                 from ./include/caffe/common.hpp:19,  
                 from ./include/caffe/blob.hpp:8,  
                 from ./include/caffe/data_transformer.hpp:6,  
                 from src/caffe/data_transformer.cpp:8:  
./include/caffe/util/cudnn.hpp: In function ‘const char* cudnnGetErrorString(cudnnStatus_t)’:  
./include/caffe/util/cudnn.hpp:21:10: warning: enumeration value ‘CUDNN_STATUS_RUNTIME_PREREQUISITE_MISSING’ not handled in switch [-Wswitch]  
   switch (status) {  
          ^  
./include/caffe/util/cudnn.hpp: In function ‘void caffe::cudnn::setConvolutionDesc(cudnnConvolutionStruct**, cudnnTensorDescriptor_t, cudnnFilterDescriptor_t, int, int, int, int)’:  
./include/caffe/util/cudnn.hpp:113:70: error: too few arguments to function ‘cudnnStatus_t cudnnSetConvolution2dDescriptor(cudnnConvolutionDescriptor_t, int, int, int, int, int, int, cudnnConvolutionMode_t, cudnnDataType_t)’  
       pad_h, pad_w, stride_h, stride_w, 1, 1, CUDNN_CROSS_CORRELATION));  
                                                                      ^  
./include/caffe/util/cudnn.hpp:15:28: note: in definition of macro ‘CUDNN_CHECK’  
     cudnnStatus_t status = condition; \  
                            ^  
In file included from ./include/caffe/util/cudnn.hpp:5:0,  
                 from ./include/caffe/util/device_alternate.hpp:40,  
                 from ./include/caffe/common.hpp:19,  
                 from ./include/caffe/blob.hpp:8,  
                 from ./include/caffe/data_transformer.hpp:6,  
                 from src/caffe/data_transformer.cpp:8:  
/usr/local/cuda-8.0/include/cudnn.h:500:27: note: declared here  
 cudnnStatus_t CUDNNWINAPI cudnnSetConvolution2dDescriptor( cudnnConvolutionDescriptor_t convDesc,  
                           ^  
Makefile:585: recipe for target '.build_release/src/caffe/data_transformer.o' failed  
make: *** [.build_release/src/caffe/data_transformer.o] Error 1  

_**Solution:**  
Replace /home/ghazal/workspace/caffe_train/include/caffe/util/cudnn.hpp  
with [This](https://github.com/BVLC/caffe/blob/master/include/caffe/util/cudnn.hpp)._

_You could also see [here.](https://github.com/BVLC/caffe/issues/5793)_
<br />

###### Probable Error B 
Coudn't find hdf5.h. It seems like:

> CXX src/caffe/util/hdf5.cpp  
In file included from src/caffe/util/hdf5.cpp:1:0:  
./include/caffe/util/hdf5.hpp:6:18: fatal error: hdf5.h: No such file or directory  
#include "hdf5.h"  
^  
compilation terminated.  
Makefile:575: recipe for target '.build_release/src/caffe/util/hdf5.o' failed  

_**Solution:**  
Ensure that the packages are installed._
```bash
sudo apt-get install libhdf5-serial-dev
sudo apt-get install libhdf5-dev
```

_Find it's path._
```bash
find /usr -iname "*hdf5.h*"
```

_That would be something like:  
/usr/include/hdf5/serial/hdf5.h_

_Finally add its path to Makefile.config as below:   
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial_

_You could also see [here](https://github.com/BVLC/caffe/issues/4808)._
<br />

###### Probable Error C
It couldn't find opencv_contrib. The error seems like:

> fatal error: opencv2/contrib/contrib.hpp: No such file or directory  
include <opencv2/contrib/contrib.hpp>

_**Solution:**_  
- _In Makefile,  
merge LIBRARIES += opencv_core opencv_highgui opencv_imgproc opencv_contrib  
with  LIBRARIES += opencv_core opencv_highgui opencv_imgproc_  
- _From ./src/caffe/cpm_data_transformer.cpp  
remove the include header of include <opencv2/contrib/contrib.hpp>_  

_The other solution is to Uninstall OpenCV, clone OpenCv and opencv_contrib from [here](https://docs.opencv.org/master/d7/d9f/tutorial_linux_install.html) and rebuild it.  
This way is not recommended._

 _You could also see [here](https://github.com/CMU-Perceptual-Computing-Lab/caffe_train/issues/6)._
<br />

###### Probable Error D
If it couldn't find BLAS related libraries like below:

> /usr/bin/ld: cannot find -lcblas  
/usr/bin/ld: cannot find -latlas  
collect2: error: ld returned 1 exit status  
make: *** [.build_release/lib/libcaffe.so] error 1  

_**Solution:**  
Install ATLAS BLAS library as:_
```bash
sudo apt-get install libatlas-base-dev
```

_You could also see [here](http://caffe.berkeleyvision.org/install_apt.html)._
<br />

###### Probable Error E
It couldn't find lhdf5 related files like:

> LD -o .build_release/lib/libcaffe.so.1.0.0-rc3  
/usr/bin/ld: cannot find -lhdf5_hl  
/usr/bin/ld: cannot find -lhdf5  
collect2: error: ld returned 1 exit status  
> Makefile:563: recipe for target '.build_release/lib/libcaffe.so.1.0.0-rc3' failed  
make: * [.build_release/lib/libcaffe.so.1.0.0-rc3] Error 1

_**Solution:**  
Complete the path in your Makefile.config file like:  
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial  
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial_

_You could also see [here](https://github.com/BVLC/caffe/pull/3186/files)._
<br />

###### Probable Error F
Couldn't find imgproc from OpenCv.

> CXX/LD -o .build_release/examples/cpp_classification/classification.bin  
/usr/bin/ld: .build_release/examples/cpp_classification/classification.o: undefined reference to symbol '_ZN2cv8cvtColorERKNS_11_InputArrayERKNS_12_OutputArrayEii'  
//usr/lib/x86_64-linux-gnu/libopencv_imgproc.so.3.2: error adding symbols: DSO missing from command line  
collect2: error: ld returned 1 exit status  
Makefile:621: recipe for target '.build_release/examples/cpp_classification/classification.bin' failed  
make: * [.build_release/examples/cpp_classification/classification.bin] Error 1

_**Solution:**  
In Makefile,  
merge LIBRARIES += opencv_core opencv_highgui  
with  LIBRARIES += opencv_core opencv_highgui opencv_imgproc_

_You could also see [here](https://github.com/BVLC/caffe/pull/3186/files)._
<br />

##### Make test
```bash
make test
```
<br />

##### Make Runtime
```bash
make runtest
```
<br />

###### Probable Error G
If you had errors with leveldb while runtime as:

> [----------] 1 test from LayerFactoryTest/3, where TypeParam = caffe::GPUDevice<double>  
[ RUN      ] LayerFactoryTest/3.TestCreateLayer   
F0821 14:27:06.187842 29116 db_leveldb.cpp:16] Check failed: status.ok() Failed to open leveldb  
IO error: /LOCK: Permission denied  
* Check failure stack trace: *  
    @     0x7f9d59c8f0cd  google::LogMessage::Fail()  
    @     0x7f9d59c90f33  google::LogMessage::SendToLog()  
    @     0x7f9d59c8ec28  google::LogMessage::Flush()  
    @     0x7f9d59c91999  google::LogMessageFatal::~LogMessageFatal()  
    @     0x7f9d571fd0d8  caffe::db::LevelDB::Open()  
    @     0x7f9d5734bca7  caffe::DataReader::Body::InternalThreadEntry()  
    @     0x7f9d57345415  caffe::InternalThread::entry()  
    @     0x7f9d580dabcd  (unknown)  
    @     0x7f9d568596db  start_thread  
    @     0x7f9d5658288f  clone  
Makefile:523: recipe for target 'runtest' failed  
make: * [runtest] Aborted (core dumped)  

_**Solution:**_  
Thats not important for this project just abandon it._

_You could also see [here](https://github.com/ZheC/Realtime_Multi-Person_Pose_Estimation/issues/31)._
<br />

##### Make pyCaffe and add the path
```bash
make pycaffe
```

Befor importing Caffe, add the module directory to your $PYTHONPATH by export PYTHONPATH=/home/ghazal/workspace/caffe_train/python:$PYTHONPATH  
but it is so better to run:
```bash
python3.6
import sys
sys.path.append('/home/ghazal/workspace/caffe_train/python/')
```
<br />

###### Probable Error H
If you had problem importing caffe because of dateutil:

_**Solution:**  
Update it._
```bash
sudo -H pip3 install python-dateutil -U --no-cache
```
_You can also check it's version by:_
```bash
pip3 freeze | grep date
```
<br />
<br />


**Concragulation! You can import Caffe now ...**
