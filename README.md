# ASTRADEPTH


An AI-powered system for transforming **2D optical satellite imagery into interactive 3D terrain models** using monocular depth estimation, geospatial calibration, terrain reconstruction, and real-time visualization.

---

## 📌 Problem Statement

Optical satellite imagery provides rich information about the Earth's surface, but conventional satellite images are primarily two-dimensional representations. Extracting accurate terrain elevation and creating usable 3D terrain models from such imagery generally requires specialized datasets, stereo imagery, LiDAR, or existing Digital Elevation Models (DEMs).

The objective of this project is to develop an AI-driven pipeline capable of estimating terrain depth from a **single optical satellite image** and converting it into a **3D terrain representation**.

The proposed system should be capable of:

- Accepting optical satellite imagery in common formats such as **PNG, JPG, and GeoTIFF**.
- Estimating relative depth using a **monocular depth estimation model**.
- Generating a **Digital Surface Model (DSM)** from the estimated depth.
- Using geospatial metadata and reference elevation data to calibrate relative depth into metric elevation where possible.
- Generating a 3D terrain mesh from the elevation data.
- Projecting the original satellite imagery onto the terrain mesh as a texture.
- Providing an interactive 3D visualization and analysis environment.

---

# 🎯 Objectives

The primary objectives of the proposed system are:

1. **Convert optical satellite imagery into depth information.**
2. **Generate a relative or absolute elevation map** from a single image.
3. **Calibrate relative depth using geospatial references** when available.
4. **Generate a 3D terrain mesh** from the resulting elevation map.
5. **Preserve the visual information** of the original satellite image through RGB texture projection.
6. **Provide real-time interactive visualization** of the reconstructed terrain.
7. Enable basic terrain analysis such as:
   - Height measurement
   - Slope visualization
   - Point-to-point distance measurement
8. Provide an evaluation pipeline for comparing reconstructed elevation against reference datasets.

---

# 🏗️ System Architecture

The complete system follows the pipeline:

