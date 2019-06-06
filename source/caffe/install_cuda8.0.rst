===========================
Installing Caffe Framework
===========================

Before installing Caffe, we have to install a bunch of dependencies. The steps below describe the install process in a computer containing Ubuntu 16.04 and pre-installed Cuda 9.0. Thus, we will not bother with Cuda installation. The process starts by configuring paths that will be helpful to our scripts. 

.. code-block:: bash

   # Cuda 8.0
   export CUDA_HOME=/usr/local/cuda-8.0
   export LD_LIBRARY_PATH=$CUDA_HOME/lib64:$LD_LIBRARY_PATH
   export PATH=$CUDA_HOME/bin:$PATH

   # Caffe Framework
   export CAFFE_HOME=/home/roger/Workspace/github/caffe_framework
   export PATH=$CAFFE_HOME/bin:$PATH
   export LD_LIBRARY_PATH=$CAFFE_HOME/lib:$LD_LIBRARY_PATH
   export LD_RUN_PATH=$CAFFE_HOME/lib

Having all our variables set, we will first create a Conda environment in order to keep all Python libraries installed in a separated env. In order to do so, we run:

.. code-block:: bash

   $ conda create -n drnet-env python=2.7 anaconda
   $ conda activate drnet-env

Now that we have the ``drnet-env`` activated, we can start the process of installing Caffe's dependencies.


Install Boost
^^^^^^^^^^^^^^

Next, we install the `Boost library <https://www.boost.org/>`_. To install it locally, we perform the following steps:

.. code-block:: bash

   $ curl -L -O http://sourceforge.net/projects/boost/files/boost/1.57.0/boost_1_57_0.tar.gz
   $ tar zxvf boost_1_57_0.tar.gz                                     
   $ cd boost_1_57_0/

We then edit the file ``bootstrap.sh`` in order to add the correct path to the Python interpreter. We have to edit the following lines:

.. code-block:: bash

   PREFIX=/home/roger/Workspace/github/envs/caffe_framework
   LIBDIR=/home/roger/Workspace/github/envs/caffe_framework/lib
   INCLUDEDIR=/home/roger/Workspace/github/envs/caffe_framework/include
   PYTHON_VERSION=2.7
   PYTHON_ROOT=/opt/anaconda2/envs/drnet-env/bin/python

Saving the file with the new paths, we configure it by running:
                                              
.. code-block:: bash

   $ ./bootstrap.sh
   $ ./b2                                                             
   $ ./b2 install

These commands will install the library in ``$CAFFE_HOME/lib`` and ``$CAFFE_HOME/include`` folders.


GFlags
^^^^^^^

Caffe also depends on `Commandline flags (GFlags) <https://gflags.github.io/gflags/>`_. As Glog, we clone its repository, compile and install it, as:

.. code-block:: bash

   $ git clone https://github.com/gflags/gflags.git
   $ cd gflags/
   $ mkdir build
   $ cd build/
   $ cmake -D CMAKE_INSTALL_PREFIX=$CAFFE_HOME ..

As Caffe needs shared libraries, we have to edit the ``CMakeCache.txt`` file, by adding ``-fPIC`` to ``CMAKE_CXX_FLAGS`` in line: 

.. code-block:: bash

   CMAKE_CXX_FLAGS:STRING=-fPIC

Now, we can compile and install as: 

.. code-block:: bash

   $ make 
   $ make install

These commands installs GFlags into ``$CAFFE_HOME/lib`` and ``$CAFFE_HOME/include`` folders.


Glog
^^^^^ 

As Protobuf, Caffe also depends on `Google logging module (GLog) <https://github.com/google/glog>`_. This library is installed by cloning its repository from Github and compiling it as: 

.. code-block:: bash

   $ git clone https://github.com/google/glog.git
   $ cd glog/
   $ git checkout v0.3.4
   $ ./configure --prefix=$CAFFE_HOME
   $ make 
   $ make install

These commands installs Glog into ``$CAFFE_HOME/lib`` and ``$CAFFE_HOME/include`` folders.


Protobuf
^^^^^^^^^

Caffe depends on a specific version of `Protobuf <https://developers.google.com/protocol-buffers/>`_. Thus, we install its version 3.3.0 by first downloading it from the `Github page <https://github.com/protocolbuffers/protobuf/releases/tag/v3.3.0/>`_. Then, we run the following commands:

.. code-block:: bash

   $ tar -zxvf protobuf-cpp-3.3.0.tar.gz
   $ cd protobuf-3.3.0/                                                     
   $ ./autogen.sh
   $ ./configure --prefix=$CAFFE_HOME
   $ make 
   $ make install

