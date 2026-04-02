# ☁️ Cloud & Cloud Shadow Detection — PlanetScope Bawean

> Automated cloud and cloud shadow detection using deep learning on **PlanetScope SuperDove 8-band** satellite imagery over **Bawean Island, East Java, Indonesia**, powered by [OmniCloudMask](https://github.com/DPIRD-DMA/OmniCloudMask) via [GeoAI](https://github.com/opengeos/geoai). Outputs a **GEE-ready masked raster** suitable for direct upload to Google Earth Engine for LULC classification.

---

## 📌 Overview

This notebook implements an end-to-end cloud detection and masking pipeline for PlanetScope imagery — from data inspection and model inference, through post-processing and vector export, to producing a cloud-free masked raster ready for **Google Earth Engine (GEE)** and land cover classification workflows.

A key motivation for this pipeline is to **prevent cloud shadows from being misclassified as mangrove or other dark vegetation** during LULC analysis — a common error in coastal remote sensing.

### Key Features
- ✅ Compatible with **PlanetScope SuperDove 8-band** (PSScene)
- ✅ Detects 4 classes: `Clear`, `Thick Cloud`, `Thin Cloud`, `Cloud Shadow`
- ✅ Multi-composite visualization: True Color, False Color CIR, and Red Edge
- ✅ Post-processing: noise cleaning, vectorization, and polygon smoothing
- ✅ Interactive map with cloud overlay and split-map view
- ✅ Cloud-free mask ready for downstream analysis
- ✅ **Cloud & shadow masking → GeoTIFF with NoData** (masked pixels set to 0)
- ✅ **Reprojection to EPSG:4326 (WGS84)** — GEE-compatible COG output
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

| Value | Class        | Visualization Color | Masked? |
|-------|--------------|---------------------|---------|
| 0     | Clear        | 🟢 Green            | No      |
| 1     | Thick Cloud  | ⚪ White            | ✅ Yes  |
| 2     | Thin Cloud   | 🩶 Gray             | ✅ Yes  |
| 3     | Cloud Shadow | ⚫ Dark             | ✅ Yes  |

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
6. Visualization — Side-by-Side & Zoom to Largest Cloud Cluster
        │
        ▼
7. Post-Processing
   ├── Noise Cleaning (raster)
   ├── Vectorization → GeoJSON
   └── Polygon Smoothing
        │
        ▼
8. Per-Class Area Summary (km²)
        │
        ▼
9. Interactive Map & Split Map Visualization
        │
        ▼
10. Cloud-Free Binary Mask (GeoTIFF)
        │
        ▼
11. Statistics Export → CSV
        │
        ▼
12. Cloud & Shadow Masking → GEE-Ready Output
    ├── Apply mask to all 8 bands (NoData = 0)
    ├── Reproject → EPSG:4326 (WGS84)
    └── Export Cloud-Optimized GeoTIFF (COG)
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
3. **Set your file path** in the configuration cell:
   ```python
   INPUT_PATH = "/content/drive/MyDrive/your_folder/planetscope_image.tif"
   ```
4. **Run all cells** sequentially (`Runtime → Run all`)

### Output Files

| File | Description |
|------|-------------|
| `*_cloud_mask.tif` | Raw raster cloud mask (4 classes) |
| `*_cloud_mask_cleaned.tif` | Cloud mask after noise removal |
| `*_cloud_vector.geojson` | Cloud polygons (vector) |
| `*_cloud_vector_smoothed.geojson` | Smoothed cloud polygons |
| `*_cloud_free_mask.tif` | Binary cloud-free pixel mask |
| `*_cloud_statistics.csv` | Per-class area statistics |
| `*_masked_4band.tif` | All bands masked (native CRS) |
| `*_masked_4band_GEE.tif` | **GEE-ready masked raster (EPSG:4326, COG)** |
| `*_preview_composites.png` | RGB / CIR / RedEdge composite preview |
| `*_cloud_detection_result.png` | Side-by-side detection result |
| `*_masking_comparison.png` | Before / after masking comparison |

---

## 🌍 Google Earth Engine (GEE) Integration

The final output (`*_masked_4band_GEE.tif`) is configured for direct upload to GEE:

| Property | Value |
|----------|-------|
| Format | GeoTIFF (Cloud-Optimized, tiled 256×256) |
| CRS | **EPSG:4326 (WGS84)** |
| NoData | **0** (masked pixels) |
| Data type | UInt16 |
| Compression | Deflate (lossless) |

Upload to GEE via the [Assets panel](https://code.earthengine.google.com/) or using `earthengine upload`.

---

## 📦 Main Dependencies

| Library | Purpose |
|---------|---------|
| [geoai-py](https://github.com/opengeos/geoai) | OmniCloudMask wrapper & geospatial utilities |
| [rasterio](https://rasterio.readthedocs.io/) | GeoTIFF I/O, reprojection, and COG export |
| [numpy](https://numpy.org/) | Numerical array computation |
| [matplotlib](https://matplotlib.org/) | Static visualization |
| [scipy](https://scipy.org/) | Spatial cluster analysis (ndimage) |
| [geopandas](https://geopandas.org/) | Vector operations & GeoJSON handling |
| [pandas](https://pandas.pydata.org/) | Statistics table and CSV export |

---

## 📍 Study Area

**Bawean Island** — a volcanic island in the Java Sea, administratively part of Gresik Regency, East Java, Indonesia. Coordinates: ±5°46′S, 112°40′E. The island's coastal mangrove ecosystems are the primary focus of downstream LULC classification after cloud masking.

---

## 📖 References

- [OmniCloudMask](https://github.com/DPIRD-DMA/OmniCloudMask) — Multi-sensor deep learning model for cloud detection
- [GeoAI (opengeos)](https://github.com/opengeos/geoai) — Python library for geospatial AI
- [PlanetScope Product Spec](https://developers.planet.com/docs/data/planetscope/) — Planet technical data specifications
- [Google Earth Engine](https://earthengine.google.com/) — Cloud-based geospatial analysis platform

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---
