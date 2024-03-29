# Contents

- [Contents](#contents)
    - [DAAF-ReID Description](#mgn-description)
    - [Model Architecture](#model-architecture)
    - [Data Prepare](#dataset)
    - [Environment Requirements](#environment-requirements)
    - [Running scripts](#running-scripts)
    - [Citation](#citation)
    - [Contact](#contact)
    <!-- - [ModelZoo Homepage](#modelzoo-homepage) -->

## [DAAF-ReID Description](#contents)


Deep attention aware feature learning is designed for person ReID task. Our method adds two branches from the backbone network at training stage so as to guide the backbone being able to learn global and local attention aware features. The PAB forces the separated groups of feature channels focus on predefined body parts by predicting their corresponding keypoints. The HAB branch predicts the mask of a person and restricts the backbone network to focus on person bodies instead of background. PAB and HAB do not influence the testing stage, thus the same inference time and model size are maintained compared with the backbone network.

[Paper](https://arxiv.org/pdf/2003.00517): Deep attention aware feature learning for person re-identification

## [Model Architecture](#contents)


We implement our method on the basis of widely used [TriNet](https://github.com/VisualComputingInstitute/triplet-reid). TriNet consists of backbone network, i.e. [ResNet-50](https://openaccess.thecvf.com/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf), and 2 fully connected layers in the end. The parameters of decoders in HAB and PAB are listed following. 


| layer | \#channels in | \#channels out | kernel size | stride |
|-------|---------------|----------------|-------------|--------|
| deconv 1 | 2048 (HAB) 341 (PAB) | 64 | 3x3 | 2 |
| deconv 2 | 64 | 64 | 3x3 | 2 |
| deconv 3 | 64 | 64 | 3x3 | 2 |
| deconv 4 | 64 | 64 | 3x3 | 2 |
| 1x1 conv | 64 | 1 (HAB) keypoint groups (PAB)  | 1x1 | 1 |

## [Data Prepare](#contents)

[Market1501](http://zheng-lab.cecs.anu.edu.au/Project/project_reid.html) dataset is used to train and test model. Market-1501 has 32668 annotated bounding boxes of 1501 identities collected by six cameras. There are 12936 images used for training. The query and gallery sets have 3368 and 19732 images respectively. 

Keypoint and mask annotations are generated by CPN (Cascaded Pyramid Network for Multi-Person Pose Estimation) and FCN (Fully Convolutional Networks for Semantic Segmentation). For more information, please refer to their project: [CPN](https://github.com/GengDavid/pytorch-cpn) and [FCN](https://github.com/shelhamer/fcn.berkeleyvision.org). And we also upload keypoint and mask annotations to [Baiduyun](https://pan.baidu.com/s/1kQzq-HDbylZxEcZRobRLDQ#list/path=%2F). The password is qp8k. 

Data structure:

```text
Datasets
├──Market-1501  
|  ├── bounding_box_test [19732 jpgs]
|  ├── bounding_box_train [12936 jpgs]
|  ├── gt_bbox [25259 jpgs]
|  ├── gt_query [6736 mats]
|  ├── query [3368 jpgs]
|  └── readme.txt
├──mask-anno
|  ├── bounding_box_test [19732 jpgs]
|  ├── bounding_box_train [12936 jpgs]
|  └── query [3368 jpgs]
└──Market_cpn_keypoints
   └── bounding_box_train_256_2 [12936*17=219912 jpgs]
```

## [Environment Requirements](#contents)

- Hardware（GPU）
    - Prepare hardware environment with GPU processor.
- Framework
    - [MindSpore](https://gitee.com/mindspore/mindspore)
- For more information, please check the resources below：
    - [MindSpore Tutorials](https://www.mindspore.cn/tutorials/en/master/index.html)
    - [MindSpore Python API](https://www.mindspore.cn/docs/api/en/master/index.html)

For convenience, we provide a [docker](https://docs.docker.com/) image which includes all environmental requirements to run experiments by MindSpore. And we also upload the image to [Baiduyun](https://pan.baidu.com/s/1kQzq-HDbylZxEcZRobRLDQ#list/path=%2F). The password is qp8k.

```bash
# load image
docker load -i name.tar

# create container
docker run -it -d --cap-add sys_ptrace --name=DAAF_mindspore --runtime=nvidia --ipc=host -p 6022:22 -v /home/cyf:/home/cyf 1683c3860cc5 /bin/bash
```

## [Running scripts](#contents)

Model uses pre-trained backbone ResNet50 trained on ImageNet2012. [Link](https://download.mindspore.cn/model_zoo/r1.3/resnet50_ascend_v130_imagenet2012_official_cv_bs256_top1acc76.97__top5acc_93.44/)

```bash
# run training example
bash scripts/run_standalone_train_gpu.sh 0 /path/to/market1501/ /path/to/output/ /path/to/pretrined_resnet50.pth

# run distributed training example
bash scripts/run_distribute_train_gpu.sh 8 /path/to/market1501/ /path/to/output/ /path/to/pretrined_resnet50.pth

# run evaluation example
bash scripts/run_eval_gpu.sh /your/path/checkpoint_file
```

## [Citation](#contents)
If you find this code useful in your research, please kindly consider citing our paper:

    @article{chen2022deep,
    title={Deep attention aware feature learning for person re-identification},
    author={Chen, Yifan and Wang, Han and Sun, Xiaolu and Fan, Bin and Tang, Chu and Zeng, Hui},
    journal={Pattern Recognition},
    volume={126},
    pages={108567},
    year={2022},
    publisher={Elsevier}
    }


## [Contact](#contents)

If you have any questions, please contact us. You could open an issue on github or email us.

[Yifan Chen](https://github.com/CYFFF)  
[EMAIL: chenyifan0627@gmail.com](mailto:chenyifan0627@gmail.com)


<!-- ## [ModelZoo Homepage](#contents)

Please check the official [homepage](https://gitee.com/mindspore/models). -->
