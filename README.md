# 2D-Human-Pose-Estimators-Strenghts-and-Caveats

## Introduction
Perform a deeper analysis of your model on the validation set of the COCO Keypoint Detection Task 2017.

To perform this deeper analysis the COCO validation set corrected the occlusion labelling and added new labels. The new labels added are:
- Occlusion types (per keypoint):
  - Self occlusion
  - Other person occlusion
  - Environment occlusion
- Truncation by the image border (per person)
- Person resolution (per person)
- Wrong annotations

Also, an analysis is executed of the influence of training models on the train set with and without one type of wrongly annotated data (76.6% of mistakes in validation set contain this type of mistake).

## Setup
- Download person_keypoints_val2017.json and 2017 Val images from https://cocodataset.org/#download. And put these in the coco/data folder (unzipped version of 2017 Val images).
- Get the models from https://drive.google.com/drive/folders/1J0S0F9Oto_GJWsSeZPrezgrwUPDZovy_?usp=sharing, or download the models directly from the source https://github.com/leoxiaobin/deep-high-resolution-net.pytorch, https://github.com/HRNet/DEKR, https://github.com/ilovepose/DarkPose and put these in the models folder
- Filtered train set annotations and the models can also be downloaded from https://drive.google.com/drive/folders/1J0S0F9Oto_GJWsSeZPrezgrwUPDZovy_?usp=sharing.

## About the labels
Note: the example images contain the original labels.

