# GEOMetrics
This is a repository to reproduce the methods from the paper "GEOMetrics: Exploiting Geometric Structure for Graph-Encoded Objects". This project is a combination of new ideas for mesh generation, applied to reconstructing mesh objects from single images. The goal of this porject is to produce mesh objects which properly take advatage of graceful scaling properties of thier graph-based representation. 
 
 
<p align="center">
  <img  src="images/density.png"  >
</p>
<sub>Example of the variation in face density our method achieves</sub>

There are 4 main ideas proposed in this project: 
 * A differentaible surface sampling of faces allowing for a point-to-point loss and a point-to-surface loss to be introduced.
 * A latent loss based on minimizing the distance between encodings of mesh objects produced through a mesh-to-voxel mapping procedure. 
 * A extension to the standard Graph Convolution Network called 0N-GCN which prevents vertex smoothing. This is defined in Layers.py.
 * An adaptive face splitting procedure which analyses local face curvature to encourage local complexity emerge.




## Data Production
 To produce the data needed to train and test the methods of this project use the 'data_prep.py' script. This will download CAD models from the core classes of the ShapeNet data set, produce the data require for the latent loss, sample the surface of each ground truth mesh, render the objects as images, and split all the data into training, validation and test sets. This script makes use of the binvoxer executable, so first call
 ```bash
sudo chmod 777 scripts/binvox 
```
Blender is also needed for this project so please ensure it is installed before beginning. 
 ```bash
sudo apt install blender
```
By default this scripts downloads the full chair class, and render 24 images for each object. To achieve this call:
 ```bash
python data_prep.py
```
As an example to further understand how to customize the data, to produced 1000 plane call:
 ```bash
python data_prep.py --object plane -no 1000
```

## Diffentiable Surface Losses
We introduce two new losses for reconstructing meshes. These losses are based of the idea of differentiating through the random selection of points on a triangular surface via the reparametrization trick. This allows the adoption of a chamfer loss comparing the samplings of ground truth and predicted mesh surfaces, which does not explicitly penalize the position of vertices. We call this the point-to-point loss. This idea also allows to the adoption of a more accurate loss which compares a sampled set of points to a surface directly, using the "3D point to triangle distance" algorithm. We call this the point-to-surface loss, and compare to the point-to-point loss and a loss which instead penalizes vertex position in their ability to reconstruct surfaces, in the Loss_Comparison directory. 


## Latent Loss 
One of the main contributions of this project, and a principle loss term for the complete mesh generation pipeline is the latent loss. To produce this loss we first train a mesh-to-voxel mapping. A mesh enocder, made up of our proposed 0N-GCN layers, takes as input a mesh object defined by vertex positions and an adjacency matrix and outputs a small lantent vector. This vector is passed to a voxel decoder which outputs a voxelized representation of the origional mesh. This mapping is trained to minimize the MSE between the ground-truth voxelization of the mesh and the predicted voxelization. When training the complete mesh prediction system the training objective is partly defined by the MSE between the latent embedding of the ground-truth mesh and the predicted mesh. 

<p align="center">
  <img  src="images/enc_dec.png"  >
</p>
<sub> A diagram illustrating the mesh-to-voxel mapping and how it is employed for procuding the latent loss. </sub>

To train this system call
 ```bash
python auto_encoder.py --object $obj$
```
where $obj$ is the object class you wish to train 


## Mesh Reconstruction
The combination of 


<ul align="center">
  <img  src="images/chair_best.png" width="400" >
  <img  src="images/plane_best.png" width="400" >
</ul>
<sub>Example objects super-resolution resolution results. High definition copies of these images can be found in the Images folder. </sub>



## Reference:
please cite my paper: https://arxiv.org/pdf/1802.09987.pdf ,if you use this repo for research with following bibtex: 

            @incollection{NIPS2018_7883,
            title = {Multi-View Silhouette and Depth Decomposition for High Resolution 3D Object Representation},
            author = {Smith, Edward and Fujimoto, Scott and Meger, David},
            booktitle = {Advances in Neural Information Processing Systems 31},
            editor = {S. Bengio and H. Wallach and H. Larochelle and K. Grauman and N. Cesa-Bianchi and R. Garnett},
            pages = {6479--6489},
            year = {2018},
            publisher = {Curran Associates, Inc.},
            url = {http://papers.nips.cc/paper/7883-multi-view-silhouette-and-depth-decomposition-for-high-resolution-3d-object-representation.pdf}
            }
