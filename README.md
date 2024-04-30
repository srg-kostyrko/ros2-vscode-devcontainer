# ros2-vscode-devcontainer

Setup to develop for ROS2 in docker containers using VSCode Devcontainers feature. Handy for case when you don't want install ROS2 on your OS. 
It features VNC for working with GUI as it allows to work on MacOS/Windows where just sharing X server from docker container is tricky.

Required software:
- docker (for Mac I recomend OrbStack as I had issues running ROS2 on Docker Desktop - periodically it would fail to a state requiring full docker reset)
- VSCode

To start working:
- opening project in VSCode 
- install recommended extensions
- open commands panel, search for `Dev Containers: Reopen in container` and run it
- OR click on icon in bottom left corner and select `Reopen in container` 

First time it takes some time to build docker containers.

All project files will be copied to `/home/ros/ws`.

There are some handy aliases configured in zsh

- `rosdi` - installs deps for packages in `src`
- `cbuild` - `colcon build --symlink-install`
- `ssetup` - `source /home/ros/ws/install/local_setup.zsh`
- `gen_pkg_py` - generates package with ament_python builder
- `gen_pkg_c` - generates package with ament_cmake builder

