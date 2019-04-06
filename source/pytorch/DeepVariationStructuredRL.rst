=============================================
Installing DeepVariationStructuredRL (DVSRL)
=============================================

This file contains all steps to install the `DeepVariationStructuredRL <https://github.com/nexusapoorvacus/DeepVariationStructuredRL>`_ tool. The process starts by configuring paths that will be helpful to our scripts. 

.. code-block:: bash

   # Cuda 8.0
   export CUDA_HOME=/usr/local/cuda-8.0
   export LD_LIBRARY_PATH=$CUDA_HOME/lib64:$LD_LIBRARY_PATH
   export PATH=$CUDA_HOME/bin:$PATH

   # DeepVariationStructuredRL folder
   export ENV_FOLDER=/home/roger/Workspace/github/pytorch-0.4
   export DVSRL_HOME=$ENV_FOLDER/DeepVariationStructuredRL
   export PATH=$ENV_FOLDER/bin:$PATH
   export LD_LIBRARY_PATH=$ENV_FOLDER/lib:$LD_LIBRARY_PATH
   export LD_RUN_PATH=$ENV_FOLDER/lib

Having all our variables set, we will first create a Conda environment in order to keep all Python libraries installed in a separated env. We call the environment `dvsrl-env` and we install the following libraries:

.. code-block:: bash

   $ conda create -n dvsrl-env python=2.7 anaconda
   $ conda activate dvsrl-env

   $ conda install -c pytorch pytorch==0.4
   $ conda install -c anaconda pip
   $ pip install -r requirements.txt


Installing Faster-RCNN
-----------------------

In order to install Faster-RCNN, we go to folder `faster_rcnn` and compile the code with:

.. code-block:: bash

   $ cd faster_rcnn/
   $ ./make.sh

Download the model trained of Faster-RCNN from `Google Drive <https://github.com/longcw/faster_rcnn_pytorch>`_ and put it in the same folder of `main.py`.
 

Adding data
------------

Download the dataset and put into a folder named `data` as:

.. code-block:: bash

   $ mkdir data
   $ cd data/

   #### Download images
   $ wget https://cs.stanford.edu/people/rak248/VG_100K_2/images.zip
   $ wget https://cs.stanford.edu/people/rak248/VG_100K_2/images2.zip
   $ unzip images.zip
   $ unzip images2.zip
   $ mv VG_100K_2/* VG_100K/
   $ rmdir VG_100K_2/

   #### Insert data into raw_data
   $ mkdir raw_data
   $ cd rawdata/
   $ wget http://visualgenome.org/static/data/dataset/objects.json.zip
   $ wget http://visualgenome.org/static/data/dataset/relationships.json.zip
   $ wget http://visualgenome.org/static/data/dataset/object_alias.txt
   $ wget http://visualgenome.org/static/data/dataset/relationship_alias.txt
   $ wget http://visualgenome.org/static/data/dataset/attributes.json.zip
   $ wget http://visualgenome.org/static/data/dataset/scene_graphs.json.zip
   $ unzip objects.json.zip
   $ unzip relationships.json.zip
   $ unzip attributes.json.zip
   $ scene_graphs.json.zip

   $ mkdir data_samples
   

Download data for Skip Thoughts model with:

   $ cd skip_thoughts
   $ wget http://www.cs.toronto.edu/~rkiros/models/dictionary.txt
   $ wget http://www.cs.toronto.edu/~rkiros/models/utable.npy
   $ wget http://www.cs.toronto.edu/~rkiros/models/btable.npy
   $ wget http://www.cs.toronto.edu/~rkiros/models/uni_skip.npz
   $ wget http://www.cs.toronto.edu/~rkiros/models/uni_skip.npz.pkl
   $ wget http://www.cs.toronto.edu/~rkiros/models/bi_skip.npz
   $ wget http://www.cs.toronto.edu/~rkiros/models/bi_skip.npz.pkl