```text
                              ┌──────────────┐
                              │     USER     │
                              └──────┬───────┘
                                     │
                                     ▼
                  ┌──────────────────────────────────┐
                  │   Upload Optical Satellite Image │
                  │       PNG / JPG / GeoTIFF        │
                  └────────────────┬─────────────────┘
                                   │
                                   ▼
                  ┌──────────────────────────────────┐
                  │          PREPROCESSING           │
                  │  Resize • Normalize • Format     │
                  │            Detection             │
                  └────────────────┬─────────────────┘
                                   │
                                   ▼
                  ┌──────────────────────────────────┐
                  │       AI DEPTH ESTIMATION        │
                  │        Monocular Model           │
                  │          CNN / ViT               │
                  └────────────────┬─────────────────┘
                                   │
                                   ▼
                  ┌──────────────────────────────────┐
                  │         RELATIVE DEPTH MAP       │
                  └────────────────┬─────────────────┘
                                   │
                    ┌──────────────┴──────────────┐
                    │                             │
                    ▼                             ▼
        ┌───────────────────────┐     ┌────────────────────────┐
        │   NO GEO-METADATA     │     │     GEOREFERENCED      │
        │      PNG / JPG        │     │        GeoTIFF         │
        └───────────┬───────────┘     └───────────┬────────────┘
                    │                             │
                    ▼                             ▼
        ┌───────────────────────┐     ┌────────────────────────┐
        │    Relative DSM       │     │  Extract Geo-Metadata  │
        │   Scale-Agnostic      │     └───────────┬────────────┘
        └───────────┬───────────┘                 │
                    │                             ▼
                    │                 ┌────────────────────────┐
                    │                 │   Reference DEM / GCPs │
                    │                 │       SRTM / GCPs      │
                    │                 └───────────┬────────────┘
                    │                             │
                    │                             ▼
                    │                 ┌────────────────────────┐
                    │                 │   Scale Calibration    │
                    │                 │ Relative → Absolute    │
                    │                 │       Regression       │
                    │                 └───────────┬────────────┘
                    │                             │
                    │                             ▼
                    │                 ┌────────────────────────┐
                    │                 │ Absolute Metric Height │
                    │                 └───────────┬────────────┘
                    │                             │
                    │                             ▼
                    │                 ┌────────────────────────┐
                    │                 │      Absolute DSM      │
                    │                 └───────────┬────────────┘
                    │                             │
                    └──────────────┬──────────────┘
                                   │
                                   ▼
                  ┌──────────────────────────────────┐
                  │          ELEVATION MAP           │
                  │     Relative or Absolute         │
                  └────────────────┬─────────────────┘
                                   │
                                   ▼
                  ┌──────────────────────────────────┐
                  │       ACCURACY VALIDATION        │
                  │   Compare with LiDAR / Reference │
                  │   DEM • ISPRS Vaihingen          │
                  │       (Offline / Eval Only)      │
                  └────────────────┬─────────────────┘
                                   │
                                   ▼
                  ┌──────────────────────────────────┐
                  │       TERRAIN MESH GENERATION    │
                  │        Heightmap → Geometry      │
                  └────────────────┬─────────────────┘
                                   │
                    ┌──────────────┴──────────────┐
                    │                             │
                    ▼                             ▼
        ┌───────────────────────┐     ┌────────────────────────┐
        │    Original RGB       │     │     Mesh Geometry      │
        │        Image          │     │                        │
        └───────────┬───────────┘     └───────────┬────────────┘
                    │                             │
                    └──────────────┬──────────────┘
                                   │
                                   ▼
                  ┌──────────────────────────────────┐
                  │      RGB TEXTURE PROJECTION      │
                  │       Drape Image onto Mesh      │
                  └────────────────┬─────────────────┘
                                   │
                                   ▼
                  ┌──────────────────────────────────┐
                  │       TEXTURED 3D TERRAIN        │
                  │              MESH                │
                  └────────────────┬─────────────────┘
                                   │
                                   ▼
                  ┌──────────────────────────────────┐
                  │       3D RENDERING / VIEWER      │
                  │              ENGINE              │
                  └────────────────┬─────────────────┘
                                   │
                                   ▼
                  ┌──────────────────────────────────┐
                  │      INTERACTIVE 3D FLYTHROUGH   │
                  │   First-Person • Real-Time       │
                  └────────────────┬─────────────────┘
                                   │
                  ┌────────────────┼────────────────┐
                  │                │                │
                  ▼                ▼                ▼
        ┌────────────────┐ ┌────────────────┐ ┌────────────────┐
        │  HEIGHT QUERY  │ │ SLOPE ANALYSIS │ │ POINT-TO-POINT │
        │      TOOL      │ │                │ │   MEASUREMENT  │
        │ Click to       │ │ Gradient View  │ │ Distance /     │
        │ Measure Height │ │                │ │ Elevation      │
        └────────────────┘ └────────────────┘ └────────────────┘
```

---

# 🔬 Proposed Methodology

## 1. Image Input

The system accepts optical satellite imagery in:

- `.PNG`
- `.JPG / .JPEG`
- `.GeoTIFF`

GeoTIFF images are additionally processed for their geospatial information, including coordinate reference system and spatial metadata.

---

## 2. Preprocessing

The uploaded image is passed through a preprocessing pipeline.

### Operations include:

- Image format detection
- Resolution normalization
- Resizing
- Pixel normalization
- Channel handling
- Missing-value handling where applicable
- GeoTIFF metadata extraction

The goal is to provide a consistent input representation to the depth estimation model.

---

## 3. AI-Based Monocular Depth Estimation

A monocular depth estimation model is used to infer depth information from the optical satellite image.

Possible model architectures include:

- CNN-based depth estimation
- Vision Transformer (ViT)-based depth estimation
- Foundation depth models
- Fine-tuned models trained on aerial/satellite datasets

