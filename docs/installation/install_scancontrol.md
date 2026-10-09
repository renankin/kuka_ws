## Install scanCONTROL SDK

For installing the scanCONTROL SDK on Ubuntu 22.04 we need to install the `linllt` libraries.

The `linllt` libraries (`libmescan` and `libllt`) are aravis-based libraries for operating scanCONTROL sensors from MICRO-EPSILON.

`libmescan` is a C library for sensor control and data acquisition.

`linllt` is a C++ wrapper.

Following the instruction contained inside the SDK from Linux we need to build the `linltt` libraries.

### Build the `linllt` libraries

The `README.md` file recommends to build the library using Meson build system.

The advantage of using Meson is that it will check for the required dependencies, like `aravis`, and it will complete the necessary steps to prepare compiling and installing the code on the currently used platform.

For compiling and installing Ninja is used.

### Install Meson build system

We can run:

```bash
sudo apt install meson
```

Meso dependencies are Python (>=3.4) and Ninja (>=1.5).

By running the command above Ninja will be installed.

### Install `aravis`

https://github.com/AravisProject/aravis/releases

Go to `aravis` releases link above and install the correct tarball from aravis 0.8, and then run:

```bash
tar -xf aravis-0.8.xx.tar.xz
```

After that we need to install `aravis` using Meson by navigating inside the `\aravis` root folder which was created:

```bash
meson setup build
meson install -C build
```

## Fix bug with `linltt`

In order to install `libllt` we need to fix a bug in the SDK by

Edit `libllt/include/llt.h` and change this:

```cpp
#ifdef __cplusplus
extern "C" {
#endif
#include <mescan.h>
#ifdef __cplusplus
}
#endif
```

To just this:

```cpp
#include <mescan.h>
```

## Install `linltt`

Once we install Meson, we need to go inside the root folder of the SDK folder (it doesn't matter where the folder is located).

We first need to install the `libmescan` and then `libllt` in sequence:

```bash
meson setup build
meson install -C build
```

It might be necessary to call `sudo ldconfig` afterwards, to reload the linker cache.

## Copy module `pylinllt`

To use the libraries with Python (>=3.4), the module pylinllt (located in the python_bindings folder) has to be copied to the site-packages of the desired python installation. The module assumes the library in /usr/local/lib.

This is the command we can run once we `cd` into the python_bindings folder:

```bash
sudo cp pylinllt/ /usr/local/lib/python3.x/dist-packages/ -r
```

## Add firewall rules

It might be necessary to allow traffic on the selected network card so that `arvis` can properly connect with the sensor.

To do that run the following:

```bash
sudo ufw allow in on <interface_name>
sudo ufw allow out on <interface_name>
```

The parameter `<interface_name>` can be get by running `ip link`.

## Test arvis

To check that `aravis` is detecting the sensor we can run the following:

```bash
arv-camera-test-0.8
```

That would show the connected camera and the frequency acquisition from the laser scanner.

Or we can also run

```bash
arv-tool-0.8
```

That would give the ip-address and serial number from the sensor.

## Test the examples

Inside the SDK folder, there is a folder called examples.

If we navigate inside the C++ examples, there is a meson build file:

```bash
meson setup build
meson install -C build
```

When we run this inside the build folder, it will create the executables for each routine.

For example:

```bash
./build/PartialProfile
```

## Install `scancontrol` driver

In order to use the `scancontrol` driver, we have the following dependencies:

* Aravis 0.8.x
* scanCONTROL Linux C++ SDK 1.0.0

Before installing that also ensure that all packages in Ubuntu are updated:

```bash
sudo apt update
suto apt upgrade
```

Then, we need to create a workspace in ROS and clone the `scancontrol` repo:

```shell
cd ros_ws/src
git clone https://github.com/sam-xl/scancontrol.git
```

Make sure ROS is source:

```bash
source /opt/ros/jazzy/setup.bash
```

Install the dependencies of the cloned packages using rosdep:

```shell
cd ros_ws
rosdep install --from-paths src --ignore-src -y
```

Finally, build all packages in the workspace:

```shell
cd ros_ws
colcon build --merge-install --symlink-install
```