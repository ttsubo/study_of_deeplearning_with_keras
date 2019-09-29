FROM nvidia/cuda:10.0-devel-ubuntu18.04
  
MAINTAINER Toshiki Tsuboi <t.tsubo2000@gmail.com>

RUN apt update \
 && apt install -y \
      git-core \
      build-essential \
      python-dev \
      python-openssl \
      libssl-dev \
      libbz2-dev \
      libffi-dev \
      libsqlite3-dev \
      libreadline-dev \
      zlib1g-dev \
      libsm6 \
      libxext6 \
      libxrender-dev \
      libblas-dev \
      curl \
      vim \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# install pyenv
ENV HOME /root
ENV PYENV_ROOT $HOME/.pyenv
ENV PATH $PYENV_ROOT/bin:$PATH
RUN git clone https://github.com/pyenv/pyenv.git $HOME/.pyenv
RUN echo 'eval "$(pyenv init -)"' >> $HOME/.bashrc \
 && eval "$(pyenv init -)"

# install python using pyenv
RUN apt update \
 && apt install -y libssl1.0-dev \
 && pyenv install anaconda3-5.3.1 \
 && pyenv global anaconda3-5.3.1

# install pip
WORKDIR /
ADD https://bootstrap.pypa.io/get-pip.py /
RUN python get-pip.py \
 && rm get-pip.py

# install python package
WORKDIR /root
COPY requirements.txt /root
RUN /root/.pyenv/shims/pip install -r requirements.txt

# install cuDNN
COPY download /root/debian_packages
RUN cd /root/debian_packages \
 && dpkg -i \
  libcudnn7_7.6.2.24-1+cuda10.0_amd64.deb \
  libcudnn7-dev_7.6.2.24-1+cuda10.0_amd64.deb \
  libcudnn7-doc_7.6.2.24-1+cuda10.0_amd64.deb

#setup jupyter
RUN /root/.pyenv/shims/jupyter notebook --generate-config \
 && sed -i -e "s/#c.NotebookApp.ip = 'localhost'/c.NotebookApp.ip = '0.0.0.0'/" /root/.jupyter/jupyter_notebook_config.py \
 && sed -i -e "s/#c.NotebookApp.allow_remote_access = False/c.NotebookApp.allow_remote_access = True/" /root/.jupyter/jupyter_notebook_config.py \
 && sed -i -e "s/#c.NotebookApp.token = '<generated>'/c.NotebookApp.token = ''/" /root/.jupyter/jupyter_notebook_config.py

EXPOSE 8888
ENTRYPOINT ["sh", "-c", "/root/.pyenv/shims/jupyter notebook --allow-root"]
