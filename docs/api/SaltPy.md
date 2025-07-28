# SaltPy
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

- `path` (str): Path to the folder containing `.laz` files.s

### `add_headers_toLAStxt`

Add column headers to las2txt output and save as csv.

**Parameters:**

- `path` (str): Path to the folder containing .txt files exported by las2txt64.

### `dmv_exported_lwt_to_dfT(path)`

Converts LWT files exported by DimCav Viewer to a `dfT`-formatted DataFrame.

Each row in the DataFrame represents a single sonar shot and includes:

- Depth  
- Range  
- Azimuth (`cAzi`)  
- Tilt

**Parameters:**

- `path` (`str`): Path to the `.LWT` file exported by DimCav Viewer.

**Returns:**

- `dfT` (`pandas.DataFrame`): Reformatted long-form DataFrame.

### `get_well_deviation_delta(df, wb)`

Returns the (dx, dy, dz) deviation from a wellbore survey (`wb`) at the correct measured depth or last available depth.

**Parameters:**

- `df` (`pandas.DataFrame`): DataFrame containing the sonar data.
- `wb` (`pandas.DataFrame`): Wellbore survey data with columns:
  - `MDepth`
  - `dx_ft`
  - `dy_ft`
  - `dz_ft`

**Returns:**

- `tuple`: Delta `(dx, dy, dz)` in feet.

### `open_lwt(file)`

Parses American format `.lwt` (CWR export) sonar file into a DataFrame.

**Parameters:**

- `file` (`str`): Path to the `.lwt` sonar file.

**Returns:**

- `df` (`pandas.DataFrame`): DataFrame parsed from the `.lwt` file.

### `open_lwt(file)`

Parses American format `.lwt` (CWR export) sonar file into a DataFrame.

**Parameters:**

- `file` (`str`): Path to the `.lwt` sonar file.

**Returns:**

- `df` (`pandas.DataFrame`): DataFrame parsed from the `.lwt` file.

### `getMagDev(long, lat, alt=0, year=2024)`

Computes magnetic declination at a given location using [pyCRGI](https://github.com/pleiszenburg/pyCRGI?tab=readme-ov-file).

**Parameters:**

- `long` (`float`): Longitude in decimal degrees.  
- `lat` (`float`): Latitude in decimal degrees.  
- `alt` (`float`, default=`0`): Altitude in kilometers.  
- `year` (`int`, default=`2024`): Year of the survey.

**Returns:**

- `d` (`float`): Magnetic declination (+ve east) in degrees.

### `lwt_df_to_dfT(df)`

Converts an unformatted `pandas.DataFrame` to a long-form format with one row per sonar shot.

**Parameters:**

- `df` (`pandas.DataFrame`): Sonar data parsed from an LWT file.

**Returns:**

- `dfT` (`pandas.DataFrame`): Reformatted long-form DataFrame where each row represents a single sonar shot, including attributes such as:
  - Depth  
  - Range  
  - Azimuth (`cAzi`)  
  - Tilt


