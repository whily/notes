Notes for setting up PyTorch under Ubuntu 17.04 running on XPS 9550.

### CUDA 9.0

Mainly based on the guide from http://docs.nvidia.com/cuda/cuda-quick-start-guide/index.html

1. Download CUDA from http://developer.nvidia.com/cuda-downloads

Select Ubuntu 17.04 and select `deb (network)` as Installation Type.

Download the based installer, and follow the steps below to install
CUDA (copied from the CUDA download page):

    sudo dpkg -i cuda-repo-ubuntu1704_9.0.176-1_amd64.deb
    sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1704/x86_64/7fa2af80.pub
    sudo apt-get update
    sudo apt-get install cuda

2. Reboot. Make sure in `NVIDIA X Server Settings`, select `NVIDIA (Performance Mode)`

3. Set up environment variables (I use zsh, so modify `~/.zshrc`)

    export PATH=/usr/local/cuda-9.0/bin${PATH:+:${PATH}}
    export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64\
                         ${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

4. Install a writable copy of the samples then build and run the nbody sample:

    cuda-install-samples-9.0.sh ~
    cd ~/NVIDIA_CUDA-9.0_Samples/5_Simulations/nbody
    make
    ./nbody

### cuDNN 7.0

Mainly based on the guide from http://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html

1. Download the .deb files (Runtime Library, Developer Library, Code
   Samples and User Guide) for Ubuntu 16.04.

2. Install.

    sudo dpkg -i libcudnn7_7.0.3.11-1+cuda9.0_amd64.deb
    sudo dpkg -i libcudnn7-dev_7.0.3.11-1+cuda9.0_amd64.deb
    sudo dpkg -i libcudnn7-doc_7.0.3.11-1+cuda9.0_amd64.deb

3. Verify

    $cp -r /usr/src/cudnn_samples_v7/ $HOME
    $ cd  $HOME/cudnn_samples_v7/mnistCUDNN
    $make clean && make
    $ ./mnistCUDNN

### Anaconda

Mainly based on the guide from
https://docs.anaconda.com/anaconda/install/linux

In China, use the mirror in
https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/

1. Installation

Download Anaconda https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-5.0.0-Linux-x86_64.sh

Install by typing

    bash ~/Downloads/Anaconda3-5.0.0-Linux-x86_64.sh

Accept the license and default location.

Do not accept the PATH variable update (as it is for .bashrc). Update
~/.zshrc by adding the following line (replacing `usr` with your user name):

    export PATH=/home/usr/anaconda3/bin:$PATH

Run `anaconda-navigator` to verify the installation.

2. Post installation

Use the mirror in Tsinghua.

    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
    conda config --set show_channel_urls yes

Test the mirror by running `conda install numpy`.


### PyTorch

    conda install pytorch torchvision cuda80 -c soumith
