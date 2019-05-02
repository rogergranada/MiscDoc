=============================================
Installing Neural-Motifs
=============================================

This file contains all steps to install the `Neural-Motifs <https://github.com/rowanz/neural-motifs>`_ tool. The process starts by configuring paths that will be helpful to our scripts. 

.. code-block:: bash

   # Cuda 8.0
   export CUDA_HOME=/usr/local/cuda-8.0
   export LD_LIBRARY_PATH=$CUDA_HOME/lib64:$LD_LIBRARY_PATH
   export PATH=$CUDA_HOME/bin:$PATH

   # Neural-Motif folder
   export ENV_FOLDER=/home/roger/Workspace/github/pytorch-0.3
   export REFREL_HOME=$ENV_FOLDER/neural-motifs
   #export HOME_DATA=/usr/share/datasets/Roger/ObjectRelation/ReferringRelationships
   #export DATA_VG100K=/usr/share/datasets/Roger/ObjectRelation/datasets/VG_100K
   #export DATA_CLEVR=/usr/share/datasets/Roger/ObjectRelation/datasets/CLEVR_v1.0
   #export DATA_VRD=/usr/share/datasets/Roger/ObjectRelation/datasets/VRD


Having all our variables set, we will first create a Conda environment in order to keep all Python libraries installed in a separated env. We call the environment `neural-motifs-env` and we install the following libraries:

.. code-block:: bash

   $ conda create -n neural-motif-env python=3.6
   $ conda activate neural-motif-env
   $ pip install -r requirements.txt


Downloading data
------------------

Download the dataset and put into a folder named `data` as:

