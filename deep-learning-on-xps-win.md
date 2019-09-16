Notes for setting up Keras and Tensorflow under Windows 10 running on XPS 9550.

### Anaconda

1. Installation

Download and install latest 64 bit Anaconda. During installation, accept the defaults.

2. Post installation

Run `Anaconda Prompt`. If behind firewall, one may set environment variable like `set https_proxy=https://host:port`. Then run the following inside Anaconda Prompt. Note that for simplicity, no environments are setup.

    conda update --all

    # Install packages for VS Code Python plugin.
    conda install autopep8 
    
    # Install Keras and Tensor flow. For CPU based deep learning, install tensorflow instead.
    conda install keras tensorflow-gpu

### Configuration in Visual Studio Code

Install `Python` extension. Select Anaconda from list of Python interpreters by typing `Ctrl+Shift+P` then `Python: Select Interpreter`. 

If meeting problems of running Anaconda `activate` when launching Python interpreter, one may launch VS Code by typing `code` inside Anaconda Prompt, or launch VS Code directly within Anaconda Navigator.