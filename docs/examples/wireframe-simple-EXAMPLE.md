# Wireframe mesh simple EXAMPLE


```python
from stl import mesh as stl_mesh
import numpy as np
import pyvista as pv
import geopandas as gpd
import pandas as pd
from scipy.interpolate import griddata
from scipy.spatial import Delaunay
import fiona

from wireframe_functions import gdf_to_points_grid_from_rc, build_horizontal_shots, build_endcap, combine_mesh_parts
```

## Load and investigate gdf

All horizontal shots


```python
gdf = gpd.read_file("02_200206.gpkg")
```


```python
# group by depth and calculate summary statistics
summary = gdf.groupby("depth").agg(
    mInc_min=("mInc", "min"),
    mInc_max=("mInc", "max"),
    count=("mInc", "count")
).reset_index()

# Print results
for _, row in summary.iterrows():
    print(f"Depth: {row['depth']}, mInc Min: {row['mInc_min']:.2f}, Max: {row['mInc_max']:.2f}, Count: {int(row['count'])}")
```

    Depth: 448.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 451.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 453.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 455.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 456.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 457.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 458.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 459.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 460.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 462.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 464.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 466.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 468.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 470.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 472.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 474.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 476.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 478.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 480.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 482.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 484.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 486.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 488.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 490.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 492.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 494.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 496.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 498.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 500.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 502.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 504.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 506.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 508.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 510.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 512.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 514.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 516.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 518.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 520.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 522.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 524.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 526.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 528.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 530.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 532.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 534.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 536.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 538.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 540.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 542.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 544.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 546.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 548.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 550.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 552.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 554.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 556.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 558.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 560.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 562.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 564.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 566.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 568.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 570.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 572.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 574.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 576.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 578.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 580.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 582.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 584.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 586.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 588.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 590.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 592.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 594.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 596.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 598.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 600.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 601.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 602.0, mInc Min: 90.00, Max: 90.00, Count: 128
    Depth: 603.0, mInc Min: 90.00, Max: 90.00, Count: 128
    

## Explore point cloud with PyVista


```python
# get points
points = np.array([[pt.x, pt.y, pt.z] for pt in gdf.geometry])

# build point cloud
point_cloud = pv.PolyData(points)
glyphs = point_cloud.glyph(geom=pv.Sphere(), orient=False)

# plot without RGB colors
plotter = pv.Plotter()
plotter.add_mesh(glyphs)
plotter.show()
```


![Simple Point Cloud Example](../images/example_simple_points.PNG)

## Build Mesh


```python
h_vertices, h_faces = build_horizontal_shots(gdf)
```

    Grouped by 'depth' into 82 layers.
    

## Plot Final Mesh


```python
combine_mesh_parts(
    parts=[
        (h_vertices, h_faces),
    ],
    filename="full_cave_mesh.stl"
)

mesh = pv.read("full_cave_mesh.stl")
mesh.plot()
```

    Combined mesh saved to: full_cave_mesh.stl
    


![Simple Mesh Example](../images/example_simple_mesh.PNG)