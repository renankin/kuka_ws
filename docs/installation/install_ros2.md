## Install ROS 2 Jazzy on Linux (dev)

Source: https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html

## Set locale

Make sure you have a locale which supports `UTF-8`. If you are in a minimal environment (such as a docker container), the locale may be something minimal like `POSIX`. We test with the following settings. However, it should be fine if you’re using a different UTF-8 supported locale.

```bash
locale  # check for UTF-8

sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8

locale  # verify settings
```

## Enable required repositories

You will need to add the ROS 2 apt repository to your system.

First ensure that the Ubuntu university repository is enabled by running the following commands:

```bash
sudo apt install software-properties-common
sudo add-apt-repository universe
```

The `ros-apt` source packages provide keys and apt source configuration for the various ROS repositories.

Installing the `ros2-apt-source` package will configure ROS 2 repositories for your system. Updates to repository configuration will occur automatically when new versions of this package are released to the ROS repositories.

```bash
sudo apt update && sudo apt install curl -y
export ROS_APT_SOURCE_VERSION=$(curl -s https://api.github.com/repos/ros-infrastructure/ros-apt-source/releases/latest | grep -F "tag_name" | awk -F\" '{print $4}')
curl -L -o /tmp/ros2-apt-source.deb "https://github.com/ros-infrastructure/ros-apt-source/releases/download/${ROS_APT_SOURCE_VERSION}/ros2-apt-source_${ROS_APT_SOURCE_VERSION}.$(. /etc/os-release && echo ${UBUNTU_CODENAME:-${VERSION_CODENAME}})_all.deb"
sudo dpkg -i /tmp/ros2-apt-source.deb
```

## Install development tools

If you are going to build ROS packages or otherwise do development, you can also install the development tools:

```bash
sudo apt update && sudo apt install ros-dev-tools
```

## Install ROS 2

Update your apt repository caches after setting up the repositories.

```bash
sudo apt update
```

ROS 2 packages are built on frequently updated Ubuntu systems. It is always recommended that you ensure your system is up to date before installing new packages.

```bash
sudo apt upgrade
```

Desktop Install (Recommended): ROS, RViz, demos, tutorials.

```bash
sudo apt install ros-jazzy-desktop
```

ROS-Base Install (Bare Bones): Communication libraries, message packages, command line tools. No GUI tools.

```bash
sudo apt install ros-jazzy-ros-base
```

## Install additional RMW implementations (optional)

The default middleware that ROS 2 uses is `Fast DDS`, but the middleware (RMW) can be replaced at runtime. See the [guide](https://docs.ros.org/en/jazzy/How-To-Guides/Working-with-multiple-RMW-implementations.html) on how to work with multiple RMWs.

## Setup environment

Set up your environment by sourcing the following file.

```bash
source /opt/ros/jazzy/setup.bash
```

> **Note:** Replace `.bash` with your shell if you’re not using bash. Possible values are: `setup.bash`, `setup.sh`, `setup.zsh`.

We can also include the following line on the `~\bashrc` so that it always sourcing when we open a new terminal:

```bash
# Source ROS2 Jazzy
source /opt/ros/jazzy/setup.bash
```

## Try some examples

If you installed `ros-jazzy-desktop` above you can try some examples.

In one terminal, source the setup file and then run a C++ `talker`:

```bash
source /opt/ros/jazzy/setup.bash
ros2 run demo_nodes_cpp talker
```

In another terminal source the setup file and then run a Python `listener`:

```bash
source /opt/ros/jazzy/setup.bash
ros2 run demo_nodes_py listener
```

You should see the `talker` saying that it’s `Publishing` messages and the `listener` saying `I heard` those messages. This verifies both the C++ and Python APIs are working properly. Hooray!

## Install rosdep

```bash
sudo apt install python3-rosdep
```
