# Intra-Picture Prediction in HEVC


## Abstract
基本步骤

    reference sample array construction
    sample prediction
    post-processing

要求

    computational requirements in both the encoder and decoder

mode--scenario

    angular prediction--objects with directional structures
    planar prediction and DC prediction modes--smooth image areas

## 1 Introduction

分类：


    angular prediction(2-34)--objects with directional structures
    planar prediction(0) and DC prediction(1) modes--smooth image areas
    总mode：35


资源：

    reference samples from the adjacent reconstructed blocks
    transform block size

提升性能

    different filtering alternatives for pre-processing the reference samples
    post-processing step to refine the sample surface continuity on the block boundaries
## 2 Reference Sample Generation

intra实现方式

    外推样本值

相比于264的区别：

     introduces a reference sample substitution process
     -use the complete set of intra prediction modes regardless of the availability of the neighboring 
     reference samples，264只允许DC prediction

     adaptive filtering process that can pre-filter the reference samples  
        to increase the diversity of the available predictors
        according to
            intra prediction mode
            block size
            directionality


### 2.1 Reference Sample Substitution

作用：

    产生更多有用的数据

某些reference samples不能用于预测的原因：

    samples outside of the picture
    slice or tile are considered unavailable for prediction

特殊情况：

    所有reference sample都不可用--substituted by a nominal average sample value for a given bit depth 
    至少一个可用：in clock-wise direction填充

        过程：
        纵向最下方如不可用，被顺时针的第一个可用值替代
        纵向每个不可用值被下方值替代
        横向每个不可用值被左方的值替代


### 2.2 Filtering Process of Reference Samples


## 3 Intra Sample Prediction


### 3.1 Angular Prediction


#### 3.1.1 Angle Definitions


#### 3.1.2 Reference Row Extension for the Negative Prediction Directions


#### 3.1.3 Sample Prediction for Angular Prediction Modes


### 3.2 DC Prediction


### 3.3 Planar Prediction


### 3.4 Post-processing for Predicted Samples


## 4 Intra Mode Coding


### 4.1 Prediction of Luma Intra Mode


### 4.2 Derived Mode for Chroma Intra Prediction


### 4.3 Syntax Design for Intra Mode Coding


## 5 Encoding Algorithms


## 6 Coding Efficiency and Decoder Complexity


### 6.1 Coding Efficiency


### 6.2 Decoder Complexity


## 7 Main Still Picture Profile and Its Applications


## 8 Summary of Differences from H.264/AVC