.. code-block:: bash

   $ cd $HOME_DATA
   $ mkdir data
   $ cd data/

   #### Download VRD dataset
   $ wget http://imagenet.stanford.edu/internal/jcjohns/scene_graphs/sg_dataset.zip
   $ unzip sg_dataset.zip
   $ rm sg_dataset.zip
   $ mv sg_dataset/ VRD/

   #### Download CLEVR dataset
   $ wget https://dl.fbaipublicfiles.com/clevr/CLEVR_v1.0.zip
   $ unzip CLEVR_v1.0.zip
   $ rm CLEVR_v1.0.zip

   #### Download VisualGenome dataset
   $ wget https://cs.stanford.edu/people/rak248/VG_100K_2/images.zip
   $ unzip images.zip
   $ rm images.zip
   $ wget https://cs.stanford.edu/people/rak248/VG_100K_2/images2.zip
   $ unzip images2.zip
   $ rm images2.zip
   $ mv VG_100K_2/* VG_100K/
   $ rmdir VG_100K_2

   $ cd ..
   $ mkdir annotations
   $ cd annotations

   #### Download VRD annotations
   $ wget https://cs.stanford.edu/people/ranjaykrishna/referringrelationships/vrd.zip
   $ unzip vrd.zip
   $ rm vrd.zip

   #### Download CLEVR annotations
   $ wget https://cs.stanford.edu/people/ranjaykrishna/referringrelationships/clevr.zip
   $ unzip clevr
   $ rm clevr.zip

   #### Download VisualGenome annotations
   $ wget https://cs.stanford.edu/people/ranjaykrishna/referringrelationships/visualgenome.zip
   $ unzip visualgenome.zip
   $ rm visualgenome.zip


Creating Dataset
-----------------

Now we have downloaded all files, we can create all datasets with:

.. code-block:: bash

   ### Create VRD dataset
   $ python data.py --save-dir $HOME_DATA/data/dataset-vrd \
                    --img-dir $DATA_VRD/images/val \
                    --test --image-metadata $HOME_DATA/annotations/VRD/test_image_metadata.json \
                    --annotations $HOME_DATA/annotations/VRD/annotations_test.json
   $ python data.py --save-dir $HOME_DATA/data/dataset-vrd \
                    --img-dir $DATA_VRD/images/train \
                    --image-metadata $HOME_DATA/annotations/VRD/train_image_metadata.json \
                    --annotations $HOME_DATA/annotations/VRD/annotations_train.json

   ### Create CLEVR dataset
   $ python data.py --save-dir $HOME_DATA/data/dataset-clevr \
                    --img-dir $DATA_CLEVR/images/val \
                    --test --image-metadata $HOME_DATA/annotations/clevr/test_image_metadata.json \
                    --annotations $HOME_DATA/annotations/clevr/annotations_test.json
   $ python data.py --save-dir $HOME_DATA/data/dataset-clevr \
                    --img-dir $DATA_CLEVR/images/train \
                    --image-metadata $HOME_DATA/annotations/clevr/train_image_metadata.json \
                    --annotations $HOME_DATA/annotations/clevr/annotations_train.json

   ### Create VisualGenome dataset
   $ python data.py --save-dir $HOME_DATA/data/dataset-vgenome \
                    --img-dir $DATA_VG100K \
                    --test --image-metadata $HOME_DATA/annotations/VisualGenome/test_image_metadata.json \
                    --annotations $HOME_DATA/annotations/VisualGenome/annotations_test.json
   $ python data.py --save-dir $HOME_DATA/data/dataset-vgenome \
                    --img-dir $DATA_VG100K \
                    --image-metadata $HOME_DATA/annotations/VisualGenome/train_image_metadata.json \
                    --annotations $HOME_DATA/annotations/VisualGenome/annotations_train.json



Training the Model
-------------------

Having created all datasets we can train a model with each dataset.

.. code-block:: bash

   ### Train a model in VRD dataset
   python train.py --workers 8 --lr 0.0001 --epochs 30 --patience 3 --lr-reduce-rate 0.7 \
                   --opt rms --loss-func weighted --w1 7.5 --batch-size 16 --dropout 0. \
                   --heatmap-threshold 0.5 --hidden-dim 1024 --embedding-dim 512 \
                   --num-predicates 70 --num-objects 100 --cnn resnet --nb-conv-im-map 0 \
                   --conv-im-kernel 0 --nb-conv-att-map 6 --conv-predicate-kernel 7 \
                   --conv-predicate-channels 10 --feat-map-dim 14 --feat-map-layer activation_40 \
                   --train-data-dir $HOME_DATA/data/dataset-vrd/train \
                   --val-data-dir $HOME_DATA/data/dataset-vrd/val \
                   --test-data-dir $HOME_DATA/data/dataset-vrd/test \
                   --save-dir $HOME_DATA/models/model-vrd \
                   --models-dir $HOME_DATA/models/model-vrd \
                   --iterations 3 --model ssas --use-predicate 1 --output-dim 14 --use-models-dir
                   --categorical-predicate --internal-loss-weight 1. --use-internal-loss --finetune-cnn

   ### Train a model in VisualGenome dataset
   python train.py --workers 8 --lr 0.0001 --epochs 50 --patience 3 --lr-reduce-rate 0.8 \
                   --opt rms --loss-func weighted --w1 7.5 --batch-size 64 --dropout 0. \
                   --heatmap-threshold 0.5 --hidden-dim 1024 --embedding-dim 512 \
                   --num-predicates 70 --num-objects 100 --cnn resnet --nb-conv-im-map 0 \
                   --conv-im-kernel 0 --nb-conv-att-map 6 --conv-predicate-kernel 7 \
                   --conv-predicate-channels 10 --feat-map-dim 14 --feat-map-layer activation_40 \
                   --train-data-dir $HOME_DATA/data/dataset-vgenome/train \
                   --val-data-dir $HOME_DATA/data/dataset-vgenome/val \
                   --test-data-dir $HOME_DATA/data/dataset-vgenome/test \
                   --save-dir $HOME_DATA/models/model-vgenome \
                   --models-dir $HOME_DATA/models/model-vgenome \
                   --iterations 3 --model ssas --use-predicate 1 --output-dim 14 --categorical-predicate \
                   --internal-loss-weight 1. --use-internal-loss --finetune-cnn --use-models-dir


   ### Train a model in CLEVR dataset
   python train.py --workers 8 --lr 0.0001 --epochs 25 --patience 4 --lr-reduce-rate 0.8 \
                   --opt rms --loss-func weighted --w1 7.5 --batch-size 32 --dropout 0. \
                   --heatmap-threshold 0.5 --hidden-dim 1024 --num-predicates 6 --num-objects 48 \
                   --cnn resnet --nb-conv-im-map 0 --conv-im-kernel 0 --nb-conv-att-map 4 \
                   --conv-predicate-kernel 5 --conv-predicate-channels 5 --feat-map-dim 14 \
                   --feat-map-layer activation_40  --use-models-dir \
                   --train-data-dir $HOME_DATA/data/dataset-clevr/train \
                   --val-data-dir $HOME_DATA/data/dataset-clevr/val \
                   --test-data-dir $HOME_DATA/data/dataset-clevr/test \
                   --save-dir $HOME_DATA/models/model-clevr \
                   --models-dir $HOME_DATA/models/model-clevr \
                   --iterations 3 --model ssas --use-predicate 1 --output-dim 14 \
                   --categorical-predicate --use-internal-loss --internal-loss-weight 2.

