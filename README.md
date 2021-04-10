# Exploring Image Deblurring via Encoded Blur Kernel Space

## About the project

This repository is the official pytorch implementation of the [CVPR'21 paper](https://arxiv.org/abs/2104.00317): 

**Explore Image Deblurring via Encoded Blur Kernel Space.** \
P. Tran, A. Tran, Q. Phung, M. Hoai (2021) \
IEEE Conference on Computer Vision and Pattern Recognition (CVPR). 

![Blur kernel space](imgs/teaser.jpg)

## Table of Content 

* [About the Project](#about-the-project)
* [Getting Started](#getting-started)
  * [Prerequisites](#prerequisites)
  * [Installation](#installation)
  * [Using the pretrained model](#Using-the-pretrained-model)
* [Training and evaluation](#Training-and-evaluation)
* [Model Zoo](#Model-zoo)

## Getting started

### Prerequisites

* Python >= 3.7
* Pytorch >= 1.4.0
* CUDA >= 10.0

### Installation

``` sh
git clone https://github.com/VinAIResearch/blur-kernel-space-exploring.git
cd blur-kernel-space-exploring

conda create -n BlurKernelSpace -y python=3.7
conda activate BlurKernelSpace
conda install --file requirements.txt

```

### Using the pre-trained model

<!--
``` diff
- Please specify a very simple one-line command to use a pre-trained model to deblur an image. You might need to specify how to download the pre-trained model in the first place. Use the best generic model that you have. 
- You might want to provide a sample input image and a sample output image. People can run this simple command to reproduce the output image and compare it with the provided out to verify that they have installed your code successfully. 

```
-->
To deblur an image using a pretrained model, use the following command:
``` sh
python deblur.py --image_path=imgs/blur_imgs/blur01.png --yml_path options/deblur.yml --save_path sharp01.png
```


## Training and evaluation
### Preparing datasets and pretrained models
You can find the datasets and pretrained models in model zoo section. You can also use `scripts/download_GOPRO.py` and `scripts/download_REDS.py` to download the datasets directly.


### Training
To train the model, first, create an lmdb dataset using `scripts/create_lmdb.py`. Then using the following script:
```
python train.py -opt path_to_yaml_file
```

where `path_to_yaml_file` is the path to yaml file that contains training configurations. You can find some default configurations in `options` folder. Checkpoints and logs will be saved in `../experiments/modelName`

### Testing
#### Data augmentation
To augment a given dataset, first, create an lmdb dataset using `scripts/create_lmdb.py`. Then using the following script:
```
python test_data_augmentation.py --target_H=256 --target_W=256 \\
                                 --model_path=experiments/pretrained/GOPRO_woVAE.pth \\
                                 --LQ_root=datasets/GOPRO/train_blur.lmdb \\
                                 --HQ_root=datasets/GOPRO/sharp_blur.lmdb \\
                                 --save_path=results/GOPRO_augmented \\
                                 --num_images=10000\\
                                 --yml_path=options/GOPRO/woVAE.yml
```
`target_H` and `target_W` is the desired shape of the augmented images, `LQ_root` and `HQ_root` is the path of the lmdb dataset that was created before. `model_path` is the path of the trained model. `yml_path` is the path to the model configuration. Results will be saved in `save_path`.

![Data augmentation examples](imgs/results/augmentation.jpg)

#### Generate novel blur kernels
To generate a blur image given a sharp image, use the following command:
```sh
python generate_blur --model_path=experiments/pretrained/GOPRO_wVAE.pth \\
		     --yml_path=options/GOPRO/wVAE.yml \\
		     --image_path=imgs/sample_sharp.png
```
**Note**: This only works with models that were trained with `--VAE` flag.
![kernel generating examples](imgs/results/generate_blur.jpg)

#### Image Deblurring
To deblur a blurry image, use the following command:
```sh
python deblur.py --image_path imgs/blur_imgs/blur1.png --yml_path options/deblur.yml --save_path res.png
```

![Image deblurring examples](imgs/results/general_deblurring.jpg)

#### PULSE-like Deblurring
To deblur and blurry image using a latent space as sharp image prior, use the following command:
```sh
python run_pulse.py -input_dir imgs/blur_faces -output_dir experiments/pulse/results
```
Results will be saved in `experiments/pulse/results` folder.

![PULSE-like Deblurring examples](imgs/results/pulse.jpg)

## Model Zoo
Pretrained models can be downloaded here.


[REDS]: https://seungjunnah.github.io/Datasets/reds.html
[GOPRO]: https://seungjunnah.github.io/Datasets/gopro

[REDS woVAE]: https://drive.google.com/file/d/12ZhjXWcYhAZjBnMtF0ai0R5PQydZct61/view?usp=sharing
[GOPRO woVAE]: https://drive.google.com/file/d/1WrVALP-woJgtiZyvQ7NOkaZssHbHwKYn/view?usp=sharing
[GOPRO wVAE]: https://drive.google.com/file/d/1QMUY8mxUMgEJty2Gk7UY0WYmyyYRY7vS/view?usp=sharing
[GOPRO + REDS woVAE]: https://drive.google.com/file/d/169R0hEs3rNeloj-m1rGS4YjW38pu-LFD/view?usp=sharing

|Model name              | dataset(s)      | status                   |
|:-----------------------|:---------------:|-------------------------:|
|[REDS woVAE]            | [REDS]          | :heavy_check_mark:       |
|[GOPRO woVAE]           | [GOPRO]         | :heavy_check_mark:       |
|[GOPRO wVAE]            | [GOPRO]         | :heavy_check_mark:       |
|[GOPRO + REDS woVAE]    | [GOPRO], [REDS] | :heavy_check_mark:       |


## Notes and references
The training code is borrowed from EDVR project: https://github.com/xinntao/EDVR

The backbone code is borrowed from DeblurGAN project: https://github.com/KupynOrest/DeblurGAN

## Citation

If you find this code useful, please cite: 

```
**Explore Image Deblurring via Encoded Blur Kernel Space.** \
P. Tran, A. Tran, Q. Phung, M. Hoai (2021) \
IEEE Conference on Computer Vision and Pattern Recognition (CVPR). 

@inproceedings{m_Tran-etal-CVPR21, \
  author = {Phong Tran and Anh Tran and Quynh Phung and Minh Hoai}, \
  title = {Explore Image Deblurring via Encoded Blur Kernel Space}, \
  year = {2021}, \
  booktitle = {Proceedings of the {IEEE} Conference on Computer Vision and Pattern Recognition (CVPR)} \
}
```

