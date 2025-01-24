
# Multimodal Archaeological Dataset (MAD), Archaeological Artefacts 3D Digitization Dataset

## Overview

Multimodal Archaeological Dataset (MAD)
This dataset contains high-resolution 3D digitizations and multi-modal imaging data of six archaeological artefacts. The dataset spans objects representative of diverse materials, forms, and historical periods, from the Middle Paleolithic to the Bronze Age. It is designed for use in 3D reconstruction, digital archaeology, and cultural heritage studies.

### Download Link

You can download the dataset using the link below. 

**⚠️ Warning**: Please note that the link may change in the future. Ensure you are accessing the most up-to-date version from the official source or repository. Contact the authors in case of persisting problem.

[Download Dataset](<https://file.dyn.bcoupry.fr/s/MAD_project>)

### Paper method

This dataset is part of the study presented in the paper:  
**Benjamin Coupry, Antoine Laurent, Baptiste Brument, Jean Mélou, Yvain Quéau, and Jean-Denis Durou.**  
*"Assessing the Quality of 3D Reconstruction in the Absence of Ground Truth: Application to a Multimodal Archaeological Dataset."*  
In *Proceedings of the IEEE/CVF Winter Conference on Applications of Computer Vision, 2025.*

The method described in this repository stems from the observation that low- and high-frequency details in 3D reconstructions are best captured by distinct methods. This insight motivated the combination of Multi-View Stereo (MVS) and Calibrated Photometric Stereo (CPS) techniques, where MVS is used to calibrate the illumination required by CPS. By integrating the strengths of both methods, the proposed approach achieves accurate 3D reconstructions that effectively capture both global shapes and fine surface details. 

The repository contains the implementation and tools required to reproduce this method, which has been successfully demonstrated on our archaeological dataset, highlighting its ability to leverage MVS and CPS for superior 3D reconstructions.


For more details, you can explore the implementation of the proposed method in the repository linked below.

**⚠️ Warning**: The following repository is specifically focused on light estimation for the proposed method. It does not include the implementation of the Photometric Stereo (PS) technique itself. Please refer to other resources or implementations for locally calibrated PS-related functionalities.

**⚠️ Warning**: Documentation is currently in progress.

[Method Repository](<https://github.com/BenjaminCoupry/HeliosMiniPS>)

---

### Objects in the Dataset

1. **Biface**: A Mousterian flint biface with a cortical surface and faceted relief.
2. **Fossil Flint**: A Middle Paleolithic flint with a preserved fossil, offering a challenging reconstruction task due to its fluted shell texture.
3. **Harpoon**: A reindeer antler harpoon from the Upper Palaeolithic with incised decorations and barbs.
4. **Blade**: A Neolithic flint dagger blade showing technical traces of usage.
5. **Axe**: A protohistoric bronze axe with a weathered metallic surface.
6. **Shard**: A ceramic shard from the Bronze Age, featuring incised decorations and surface shaping marks.

---

## Folder Structure

The dataset is organized into subfolders for each object, with data further divided by digitization modality:

```
data/
│
├── biface/ (studied object)
│   │ 
│   ├── scan/ (scanner data)
│   │   ├── mesh/ (mesh obtained by scanner, aligned with ICP)
│   │   │   └── mesh.ply
│   │   └── source/
│   │       ├── biface.a3d
│   │       └── data/ (raw scanner data)
│   │ 
│   ├── photo_stereo/ (photo stereo data)
│   │   ├── lights.lp (directional lights file)
│   │   ├── pose.json (K, R, t camera matrices and width, height)
│   │   ├── raw/ (RAW NEF files)
│   │   │	├── DARK.NEF (image with all lights off)
│   │   │	├── LIGHT.NEF (image with all lights on)PS_00000
│   │   │	├── PS_00000.NEF (photo stereo images)
│   │   │	...
│   │   ├── png/ (developped undisto files)
│   │	│		├──... (same as RAW)
│   │	│		...
│   │   └── projection/ (Scan and MV meshes in PS view)
│   │		├── multi_view/ (projection of MV mesh)
│   │		│	├──mask.png (mask of the mesh)
│   │		│	├──normalmap.png (normalmap of the mesh)
│   │		│	├──zmap.png (z-map of the mesh)
│   │		│	└── geometry.exr (EXR containing 'normals', 'points' and 'mask' fields)
│   │		└── scan/ (projection of Scan mesh)
│   │			├──... (same as multi_view)
│   │			... 
│   │ 
│   └── multi_view/ (multi view data)
│       ├── raw/ (RAW NEF files)
│       │	├── COLOR.NEF (image of the colorimetric chart)
│       │	├── MV_00000.NEF (multi view images)
│       │	...
│       ├── png/ (developped undisto files)
│   	│	├──... (same as RAW)
│   	│	...
│       ├── mesh/ (mesh obtained by SfM + MVS)
│       │   └── mesh.ply
│       ├── masks/ (masks of the mesh)
│       │	├── MV_00000.png (mask of each view)
│       │	...
│       └── poses/
│        	├── MV_00000.json (K, R, t camera matrices and width, height of each view)
│        	...
│
... (same structure for fossil, harpoon, blade, axe, shard)
```
---

## File details
This section describes the purpose and usage of each file in the dataset.



### Scanner Data (`scan/`)

- **`mesh/mesh.ply`**:  

  A 3D mesh generated from the scanner, aligned with the Multi-View mesh using ICP.  

  *Usage*: Use for detailed geometric analysis or as a ground truth for comparisons.



- **`source/biface.a3d`**:  

  Project file from the Artec Studio scanner software containing metadata and processing steps.  

  *Usage*: Use for re-processing or further modifications in Artec Studio.



- **`source/data/`**:  

  Directory containing raw scanner data.  

  *Usage*: Use for re-processing or further modifications in Artec Studio.



### Photo Stereo Data (`photo_stereo/`)

- **`lights.lp`**:  

  A file specifying the directions of all LEDs in the photo stereo acquisition setup. 
  
	1. **Header**:
	   - The first line indicates the total number of light sources (`105` in this example).  

	2. **Data Entries**:
	   - Each subsequent line represents an individual light source:
	     - **Identifier**: A unique label for the image captured under this light condition (e.g., `PS_00000`).
	     - **Direction Vector**: The three numbers following the identifier specify the light's direction and intensity as `(x, -y, -z)` coordinates. These coordinates define the vector pointing from the object towards the light source.


  *Usage*: Necessary for calibrated photometric stereo reconstruction.



- **`pose.json`**:  

  A JSON file containing intrinsic (K) and extrinsic (R, t) camera matrices, along with image dimensions (width, height), and distorsion parameters (k1, k2,k3).
  R is the Camera to World rotation, t is the camera coordinates, we use the GL coordinates convention.

  *Usage*: Use for mapping between 2D images and 3D models, including link between photometric and geometric methods.



- **`raw/`**:

  - **`DARK.NEF`**: Image captured with all LEDs turned off, representing ambient light conditions.  

    *Usage*: Used for ambient light removal during photometric stereo processing.  

  - **`LIGHT.NEF`**: Image captured with all LEDs turned on, providing uniform illumination.  

    *Usage*: Can be used for registration with Multi-View data or as a reference for photo stereo processing.  

  - **`PS_00000.NEF` (and subsequent files)**: Images captured with individual LEDs turned on for photometric stereo. (105 images) 

    *Usage*: Use for photometric stereo reconstruction or light direction analysis.



- **`png/`**:  

  Directory containing developed PNG versions of RAW files.  

  *Usage*: Used for visualization or workflows requiring PNG formats.  

  **Note**: PNG files are normalized in intensity for each object, and are undistorted to comply to pinhole camera model;



- **`projection/`**:

  - **`multi_view/`** and **`scan/`** subdirectories: Contain projections of respective meshes in the photo stereo view.  

    *Common files*:  

    - **`mask.png`**: Binary mask defining the object region in the projection.  

    - **`normalmap.png`**: Encoded surface normals of the projected mesh.  

    - **`zmap.png`**: Depth map of the projected mesh.  

    - **`geometry.exr`**: High-precision EXR file containing normals, 3D points, and mask data.  

    *Usage*: Use for comparison, alignment, or reconstruction workflows.




### Multi-View Data (`multi_view/`)

- **`raw/`**:

  - **`COLOR.NEF`**: Image of a colorimetric chart used for color calibration and correction.  

    *Usage*: Provides a reference for color consistency.  

  - **`MV_00000.NEF`** (and subsequent files): Images captured from multiple viewpoints. (biface : 79 images, fossil : 40 images , harpoon : 87 images, blade : 77 images, axe : 52 images, shard : 74 images) 

    *Usage*: Input for Multi-View Stereo (MVS) reconstruction algorithms.



- **`png/`**:  

  Directory containing developed PNG versions of RAW files.  

  *Usage*: Used for visualization or workflows requiring PNG formats.  

  **Note**: PNG files are normalized in intensity for each object, and are undistorted to comply to pinhole camera model;


- **`mesh/mesh.ply`**:  

  3D mesh generated from Structure-from-Motion (SfM) and Multi-View Stereo (MVS) processes, with AliceVision Meshroom.  

  *Usage*: Use as a baseline for comparisons with scanner meshes or photo stereo data.



- **`masks/`**:

  - **`MV_00000.png`** (and subsequent files): Masks corresponding to each Multi-View image.

    *Usage*: Define object regions for SfM or other processing workflows.



- **`poses/`**:

  - **`MV_00000.json`** (and subsequent files): Intrinsic (K) and extrinsic (R, t) camera matrices, along with image dimensions, and distorsion parameters (k1, k2,k3).
    R is the Camera to World rotation, t is the camera coordinates, we use the GL coordinates convention.

    *Usage*: Use for mapping between 2D Multi-View images and the 3D model.




---

## Modalities


### 1. **Pulsed-Light Scanning**
- Scanner data captured using an Artec Space Spider scanner.

### 2. **Camera**
- The camera is a Nikon Z7II.
- All pictures where taken at 8288*5520 resolution, and stored as *.NEF files.
- For Multi-View acquisitions, the camera parameters are : time=1/200s, ISO=100, f=50mm
- For Photo-Stereo acquisitions, the camera parameters are : time=1/3s, ISO=100, f=50mm


### 3. **Photo-Stereo Imaging**
- Photo-Stereo acquisitions made under a Reflectance Transformation Imaging (RTI) controlled LED dome build by Mercurio Imaging
- The dome is composed of 105 LEDs 

### 3. **Color calibration**
- The colorimetric chart is a Calibrite Colorchecker Classic

---


## Processing Details

- **RAW to PNG Conversion**: All RAW images were processed using the following parameters:
  - White balance applied using camera settings.
  - No gamma correction.
  - 8-bit output.
  - Brightness adjustments determined per object and modalities.
  
- **Registration**:
  - The meshes obtained from the scanner were all realigned with the meshes obtained from MVS. 
  - Initialisation by matching 3D FPFH descriptors on a decimated mesh
  - Robust ICP
  
- **Undistorsion**: All PNG images are undistorted:
  - 3 parameters radial distorsion model.
  - Undistorsion parameters evaluated on AliceVision Meshroom

---
## Metadata

The dataset is composed of 104 folders, 3396 files for a total of 98.7 Go

---
## Licensing and Citation

This dataset is released under the [CC BY-NC-SA 4.0]. Please cite the following paper when using this dataset in your research:

Benjamin Coupry, Antoine Laurent, Baptiste Brument, Jean Mélou, Yvain Quéau, and Jean-Denis Durou.
"Assessing the Quality of 3D Reconstruction in the Absence of Ground Truth: Application to a Multimodal Archaeological Dataset."
In Proceedings of the IEEE/CVF Winter Conference on Applications of Computer Vision, 2025.

The photographic data in this dataset were captured by Antoine Laurent from the TRACES laboratory at Université de Toulouse, France. His expertise and meticulous work have been instrumental in ensuring the high quality and accuracy of the photographic acquisitions used in this study. We extend our gratitude for his valuable contributions.

We extend our deepest gratitude to Bertrand Defois, Conservator at the Centre de Préhistoire de Pech Merle, for his unwavering support and invaluable expertise. His guidance has been instrumental in ensuring the scientific and cultural integrity of this dataset. This work would not have been possible without his dedication to preserving and sharing our archaeological heritage.

---
