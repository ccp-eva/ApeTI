# ApeTI
ApeTI: A Thermal Image Dataset for Face and Nose Segmentation with Apes

ApeTI dataset was built with the aim of retrieving physiological signals such as heart rate, breath rate, and cognitive load from thermal images of great apes. We want to develop computer vision tools that psychologists and animal behaviour researchers can use to retrieve physiological signals non-invasively.
Our goal is to increase the use of thermal imaging modality in the community and avoid using more invasive recording methods to answer research questions. The first step to retrieving physiological signals from thermal imaging is their spatial segmentation to then analyse the time series of the regions of interest.
For this purpose, we present a Thermal Imaging dataset based on recordings of chimpanzees with their face and nose annotated using a bounding box and nine landmarks.
The face and landmarks' locations can then be used to extract physiological signals. The dataset was acquired using a thermal camera at the Leipzig Zoo. 
Juice was provided in the vicinity of the camera to encourage the chimpanzee to approach and have a good view of the face. Several computer vision methods are presented and evaluated on this dataset.
We reach mAPs of 0.74 for face detection and 0.98 for landmark estimation using our proposed combination of the Tifa and Tina models inspired by the HRNet models.
A proof of concept of the model is presented for physiological signal retrieval but requires further investigation to be evaluated.
The dataset and the implementation of the Tina and Tifa models are available to the scientific community for performance comparison or further applications.

## Dataset
The thermal images have been recorded at the Wolfgang Köhler Primate Research Center (WKPRC) from the Leipzig Zoo affiliated to the Max Planck Institute for Evolutionary Anthropology (MPI EVA). 
The images are RGB encoded using an InfraTec Variocam HD camera with a resolution of 1024x768 pixels. Six different chimpanzees were filmed alone in a testing room through the mesh across a total of 26 sessions. A juice dispenser was located close to the camera to encourage the chimpanzee to approach in order to acquire a good view of the face and the nose. Fifty frames were manually extracted from each video for annotation. Extremely blurry frames or frames without a chimpanzee in the field of view were discarded.

The temperature information was saved using a JPEG extension and a JET colormap. Sadly the real temperature was lost during these sessions. Nevertheless, using HSV color distance, we map the RGB information to temperature using the color bar on the right side of the sample below. This transformation uses a minimum temperature of 28°C and a maximum temperature of 43°C which leads to a temperature resolution of 0.0625°C. Even if the calibration changes across frames, using the same color bar allows us to retrieve qualitative temperature information and gradients between the different regions. For this dataset, we consider this approximation acceptable because of the property of CNNs to capture gradients.
The original images and transformation details are available upon request.

![](dataset_sample.png)

