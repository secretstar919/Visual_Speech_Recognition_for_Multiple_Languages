<p align="center"><img width="160" src="doc/lip_white.png" alt="logo"></p>
<h1 align="center">Visual Speech Recognition for Multiple Languages</h1>

<div align="center">

[📘Introduction](#introduction) |
[📝License](#License) |
[🛠️Installation](#Installation) |
[👄Recognition](#Recognition) |
[🐯Model Zoo](#Model-Zoo) |
[📧Contact](#Contact)
</div>

## Authors

[Pingchuan Ma](https://mpc001.github.io/), [Stavros Petridis](https://ibug.doc.ic.ac.uk/people/spetridis), [Maja Pantic](https://ibug.doc.ic.ac.uk/people/mpantic).

## Introduction

This is the respository of [Visual Speech Recognition for Multiple Languages](https://arxiv.org/abs/2202.13084), which is the successor of [End-to-End Audio-Visual Speech Recognition with Conformers](https://arxiv.org/abs/2102.06657). The repostitory is mainly based on [ESPnet](https://github.com/espnet/espnet). In this repository, we provide state-of-the-art algorithms for end-to-end visual speech recognition in the wild.

<details closed>
<summary>Major features</summary>

- **Modular Design**

    The repository is composed of face tracking, pre-processing, and acoustic/visual encoder backbones.

- **Support of Benchmarks for Speech Recognition**

    The repository provides state-of-the-art performance for speech recognition datasets.

- **Support of Extraction of Representations or Mouth Region Of Interest**

    The repository directly supports extraction of speech representations or mouth region of interests (ROIs).

- **Support of Recognition of Your Own Videos**

    The repository supports performing visual speech recognition for your own videos.

</details>

## Demo

English -> Mandarin -> Spanish   |    French -> Portuguese -> Italian  |
:-------------------------------:|:------------------------------------:
<img src='doc/vsr_1.gif' title='vsr1' style='max-width:320px'></img>  |  <img src='doc/vsr_2.gif' title='vsr2' style='max-width:320px'></img>  |

<div align="center">

[Youtube](https://youtu.be/FIau-6JA9Po) |
[Bilibili](https://www.bilibili.com/video/BV1Wu411D7oP)
</div>


## Installation
### How to Install Environments
1. Clone the repository into a directory. We refer to that directory as *`${lipreading_root}`*.

```Shell
git clone https://github.com/mpc001/Visual_Speech_Recognition_for_Multiple_Languages
```

2. Install [PyTorch](https://pytorch.org) (**>=1.8.0**)

3. Install other packages.

```Shell
pip install -r requirements.txt
```

### How to Prepare Models and Landmarks

- **Model.** Download a model from [Model Zoo](./models/README.md).
    - For models trained on the CMU-MOSEAS dataset, which contains multiple languages, please unzip them into *`${lipreading_root}/models/${dataset}/${language_code}`* folder (e.g. *`${lipreading_root}/models/CMUMOSEAS/pt`*).
    - For models trained on a dataset with single language, please unzip them into *`${lipreading_root}/models/${dataset}`* folder.

- **Language Model.** The performance can be improved in most cases by incorporating an external language model. Please download a language model from [Model Zoo](./models/README.md).
    - For a language model trained for the CMU-MOSEAS dataset, please unzip them into *`${lipreading_root}/language_models/${dataset}/${language_code}`* folder.
    - For a language model trained for datasets with single language, please unzip them into *`${lipreading_root}/language_models/${dataset}`* folder.

- **Tracker [option].** If you intend to test your own videos, additional packages for face detection and face alignment need to be pre-installed, which are provided in the [tools](./tools) folder.

- **Landmarks [option].** If you want to evaluate on benchmarks, there is no need to install the tracker. Please download pre-computed landmarks from [Model Zoo](#Model-Zoo) and unzip them to *`${lipreading_root}/landmarks/${dataset}`* folder.

## Recognition

- We call a path name (.ini) that includes configuration information as *`<CONFIG-FILENAME-PATH>`*. We put configuration files in *`${lipreading_root}/configs`* folder by default.

- We call a path name (.ref) that includes labels information as *`<LABELS-FILENAME-PATH>`*.
    - For the CMU-MOSEAS dataset and Multilingual TEDx dataset, which include multiple languages, we put labels files (.ref) in *`${lipreading_root}/labels/${dataset}/${language_code}`* folder.
    - For datasets with single language, we put label files in *`${lipreading_root}/labels/${dataset}`* folder.

- We call original dataset directory as *`<DATA-DIRECTORY-PATH>`*, and the path name of a single original video as *`<DATA-FILENAME-PATH>`*.

- We call landmarks diectory as *`<LANDMARKS-DIRECTORY-PATH>`*. We assume the default directory is *`${lipreading_root}/landmarks/${dataset}/${dataset}_landmarks`*.

- We use CPU for inference by default. If you want to speed up the decoding process, please consider
    -   adding a command-line argument about the **GPU** option (e.g. *`--gpu-idx <GPU_ID>`*). *`<GPU_ID>`* is the ID of your selected GPU, which is a 0-based integer.
    -   setting *`beam_size`* in the configuration filename (.ini) *`<CONFIG-FILENAME-PATH>`* to a small value (e.g. 5) in case you see the OOM case.

### Run Benchmarks

- We assume videos for [desired dataset](#Model-Zoo) have been downloaded to the dataset directory *`<DATA-DIRECTORY-PATH>`* and landmarks have been unzipped to the landmark directory *`${lipreading_root}/landmarks/${dataset}`*.

```Shell
python main.py --config-filename <CONFIG-FILENAME-PATH> \
               --labels-filename <LABELS-FILENAME-PATH> \
               --data-dir <DATA-DIRECTORY-PATH> \
               --landmarks-dir <LANDMARKS-DIRECTORY-PATH>
```

### Test Your Own Video (Single Video)

- The frame per seconds (fps) of your video should match the input *`v_fps`* in the configuration file.

```Shell
python main.py --config-filename <CONFIG-FILENAME-PATH> \
               --data-filename <DATA-FILENAME-PATH> \
```

### Extract Mouth ROIs or Speech Representations

- Mouth ROIs can be extracted by setting *`<FEATS-POSITION>`* to *`mouth`*. The mouth ROIs will be saved to *`<OUTPUT-DIRECTORY-PATH>`* or *`<OUTPUT-FILENAME-PATH>`* with the .avi file extension.

- Speech representations can be extracted from the top of ResNet-18 (512-D) or Conformer (256-D) by setting *`<FEATS-POSITION>`* to *`resnet`* or *`conformer`*, respetively. The representations will be saved to *`<OUTPUT-DIRECTORY-PATH>`* or *`<OUTPUT-FILENAME-PATH>`* with the .npz file extension.

- The *`${lipreading_root}/outputs`* folder can be used to save the mouth ROIs or speech representations.

* To extract mouth ROIs or speech representations from benchmarks.

```Shell
python main.py --config-filename <CONFIG-FILENAME-PATH> \
               --labels-filename <LABELS-FILENAME-PATH> \
               --data-dir <DATA-DIRECTORY-PATH> \
               --landmarks-dir <LANDMARKS-DIRECTORY-PATH> \
               --feats-position <FEATS-POSITION> \
               --dst-dir <OUTPUT-DIRECTORY-PATH>
```

* To extract mouth ROIs or speech representations from a single video file.

```Shell
python main.py --config-filename <CONFIG-FILENAME-PATH> \
               --data-filename <DATA-FILENAME-PATH> \
               --feats-position <FEATS-POSITION> \
               --dst-filename <OUTPUT-FILENAME-PATH>
```

## Model Zoo

### Overview
It supports a number of datasets for speech recognition:
- [x] [Lip Reading Sentences 2 (LRS2)](https://www.robots.ox.ac.uk/~vgg/data/lip_reading/lrs2.html)
- [x] [Lip Reading Sentences 3 (LRS3)](https://www.robots.ox.ac.uk/~vgg/data/lip_reading/lrs3.html)
- [x] [Chinese Mandarin Lip Reading (CMLR)](https://www.vipazoo.cn/CMLR.html)
- [x] [CMU Multimodal Opinion Sentiment, Emotions and Attributes (CMU-MOSEAS)](http://immortal.multicomp.cs.cmu.edu/cache/multilingual)
- [x] [GRID](http://spandh.dcs.shef.ac.uk/gridcorpus)
- [x] [Lombard GRID](http://spandh.dcs.shef.ac.uk/avlombard)
- [x] [TCD-TIMIT](https://sigmedia.tcd.ie)

### Evaluation

We provide landmarks, language models, models for each datasets. Please see the [models](./models/README.md) page for details.

## Citation

If you find this code useful in your research, please consider to cite the following papers:

```bibtex
@article{ma2022visual,
  title={{Visual Speech Recognition for Multiple Languages in the Wild}},
  author={Ma, Pingchuan and Petridis, Stavros and Pantic, Maja},
  journal={{arXiv Preprint: 2202.13084}},
  year={2022}
}
```

## License

It is noted that the code can only be used for comparative or benchmarking purposes. Users can only use code supplied under a [License](./LICENSE) for non-commercial purposes.

## Contact

```
[Pingchuan Ma](pingchuan.ma16[at]imperial.ac.uk)
```
