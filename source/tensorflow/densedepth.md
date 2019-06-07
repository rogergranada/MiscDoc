======================
Installing DenseDepth
======================

This file contains all steps to install the `DenseDepth <https://github.com/ialhashim/DenseDepth>`_ tool. The process starts by configuring paths that will be helpful to our scripts. 

.. code-block:: bash

   # Cuda 8.0
   export CUDA_HOME=/usr/local/cuda-10.0
   export LD_LIBRARY_PATH=$CUDA_HOME/lib64:$LD_LIBRARY_PATH
   export PATH=$CUDA_HOME/bin:$PATH

   # DeepVariationStructuredRL folder
   export ENV_FOLDER=/home/roger/Workspace/github/DenseDepth
   export ANACONDA_HOME=/opt/anaconda2/envs/densedepth-env
   export PATH=$PATH:$ANACONDA_HOME/bin

Having all our variables set, we will first create a Conda environment in order to keep all Python libraries installed in a separated env. We call the environment `dvsrl-env` and we install the following libraries:

.. code-block:: bash

   $ conda create -n densedepth-env python=3.7 anaconda
   $ conda activate densedepth-env
   $ conda install pip
   $ pip install pillow matplotlib scikit-learn scikit-image opencv-python pydot
   $ pip install tensorflow-gpu==1.13.0rc1
   $ pip install keras==2.2.4
   

Clone DenseDepth and download the pre-trained models with:

.. code-block:: bash

   $ git clone https://github.com/ialhashim/DenseDepth.git
   $ cd DenseDepth/
   $ mkdir models
   $ cd models/
   $ wget https://s3-eu-west-1.amazonaws.com/densedepth/nyu.h5
   $ wget https://s3-eu-west-1.amazonaws.com/densedepth/kitti.h5
   
Now you can call `test.py` to run the tool.

 