**Occlusion** <br />
The occlusion labels are corrected, because many occlusion labels are inaccurate:
![image](https://user-images.githubusercontent.com/63635825/174550627-655c1f68-94ff-4082-8ecc-f2267b36bfba.png)

**Occlusion types** <br />
There are different ways keypoints can be occluded. We separated these ways by three categories: self occlusion, other person occlusion and environment occlusion which are respectively shown below. The COCO 

**Self occlusion** <br />
The COCO dataset only counts other person occlusion and environment occlusion as occlusion, but self occlusion can also lead to worse performance. Hence, self occlusion is also added.
![image](https://user-images.githubusercontent.com/63635825/174561185-12ed0ce6-51e4-4aa7-99c1-a286bcf2954f.png)


**Other person occlusion** <br />
Other person occlusion are keypoints occluded by other people.
![image](https://user-images.githubusercontent.com/63635825/174561216-06fe3630-ec6a-4169-acd5-a41e6497f833.png)


**Environment occlusion** <br />
This type of occlusion is any type of occlusion which is not self- or other person-occlusion. Hence, any object, helmet, blanket, etc.
![image](https://user-images.githubusercontent.com/63635825/174561247-0d6bd9e7-4671-4105-bd4e-c93f30c36c6c.png)


**Truncation by the image border** <br />
Truncation by the image border happens when the image is cutoff in such a way such that only a subset of the person is present in the image.
![image](https://user-images.githubusercontent.com/63635825/174556198-6838f68e-d00c-4dc4-8432-adb1065470bb.png)

**Person resolution examples** <br />
These are calculated by taking the area of a bounding box generated by the minimum and maximum coordinates of the keypoints.
![image](https://user-images.githubusercontent.com/63635825/177355555-47c7ccad-f58b-4426-8ac7-d40f1d221bcc.png)

**Wrong annotations** <br />
The COCO validation set contains a lot of wrong annotations. From the validation set 107 people are removed to get a more accurate performance measure. These mistakes consist of wrong keypoint mappings, labelling of objects and wrong people mapping. The latter contains the right keypoints, but leads to problems during the evaluation phase. Since, the Euclidian distance is used during the evaluation between the ground truth keypoints and the predicted keypoints on the segmented person. This is also the majority mistake and is filtered for the filtering experiment.
![image](https://user-images.githubusercontent.com/63635825/174558124-f4b864c1-a65f-4be0-8b29-6445c80e2085.png)

## Results
Results gathered for:
- ResNet-152
- HRNet-W48
- DEKR-W48 (with multi-scale test)
- HRNet-W48 + DarkPose


### General results 
on the validation set where the wrong annotations are removed
| Method       | Input size   |    AP |   AP50 |   AP75 |    AR |   AR50 |   AR75 |
|:-------------|:-------------|------:|-------:|-------:|------:|-------:|-------:|
| ResNet-152   | 384x288      | 0.752 |  0.905 |  0.821 | 0.805 |  0.945 |  0.868 |
| HRNet-W48    | 384x288      | 0.771 |  0.914 |  0.837 | 0.820 |  0.949 |  0.879 |
| DEKR-W48 ms  | 640x640      | 0.732 |  0.892 |  0.796 | 0.786 |  0.934 |  0.843 |
| DarkPose-W48 | 384x288      | 0.777 |  0.914 |  0.843 | 0.825 |  0.949 |  0.884 |

### Occlusion results
| Method       | Input size   | Variable   |    AP |   AP50 |   AP75 |    AR |   AR50 |   AR75 |
|:-------------|:-------------|:-----------|------:|-------:|-------:|------:|-------:|-------:|
| ResNet-152   | 384x288      | Visible    | 0.794 |  0.915 |  0.856 | 0.847 |  0.955 |  0.901 |
| ResNet-152   | 384x288      | Occluded   | 0.554 |  0.803 |  0.577 | 0.666 |  0.878 |  0.694 |
| HRNet-W48    | 384x288      | Visible    | 0.811 |  0.922 |  0.869 | 0.859 |  0.956 |  0.908 |
| HRNet-W48    | 384x288      | Occluded   | 0.576 |  0.825 |  0.611 | 0.685 |  0.891 |  0.722 |
| DEKR-W48 ms  | 640x640      | Visible    | 0.776 |  0.902 |  0.839 | 0.829 |  0.945 |  0.883 |
| DEKR-W48 ms  | 640x640      | Occluded   | 0.530 |  0.782 |  0.553 | 0.637 |  0.853 |  0.665 |
| DarkPose-W48 | 384x288      | Visible    | 0.817 |  0.923 |  0.872 | 0.863 |  0.957 |  0.910 |
| DarkPose-W48 | 384x288      | Occluded   | 0.589 |  0.833 |  0.624 | 0.695 |  0.896 |  0.733 |

### Occlusion type results
| Method       | Input size   | Variable    |    AP |   AP50 |   AP75 |    AR |   AR50 |   AR75 |
|:-------------|:-------------|:------------|------:|-------:|-------:|------:|-------:|-------:|
| ResNet-152   | 384x288      | Self        | 0.621 |  0.829 |  0.671 | 0.735 |  0.909 |  0.777 |
| ResNet-152   | 384x288      | Person      | 0.405 |  0.619 |  0.403 | 0.595 |  0.812 |  0.608 |
| ResNet-152   | 384x288      | Environment | 0.501 |  0.742 |  0.515 | 0.654 |  0.858 |  0.679 |
| HRNet-W48    | 384x288      | Self        | 0.636 |  0.849 |  0.690 | 0.745 |  0.915 |  0.790 |
| HRNet-W48    | 384x288      | Person      | 0.420 |  0.639 |  0.424 | 0.613 |  0.828 |  0.635 |
| HRNet-W48    | 384x288      | Environment | 0.538 |  0.771 |  0.566 | 0.683 |  0.877 |  0.718 |
| DEKR-W48 ms  | 640x640      | Self        | 0.594 |  0.811 |  0.642 | 0.715 |  0.896 |  0.760 |
| DEKR-W48 ms  | 640x640      | Person      | 0.380 |  0.599 |  0.374 | 0.536 |  0.766 |  0.542 |
| DEKR-W48 ms  | 640x640      | Environment | 0.510 |  0.750 |  0.523 | 0.644 |  0.854 |  0.670 |
| DarkPose-W48 | 384x288      | Self        | 0.645 |  0.849 |  0.700 | 0.752 |  0.917 |  0.799 |
| DarkPose-W48 | 384x288      | Person      | 0.428 |  0.650 |  0.440 | 0.628 |  0.842 |  0.664 |
| DarkPose-W48 | 384x288      | Environment | 0.550 |  0.775 |  0.581 | 0.692 |  0.880 |  0.727 |

### Truncation results
presence or absence of any truncation where k is the amount of keypoints truncated by the image border

| Method       | Input size   | Variable   |    AP |   AP50 |   AP75 |    AR |   AR50 |   AR75 |
|:-------------|:-------------|:-----------|------:|-------:|-------:|------:|-------:|-------:|
| ResNet-152   | 384x288      | k=0        | 0.779 |  0.923 |  0.850 | 0.824 |  0.958 |  0.890 |
| ResNet-152   | 384x288      | 0<k        | 0.629 |  0.783 |  0.683 | 0.767 |  0.919 |  0.821 |
| HRNet-W48    | 384x288      | k=0        | 0.795 |  0.929 |  0.860 | 0.837 |  0.960 |  0.899 |
| HRNet-W48    | 384x288      | 0<k        | 0.660 |  0.809 |  0.717 | 0.786 |  0.927 |  0.839 |
| DEKR-W48 ms  | 640x640      | k=0        | 0.766 |  0.917 |  0.828 | 0.803 |  0.944 |  0.860 |
| DEKR-W48 ms  | 640x640      | 0<k        | 0.561 |  0.715 |  0.617 | 0.752 |  0.915 |  0.808 |
| DarkPose-W48 | 384x288      | k=0        | 0.801 |  0.929 |  0.868 | 0.842 |  0.961 |  0.905 |
| DarkPose-W48 | 384x288      | 0<k        | 0.665 |  0.808 |  0.718 | 0.790 |  0.926 |  0.841 |

per truncation bin

| Method       | Input size   | Variable   |    AP |   AP50 |   AP75 |    AR |   AR50 |   AR75 |
|:-------------|:-------------|:-----------|------:|-------:|-------:|------:|-------:|-------:|
| ResNet-152   | 384x288      | 0<k<5      | 0.726 |  0.846 |  0.786 | 0.849 |  0.962 |  0.906 |
| ResNet-152   | 384x288      | 5<=k<9     | 0.480 |  0.623 |  0.523 | 0.782 |  0.949 |  0.844 |
| ResNet-152   | 384x288      | 9<=k<13    | 0.252 |  0.351 |  0.272 | 0.679 |  0.853 |  0.723 |
| ResNet-152   | 384x288      | 13<=k      | 0.058 |  0.104 |  0.056 | 0.582 |  0.805 |  0.625 |
| HRNet-W48    | 384x288      | 0<k<5      | 0.751 |  0.862 |  0.815 | 0.862 |  0.967 |  0.922 |
| HRNet-W48    | 384x288      | 5<=k<9     | 0.513 |  0.661 |  0.563 | 0.800 |  0.949 |  0.862 |
| HRNet-W48    | 384x288      | 9<=k<13    | 0.275 |  0.382 |  0.290 | 0.701 |  0.861 |  0.743 |
| HRNet-W48    | 384x288      | 13<=k      | 0.094 |  0.156 |  0.093 | 0.619 |  0.837 |  0.645 |
| DEKR-W48 ms  | 640x640      | 0<k<5      | 0.659 |  0.788 |  0.715 | 0.841 |  0.961 |  0.898 |
| DEKR-W48 ms  | 640x640      | 5<=k<9     | 0.377 |  0.525 |  0.424 | 0.758 |  0.943 |  0.820 |
| DEKR-W48 ms  | 640x640      | 9<=k<13    | 0.137 |  0.203 |  0.146 | 0.654 |  0.851 |  0.704 |
| DEKR-W48 ms  | 640x640      | 13<=k      | 0.030 |  0.050 |  0.032 | 0.579 |  0.801 |  0.625 |
| DarkPose-W48 | 384x288      | 0<k<5      | 0.757 |  0.861 |  0.816 | 0.868 |  0.965 |  0.924 |
| DarkPose-W48 | 384x288      | 5<=k<9     | 0.519 |  0.660 |  0.563 | 0.803 |  0.947 |  0.854 |
| DarkPose-W48 | 384x288      | 9<=k<13    | 0.283 |  0.387 |  0.299 | 0.704 |  0.861 |  0.751 |
| DarkPose-W48 | 384x288      | 13<=k      | 0.095 |  0.155 |  0.094 | 0.624 |  0.845 |  0.661 |

### Image resolution
Image resolution per person categorized as Low (pixels < 32^2), Medium (32^2 <= pixels < 96^2) and High (96^2 <= pixels)

| Method       | Input size   | Variable   |    AP |   AP50 |   AP75 |    AR |   AR50 |   AR75 |
|:-------------|:-------------|:-----------|------:|-------:|-------:|------:|-------:|-------:|
| ResNet-152   | 384x288      | Low        | 0.215 |  0.337 |  0.231 | 0.599 |  0.827 |  0.653 |
| ResNet-152   | 384x288      | Medium     | 0.695 |  0.859 |  0.766 | 0.771 |  0.929 |  0.839 |
| ResNet-152   | 384x288      | High       | 0.817 |  0.937 |  0.882 | 0.874 |  0.981 |  0.931 |
| HRNet-W48    | 384x288      | Low        | 0.231 |  0.356 |  0.247 | 0.626 |  0.841 |  0.682 |
| HRNet-W48    | 384x288      | Medium     | 0.714 |  0.872 |  0.784 | 0.784 |  0.932 |  0.852 |
| HRNet-W48    | 384x288      | High       | 0.837 |  0.946 |  0.896 | 0.889 |  0.984 |  0.939 |
| DEKR-W48 ms  | 640x640      | Low        | 0.198 |  0.322 |  0.212 | 0.556 |  0.791 |  0.606 |
| DEKR-W48 ms  | 640x640      | Medium     | 0.687 |  0.863 |  0.755 | 0.747 |  0.920 |  0.811 |
| DEKR-W48 ms  | 640x640      | High       | 0.790 |  0.905 |  0.847 | 0.864 |  0.971 |  0.913 |
| DarkPose-W48 | 384x288      | Low        | 0.238 |  0.361 |  0.253 | 0.630 |  0.846 |  0.691 |
| DarkPose-W48 | 384x288      | Medium     | 0.718 |  0.871 |  0.788 | 0.789 |  0.933 |  0.856 |
| DarkPose-W48 | 384x288      | High       | 0.845 |  0.946 |  0.901 | 0.895 |  0.983 |  0.944 |
