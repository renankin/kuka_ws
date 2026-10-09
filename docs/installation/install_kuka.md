## Scope

This guide explain how to install KUKA driver and micro-epsilon drivers for using ROS 2 in the welding cell.


## Setting up EKI and RSI in KUKA Robot

Repository: https://github.com/kroshu/kuka-external-control-sdk

### Networking Steps

During configuration and setup, connect your computer with WorkVisual installed to the controller using an Ethernet cable. To enable communication between the two systems, set the IP addresses of the relevant interfaces (KLI on the robot controller and the Ethernet interface on the computer) to be on the same subnet.

Create a new network interface on the KRC for RSI. Ensure the RSI interface is on a different subnet than the KLI. To add a new network interface on the robot controller, follow these steps:

- Select at least the *Expert* user group.
- Navigate to *Main menu* → *Start-up* → *Network configuration*.
- Click on the *Advanced...* button.
- At the bottom, click *Add interface*.
- Select the new interface, which will be listed as *virtual6 (virtual)* by default.
- Select Mixed IP address and keep the default settings:
  - Receiving task: Target subnet
  - Real-time receiving Task: UDP
- Choose an IP address that is on a different subnet than the KLI interface:
  - IP address: 172.31.2.20
  - Subnet mask: 255.255.255.0
- Windows interface checkbox should NOT be checked 8. At the bottom, click *Save*.
- Restart the robot control controller to ensure configuration changes are applied.

Update the IP address in the `rsi_ethernet.xml` file with the IP address assigned to the external control computer. This informs the controller where to send data packets during each RSI cycle.

> Set the network interface of the computer performing external control to be on the same subnet as the RSI interface. If you are using the EKI or mxA wrappers, make sure that the computer for external control can reach both the KLI and RSI interfaces. The best way to do this is to assign multiple IP addresses to one physical network connection.

#### Example network configuration

- Windows computer with WorkVisual: `172.32.1.150/24`
- Robot controller KLI: `172.32.1.147/24`
- Robot controller RSI network: `172.32.2.147/24`
- Control PC RSI connection: `172.32.2.148/24`
- Control PC KLI connection: `172.32.1.148/24` (needed for EKI/mxA wrappers)

### Steps within WorkVisual

There are several KUKA-specific files located in the `krc_setup/kss` directory that need to be copied into the WorkVisual project

**RSI setup**

- Copy all files from the `kuka-external-control-sdk/krc_setup/Config/User/Common/SensorInterface` directory into the `Config/User/Common/SensorInterface/` directory.
  > The Config folder can be found inside WorkVisual in the Configuration and Commissioning workspace, in the `Files` tab (left hand side).
- Copy all files from the `kuka-external-control-sdk/krc_setup/KRC/C1/Program/RSI` directories into `KRC/R1/Program/`.

**EKI server setup**

- Copy the `EkiKSSinterface.xml` file into the `Config/User/Common/EthernetKRL/` directory.
- Copy all files from the `kuka-external-control-sdk/krc_setup/KRC/C1/EKIServer/` directories into `KRC/R1/Program/`.
- Modify the `KRC/STEU/Mada/$option.dat` file.
- Change the `$CHCK_MOVENA` flag to `FALSE` to prevent the system from checking whether `$MOVE_ENABLE` is connected to `$IN[1025]`.
- To ensure the EKI server runs automatically, edit the `KRC/R1/System/sps.sub` submit interpreter file.
- Add a call to `SPS_Init()` in the `USER INIT` fold and a call to `SPS_LoopCall()` in the `USER PLC` fold.
- Deploy WorkVisual project.

## Install kuka driver on ROS workspace

Link to repository: https://github.com/kroshu/kuka_drivers

Create ROS2 workspace (if not already created).

```bash
mkdir -p ~/ros2_ws/src
```

Clone KUKA ROS2 repositories.

```bash
cd ~/ros2_ws/src
git clone https://github.com/kroshu/kuka_drivers.git
vcs import < kuka_drivers/upstream.repos
```

Install dependencies using `rosdep`.

```bash
cd ~/ros2_ws
rosdep update
sudo apt upgrade
rosdep install --from-paths . --ignore-src --rosdistro $ROS_DISTRO -y
```

Build all packages in workspace.

```bash
cd ~/ros2_ws
colcon build
```

Source workspace.