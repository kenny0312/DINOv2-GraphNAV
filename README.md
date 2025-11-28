DINOv2-GraphNAV
Topological First-Person Visual Navigation Using DINOv2 Feature Graphs
<p align="center"> <img src="dinov2.jpg" width="800"> </p>

DINOv2-GraphNAV is a lightweight topological visual navigation system designed for first-person indoor exploration and goal-directed navigation.
The method relies entirely on visual similarity rather than SLAM, pose estimation, or reinforcement learning.

All computationally expensive steps (feature extraction, PCA, graph construction) are performed offline, enabling extremely fast online navigation with only a small DINOv2-S model.

Highlights

Offline DINOv2-B/14 feature extraction (global + local)

PCA-compressed local patch token descriptors

KNN graph construction based on visual similarity

FAISS-accelerated real-time localization

Multi-step Next-Best-View planning (3-step)

Zero modification to the original vis_nav_game keyboard logic

Online stage uses only DINOv2-S → fast inference (<10 ms)

Repository Structure
.
├── build_graph.py                   # Offline feature & graph builder
├── effonav_config.json              # Config for graph building
│
├── effonav_dino_global.npy          # Global CLS features
├── effonav_dino_local.npy           # Local PCA features
├── effonav_dino_pca.pkl             # PCA reduction model
├── effonav_dino_graph.pkl           # KNN graph
├── effonav_dino_paths.pkl           # Image path list
│
├── data/images_subsample/           # Exploration dataset
├── kenny_online.py                  # Online navigation agent
├── target.jpg                       # Goal front-view image
├── startup.json
└── dinov2.jpg                       # Architecture figure

Installation
conda create -n graphnav python=3.10
conda activate graphnav
pip install -r requirements.txt

Offline Graph Construction

The offline pipeline extracts both global and local DINOv2 features, compresses patch tokens via PCA, and builds a visual KNN graph.

Run
python build_graph.py --config effonav_config.json

Generates
File	Description
effonav_dino_global.npy	Global CLS embeddings
effonav_dino_local.npy	Local PCA-compressed patch tokens
effonav_dino_pca.pkl	PCA model
effonav_dino_graph.pkl	KNN visual graph
effonav_dino_paths.pkl	Dataset file paths
Online Navigation

The online agent uses only DINOv2-S to process each live FPV image.

Responsibilities:

Extract global FPV features in real time

FAISS lookup for closest visual node

Set goal by matching target.jpg

Plan on the offline visual graph

Provide Top-3 best next viewpoints

Maintain original keyboard navigation logic

Run
python kenny_online.py

Controls
Key	Action
↑ ↓ ← →	Move robot
SPACE	Check-in
T	Show target 4-view images
Q	Show 3-step Next-Best-Views
ESC	Quit
System Overview
<p align="center"> <img src="dinov2.jpg" width="800"> </p>
Offline Stage

Extract global & local DINOv2 embeddings

Perform PCA reduction on patch tokens

Compute pairwise similarity

Build KNN topological graph

Online Stage

Extract global FPV embedding (DINOv2-S)

Localize via FAISS nearest neighbor

Determine goal node from target image

Compute shortest path on graph

Output Top-3 Next-Best-View frames

Why Graph Navigation Instead of SLAM?

Works purely from image similarity

No camera calibration

No pose estimation

No geometric mapping

Stable in feature-rich indoor environments

Much simpler than SLAM/RL pipelines

Citation
@misc{dinov2graphnav2025,
  title={DINOv2-GraphNAV: Lightweight Topological Visual Navigation Using DINOv2 Features},
  author={Kenny Qiu},
  year={2025},
  publisher={GitHub}
}