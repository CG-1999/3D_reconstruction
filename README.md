# sfm from scratch

My attempt at writing a structure-from-motion pipeline myself instead of just running COLMAP. Everything is in `sfm.ipynb`.

It takes a folder of overlapping photos and gives you camera poses plus a sparse 3D point cloud.

## What it does

1. Calibrates the camera from chessboard photos
2. Undistorts the images and extracts SIFT features
3. Initializes from the first two images (essential matrix + triangulation)
4. Adds the remaining images one by one with PnP + RANSAC
5. Runs bundle adjustment (scipy `least_squares`, linear first, then Huber)
6. Shows the point cloud in Open3D and exports a COLMAP text model

## Setup

```
pip install opencv-python numpy scipy tqdm open3d jupyter
```

Put chessboard photos in `calib/` and the scene photos in `images/`, then run the notebook top to bottom. Edit `SQUARE_SIZE` and `PATTERN` in the calibration cell to match your board.

Images are matched to the previous one in filename order, so shoot them in sequence with good overlap.

## Results

On my 36-image set: 3039 points, 1.12 px final reprojection RMSE, about 86% of observations under 2 px. Calibration RMS was 0.60 px.

## Known issues

- Only consecutive images are matched, so no loop closure and it will drift on long captures
- Intrinsics aren't refined in bundle adjustment
- Sparse output only
- Some paths in the export cell are hardcoded to my machine
