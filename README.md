# Jetson-Nano

*Ce répertoire sert de documentation pour le Jetson Nano. Il relatera des différents tests effectués avec la carte.*

## Installation de l'OS

Suivre le [tutoriel de Nvidia](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit#intro)

## Installation de ROS

### Installation classique

Suivre le [tutoriel officiel de ROS](https://wiki.ros.org/noetic/Installation/Ubuntu)

### Installation par Docker

La Jetson Nano tourne sur une architecture arm64 donc il faut télécharger une image adaptée.

#### Création du `Dockerfile`

```Dockerfile
FROM ros:noetic

RUN apt-get update
RUN apt-get install vim iputils-ping iproute2 -y
RUN apt-get install ros-noetic-rqt ros-noetic-rqt-common-plugins ros-noetic-ros-tutorials ros-noetic-rviz -y
RUN apt-get install ros-noetic-mavros ros-noetic-mavros-extras -y
RUN wget https://raw.githubusercontent.com/mavlink/mavros/master/mavros/scripts/install_geographiclib_datasets.sh && chmod +x install_geographiclib_datasets.sh && ./install_geographiclib_datasets.sh
RUN echo ". /opt/ros/$ROS_DISTRO/setup.bash" >> ~/.bashrc
```

#### Construction de l'image `ros`

```sh
# Dans le répertoire du fichier Dockerfile 
docker build -t ros .
```

#### Création du conteneur `ros_noetic`

```sh
sudo docker run -it \
    --privileged \
    --restart=always \
    --network host \
    --name=ros_noetic \
    --user=$(id -u $USER):$(id -g $USER) \
    --env="DISPLAY" \
    --env="QT_X11_NO_MITSHM=1" \
    --env="LIBGL_ALWAYS_SOFTWARE=1" \
    --volume="/dev/serial:/dev/serial" \
    --volume="/etc/group:/etc/group:ro" \
    --volume="/etc/passwd:/etc/passwd:ro" \
    --volume="/etc/shadow:/etc/shadow:ro" \
    --volume="/etc/sudoers.d:/etc/sudoers.d:ro" \
    --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw" \
    --volume="$HOME/.Xauthority:/root/.Xauthority:rw" \
    -u root \
    ros \
    bash
```

## Liens

- [RPlidar A2M8](https://www.slamtec.ai/home/rplidar_a2/)
- [ros](http://wiki.ros.org/rplidar)
- [rplidar_ros](https://github.com/slamtec/rplidar_ros)
