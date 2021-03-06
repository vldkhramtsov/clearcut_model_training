# Scripts for deforestation detection on the Sentinel-2 Level-A images 

## Description
This is a source code repository for downloading specified Sentinel-2 tiles and learning segmentation model. 

## Repository structure info
 * `data` - datafiles (polygons of deforestation regions in Ukraine)
 * `sentinel_download` - scripts for downloading S2A images
 * `model` - **TBD:** scripts for training U-Net segmentation model

## Setup: `sentinel_download`
The pipeline (`sentinel_download/main.py`) is the following:
1. Download raw `.jp2` files.
2. Preprocess and merge downloaded bands into the single `.tiff` file.
3. Divide the resulting file into the pieces, keeping the regions of clearcuts only.
4. Construct differences between pieces of images, separated by time.

The structure of the local datafiles after running the pipeline will be the following:

```
sentinel_download
|
└── data
    |
    ├── source_images
    │   ├── image_1_TCI.jp2
    │   └── image_2_TCI.jp2
    |
    ├── model_tiffs
    │   ├── image_1
    │   │   ├── image_1_merged.tiff
    │   │   └── clouds.tiff
    │   └── image_2
    │       ├── image_2_merged.tiff
    │       └── clouds.tiff
    |
    ├── output
    │   ├── image_1
    │   │   ├── full_mask.png
    │   │   ├── image_pieces.csv
    │   │   ├── image_1_merged.png
    │   │   ├── images/
    │   │   ├── masks/
    │   │   ├── geojson_polygons/
    │   │   └── clouds/
    │   └── image_2
    │       ├── full_mask.png
    │       ├── image_pieces.csv
    │       ├── image_2_merged.png
    │       ├── images/
    │       ├── masks/
    │       ├── geojson_polygons/
    │       └── clouds/
    |
    └── diff
        ├── date1-date2/
        ├── onlymasksplit/
        ├── data_info.csv
        ├── train_df.csv
        ├── test_df.csv
        └── valid_df.csv
```
The final result is stored in the `diff` directory, which contains fully prepared image differences with corresponding masks, and `*.csv` files with the names of images in the datasets.

All main constants (directories, sizes of pieces, etc.) are initialized in the `sentinel_download/settings.py` file.

To download the tiles, you have to put the `key.json` file (with your GCP key) in the `sentinel_download` path.

The tiles which should be downloaded are listed in `../data/tiles/tiles_time-dependent.txt`, but the actual file, containing names of tiles could be changed, correcting the `self.labeled_tiles_to_download` in `class SentinelDownload` (`sentinel_download.py`). The files will be downloaded into the `DOWNLOADED_IMAGES_DIR = 'data/source_images/'`. Update `gcp_config.ini` to specify the names of tiles and bands (10m, 20m only) which should be downloaded; the cloud masks (20m) are retrieved dy default with any set of the bands.

Tiles are preprocessed (merging bands into `MODEL_TIFFS_DIR = 'data/model_tiffs'`) and cropped into small pieces with `from prepare_tiff import preprocess` and `from prepare_pieces import PreparePieces` accordingly.

All dependencies are given in requirements.txt. Possibly, you will not be able to use `gdal` features; to install the `gdal` program, we provide the `install_gdal.sh` script (for Ubuntu users).

## Dataset
You can download our datasets directly from Google drive for the baseline and time-dependent models. The image tiles from Sentinel-2, which were used for our research, are listed in [this folder](https://nositeyet).

The data include *.geojson polygons:
* [baseline](https://nositeyet): 2318 polygons, **36UYA** and **36UXA**, **2016-2019** years;
* [time-dependent](https://nositeyet): **36UYA** (two sets of separated annotations, 278 and 123 polygons -- for spring and summer seasons respectively, **2019** year) and **36UXA** (1404 polygons, **2017-2018** years).
The files contain the following columns: `tileID` (ID of a tile, which was annotated), `img_date` (the date, at which the tile was observed), and `geometry` (polygons of deforestation regions). 

**TBD:** Also, we provide the set of images and masks prepared for training segmentation models as the [Kaggle dataset](https://nositeyet).

## Training
**TBD**