This commands will put the compiled library into ``$CAFFE_HOME/lib`` and ``$CAFFE_HOME/include`` folders.


LevelDB
^^^^^^^^

Caffe depends on the fast key-value storage library ``LevelDB <https://github.com/google/leveldb>`_. As occurred in Protobuf, Caffe needs a specific version of the LevelDB library. Thus, we download its 1.10 version from the `Github site <https://github.com/google/leveldb/tags?after=v1.11>`_. Having downloaded it, we run the commands:

.. code-block:: bash

   $ tar -zxvf leveldb-1.10.tar.gz
   $ cd leveldb-1.10/
   $ make
   $ cp --preserve=links libleveldb.* $CAFFE_HOME/lib
   $ cp -r include/leveldb $CAFFE_HOME/include/ 

As LevelDB does not have a ``make install`` command, the last two commands will copy files to the ``$CAFFE_HOME/lib`` and ``$CAFFE_HOME/include`` folders.


Snappy
^^^^^^^

Next, we install the compression/decompression library called `Snappy <https://github.com/google/snappy>`_ by cloning its repository from Github and compiling it locally, as:

.. code-block:: bash

   $ git clone https://github.com/google/snappy.git                                                                                                                
   $ cd snappy/                                                       
   $ mkdir build
   $ cd build/
   $ cmake -D CMAKE_INSTALL_PREFIX=$CAFFE_HOME ..

We have to edit the ``CMakeCache.txt`` file in order to add the compilation of shared libraries. Thus, we add the line:

.. code-block:: bash

   CMAKE_CXX_FLAGS:STRING=-fPIC

Save file and then we can compile and install as:

.. code-block:: bash

   $ make 
   $ make install

These commands puts the compiled library into ``$CAFFE_HOME/lib`` and ``$CAFFE_HOME/include`` folders.

LMDB
^^^^^

Next, we have to compile and install `LMDB <https://github.com/LMDB/lmdb>`_ library. In order to do so, we clone its repository and compile it locally as:

.. code-block:: bash

   $ git clone https://github.com/LMDB/lmdb.git
   $ cd lmdb/libraries/liblmdb

Inside ``liblmdb`` folder, we edit the ``Makefile`` file changing the ``prefix`` to our local installation by changing the line:


.. code-block:: bash

   prefix  = /home/roger/Workspace/github/envs/caffe_framework/

Now, we can compile by running:

.. code-block:: bash

   $ make
   $ make install

These commands puts the compiled library into ``$CAFFE_HOME/lib`` and ``$CAFFE_HOME/include`` folders.



Caffe
^^^^^^

After installing all dependencies, we are ready for installing the `Caffe <https://github.com/BVLC/caffe>`_ framework. To install it locally, we clone the repository, install its Python dependencies and compile it. The steps to do so are: 

.. code-block:: bash

   $ git clone https://github.com/BVLC/caffe.git
   $ cd caffe/
   $ conda install -c anaconda cython ipython networkx six -n caffe-env
   $ conda install numpy==1.14.5 -n caffe-env
   $ conda install scipy scikit-image matplotlib h5py leveldb nose pandas pyyaml Pillow python-gflags python-dateutil -n caffe-env
   $ conda install -c menpo opencv3 -n caffe-env
   $ conda uninstall protobuf libprotobuf

After installing its Python dependencies, we convert the ``Makefile.config.example`` to ``Makefile.config`` and edit it as:

.. code-block:: bash

   $ cp Makefile.config.example Makefile.config
   $ vi Makefile.config

   USE_CUDNN := 1
   OPENCV_VERSION := 3

   CUDA_DIR := /usr/local/cuda-8.0
   CUDA_ARCH := -gencode arch=compute_20,code=sm_20 \
                -gencode arch=compute_20,code=sm_21 \
                -gencode arch=compute_30,code=sm_30 \
                -gencode arch=compute_35,code=sm_35 \
                -gencode arch=compute_50,code=sm_50 \
                -gencode arch=compute_52,code=sm_52 \
                -gencode arch=compute_60,code=sm_60

   BLAS := open
   BLAS_INCLUDE := /opt/OpenBLAS/include
   BLAS_LIB := /opt/OpenBlas/lib

   ANACONDA_HOME := /opt/anaconda2/envs/drnet-env
   PYTHON_INCLUDE := $(ANACONDA_HOME)/include \
                     $(ANACONDA_HOME)/include/python2.7 \
                     $(ANACONDA_HOME)/lib/python2.7/site-packages/numpy/core/include
   PYTHON_LIB := /opt/anaconda2/envs/drnet-env/lib
   WITH_PYTHON_LAYER := 1

   INCLUDE_DIRS := $(PYTHON_INCLUDE) /home/roger/Workspace/github/envs/caffe_framework/include
   LIBRARY_DIRS := $(PYTHON_LIB) /home/roger/Workspace/github/envs/caffe_framework/lib

   BUILD_DIR := build
   DISTRIBUTE_DIR := distribute
   TEST_GPUID := 0
   Q ?= @

