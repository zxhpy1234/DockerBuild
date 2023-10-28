FROM registry.baidubce.com/paddlepaddle/serving:0.9.0-devel

WORKDIR /home
RUN git clone https://github.com/PaddlePaddle/Serving
RUN cd Serving && git submodule update --init --recursive

RUN git clone https://github.com/PaddlePaddle/PaddleOCR.git

RUN wget -O opencv-3.4.7.tar.gz https://codeload.github.com/opencv/opencv/tar.gz/refs/tags/3.4.7
RUN tar xf opencv-3.4.7.tar.gz && rm -f  opencv-3.4.7.tar.gz


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