## Download
The dataset is available on a dedicated [Zenodo repository](https://zenodo.org/records/11192141).
You may download it using your terminal and check its consistency.
A step-by-step instruction.

1. Make sure to have cloned this repo:
```
git clone https://github.com/ccp-eva/ApeTI.git
cd ApeTI
```

2. Download the 11192141.zip file from Zenodo:
```
wget --content-disposition https://zenodo.org/api/records/11192141/files-archive
```
Alternatively, you can use your browser using this [link](https://zenodo.org/api/records/11192141/files-archive).

3. Check your download consistency with md5sum:
```
md5sum -c 11192141.md5
```
If it fails, redo step 2.

4. unzip the downloaded archive:
```
unzip 11192141.zip
```

You should obtain a data.zip archive, with the md5sum files, the weights of the models and the data agreement regarding the usage of this dataset.

5. Check the data.zip consistency with md5sum:
```
md5sum -c data.md5
```
If it fails, redo step 4.

6. unzip the data.zip archive:
```
unzip data.zip
```

You should obtain a data folder with the thermal images encoded using [NPY format](https://numpy.org/doc/stable/reference/generated/numpy.lib.format.html) and the annotations under the [COCO annotation JSON format](https://cocodataset.org/#format-data).

7. Check the data folder content consistency with md5sum:
```
md5sum -c data_checksum.txt
```
If it fails, redo step 6.

You are now ready to use the dataset. The thermal images are encoded using nympy format. You may visualize them as images but they need to be normalized first. Several methods exist such as using min and max of the image or pre-defined values. Refer to the paper in the "Cite this work" section for more guidance.

## Leaderboard

We evaluate the results on the test set using the mean Average Precision metric (mAP) for both detection and landmark regression tasks.
The mAP uses several Intersections over Union (IoU) and Object Keypoint Similarity (OKS): mAP at IoU=.50:.05:.95 for  and mAP at OKS=.50:.05:.95 for landmarks regression. 

### Face Detection

For face detection, the mAP uses ten Intersections over Union (IoU) thresholds: IoU=.50:.05:.95.

 **Method**                | **mAP**          | **mAP 50**          | **mAP 75**          | **mIoU** 
---------------------------|------------------|---------------------|---------------------|-------------------
 GT+10%                    | .7               | 1                   | 1                   | .836              
 GT$-10%                   | .7               | 1                   | 1                   | .821              
 **Tifa**                  | **.744**         | **.980**            | **.902**            | **.868**           
 no ROI                    | 0                | 0                   | 0                   | .128              
 Thresh (35.6)             | 0                | 0                   | 0                   | .183              
 BlazeFace                 | .007             | .037                | 0                   | .398              
 Thresh (36.5) + BlazeFace | .136             | .512                | .025                | .576              

### Landmark Regression

For landmark regression, the mAP uses ten Object Keypoint Similarity (OKS) thresholds: IoU=.50:.05:.95.

#### With Ground Truth Face Location

 **Method**                           | **mAP**  | **AP 50** | **AP 75** | **mOKS** 
--------------------------------------|----------|-----------|-----------|----------
 GT + Tina                            | .989     | .989      | .989      | .524     
 GT+10% + Tina                        | .989     | .989      | .989      | .523     
 **GT-10% + Tina**                    | **.995** | **1**     | **1**     | **.524** 

#### Without Ground Truth Face Location

 **Method**                           | **mAP**  | **AP 50** | **AP 75** | **mOKS** 
--------------------------------------|----------|-----------|-----------|----------
 no ROI + Tina                        | .987     | .988      | .988      | .532     
 **Thresh (29.8) + Tina**             | **.989** | **.990**  | **.990**  | **.532** 
 Tifa + Tina                          | .980     | .980      | .980      | .524     
 Tifa+10% + Tina                      | .980     | .980      | .980      | .523     
 Tifa-10% + Tina                      | .980     | .980      | .980      | .524     
 Thresh (29.8) + Tifa + Tina          | .952     | .953      | .953      | .518     
 BlazeFace + FaceMesh                 | .336     | .652      | .312      | .398     
 Thresh (36.5) + BlazeFace + FaceMesh | .566     | .819      | .617      | .41      

## Submit your results

Do not hesitate to share your methods' performance so we can update the leaderboard.

## Tina and Tifa
The implementations of the models have been carried out using [MMLab](https://github.com/open-mmlab). We share the configuration files along with our extra pipeline functions and dataset registration in the config folder.

The weights of both models are available on our [Nexcloud](https://share.eva.mpg.de/index.php/s/MnD33qD9ZxCYdJL) with password: xkL4ezPMsw

## To cite this work

### Paper:

Martin, P.-E., Kachel, G., Wieg, N., Eckert, J., & Haun, D. ApeTI: A Thermal Image Dataset for Face and Nose Segmentation with Apes. Signals 2024, 5, 147-164. https://doi.org/10.3390/signals5010008 
```
@Article{signals5010008,
 AUTHOR = {Martin, Pierre-Etienne and
           Kachel, Gregor and
           Wieg, Nicolas and
           Eckert, Johanna and
           Haun, Daniel B. M.},
 TITLE = {ApeTI: A Thermal Image Dataset for Face and Nose Segmentation with Apes},
 JOURNAL = {Signals},
 VOLUME = {5},
 YEAR = {2024},
 NUMBER = {1},
 PAGES = {147--164},
 URL = {https://www.mdpi.com/2624-6120/5/1/8},
 ISSN = {2624-6120},
 ABSTRACT = {The ApeTI dataset was built with the aim of retrieving physiological signals such as heart rate, breath rate, and cognitive load from thermal images of great apes. We want to develop computer vision tools that psychologists and animal behavior researchers can use to retrieve physiological signals noninvasively. Our goal is to increase the use of a thermal imaging modality in the community and avoid using more invasive recording methods to answer research questions. The first step to retrieving physiological signals from thermal imaging is their spatial segmentation to then analyze the time series of the regions of interest. For this purpose, we present a thermal imaging dataset based on recordings of chimpanzees with their face and nose annotated using a bounding box and nine landmarks. The face and landmarks’ locations can then be used to extract physiological signals. The dataset was acquired using a thermal camera at the Leipzig Zoo. Juice was provided in the vicinity of the camera to encourage the chimpanzee to approach and have a good view of the face. Several computer vision methods are presented and evaluated on this dataset. We reach mAPs of 0.74 for face detection and 0.98 for landmark estimation using our proposed combination of the Tifa and Tina models inspired by the HRNet models. A proof of concept of the model is presented for physiological signal retrieval but requires further investigation to be evaluated. The dataset and the implementation of the Tina and Tifa models are available to the scientific community for performance comparison or further applications.},
 DOI = {10.3390/signals5010008}
}

```

### Dataset:

Martin, P.-E., Kachel, G., Wieg, N., Eckert, J., & Haun, D. (2024). ApeTI dataset and models weights [Data set]. Zenodo. https://doi.org/10.5281/zenodo.11192141

```
@dataset{martin_2024_11192141,
  author       = {Martin, Pierre-Etienne and
                  Kachel, Gregor and
                  Wieg, Nicolas and
                  Eckert, Johanna and
                  Haun, Daniel B. M.},
  title        = {ApeTI dataset and models weights},
  month        = may,
  year         = 2024,
  publisher    = {Zenodo},
  doi          = {10.5281/zenodo.11192141},
  url          = {https://doi.org/10.5281/zenodo.11192141}
}
```
### Software:

Pierre-Etienne Martin. (2024). ccp-eva/ApeTI: Software (v1.0.0). Zenodo. https://doi.org/10.5281/zenodo.11204561

```
@software{pierre_etienne_martin_2024_11204561,
  author       = {Pierre-Etienne Martin},
  title        = {ccp-eva/ApeTI: Software},
  month        = may,
  year         = 2024,
  publisher    = {Zenodo},
  version      = {v1.0.0},
  doi          = {10.5281/zenodo.11204561},
  url          = {https://doi.org/10.5281/zenodo.11204561}
}
```

Get in touch with us directly if you have any questions: pierre_etienne_martin (at) eva.mpg.de.

## License

CC-BY. Please read the data agreement before usage.

<img src="by.png" width=15%>



