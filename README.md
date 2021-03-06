# Single Image Super Resolution 
This is a pytorch implementation of a simplified version of
[SRResNet](http://cs231n.stanford.edu/reports/2016/pdfs/212_Report.pdf) which we will call VICLAB network. 
[SRCNN](http://personal.ie.cuhk.edu.hk/~ccloy/files/eccv_2014_deepresolution.pdf), and 
[VDSR](https://cv.snu.ac.kr/research/VDSR/) models are also implemented for comparison. 
This project is used for Image Quality Enhancement graduate course. This code is self-contained, you don't need to do 
any pre-processing on the dataset.

To run this project you need to setup the environment with the required packages (see requirements.txt), download the 
dataset, and train and test the network models. 

## Hardware 
I tested my project in Corei7, 12G RAM, GPU GTX1070. 

## Dataset
An special crop of MSCOCO2017 dataset provided by class TA  was used. It was ensured that all the images have even 
dimmensions and are saved in PNG format to avoid coding artifacts. LR patches are generated by bicubic downsampling 
the HR pictures. Put your trainig set at data->train and your validation and test sets at data->test. 

When training, the train.py script only accesses the HR folder, aplies data augmentation, and generates the LR on the 
fly using bicubic kernel. During test and validation, the validate.py and ta_test.py scripts access both the HR and 
LR folders and LR folder only respectively.

> NOTE: For this assigment only x2 upscaling factor was requested, but you can easily set any integer upscaling factor
 by changing the scale script argument.

## Setup
Install depenencies in requirements.txt

Install Pytorch: follow instruction in Pytorch official [website](http://pytorch.org/) to install pytorch 0.4.1. 
Previous versions are not supported as we are using the torch.no_grad() function to disable gradient computation 
during evaluation and test (among other things).

## Execute the code
To train the network use ```python train.py --verbose``` you should see something like the figure below. The trained
models are saved in check_point/model_name/scale directory. The best and the last epoch model are saved at 
best_timestamp.pt and cp_timesamp.pt files.

![train](misc/training.png)

To validate the network (this will compare the output with the ground truth in terms of PSNR and SSIM):
```python validate.py```
you should see something like:

![test](misc/validate.png)

To test the network (only SR images are generated there is no ground truth for comparison):
```python ta_test.py --test-set your_test_set_at_data_test_directory```
you should see something like:

![test](misc/ta_testing.png)

The experiment results will be saved in val_results and test_results directories. 

To evaluate the results run in your terminal (at this project directory): 
```python evaluation.py your_results_dir  ground_truth_dir output_dir```

>NOTE: The above is the simplest way to train and test the model, all the settings will take default values. 
You can add options for training and testing. For example if i want to train model ``MFCNN``, initial learning-rate 
1e-2, num of epoch 100, batch_size 64, scale factor 3, verbose true: 
``python train.py -m MFCNN -l 1e-2 -n 100 -b 64 -s 3 --verbose``. I STRONGLY recommend to use an IDE like pycharm, 
there you can edit the training options in the top part of the scripts and just hit the run button.

## Comparisons
The simplified SRResNet was compared against SRCNN, ESPCN and VDSR under the same training conditions in terms of 
PSNR and SSIM. PSNR is obtained by: ```20*log10(255/rmse)```


Quantitative comparison (PSRN [dB] / SSIM):

SRCNN: 29.169 / 0.864

VDSR: 30.093 / 0.877

VCILAB: 30.101 / 0.879

![train](misc/qualitative.png)


## Project explanation
- ``train.py``: where you can train the network
- ``validate.py``: where you can test and compare against ground truth
- ``ta_test.py``: where you can only test the network
- ``bicubic_baseline.py``: Use it to generate bicubic upsampled images for reference
- ``model.py``: Here you will find the models VICLAB, SRCNN, VDSR. 
- ``SR_dataset.py``: Data handling classes. One for training, one for validation on the fly, and a couple for testing.
- ``solver.py``: encapsulate all the logics to train the network
- ``pytorch_ssim.py``: pytorch implementation for SSIM loss (with autograd), clone from this 
[repo](https://github.com/Po-Hsun-Su/pytorch-ssim)
- ``loss.py``: loss function for models (default and required for this assigment: L1 loss)

## Need help?
I know these instructions are pretty general. If you have any problem, you can contact me through juanluisgb@kaist.ac.kr.