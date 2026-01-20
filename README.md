# hpe plugin for MADS

This is a Source plugin for [MADS](https://github.com/MADS-NET/MADS). 

<provide here some introductory info>

*Required MADS version: 1.3.1.*
*Required PCL version: 1.13 or 1.14.*


## Supported platforms

Currently, the supported platforms are:

* **Linux** 
* **MacOS**
* **Windows**


## Installation

Linux and MacOS:

```bash
cmake -Bbuild -DCMAKE_INSTALL_PREFIX="$(mads -p)"
cmake --build build -j4
sudo cmake --install build
```

Windows:

```powershell
cmake -Bbuild -DCMAKE_INSTALL_PREFIX="$(mads -p)"
cmake --build build --config Release
cmake --install build --config Release
```

## Windows — Detailed installation

The steps below describe how to prepare a Windows development machine for building and running the `hpe` plugin and its dependencies.

1. Install MADS

	- Download and install the latest MADS executable from:
	  https://github.com/pbosetti/MADS/releases

2. OpenCV

	- Download the OpenCV Windows release from https://opencv.org/releases/
	- Run the downloaded executable and extract the folder to `C:\opencv`.

3. OpenVINO 2023.3

	- Download the OpenVINO 2023.3 archive for Windows:
	  https://docs.openvino.ai/2023.3/openvino_docs_install_guides_overview.html?VERSION=v_2023_3_0&OP_SYSTEM=WINDOWS&DISTRIBUTION=ARCHIVE
	- Extract the archive and move the resulting folder to `C:\Program Files (x86)\Intel`.
	- Rename the folder to `openvino_2023.3`.

4. Add required directories to the system PATH (verify the exact paths after installation):

	- `C:\opencv\build\x64\vc16\bin`
	- `C:\Program Files (x86)\Intel\openvino_2023.3\runtime\bin\intel64\Release`
	- `C:\Program Files (x86)\Intel\openvino_2023.3\runtime\3rdparty\tbb\bin`

5. Set environment variables (PowerShell example):

```powershell
setx OpenCV_DIR C:\opencv
setx OpenVINO_DIR "C:\Program Files (x86)\Intel\openvino_2023.3"
```

6. Install PCL (Point Cloud Library)

	- Download the PCL v1.14.1 installer from:
	  https://github.com/PointCloudLibrary/pcl/releases
	- Run the installer and accept the defaults (you can disable desktop icons if desired).
	- Add the following paths to PATH (adjust if you installed to a different folder):

	- `C:\Program Files\PCL 1.14.1`
	- `C:\Program Files\PCL 1.14.1\bin`
	- `C:\Program Files\PCL 1.14.1\include`
	- `C:\Program Files\PCL 1.14.1\3rdParty`
	- `C:\Program Files\PCL 1.14.1\3rdParty\VTK\bin`

7. Azure Kinect SDK and Body Tracking SDK

	- Install Azure Kinect Sensor SDK v1.4.1:
	  https://www.microsoft.com/en-us/download/details.aspx?id=101454
	- Install Azure Kinect Body Tracking SDK v1.1.2:
	  https://www.microsoft.com/en-us/download/details.aspx?id=104221
	- Set environment variables:

	  - `K4A_DIR` => `C:\Program Files\Azure Kinect SDK v1.4.1`
	  - `K4ABT_DIR` => `C:\Program Files\Azure Kinect Body Tracking SDK`

	- Add to PATH:

	  - `C:\Program Files\Azure Kinect SDK v1.4.1\tools`
	  - `C:\Program Files\Azure Kinect Body Tracking SDK\tools`

8. Microsoft MPI

	- Install Microsoft MPI (MS-MPI) from:
	  https://www.microsoft.com/en-us/download/details.aspx?id=105289
	- Add to PATH (if not added by the installer):

	  - `C:\Program Files\Microsoft MPI\Bin`

9. pkg-config-lite (optional, used by some build tools)

	- Download pkg-config-lite from:
	  https://sourceforge.net/projects/pkgconfiglite/files/0.28-1/
	- Extract `pkg-config-lite-0.28-1` to `C:\pkg-config-lite-0.28-1` and add `C:\pkg-config-lite-0.28-1\bin` to PATH.

10. Restart your PC to ensure environment variable changes take effect.

11. Build and install `hpe` (assuming `mads` is on PATH and `mads -p` returns MADS prefix):

```powershell
git clone <repo-url> C:\mirrorworld\hpe
cd C:\mirrorworld\hpe
cmake -Bbuild -DCMAKE_INSTALL_PREFIX="$(mads -p)"
cmake --build build --config Release
cmake --build build --config Release -t install
```

12. Optional: MongoDB for storing logs

	- Install and run Docker Desktop (no login required): https://www.docker.com/
	- Start a MongoDB container (from the project root you can create a `db` folder to persist data) and install the MongoDB C++ driver locally (instructions summarized). Add `C:\mongo-cxx-driver\bin` to PATH if you installed it there.

```powershell
docker run --name mads-mongo --restart unless-stopped -v ${PWD}/db:/data/db -p27017:27017 -d mongo
# Download the mongo-cxx-driver archive (or use browser to download if curl flags are not supported)
curl -OL https://github.com/mongodb/mongo-cxx-driver/releases/download/r4.1.1/mongo-cxx-driver-r4.1.1.tar.gz
# or download via browser and extract
# tar -xzf mongo-cxx-driver-r4.1.1.tar.gz
# cd mongo-cxx-driver-r4.1.1/build
cmake -G "Visual Studio 17 2022" -A "x64" -DCMAKE_CXX_STANDARD=17 -DCMAKE_INSTALL_PREFIX=C:\mongo-cxx-driver
cmake --build . --config RelWithDebInfo
cmake --build . --target install --config RelWithDebInfo
```

### Updating MADS without recompiling everything

If you only need to update MADS to a new version and want to avoid recompiling all dependencies, do the following before building again:

1. Delete the following folders from the build tree (these are re-downloaded/build subprojects):

	- `build/_deps/plugin-build`
	- `build/_deps/pugg-build`

2. Rebuild and install (from project root):

```powershell
cmake -Bbuild -DCMAKE_INSTALL_PREFIX="$(mads -p)"
cmake --build build --config Release -t install
```

This forces CMake to refresh the plugin/pugg subprojects and pick up the new MADS installation without re-building every external dependency.

## Running the broker, logger and `hpe` agents

1. Find the broker IP address to provide to remote agents (run on the broker machine). This prints available network interfaces and their IP addresses. 

```powershell
mads-broker -n list
```
2.  Choose the interface you want (for example, index `11` or name `en0`) and start the broker bound to that interface.The broker will print the `<IP>` and `<PORT>` that remote agents should use. If agents run on the same machine as the broker, you can omit IP and PORT when starting them.

```powershell
# Example: bind to interface named en0
mads-broker -n en0
```

3. If you want to save data to MongoDB, ensure the Mongo container is running (see step 12 above) or start it with Docker.

4. Start the logger:

```powershell
mads-logger
```

5. Start `hpe` agents (on each agent machine, or locally):

```powershell
mads-source -s tcp://<IP>:<PORT> hpe.plugin
```

Replace `<IP>` and `<PORT>` with the values printed by `mads-broker`.


## Disabling Kinect Azure libraries compilation

If you don't want to compile the Kinect Azure libraries, add the option:

```powershell
cmake -Bbuild -DSKIP_KINECT_AZURE=ON
```

This allows you to compile the project even on systems where Kinect Azure libraries are not available.


## Debug Options

All runtime debug flags are read from the `mads.ini` file under the `[hpe.debug]` section (see the example below). Each boolean flag enables additional logging, diagnostics and (in some cases) saving of intermediate files into a `Debug/` folder. Enabling debug options can slow processing and increase disk usage — use them only when troubleshooting.

For each flag we list (A) what is printed to the terminal when the flag is enabled and (B) what files (if any) are written to disk and where.

- `check_computation_time` (boolean): 
    - Prints computational times for each step.

- `setup_video_capture` (boolean)
    - Camera/device detection and chosen device id (e.g. "Camera id: 0" or default message).
    - Selected resolution and framerate information.
    - Kinect Azure calibration and tracker processing mode messages (e.g. "Body tracker processing mode: ...").
    - Warnings/errors if devices fail to open or transforms cannot be created.
    - Messages when debug directories are created (e.g. "Created debug directory: <path>").
    - `Debug/camera_intrinsics.csv` — CSV with cx, cy, fx, fy (created for MKV/dummy and Azure cases).
    - `Debug/conversion matrices/x_conversion_matrix.txt` and `.../y_conversion_matrix.txt` — pixel-to-real-world conversion matrices (TXT).
    - Transformed RGB images saved as `azure_rgb_transformed_to_depth_coordinates.jpg` or `mkv_rgb_transformed_to_depth_coordinates.jpg` inside the `Debug/` tree.

- `acquire_frame` (boolean)
    - Per-frame acquisition status and errors (e.g. "Error: Failed to get valid color image ...").
    - For MKV/dummy mode it logs frame timestamps and the global frame counter (frame time and counter values).
    - Warnings about missing streams in MKV (e.g. "This MKV file might not contain color stream data").
    - Dummy/MKV mode: RGB and depth images saved under `Debug/rgb images in depth coordinates/` and `Debug/depth images/` with filenames containing agent id and timestamp/frame number.

- `skeleton_from_depth_compute` (boolean)
    - Logs when no bodies are detected ("No bodies detected in the frame.").
    - If a body is detected prints the entire 3D skeleton to the terminal (each joint name and [x,y,z]).

- `skeleton_from_rgb_compute` (boolean)
    - Pipeline readiness and errors (e.g. "Pipeline is not ready to process", "Timeout waiting for pipeline data").
    

- `hessian_compute` (boolean)
    - Warnings/errors if the pipeline is not initialized or no keypoints are available.
    - The function draws ellipses and keypoints on `_rgb` (visible in the viewer if enabled).

- `cov3D_compute` (boolean)
    - `Debug/Covariances 3D/cov3D_data.json` appended JSON frames containing the 3x3 covariance matrices for each processed frame.

- `consistency_check` (boolean)
    - Currently the code only prints a single line: "Performing consistency check..."; the actual consistency logic is not implemented.

- `point_cloud_filter` (boolean)
    - Messages when body index map is null or when filtering is disabled (e.g. "Body index map is null. Creating unfiltered point cloud.").
    - Writes PLY files for filtered/unfiltered (according to `filter_point_cloud` value) point clouds into `Debug/point clouds/` named `<frame>_filtered_point_cloud_frame.ply` or `<frame>_unfiltered_point_cloud_frame.ply`.

- `coordinate_transform` (boolean)
    - Prints the message "Applying camera coordinate transformation..." and prints the transformed 3D joint coordinates to the terminal.

- `viewer` (boolean)
    - The viewer displays images in real-time but does not save files by itself

Notes: Clean up `Debug/` periodically — images, PLYs and JSON logs can grow quickly when debugging is enabled.

## INI settings (example)

The plugin supports the following settings in the INI file:

```ini
[hpe]
pub_topic = "hpe_out"
period = 30
azure_device = 0
CUDA = false
model_file = "C:/mirrorworld/hpe/models/human-pose-estimation-0001.xml"
dummy = false
filter_point_cloud = false
resolution_rgb = "1280x720"
calibration_mode = false

[hpe.debug]
check_computational_times = true
setup_video_capture = false
acquire_frame = false
skeleton_from_depth_compute = false
skeleton_from_rgb_compute = false
hessian_compute = false
cov3D_compute = false
consistency_check = false
point_cloud_filter = false
coordinate_transform = false
viewer = false

[hpe.SN000367520812]
Rxx = 1
Rxy = 0
Rxz = 0
Ryx = 0
Ryy = 1
Ryz = 0
Rzx = 0
Rzy = 0
Rzz = 1
Tx = 0
Ty = 0
Tz = 0
```