The model produces a **relative depth map**.

### Important distinction

The output of monocular depth estimation is generally **relative depth rather than directly measurable elevation**.

Therefore:

```text
Optical Image
      │
      ▼
Monocular Depth Model
      │
      ▼
Relative Depth
      │
      ▼
Scale Calibration
      │
      ▼
Metric Elevation
```

This distinction is important because a visually convincing depth map does not automatically represent true-world elevation in metres.

---

# 🗺️ Elevation & Scale Calibration

The system handles input imagery differently depending on whether geospatial information is available.

## PNG / JPG

For images without geospatial metadata:

```text
PNG / JPG
   │
   ▼
Relative Depth Map
   │
   ▼
Relative DSM
```

The resulting DSM is **scale-agnostic** and represents relative terrain structure.

---

## GeoTIFF

For georeferenced satellite imagery:

```text
GeoTIFF
   │
   ├── Image Data
   │
   └── Geo-Metadata
          │
          ▼
   Reference DEM / GCPs
          │
          ▼
   Scale Calibration
          │
          ▼
   Absolute Metric Height
          │
          ▼
      Absolute DSM
```

Reference elevation information can be obtained from suitable DEM datasets such as **SRTM**, or from available Ground Control Points (GCPs).

A calibration/regression module maps the model's relative depth values to real-world elevation values.

---

# 🧠 Scale Calibration

A calibration model can learn a transformation between:

```text
Relative Depth → Absolute Elevation
```

Possible approaches include:

- Linear regression
- Polynomial regression
- Robust regression
- Least-squares fitting
- Machine-learning-based calibration

Conceptually:

```text
Z_absolute = f(Z_relative, X, Y, reference_elevation)
```

where:

- `Z_relative` = depth model output
- `Z_absolute` = estimated real-world elevation
- `X, Y` = spatial coordinates
- `reference_elevation` = elevation obtained from DEM/GCP data

The exact calibration method can be selected based on the characteristics of the available reference data.

---

# 🏔️ Terrain Mesh Generation

The generated elevation map is converted into a 3D terrain surface.

```text
Elevation Map
      │
      ▼
Heightmap
      │
      ▼
Grid / Vertex Generation
      │
      ▼
Triangle Mesh
      │
      ▼
3D Terrain Geometry
```

Each pixel or sampled point in the elevation map can be represented as a vertex whose height corresponds to the estimated elevation.

The vertices are then connected to form a terrain mesh.

---

# 🎨 RGB Texture Projection

To preserve the appearance of the original satellite imagery, the RGB image is projected onto the generated terrain mesh.

```text
Original Satellite Image
          │
          ▼
    Texture Mapping
          │
          ▼
    Terrain Geometry
          │
          ▼
   Textured 3D Terrain
```

This combines:

- **Geometry** from the estimated elevation
- **Appearance** from the original satellite image

The result is a visually interpretable 3D representation of the observed terrain.

---

# 🖥️ Interactive 3D Visualization

The final terrain is rendered using a 3D visualization engine.

The viewer provides:

### 🚁 Interactive Flythrough

Users can navigate through the reconstructed terrain using a first-person or free-camera interface.

### 📏 Height Query

Users can select a point on the terrain and obtain its estimated elevation.

### 📐 Slope Analysis

The system calculates terrain gradients to visualize areas with different slope characteristics.

### 📍 Point-to-Point Measurement

Users can select two locations and measure the distance between them.

---

# 📊 Accuracy Validation

An offline evaluation module is included to measure reconstruction performance against trusted elevation references.

Possible reference datasets include:

- LiDAR-derived elevation data
- High-quality DEMs
- Ground Control Points
- Benchmark datasets such as **ISPRS Vaihingen**, where applicable

Example evaluation metrics:

| Metric | Purpose |
|---|---|
| MAE | Mean absolute elevation error |
| RMSE | Root mean square elevation error |
| ME | Mean elevation bias |
| \(R^2\) | Correlation between predicted and reference elevation |
| Relative Error | Normalized elevation error |

