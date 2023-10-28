FROM registry.baidubce.com/paddlepaddle/serving:0.9.0-devel

WORKDIR /home
RUN git clone https://github.com/PaddlePaddle/Serving
RUN cd Serving && git submodule update --init --recursive

RUN git clone https://github.com/PaddlePaddle/PaddleOCR.git

RUN wget -O opencv-3.4.7.tar.gz https://codeload.github.com/opencv/opencv/tar.gz/refs/tags/3.4.7
RUN tar xf opencv-3.4.7.tar.gz && rm -f  opencv-3.4.7.tar.gz
