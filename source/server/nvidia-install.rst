===================================
Installing NVIDIA Drivers and CUDA
===================================

Before installing NVIDIA drivers we check if the OS can "see" any connected board. In order to do so, we run:

.. code-block:: bash

    $ lspci | grep -i nvidia

    02:00.0 VGA compatible controller: NVIDIA Corporation GP102 [TITAN X] (rev a1)
    02:00.1 Audio device: NVIDIA Corporation GP102 HDMI Audio Controller (rev a1)
    03:00.0 VGA compatible controller: NVIDIA Corporation GM107GL [Quadro K2200] (rev a2)
    03:00.1 Audio device: NVIDIA Corporation Device 0fbc (rev a1) 


More information can be seen running `` command, as:

.. code-block:: bash

    $ lshw -numeric -C display

    *-display UNCLAIMED     
       description: VGA compatible controller
       product: GP102 [TITAN X] [10DE:1B00]
       vendor: NVIDIA Corporation [10DE]
       physical id: 0
       bus info: pci@0000:02:00.0
       version: a1
       width: 64 bits
       clock: 33MHz
       capabilities: vga_controller bus_master cap_list
       configuration: latency=0
       resources: memory:f4000000-f4ffffff memory:c0000000-cfffffff memory:d0000000-d1ffffff ioport:2000(size=128) memory:f5080000-f50fffff
    *-display UNCLAIMED
       description: VGA compatible controller
       product: GM107GL [Quadro K2200] [10DE:13BA]
       vendor: NVIDIA Corporation [10DE]
       physical id: 0
       bus info: pci@0000:03:00.0
       version: a2
       width: 64 bits
       clock: 33MHz
       capabilities: vga_controller cap_list
       configuration: latency=0
       resources: memory:f2000000-f2ffffff memory:e0000000-efffffff memory:f0000000-f1ffffff ioport:1000(size=128) memory:f3080000-f30fffff


We also check whether exists any NVIDIA driver previously installed by `dpkg` or `apt-get`. To check it, we run:

.. code-block:: bash

    $ dpkg -l | grep nvidia

    ii  nvidia-375                                                  384.130-0ubuntu0.14.04.2                               amd64        Transitional package for nvidia-384
    ii  nvidia-375-dev                                              384.130-0ubuntu0.14.04.2                               amd64        Transitional package for nvidia-384-dev
    ii  nvidia-384                                                  384.130-0ubuntu0.14.04.2                               amd64        NVIDIA binary driver - version 384.130
    ii  nvidia-384-dev                                              384.130-0ubuntu0.14.04.2                               amd64        NVIDIA binary Xorg driver development files
    ii  nvidia-modprobe                                             375.26-0ubuntu1                                        amd64        Load the NVIDIA kernel driver and create device files
    ii  nvidia-opencl-icd-375                                       384.130-0ubuntu0.14.04.2                               amd64        Transitional package for nvidia-opencl-icd-384
    ii  nvidia-opencl-icd-384                                       384.130-0ubuntu0.14.04.2                               amd64        NVIDIA OpenCL ICD
    ii  nvidia-prime                                                0.6.2.1                                                amd64        Tools to enable NVIDIA's Prime
    ii  nvidia-settings                                             418.56-0ubuntu0~gpu14.04.1                             amd64        Tool for configuring the NVIDIA graphics driver


Removing Old Versions
----------------------

As we can see, there are several old drivers installed that may conflict with the new version. Thus, we have to remove them before installing the new version. To uninstall them we run:

.. code-block:: bash

    $ sudo apt-get --purge remove nvidia-*
    $ sudo apt-get --purge remove cuda*
    $ sudo apt autoremove
    $ sudo reboot


These commands will uninstall any NVIDIA driver or CUDA that was previously installed in the computer. Now that we do not have any driver in our computer, we check the available drivers to the NVIDIA board. To do so, we can install and run `ubuntu-drivers` as:

.. code-block:: bash

    $ ubuntu-drivers devices | grep nv

    driver   : nvidia-410 - third-party free
    driver   : nvidia-418 - third-party free recommended
    driver   : nvidia-396 - third-party free
    driver   : nvidia-390 - third-party free
    driver   : nvidia-340 - distro non-free
    driver   : nvidia-384 - distro non-free
    driver   : nvidia-415 - third-party free


Installing NVIDIA Driver
-------------------------

As `nvidia-418` is the latest and recommended version, we download the file `NVIDIA-Linux-x86_64-418.56.run` from `NVIDIA page <https://www.nvidia.com/Download/index.aspx?lang=en-us>`_ and install it. Before installing NVIDIA driver, we have to install Gcc version 4.8.4 to compile the driver. We download the file `gcc-4.8.4.tar.bz2` from `Gcc site <https://ftp.gnu.org/gnu/gcc/gcc-4.8.4/>`_ to our machine and run the following commands:

.. code-block:: bash
    
    $ tar -xvjf gcc-4.8.4.tar.bz2
    $ cd gcc-4.8.4/
    $ mkdir build 
    $ cd build/
    $ ../configure --prefix=/home/master/installs
    $ make
    $ make install


Having Gcc compiled, you have to set the path of `cc` to the `gcc` file inside the `bin/` folder. To do so, you can do:

.. code-block:: bash
    
    $ sudo ln -sf /home/master/installs/bin/gcc /etc/alternatives/cc
    

Do not forget to get back the correct link to the `cc` after compiling NVIDIA driver. The original link of `cc` was set to `/usr/bin/gcc-5`. NVIDIA driver also need Linux headers to be updated before compiling it. In order to do so, we run:

.. code-block:: bash
    
    $ sudo apt-get install linux-headers-$(uname -r) 


Now that we have the version 4.8.4 of Gcc set and the Linux headers updated, we can stop X and install the NVIDIA drive by running:

.. code-block:: bash
    
    $ sudo service lightdm stop
    $ chmod +x NVIDIA-Linux-x86_64-418.56.run
    $ sudo ./NVIDIA-Linux-x86_64-418.56.run


These commands will install the NVIDIA drive `nvidia-418` in the SO. After installing, we can change the link of `cc` back to the original Gcc and reboot the computer.

.. code-block:: bash
    
    $ sudo ln -sf /usr/bin/gcc /etc/alternatives/cc
    $ sudo reboot


Installing CUDA Toolkit
------------------------

In order to install CUDA Toolkit, we first download the `deb (network)` package from the `NVIDIA site <https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1404&target_type=debnetwork>`_. Having downloaded the package, we perform the following commands to install it:

.. code-block:: bash
    
    $ sudo dpkg -i cuda-repo-ubuntu1404_10.1.105-1_amd64.deb
    $ sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1404/x86_64/7fa2af80.pub
    $ sudo apt-get update
    $ sudo apt-get install cuda-toolkit-10-0


These commands will install CUDA version 10.0-0 into `/usr/local/cuda-10.0` and set the link of `/usr/local/cuda` to this folder. As some tolls are developed for CUDA version 8.0, we also install it on the computer. To do so, we run:

.. code-block:: bash
    
    $ sudo apt-get install cuda-toolkit-8-0


Thus, CUDA version 8.0 is installed into `/usr/local/cuda-8.0` and the link of `/usr/local/cuda` is set to the version 8.0. To test the installation process, we can run `nvcc` and `nvidia-smi` to check if they work, as:

.. code-block:: bash
    
    $ nvcc --version

    nvcc: NVIDIA (R) Cuda compiler driver
    Copyright (c) 2005-2016 NVIDIA Corporation
    Built on Tue_Jan_10_13:22:03_CST_2017
    Cuda compilation tools, release 8.0, V8.0.61

    $ nvidia-smi

    Mon Apr 15 16:03:27 2019       
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 418.56       Driver Version: 418.56       CUDA Version: 10.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  TITAN X (Pascal)    Off  | 00000000:02:00.0 Off |                  N/A |
    | 23%   39C    P0    54W / 250W |      0MiB / 12194MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    |   1  Quadro K2200        Off  | 00000000:03:00.0 Off |                  N/A |
    | 36%   41C    P0     1W /  39W |      0MiB /  4043MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
                                                                                   
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID   Type   Process name                             Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+


After installing CUDA, we have to install CuDNN library. To do so, we first download the lastest version from `CuDNN page <https://developer.nvidia.com/cudnn>`_. Having downloaded the `` file, we extract it and copy files to CUDA folder, as:

.. code-block:: bash
    
    $ tar -xzvf cudnn-8.0-linux-x64-v7.tgz
    $ cd cuda/
    $ sudo cp include/cudnn.h /usr/local/cuda-8.0/include
    $ sudo cp lib64/libcudnn* /usr/local/cuda-8.0/lib64
    $ sudo chmod a+r /usr/local/cuda-8.0/include/cudnn.h /usr/local/cuda-8.0/lib64/libcudnn*

    $ tar -xzvf cudnn-10.0-linux-x64-v7.5.0.56.tgz
    $ cd cuda/
    $ sudo cp include/cudnn.h /usr/local/cuda-10.0/include
    $ sudo cp lib64/libcudnn* /usr/local/cuda-10.0/lib64
    $ sudo chmod a+r /usr/local/cuda-10.0/include/cudnn.h /usr/local/cuda-10.0/lib64/libcudnn*

Finally, we can export CUDA_HOME, PATH, and LD_LIBRARY_PATH in .bashrc to always access CUDA when logged, as:

.. code-block:: bash
    
    $ vi ~/.bashrc

    export CUDA_8=/usr/local/cuda-8.0
    export CUDA_10=/usr/local/cuda-10.0
    export CUDA_HOME=$CUDA_8
    export PATH=$CUDA_HOME/bin:$PATH
    export LD_LIBRARY_PATH=$CUDA_HOME/lib64:$LD_LIBRARY_PATH
