# MIA-TCL
This repository contains all our materials except the whole dataset of our paper. Note that the .gitkeep file is **only for** keeping the integrity of our directory structure.

## Overview
We proposed a novel self-supervised learning using TCL for cervical OCT image classification. we use CNNs which can be implemented by various architectures, such as VGG, standard ResNet family, etc.  
The picture below demonstrates an overview of the proposed TCL framwork. The pretext task part uses texture contrast learning to extract feature maps from the texture features of the input images, and the downstream part linear classification is used to fine-tune the network Conv network.  
<img src="https://github.com/ChrisNieo/MIA-TCL/blob/main/figures/Figure_2%20Framework.png" width="800"/><br/>
## Experiment Results
Our training step results are shown in the picture below. This pictures mainly demonstrates both the efficiency an accuracy of our proposed method.  
<img src="https://github.com/ChrisNieo/MIA-TCL/blob/main/figures/Figure_4%20acc_curve.png" width="600"/><br/> 
Compared to four human experts, the performance of our model is better. And the picture below displays the confusion matrices for both the five-classed and binary classification task.  
<img src="https://github.com/ChrisNieo/MIA-TCL/blob/main/figures/Figure_5%20cfu_human%26net.png" width="800"/><br/>
Our paper gives more information about the performance of our proposed method. We give a result for an external validation samples which denotes that great generalization of our model. And we also give a visualization result of our model which denotes a better interpretability of our model.  
## Data preperation
By default. The `./dataset` directory stores our dataset. But we are sorry that our dataset cannot be made public due to the confidentiality agreement. You can get more information about our dataset by [`reading this paper`](https://www.researchgate.net/publication/341467052_Multi-center_Clinical_Study_Using_Optical_Coherence_Tomography_for_Evaluation_of_Cervical_Lesions_In-vivo).  
Stll, you can make your own dataset if possible through the following steps.  
* Put all you dataset samples in directory `./dataset`. Keep sure that ***the label and patient name(if exists) are able to be obtained from the sample file name.***
* Call function *generate_datatxt()* from `create_list.py` to generate a `data.txt` file in `./data_folder` directory. Modify the code in `create_list.py` if necessary. Keep sure that each line in `data.txt` is like '**[file_directory&emsp;label&emsp;patient_name(if exists)]**', where the spaces represent tabs('\t') with a newline('\n') in the end.  
*Example*:  `./dataset/0_M0003_2019_P0000004_circle_2.0x2.0_C06_S006_5.png  0 P0000004`.  
* Find the best P and R for your own dataset when call function *local_binary_pattern(LBP)* if you want to use TCL to boost model performance.  
* Call function *split_ssl_and_sl()* from `create_list.py` to generate a `self_supervised_list_folder.txt` file for self-supervised training and a `supervised_folder.txt` file for 10-fold cross validation experiment. Call function *generate_folder()* from `create_list.py` to generate `10_folder.txt` files for fine-tuning or training from scratch.
## Module envirment
The `requirement.txt` file records all dependencies and versions our work needs. Make sure your python version is 3.6.12 and to install all  dependencies at once, run `pip install -r requirements.txt`.  
## Runing
The `train.py` file defines all parameters and mode for each experiment.  
* Run self-supervised learning based on ResNet101 with TCL by:  
`python train.py --model_name 'lbp_resnet101' --epoch 150 --train_mode 'self_supervised' --use_lbp --lr 1e-2 --weight_decay 1e-6 --init --pre_train`
* Run self-supervised learning based on ResNet101 with SimCLR by:  
`python train.py --model_name 'resnet101' --epoch 150 --train_mode 'self_supervised' --lr 1e-2 --weight_decay 1e-6 --init --pre_train`
* Run self-supervised learning based on ResNet50 with SimCLR by:  
`python train.py --model_name 'resnet50' --epoch 150 --train_mode 'self_supervised' --lr 1e-2 --weight_decay 1e-6 --init --pre_train`
* Run supervised learning based on ResNet101 by:  
`python train.py --model_name 'resnet101' --epoch 40 --train_mode 'supervised' --lr 5e-3 --init`
* Run supervised learning based on ResNet50 by:  
`python train.py --model_name 'resnet50' --epoch 40 --train_mode 'supervised' --lr 5e-3 --init`
* Run supervised learning based on VGG19 by:  
`python train.py --model_name 'VGG19' --epoch 40 --train_mode 'supervised' --lr 5e-3 --init`
* Fine-tune ResNet101 with TCL by:  
`python train.py --model_name 'lbp_resnet101' --epoch 40 --train_mode 'fine_tune' --use_lbp --lr 5e-3 --pre_load`
* Fine-tune ResNet101 with SimCLR by:  
`python train.py --model_name 'resnet101' --epoch 40 --train_mode 'fine_tune' --use_lbp --lr 5e-3 --pre_load`
* Fine-tune ResNet50 with SimCLR by:  
`python train.py --model_name 'resnet50' --epoch 40 --train_mode 'fine_tune' --use_lbp --lr 5e-3 --pre_load`  
* Make sure you have prepared your data and install all dependencies before running your experiments.
* You can change the superparameters such as `lr(learning rate)`,`weight_decay`, etc and you can define and change the models based on CNNs if necessary.
## Repository description  
***checkpoint***: `stores checkpoints for different train_mode when training on epoch.`    
&emsp;***fine_tune***: `stores checkpoints for different model on fine-tuning after self-supervised training.`  
&emsp;&emsp;***lbp_resnet101***: `stores state of resnet-101 using TCL on fine-tuning.`   
&emsp;&emsp;***resnet101***: `stores state of resnet-101 using SimCLR on fine-tuning.`  
&emsp;&emsp;***resnet50***: `stores state of resnet-50 using SimCLR on fine-tuning.`  
&emsp;***self_supervised***: `stores checkpoints for different model on self-supervised learning.`  
&emsp;&emsp;***lbp_resnet101***: `stores state of resnet-101 using TCL.`   
&emsp;&emsp;***resnet101***: `stores state of resnet-101 using SimCLR.`  
&emsp;&emsp;***resnet50***: `stores state of resnet-50 using SimCLR.`  
&emsp;***supervised***: `stores checkpoints for different model on training from scratch.`  
&emsp;&emsp;***resnet101***: `stores state of resnet-101 training from scratch.`  
&emsp;&emsp;***resnet50***: `stores state of resnet-50 training from scratch.`  
&emsp;&emsp;***vgg19***: `stores state of vgg19 training from scratch.`   
***data_folder***: `contains the training, test, and validation "data.txt" for traning, test and validation.`  
***dataset***: `contains the whole dataset samples.`  
***figures***: `stores all figures displayed in our paper.`  
***result***: `same as checkpoint directory. stores the training results in .txt files.`  
&emsp;***fine_tune***  
&emsp;&emsp;***lbp_resnet101***  
&emsp;&emsp;***resnet101***  
&emsp;&emsp;***resnet50***  
&emsp;***self_supervised***  
&emsp;&emsp;***lbp_resnet101***  
&emsp;&emsp;***resnet101***  
&emsp;&emsp;***resnet50***  
&emsp;***supervised***  
&emsp;&emsp;***resnet101***  
&emsp;&emsp;***resnet50***  
&emsp;&emsp;***vgg19***  
***create_list.py***: `create data list from ./dataset. split data into self_supervised_trian/supervised_train/train/test/validation.`  
***data_reader.py***: `data loader class.`  
***model.py***: `define model structure for vgg and resnet family.`  
***train.py***: `training step.`  
***util.py***:  `uitlization function and class such as ImageProcessor.`  
***requirement.txt***:  `record all dependencies and versions.` 
## Contact us
If you have any issues, contact us at email address kaiyi@whu.edu.cn or leave a message under `Issues` module!
