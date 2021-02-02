## Multiview Consistency as Supervisory Signal for Learning Shape and Pose Prediction

**Authors** : Shubham Tulsiani, Alexei A. Efros, Jitendra Malik
(University of California, Berkeley) \
**Paper link** : ([here](https://arxiv.org/pdf/1801.03910.pdf)) \
**Code link** : ([here](https://github.com/shubhtuls/mvcSnP))

***Overview*** : Single view shape and pose prediction without using direct supervision.

- **What ?** - leverage multiview observations form unknow poses and consequently learn to predict shape andd shape in a view-agnostic frame During training, inputs are multiple poses and multiple shapes (both unknown) of an object. The model eventually learns to predict the shape and pose of an object, given a single view image. 
During testing, the input is a single input image of an object and the output is a predicted shape viewed and predicted pose

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9452d9d6-bc3e-4761-9cdd-264d979688a4/Untitled.png](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/9452d9d6-bc3e-4761-9cdd-264d979688a4/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210202%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210202T232409Z&X-Amz-Expires=86400&X-Amz-Signature=8884f5830c0c2b28358c5daca2f61cfa7b23c04c707fa19ebfc07ee7d8f6d53c&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

- **How ?** Leverage the principle of multi-view consistency: a common geometry, observed from different perspectives can consistently explain multiple views of an instance. During training, enforce geometric consistency between predicted shape from the two views of the same instances.
- **Evolutionary Inspiration -** Humans learn to perceive the 3D structure of an object from a single view of an object by using previous knowledge of the 3D structure of previously seen objects

***Previous research:***

- **Geometry based reconstruction technique (SFM techniques)** : 
— Point Cloud Representations :These techniques aim to recover the shapes as 3D point clouds and camera pose for each image.
— Volumetri Representation or Voxel  of Range Images ([here](http://homepages.inf.ed.ac.uk/rbf/CVonline/LOCAL_COPIES/CANTZLER2/range.html)) or multiple color images or additional signals (depth or semantics) 
However, a drawback is these techniques require multiple views of the *same instance*. Also, these techniques works for specific instances (do not generalise).
- **Learning based methods** : Some approaches rely on full-supervision and require known 3D shape for each training object. It fails at the impracticality of gathering enough data of shapes of objects of a large number of wide variety of objects. 
— Information bottleneck
— Intermediate task that do not directly encourage learning of shapes, rather a temporal or spatial structure
    - [ ]  Some papers try to model around deformation around mean shape from annotations
    - [ ]  Some papers try to build around generation model with mutli-view images
    - [ ]  Some papers remove the need of supervised depth prediction from single view images and instead use the idea of multiple view images for training (leverage stereo images to learn about the depth perception). The idea of relaxing known relative pose between muliple-view images and instread learn single-view depth images is taken from Zhou et. al. ([here](https://arxiv.org/abs/1704.07813)).
    - [ ]  Some papers remove the need of fuly-supervised voxel occupancy representations from a single image. Some papers [like](https://arxiv.org/pdf/1801.03910.pdf#page=9&zoom=100,66,814)  use pose-aware 3D voxelised shape of an object from single natural imag using instance segmentation masks

**Architecture**

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f8864020-23fb-4c76-b309-c04d8abe3dec/Untitled.png](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/f8864020-23fb-4c76-b309-c04d8abe3dec/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210202%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210202T232503Z&X-Amz-Expires=86400&X-Amz-Signature=4809964d26a66fbae201b88331508b9d69dc873659b373f6e99442955ceed419&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

Learn shape and pose prediction by enforcing consistency between predictiona dn available views of observations

```
Given an Image I1 and shape predictor S, we have Fs(I1)
Given an Image I2 and pose predictor P, we have Fp(I2)
Technique: Enforce the consistency between the Fs(I1) and Fp(I2) such that the Fs(I1) should be consistent with with a depth mask observation from Fp(I2)

 That is, Fp(Fs(I1)) should be consistet with 
```