This entire ``Makefile8.0.config`` file can be seen in our `Github page <scripts/Makefile.config>`_. After setting up all paths, we compile it using:

.. code-block:: bash

   $ mkdir build
   $ cd build/
   $ cmake -D BLAS=open \
     -D CMAKE_INSTALL_PREFIX=$CAFFE_HOME \
     -D PYTHON_INCLUDE_DIR=$(python3 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") $(python3 -c "import numpy; print(numpy.get_include())") \
     -D PYTHON_LIBRARY=$(python3 -c "from distutils.sysconfig import get_config_var;from os.path import dirname,join ; print(join(dirname(get_config_var('LIBPC')),get_config_var('LDLIBRARY')))") \
     -D PYTHON_EXECUTABLE=$(which python3) \
     -D GFLAGS_INCLUDE_DIR=$CAFFE_HOME/include/ \
     -D GFLAGS_LIBRARY=$CAFFE_HOME/lib/libgflags.a \
     -D GLOG_INCLUDE_DIR=$CAFFE_HOME/include \
     -D GLOG_LIBRARY=$CAFFE_HOME/lib/libglog.so \
     -D LMDB_INCLUDE_DIR=$CAFFE_HOME/include/ \
     -D LMDB_LIBRARIES=$CAFFE_HOME/lib/liblmdb.so \
     -D LevelDB_INCLUDE_DIR=$CAFFE_HOME/include/ \
     -D LevelDB_LIBRARY=$CAFFE_HOME/lib/libleveldb.so \
     -D OpenBLAS_INCLUDE_DIR=$CAFFE_HOME/include/ \
     -D OpenBLAS_LIB=$CAFFE_HOME/lib/libopenblas.so \
     -D Snappy_INCLUDE_DIR=$CAFFE_HOME/include/ \
     -D Snappy_LIBRARIES=$CAFFE_HOME/lib/libsnappy.a \
     -D Boost_INCLUDE_DIR=$CAFFE_HOME/include/ \
     -D Boost_LIBRARY_DIR_RELEASE=$CAFFE_HOME/lib/ \
     -D BOOST_ROOT=$CAFFE_HOME/boost_1_57_0 \
     -D BOOST_LIBRARYDIR=$CAFFE_HOME/lib/ ..
   $ make install 

This commands will compile Caffe with all our installed dependencies. The generated ``CMakeCache.txt`` file containing all paths can be seen in our `Github page <scripts/CMakeCache.txt>`_. 


As we created an environment to the Caffe framework, we create a ``.bashrc`` file containing all exports that we need to use the framework. These exports contain:

.. code-block:: bash

   export CUDA_HOME=/usr/local/cuda-9.0
   export CUDALIB=$CUDA_HOME/targets/x86_64-linux/lib
   export CAFFE_HOME=/home/roger/Workspace/github/CaffeFramework
   export PATH=$CAFFE_HOME/bin:$PATH
   export LD_LIBRARY_PATH=$CAFFE_HOME/lib:$LD_LIBRARY_PATH
   export OpenBLAS_HOME=$CAFFE_HOME/OpenBLAS-0.2.20/
   export PYTHONPATH=/opt/anaconda/envs/caffe-env/
   export PYTHONPATH=$CAFFE_HOME/caffe/python:$PYTHONPATH


export CUDA_HOME=/usr/local/cuda-8.0
export CONDA_ENV=/opt/anaconda/envs/caffe-env
export CUDALIB=$CONDA_ENV/lib
export CAFFE_HOME=/home/roger/Workspace/github/CaffeFramework
export PATH=$CAFFE_HOME/bin:$PATH
export LD_LIBRARY_PATH=$CAFFE_HOME/lib:$LD_LIBRARY_PATH
export OpenBLAS_HOME=$CAFFE_HOME/OpenBLAS-0.2.20
export PYTHONPATH=$CONDA_ENV
export PYTHONPATH=$CAFFE_HOME/caffe/python:$PYTHONPATH