### Evaluation Flow

```text
Satellite Image
      │
      ▼
Depth Estimation
      │
      ▼
Elevation Reconstruction
      │
      ▼
Predicted DSM
      │
      ├──────────────┐
      │              │
      ▼              ▼
Reference DEM     LiDAR / GCP
      │              │
      └──────┬───────┘
             ▼
     Accuracy Metrics
             │
             ▼
       Error Analysis
```

> **Note:** Accuracy validation is intended for offline evaluation and benchmarking. It is not a mandatory step in the real-time visualization pipeline.

---

# ✨ Key Features

- 🛰️ Optical satellite image support
- 🧠 AI-powered monocular depth estimation
- 🗺️ GeoTIFF metadata support
- 📐 Relative-to-absolute elevation calibration
- 🏔️ Automatic DSM generation
- 🌐 3D terrain mesh reconstruction
- 🎨 Satellite texture projection
- 🚁 Interactive 3D flythrough
- 📏 Point height measurement
- 📐 Slope analysis
- 📍 Point-to-point distance measurement
- 📊 Quantitative elevation validation
- ⚡ Modular architecture for future model replacement

---

# 🧰 Proposed Technology Stack

<img width="1021" height="800" alt="image" src="https://github.com/user-attachments/assets/03673aa5-6ec7-42fc-88e4-7cc5f7dedc1e" />


The final stack can be adjusted depending on deployment requirements and the selected depth-estimation model.

---

# 🚀 Future Scope

The proposed system can be extended in several directions:

- Multi-spectral satellite imagery support
- Multi-view and stereo reconstruction
- Temporal terrain-change detection
- Improved satellite-specific depth models
- Automatic DEM selection based on geographic location
- Real-time streaming of large satellite datasets
- Cloud-based terrain reconstruction
- GPU-accelerated inference
- Large-scale regional terrain reconstruction
- Integration with GIS platforms
- Disaster-management applications
- Infrastructure and urban planning
- Environmental monitoring
- Geological and topographical analysis

---

# 🌍 Potential Applications

The reconstructed terrain can support applications such as:

### Disaster Management
- Flood-risk visualization
- Landslide-prone terrain analysis
- Post-disaster terrain assessment

### Urban Planning
- Terrain-aware infrastructure planning
- 3D visualization of urban regions
- Site analysis

### Environmental Monitoring
- Terrain change analysis
- Vegetation and landscape studies
- Watershed analysis

### Defence & Strategic Planning
- Terrain visualization
- Route analysis
- Elevation-aware reconnaissance

### Education & Research
- 3D geographic visualization
- Remote-sensing research
- Computer vision benchmarking

---

# ⚠️ Limitations

The system has several inherent limitations:

1. **Monocular depth estimation is inherently ambiguous.** A single image does not contain enough information to uniquely determine absolute depth.

2. **Relative depth is not automatically metric elevation.** Absolute height requires additional geospatial information or calibration.

3. **Reference DEM quality affects calibration accuracy.**

4. **Vegetation and buildings can affect DSM estimation**, since optical imagery observes surface appearance rather than bare-earth elevation.

5. **Image resolution and terrain complexity influence reconstruction quality.**

6. **Occlusions and shadows may introduce errors** into depth estimation.

7. **Large satellite images may require tiling** to fit within GPU memory limitations.

These limitations are considered explicitly in the architecture rather than treating AI-generated depth as magically equivalent to surveyed elevation, because physics remains annoyingly persistent.

---

# 📈 Expected Outcome

The final system is expected to transform an optical satellite image into an interactive 3D representation:

```text
2D Optical Satellite Image
            │
            ▼
     AI Depth Estimation
            │
            ▼
      Elevation / DSM
            │
            ▼
      3D Terrain Mesh
            │
            ▼
     RGB Texture Mapping
            │
            ▼
     Interactive 3D Model
```

