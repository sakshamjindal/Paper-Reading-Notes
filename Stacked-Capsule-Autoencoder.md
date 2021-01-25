Paper : **Stacked Capsule Autoencoders**

**Motivation**

Convolutional Neural Networks (CNN) work when feature is useful in one image location, the same feature is likely to be useful in other locations. To account for other effects of viewpoint changes by replicating features across scales, orientations and other affine degree of freedom, we need high dimenstional feature maps.

To replicate features across the viewpoint changes like scale , orientation and other degrees of freedome is to explicily learn transformation betwenn the natural coorinate frame of a whole obect and natural coordinates of each of its parts. 

An object part relationship is viewpoint invariant, approximately constant and could be easily coded by learned weights. The relative cooordinates of an obect (or a part) with respect to the viewer change with the the viewpoint.

The pose of an object is representated by its relationship with respect to its viewer. 

**Overview**

![Main Image](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/0d2e8860-7434-4a4f-abd6-bb0050bf2b68/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210125%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210125T205848Z&X-Amz-Expires=86400&X-Amz-Signature=345ca8d30a530b59d8abad573dacf293e1ec25c13251e52b1929a51e9f44d085&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

Stacked Capsule Autoencoders capture spatial relationship between whole obects and their parts when trained on unlabelled data

Two stages:

- Part Capsule Autoencoder (PCAE) :

     — segments an image into its consituent parts

     — infers their poses

       —- reconstructs the image by appropriately arranging affine-transformed part templates

- Object Capsule Autoencoder: tries to organise the discoverred parts and their poses into smaller set of objects. These objects then try to reconstruct the part poses using a separate mixture of predictions for each part. Every object capsule contributes components to each of these mixtures by multiplying it's pose — the object viewer relationship (OV) by the relevant object-part relationship

**Architecture**

![Architectures](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/72dbec03-24d1-4a5a-8021-efc2d979d04f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210125%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210125T205931Z&X-Amz-Expires=86400&X-Amz-Signature=1fd89a3c4a106a0287f9feec33d083dc9272d2fd99c56572af0b8c9eec50c0bd&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

**Step 1** : Part Encoder : 

Aim:
— Input is an image (hxwxc). 
— Output: M part capsules haveing their poses (x_m; relationships of the parts to the viewer; their pose; six-dimensional vector; two rotatio; two translation, scale and shear), presence probabilities (d_m; range of 0 to 1) and special feature (z_m; used to alter the templates in an input-dependent manner and also, inform the OCAE about unique aspect of the corresponding part like occlusion and relationhip to other parts; 16 dimensional )

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d7ca0740-0bfc-4c62-8d8c-fd795a3b5c1c/Untitled.png](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/d7ca0740-0bfc-4c62-8d8c-fd795a3b5c1c/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210125%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210125T210016Z&X-Amz-Expires=86400&X-Amz-Signature=d37ae46f2ed7f734dbe5177e53efe38c5b71c8f7a608c50ff400a625dacffacd&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

**Step 2:**  OCAE decoder: Permutation Invariant transformer
Aim : After having found parts and their parameters (pose, features and presence probabilities), dicovert obects that could be composed out of them)
Input : Poses (x_m), special features (z_m) and flattened templates (T_m)
Output: K object capsules with feature vector, object-pose and presence probabilites

Steps: Encode input parts intp K obect capsules, each object capsule containing feature vector (c_k), its presence probabilities, and 3x3 object-viewer relationship matrix, which represents the affine transformation between the viewer and the object (O-V)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/886bda1b-aa8d-479e-8f1f-56e6b68429dc/Untitled.png](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/886bda1b-aa8d-479e-8f1f-56e6b68429dc/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210125%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210125T210111Z&X-Amz-Expires=86400&X-Amz-Signature=20051b5b0c05e0ed9c5a5235c9df89df573c48290373a0a20107bdc51cddab8f&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

**Step 3:** OCAE decoder : 
Aim:
Input: Outputs of the OCAE encoder 
Output: Each object capsule with N candidates with conditional probabilities (that a given a part candidate exists, an assosiated scalar standard deviation) and 3x3 object-part relationship matrix (affine transformaton between te object capsule and candidate part)

Steps: 
1) K MLPs (each object witih one MLP to predict N≤M part candidates from the capsule feature vectors c_k and O-V relationsip). 
2) Candidate predictions µk,n are given by the product of the object capsule OV and the candidate OP matrices
3) parts disoverred by PCAE have independependent entities and this part-pse is explanied an independent mixture of predictions from object capsules- where every object-capsules
4) The part capsule m can be assigned to the object capsule k by looking at the mixture component responsibility, 

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/010de097-efe7-4528-99be-02d82b4d18c7/Untitled.png](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/010de097-efe7-4528-99be-02d82b4d18c7/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210125%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210125T210235Z&X-Amz-Expires=86400&X-Amz-Signature=53e0913de7895945c8ebb47b8f48b74a747929bb18e5c81161473d5fc703b96d&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9311afd6-28ea-403e-9398-35e227fc5c86/Untitled.png](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/9311afd6-28ea-403e-9398-35e227fc5c86/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210125%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210125T210247Z&X-Amz-Expires=86400&X-Amz-Signature=fb17541e69f72b74833d44f9594e19aec569ce3229ce4bbc9d01946edd2b6436&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/622f09c0-3ff6-4957-a5fe-cac75fe17694/Untitled.png](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/622f09c0-3ff6-4957-a5fe-cac75fe17694/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210125%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210125T210301Z&X-Amz-Expires=86400&X-Amz-Signature=a2d590f4ad76ccc0540757ac9b6a106511d5d729ae9c2d1269259867ba2e9755&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ccb83139-c351-42b1-99be-c7da224f66b1/Untitled.png](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/ccb83139-c351-42b1-99be-c7da224f66b1/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210125%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210125T210314Z&X-Amz-Expires=86400&X-Amz-Signature=89a3e59aa597a60d9057afd5fd0e6dc9e5c99b50e655ba4b416c5c5b06411a53&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ac77fe3c-f008-4b0f-81da-3999e7f0971e/Untitled.png](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/ccb83139-c351-42b1-99be-c7da224f66b1/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210125%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210125T210314Z&X-Amz-Expires=86400&X-Amz-Signature=89a3e59aa597a60d9057afd5fd0e6dc9e5c99b50e655ba4b416c5c5b06411a53&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

**Step 5:** Losses

Stacked Capsule Autoencoders are trained to maximise pixel and part log-likelihoods (Lll =
log p(y) + log p(x1:M))

We want the model to learn in such a way that different sets of part-capsules are used for different input examples and to specialise object capsules to particular arrangement of parts. If not constrained, they tend to either use of all of the part and object capsules to expain evey data example or collaspe onto always using the dame subset of capsules, regardless of the input. 

— Prior Sparsity : consider a batch of training examples and sum up the presence probabilites of the obect capsule k among different training examples and consider for sum of the presence probabilites for a given example.