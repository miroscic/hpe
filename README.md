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


## Disabling Kinect Azure libraries compilation

If you don't want to compile the Kinect Azure libraries, add the option:

```powershell
cmake -Bbuild -DSKIP_KINECT_AZURE=ON
```

This allows you to compile the project even on systems where Kinect Azure libraries are not available.


## Debug Options

The plugin supports several debug options that can be configured in the `params.json` file under the `debug` section. Each option enables debugging output for specific processing stages:

- **`skeleton_from_depth_compute`**: Enables debug output for 3D skeleton computation from depth camera data (Kinect Azure). Prints body detection status and joint position processing on the terminal.

- **`skeleton_from_rgb_compute`**: Enables debug output for 2D skeleton computation from RGB camera using OpenVINO human pose estimation model. Draws pose detection pipeline status.

- **`point_cloud_filter`**: Not developed yet.

- **`hessian_compute`**: Not developed yet.

- **`cov3D_compute`**: Saves a json file with the covariances in the folder "Debug/Covariances 3D".

- **`viewer`**: Enables real-time visualization windows showing RGB and depth images side by side. Press ESC to exit the viewer.

## INI settings

The plugin supports the following settings in the INI file:

```ini
[hpe]
# Describe the settings available to the plugin
```

All settings are optional; if omitted, the default values are used.


## Executable demo

<Explain what happens if the test executable is run>

---