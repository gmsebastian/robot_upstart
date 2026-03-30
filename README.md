
# ⏻ robot_upstart

Forked from https://github.com/clearpathrobotics/robot_upstart

This package provides an utility to manage ROS2 nodes as systemd services, allowing them to be **automatically started on boot** and easily managed with standard systemd tools.

## Installation

clone this repository in a convenient path inside your workspace:

```bash
git clone https://github.com/gmsebastian/robot_upstart.git
```

From the root of your ROS2 workspace, run:

```bash
colcon build --packages-select robot_upstart
source install/setup.bash
```

## Usage

### Create a systemd service for a ROS2 node:

```bash
ros2 run robot_upstart install <package_name>/launch/<launch_file.launch.py> --job <service_name> --setup <path/to/setup.bash> --symlink
```

Where:
- `<service_name>`: The name of the systemd service.
- `<path/to/setup.bash>`: Specifies the setup.bash file to be used.
- `--symlink`: Creates a symlink that allows the launch file to be modified without needing to reinstall the service.

> [!WARNING]
> The path to the launch file must start with the package directory.
>
> **Absolute paths won't work.**

after that, restart the systemd daemon to apply the changes:

```bash
sudo systemctl daemon-reload
```

### Manage the service:

```bash
sudo systemctl <action> <service_name>
```

Where `<action>` can be:

- `start`: To start the service.
- `stop`: To stop the service.
- `enable`: To enable the service to start at startup.
- `disable`: To disable the service to start at startup.

### Watch the ROS2 launch logs

```bash
journalctl -u <service_name> -f
```

### Remove the service

```bash
ros2 run robot_upstart uninstall <service_name>
```

And restart the systemd daemon:

```bash
sudo systemctl daemon-reload
```

### Convenient aliases

To avoid long systemd commands, I suggest you add the following bash functions to manage the services:

For `start`, `stop`, `enable` or `disable` services:

```bash
robot() {
    sudo systemctl "$1" "$2"
}
```

For watching system logs:

```bash
robotlog() {
    journalctl -u "$1" -f -o cat
}
```

This way, you can run `robot start <service>` instead of `sudo systemctl start <service>` and `robotlog <service>` instead of `journalctl -u <service> -f -o cat`
