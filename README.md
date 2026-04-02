# ☁️ Cloud & Cloud Shadow Detection — PlanetScope Bawean

> Automated cloud detection using deep learning on **PlanetScope SuperDove 8-band** satellite imagery over **Bawean Island, East Java, Indonesia**, powered by [OmniCloudMask](https://github.com/DPIRD-DMA/OmniCloudMask) via [GeoAI](https://github.com/opengeos/geoai).

---

## 📌 Overview

This notebook implements an end-to-end cloud detection pipeline for PlanetScope imagery — from data inspection and model inference to post-processing, vector export, and statistics. The pipeline is designed to run on **Google Colab** with Google Drive as the data storage backend.

### Key Features
- ✅ Compatible with **PlanetScope SuperDove 8-band** (PSScene)
- ✅ Detects 4 classes: `Clear`, `Thick Cloud`, `Thin Cloud`, `Cloud Shadow`
- ✅ Multi-composite visualization: True Color, False Color CIR, and Red Edge
- ✅ Post-processing: noise cleaning, vectorization, and polygon smoothing
- ✅ Interactive map with cloud overlay and split-map view
- ✅ Cloud-free mask ready for downstream analysis
- ✅ Statistics export to CSV

---

## 🛰️ Data Specification

### PlanetScope SuperDove 8-Band Configuration

| Band | Name         | Wavelength |
|------|--------------|------------|
| 1    | Coastal Blue | ~431 nm    |
| 2    | Blue         | ~465 nm    |
| 3    | Green I      | ~513 nm    |
| 4    | Green        | ~549 nm    |
| 5    | Yellow       | ~583 nm    |
| 6    | Red          | ~665 nm    |
| 7    | Red Edge     | ~705 nm    |
| 8    | NIR          | ~865 nm    |

> **Bands used by OmniCloudMask:** Red (Band 6), Green (Band 4), NIR (Band 8)

### Output Classes

| Value | Class        | Visualization Color |
|-------|--------------|---------------------|
| 0     | Clear        | 🟢 Green            |
| 1     | Thick Cloud  | ⚪ White            |
| 2     | Thin Cloud   | 🩶 Gray             |
| 3     | Cloud Shadow | ⚫ Dark             |

---

## 🗂️ Pipeline Workflow

```
Input GeoTIFF (PlanetScope 8-band)
        │
        ▼
1. Installation & Google Drive Mount
        │
        ▼
2. Path & Band Configuration
        │
        ▼
3. Data Inspection & Preview
   (True Color, False Color CIR, Red Edge Composite)
        │
        ▼
4. Cloud Detection — OmniCloudMask
   (Red / Green / NIR → 4-class mask)
        │
        ▼
5. Cloud Class Statistics (per pixel & area in km²)
        │
        ▼
6. Post-Processing
   ├── Noise Cleaning (raster)
   ├── Vectorization → GeoJSON
   └── Polygon Smoothing
        │
        ▼
7. Interactive Map & Split Map Visualization
        │
        ▼
8. Cloud-Free Mask (GeoTIFF)
        │
        ▼
9. Statistics Export → CSV
```

---

## 🚀 Getting Started

### Prerequisites

```bash
pip install -U "geoai-py[extra]"
```

### Run on Google Colab

1. **Open the notebook** in Google Colab (click the badge above)
2. **Mount Google Drive** — run Cell 2
3. **Set your file path** in the configuration cell (Cell 4):
   ```python
   INPUT_PATH = "/content/drive/MyDrive/Data_Bawean/planetscope_bawean.tif"
   ```
4. **Run all cells** sequentially (`Runtime → Run all`)

### Output Files

| File | Description |
|------|-------------|
| `*_cloud_mask.tif` | Raster cloud mask (4 classes) |
| `*_cloud_mask_cleaned.tif` | Cloud mask after noise removal |
| `*_cloud_vector.geojson` | Cloud polygons (vector) |
| `*_cloud_vector_smoothed.geojson` | Smoothed cloud polygons |
| `*_cloud_free_mask.tif` | Binary cloud-free pixel mask |
| `*_cloud_statistics.csv` | Per-class area statistics |
| `*_preview_composites.png` | RGB / CIR / RedEdge composite preview |
| `*_cloud_detection_result.png` | Side-by-side detection result |

---

## 📦 Main Dependencies

| Library | Purpose |
|---------|---------|
| [geoai-py](https://github.com/opengeos/geoai) | OmniCloudMask wrapper & geospatial utilities |
| [rasterio](https://rasterio.readthedocs.io/) | GeoTIFF raster I/O and metadata |
| [numpy](https://numpy.org/) | Numerical array computation |
| [matplotlib](https://matplotlib.org/) | Static visualization |
| [scipy](https://scipy.org/) | Spatial cluster analysis (ndimage) |
| [geopandas](https://geopandas.org/) | Vector operations & GeoJSON handling |

---

## 📍 Study Area

**Bawean Island** — a volcanic island in the Java Sea, administratively part of Gresik Regency, East Java, Indonesia. Coordinates: ±5°46′S, 112°40′E.

---

## 📖 References

- [OmniCloudMask](https://github.com/DPIRD-DMA/OmniCloudMask) — Multi-sensor deep learning model for cloud detection
- [GeoAI (opengeos)](https://github.com/opengeos/geoai) — Python library for geospatial AI
- [PlanetScope Product Spec](https://developers.planet.com/docs/data/planetscope/) — Planet technical data specifications

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---
