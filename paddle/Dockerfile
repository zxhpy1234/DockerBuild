FROM registry.baidubce.com/paddlepaddle/serving:0.9.0-devel

WORKDIR /home
RUN git clone https://github.com/PaddlePaddle/Serving
RUN cd Serving && git submodule update --init --recursive

RUN git clone https://github.com/PaddlePaddle/PaddleOCR.git

RUN wget -O opencv-3.4.7.tar.gz https://codeload.github.com/opencv/opencv/tar.gz/refs/tags/3.4.7
RUN tar xvf opencv-3.4.7.tar.gz && rm -f  opencv-3.4.7.tar.gz
RUN unzip Serving.zip && rm -f Serving.zip

# 安装opencv
RUN cd opencv-3.4.7 && rm -rf build && mkdir build && cd build
ENV root_path=/home/opencv-3.4.7
ENV install_path=${root_path}/opencv3
WORKDIR ${root_path}/build
RUN pwd
RUN cmake .. \
    -DCMAKE_INSTALL_PREFIX=${install_path} \
    -DCMAKE_BUILD_TYPE=Release \
    -DBUILD_SHARED_LIBS=OFF \
    -DWITH_IPP=OFF \
    -DBUILD_IPP_IW=OFF \
    -DWITH_LAPACK=OFF \
    -DWITH_EIGEN=OFF \
    -DCMAKE_INSTALL_LIBDIR=lib64 \
    -DWITH_ZLIB=ON \
    -DBUILD_ZLIB=ON \
    -DWITH_JPEG=ON \
    -DBUILD_JPEG=ON \
    -DWITH_PNG=ON \
    -DBUILD_PNG=ON \
    -DWITH_TIFF=ON \
    -DBUILD_TIFF=ON
RUN make -j
RUN make install

WORKDIR /home/Serving
# 首先需要下载Serving代码库, 把OCR文本检测预处理相关代码替换到Serving库中
RUN /bin/cp -rf /home/PaddleOCR/deploy/pdserving/general_detection_op.cpp core/general-server/op
# 环境变量准备
ENV PYTHON_INCLUDE_DIR=/usr/local/include/python3.9/
ENV PYTHON_LIBRARIES=/usr/local/lib/libpython3.9.so
ENV PYTHON_EXECUTABLE=/usr/local/bin/python3.9
RUN python3.9 -m pip install -r python/requirements.txt -i https://mirror.baidu.com/pypi/simple

ENV PATH=$PATH:$GOROOT/bin
RUN go env -w GO111MODULE=on
RUN go env -w GOPROXY=https://goproxy.cn,direct
RUN go install github.com/grpc-ecosystem/grpc-gateway/protoc-gen-grpc-gateway@v1.15.2
RUN go install github.com/grpc-ecosystem/grpc-gateway/protoc-gen-swagger@v1.15.2
RUN go install github.com/golang/protobuf/protoc-gen-go@v1.4.3
RUN go install google.golang.org/grpc@v1.33.0 || true
RUN go env -w GO111MODULE=auto
ENV OPENCV_DIR=/home/opencv-3.4.7/opencv3
# 编译paddle-serving-server
RUN mkdir build_server
WORKDIR /home/Serving/build_server
RUN cmake -DPYTHON_INCLUDE_DIR=$PYTHON_INCLUDE_DIR \
    -DPYTHON_LIBRARIES=$PYTHON_LIBRARIES \
    -DPYTHON_EXECUTABLE=$PYTHON_EXECUTABLE \
    -DSERVER=ON \
    -DOPENCV_DIR=${OPENCV_DIR} \
    -DWITH_OPENCV=ON \
    -DWITH_GPU=OFF ..
RUN make -j20

