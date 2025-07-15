# SonarPy

A Python Class for processing and analyzing sonar data.

---

## Parameters

| Name            | Type                          | Description                                                                 |
|-----------------|-------------------------------|-----------------------------------------------------------------------------|
| `surveyxyz`     | `tuple` or `None`             | The (x, y, z) coordinates of the survey datum in the input CRS.             |
| `surveyxyzWGS84`| `tuple` or `None`             | The (lat, lon, elevation) of the survey datum in WGS84.                     |
| `year`          | `int`, default=`2024`         | Year of the survey.                                                         |
| `metric`        | `bool`, default=`False`       | Whether units are in meters (True) or feet (False).                         |
| `crs`           | `str` or `pyproj.CRS`         | Input coordinate reference system.                                          |
| `utm_shp_path`  | `str` or `None`, optional     | Path to the UTM zone shapefile. Defaults to a world UTM grid if not set.    |

---

## Attributes

| Name             | Type                          | Description                                                                |
|------------------|-------------------------------|----------------------------------------------------------------------------|
| `crs`            | `str` or `pyproj.CRS`         | Output coordinate reference system.                                        |
| `metric`         | `bool`                        | True for meters, False for feet.                                           |
| `utm_shp_path`   | `str`                         | Path to the UTM shapefile.                                                 |
| `utmzones`       | `geopandas.GeoDataFrame`      | UTM zone polygons.                                                         |
| `zonecrs`        | `str`                         | EPSG code of the correct UTM zone.                                         |
| `magdec`         | `float`                       | Magnetic declination in degrees.                                           |
| `surveyxyz`      | `tuple`                       | Survey point in original CRS.                                              |
| `pntgdf`         | `geopandas.GeoDataFrame`      | Survey point as GeoDataFrame.                                              |
| `pntgdfUTM`      | `geopandas.GeoDataFrame`      | Survey point reprojected to UTM.                                           |
| `surveyxyzUTM`   | `tuple`                       | Survey datum in UTM coordinates.                                           |
| `xyz_gdf`        | `geopandas.GeoDataFrame`      | 3D point cloud in UTM.                                                     |
| `xyz_gdfCRS`     | `geopandas.GeoDataFrame`      | 3D point cloud in final user-specified CRS.                                |

---

## Setup

To calculate magnetic declination, download the IGRF13 coefficient file from:

> https://www.ngdc.noaa.gov/IAGA/vmod/coeffs/igrf13coeffs.txt

Place it here:
```text
C:/ProgramData/anaconda3/envs/<yourenv>/lib/site-packages/pyCRGI/data/igrf13coeffs.txt
```

---

## Methods

### `las2txt_path(path)`

Converts all `.laz` files in a directory to ASCII text files using `las2txt64`.

Each output `.txt` file includes:
- X, Y, Z coordinates (columns 1–3)
- Intensity (column 4)
- GPS time (column 5)
- A header row for compatibility with `txt2las64`

**Parameters:**

- `path` (`str`): Path to the folder containing `.laz` files.s

### `add_headers_toLAStxt`

Add column headers to las2txt output and save as csv.

**Parameters:**

- `path` (`str`): Path to the folder containing .txt files exported by las2txt64.