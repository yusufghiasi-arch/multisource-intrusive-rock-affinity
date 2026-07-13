# Multisource Geodata Analysis for Intrusive-Rock Affinity Mapping

This repository contains an exploratory geodata-science workflow developed for the North Swayze area in Ontario.

The project tests whether field geology, airborne electromagnetic data, magnetic data, and Sentinel-2 imagery can be combined to distinguish intrusive-rock settings from mafic metavolcanic rocks.

The goal was not to produce a definitive geological map or predict the location of a mine. The purpose was to build and evaluate a reproducible multisource workflow, compare the value of different geodata families, and produce interpretable probability and uncertainty layers.

 Study area

The analysis covers part of the North Swayze greenstone belt near Timmins, Ontario. The area is heavily vegetated and includes limited surface exposure, making it useful for testing the relative value of optical imagery and subsurface geophysical datasets.

 Data used

The workflow combines several public datasets with different formats, resolutions, and physical meanings:

- Sentinel-2 multispectral imagery
- airborne total magnetic field
- magnetic second vertical derivative
- electromagnetic decay constant
- apparent resistivity
- field-mapped lithology observations
- geological and contextual GIS layers

The source datasets were reprojected, resampled, and aligned to a common 20 m analysis grid.

Large source datasets are not stored in this repository. Their original public sources and dataset identifiers are documented in the notebook.

 Workflow

The main steps were:

1. Search and load Sentinel-2 imagery
2. Create a cloud-filtered summer composite
3. Calculate spectral indices and neighbourhood statistics
4. Read and align airborne magnetic and EM grids
5. Prepare and clean field-lithology observations
6. Extract raster predictors at observation locations
7. Build a unified modelling table with Pandas
8. Compare feature families and machine-learning algorithms
9. Apply geographically separated spatial cross-validation
10. Tune the selected Random Forest model
11. Generate intrusive-rock affinity and uncertainty GeoTIFFs
12. Export the outputs for visualization in ArcGIS Pro

 Software

The analysis was completed mainly with:

- Python
- Jupyter Notebook
- Pandas
- NumPy
- scikit-learn
- Rasterio
- Xarray
- odc-stac
- Microsoft Planetary Computer
- Matplotlib
- ArcGIS Pro

 Machine-learning target

The original lithology observations were simplified into two classes:

- Intrusive rocks
- Mafic metavolcanic rocks

This binary grouping was used because the initial three-class model showed weak discrimination for the least represented lithology class.

Random Forest, Extra Trees, gradient boosting, support vector machine, and logistic regression models were compared. Random Forest provided the most consistent result under spatial cross-validation.

 Model comparison

| Input data | Accuracy | Balanced accuracy | Macro F1 |
|---|---:|---:|---:|
| EM only | 0.594 | 0.595 | 0.593 |
| EM + magnetics | 0.591 | 0.585 | 0.585 |
| EM + magnetics + Sentinel-2 | 0.484 | 0.482 | 0.481 |

The EM-only model produced the strongest spatially transferable result.

Adding Sentinel-2 did not improve the lithology model in this forested area. The optical predictors were influenced mainly by vegetation, moisture, water, and surface disturbance, while the target represented concealed bedrock geology.

This was an important result of the project: adding more datasets did not automatically produce a better model.

 Outputs

The workflow produces georeferenced raster outputs including:

- intrusive-rock affinity from EM predictors;
- intrusive-rock affinity from EM and magnetic predictors;
- intrusive-rock affinity from Sentinel-2, EM, and magnetics;
- prediction uncertainty;
- aligned magnetic and EM input rasters.

The affinity value ranges from 0 to 1:

- values closer to 1 indicate stronger similarity to the intrusive-rock training class;
- values closer to 0 indicate stronger similarity to the mafic-metavolcanic class;
- values close to 0.5 indicate an ambiguous model response.

These values should be interpreted as model affinity, not as confirmed geology or mineral-deposit probability.

 Validation

A geographically separated spatial cross-validation strategy was used instead of a conventional random train-test split.

This reduces the risk of inflated accuracy caused by nearby observations sharing similar geological and spatial characteristics.

The selected EM-only Random Forest achieved:

- pooled balanced accuracy: 0.595;
- macro F1: 0.593;
- mean fold balanced accuracy: approximately 0.620;
- fold-to-fold standard deviation: approximately 0.080.

The result indicates moderate and spatially variable discrimination rather than production-level geological classification.

 Important limitations

- Field observations were spatially clustered and unevenly distributed.
- The binary classes combine geological units with internal variability.
- EM responses may reflect overburden, groundwater, clays, graphite, cultural features, or conductive minerals.
- Sentinel-2 mainly observes surface conditions and vegetation.
- The model was trained for lithological affinity, not direct mineral prospectivity.
- Low model uncertainty does not guarantee that a prediction is correct.
- The outputs should be used as regional screening information, not as a replacement for geological interpretation, field mapping, geochemistry, or drilling.

 Repository structure

.
├── notebooks/
│   └── intrusive_rock_affinity_workflow.ipynb
├── outputs/
│   ├── figures/
│   └── sample_maps/
├── README.md
├── requirements.txt
└── .gitignore

Running the notebook

Create a Python environment and install the required packages:

pip install -r requirements.txt

Open the notebook:

jupyter notebook notebooks/intrusive_rock_affinity_workflow.ipynb

The notebook expects the source datasets to be placed in a local data/ directory. Because the original files are large, they are not included in the repository.

Update the paths in the configuration section at the beginning of the notebook before running the analysis.

Why I developed this project

I developed this project as an exploratory case study in multisource geodata integration and spatial machine learning.

The main focus was the workflow itself:

harmonizing datasets with different structures and resolutions;
engineering meaningful geophysical and remote-sensing features;
comparing different input families;
testing models using spatial validation;
interpreting results according to the physical meaning of each dataset;
communicating probability, uncertainty, and limitations clearly.

The project is intended as a portfolio example rather than a production exploration model.

Author

Yusof Ghiasi, PhD
Geospatial Data Scientist | Remote Sensing and GIS
