ARG CUDA_VERSION=10.0

FROM nvidia/cudagl:$CUDA_VERSION-devel-ubuntu18.04

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-key adv --keyserver keys.gnupg.net --recv-key F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE \
   || apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE
   
#RUN apt-get install -y gcc-8 g++-8 

RUN apt-get update && apt-get install -y --no-install-recommends \
   software-properties-common \
   && add-apt-repository "deb http://librealsense.intel.com/Debian/apt-repo bionic main" -u \
   && apt-get update && apt-get install -y --no-install-recommends \
   cmake \
   build-essential \
    cmake-qt-gui \
    freeglut3-dev \
    git \
    unzip \
    freeglut3-dev \
    git \
    libeigen3-dev \
    libcxx* \
    libjpeg-dev \
    python3-pip \
    libopencv-dev \
    libgl1-mesa-dev \
    libglew-dev \
    librealsense2-dev \ 
    libboost-filesystem-dev \
    libboost-system-dev \
    librealsense2-dkms \
    librealsense2-utils \
    libsuitesparse-dev \
    libudev-dev \
    libusb-1.0-0-dev \
    libxkbcommon-x11-dev \
    openjdk-8-jdk \
    libboost-dev \
    python-setuptools \
    python3-setuptools \
    zlib1g-dev \
    wget \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /opt


RUN wget -O boost_1_62_0.tar.bz2 https://sourceforge.net/projects/boost/files/boost/1.62.0/boost_1_62_0.tar.bz2/download \
    && tar -xjvf boost_1_62_0.tar.bz2  \
    && rm boost_1_62_0.tar.bz2 \
    && cd boost_1_62_0 \
    && mkdir -p ../boost \
    && ./bootstrap.sh --prefix=../boost \
    && ./b2 --prefix=../boost --with-filesystem install > /dev/null \
    && cd .. \
    && rm -rf boost_1_62_0 \
    && BOOST_ROOT=$(pwd)/boost 



RUN pip3 install --upgrade pip

RUN pip3 install setuptools tensorflow-gpu==1.8.0 scikit-image keras IPython h5py cython imgaug opencv-python pytoml


RUN git clone https://github.com/martinruenz/densecrf.git \
   && cd densecrf \
   && git pull \
   && mkdir build && cd build \
   && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="${CMAKE_CXX_FLAGS} -fPIC" .. \
   && make -j8 


RUN git clone https://github.com/carlren/gSLICr.git \
   && cd gSLICr \
   && mkdir build && cd build \
   && gSLICr_Root=$(pwd) \
   && cmake -DCMAKE_BUILD_TYPE=Release -DCUDA_HOST_COMPILER=/usr/bin/gcc -DCMAKE_CXX_FLAGS="${CMAKE_CXX_FLAGS} -D_FORCE_INLINES" .. \
   && make -j8 


RUN git clone https://github.com/aiqu/Pangolin.git \
   && cd Pangolin \
   && mkdir build && cd build \
   && cmake -DAVFORMAT_INCLUDE_DIR="" -DCPP11_NO_BOOST=ON .. \
   && cmake --build . \
   && Pangolin_DIR=$(pwd) 



RUN git clone https://github.com/occipital/OpenNI2.git \
   && cd OpenNI2 \
   && make -j8


RUN ln -sf /usr/include/eigen3/Eigen /usr/include/Eigen \
   && ln -sf /usr/include/eigen3/unsupported /usr/include/unsupported


#RUN echo($"gcc --version")
#RUN apt-get install -y gcc-8 g++-8

#RUN apt-get update ; apt-get upgrade -y ; apt-get install -y  g++-8 && \
#  update-alternatives /usr/bin/g++ g++ /usr/bin/g++-8 && \
#  update-alternatives --config gcc

RUN git clone --depth=1 --recurse-submodules https://github.com/martinruenz/freetype-gl-cpp.git \
  && cd freetype-gl-cpp \
  && mkdir -p build && cd build \
  && cmake -DBUILD_EXAMPLES=OFF -DCMAKE_INSTALL_PREFIX="`pwd`/../install" -DCMAKE_BUILD_TYPE=Release .. \
  && make -j8 \
  && make install   

RUN yes | apt-get install -f python3*

RUN pip install git+https://github.com/waleedka/cocoapi.git#egg=pycocotools&subdirectory=PythonAPI

RUN pip3 install numpy

RUN git clone --depth=1 https://github.com/matterport/Mask_RCNN.git \
  && git clone --depth=1 --branch v2.4.0 https://github.com/ToruNiina/toml11.git \
  #&& git clone --depth=1 https://github.com/Suraj520/cocoapi.git \
  #&& cd cocoapi/PythonAPI \
  #&& make \
  #&& make install  \    
  #&& cd ../../  \
  && cd Mask_RCNN \
  && mkdir -p data \
  && cd data \
  && wget --no-clobber https://github.com/matterport/Mask_RCNN/releases/download/v1.0/mask_rcnn_coco.h5 

#RUN ln -s `python3 -c "import numpy as np; print(np.__path__[0])"`/core/include/numpy Core/Segmentation/MaskRCNN || true

RUN git clone https://github.com/Suraj520/maskfusion.git \
   && cd maskfusion \
   && mkdir build && cd build \
   && cmake -DPangolin_DIR="${Pangolin_DIR}" -DWITH_FREENECT2=OFF  -DCUDA_HOST_COMPILER=/usr/bin/gcc .. \
   && make -j8 

#-DOpenCV_DIR="${OpenCV_DIR}"

ENV PATH $PATH:/opt/maskfusion/GUI/build/

#set(OpenCV_DIR /usr/local/opencv3.1/OpenCV/*)
WORKDIR /workspace 
#This repository contains Co-Fusion, a dense SLAM system that takes a live stream of RGB-D images as input and segments the scene into different objects.

