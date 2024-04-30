ARG ROS_DISTRO=iron
FROM osrf/ros:${ROS_DISTRO}-desktop-full

ARG USERNAME=ros
ARG USER_UID=1000
ARG USER_GID=$USER_UID
ARG HOME=/home/$USERNAME

ENV DEBIAN_FRONTEND noninteractive
ENV QT_X11_NO_MITSHM 1
ENV NVIDIA_VISIBLE_DEVICES \
    ${NVIDIA_VISIBLE_DEVICES:-all}
ENV NVIDIA_DRIVER_CAPABILITIES \
    ${NVIDIA_DRIVER_CAPABILITIES:+$NVIDIA_DRIVER_CAPABILITIES,}graphics
ENV XDG_RUNTIME_DIR=/tmp/ros-xdg
ENV LIBGL_ALWAYS_SOFTWARE 1

# Install base paackages
RUN apt-get update && apt-get install software-properties-common -y
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        sudo \
        curl \
        wget \
        zsh \
        git \
        nano \
        vim \
        tmux \
        xterm \
        mesa-utils \
        libglu1-mesa-dev \
        xdg-utils \
        dbus-x11 \
        python3-pip \
        psmisc

# Create the user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && usermod --shell /bin/zsh $USERNAME
RUN chmod 0440 /etc/sudoers.d/$USERNAME \
    && chown -R $USERNAME:$USERNAME $HOME \
    && chmod 666 /dev/null \
    && chmod 666 /dev/urandom \
    && mkdir -p /tmp/ros-xdg \
    && chmod 700 /tmp/ros-xdg \
    && chown -R $USERNAME:$USERNAME /tmp/ros-xdg

# Installing shell fonts
RUN mkdir -p ~/.local/share/fonts \
    && cd ~/.local/share/fonts \
    && curl -fLO https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Regular.ttf \
    && curl -fLO https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold.ttf \
    && curl -fLO https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Italic.ttf \
    && curl -fLO https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold%20Italic.ttf \
    && fc-cache -f -v

# Shell configuration
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
RUN git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k

# Installing ROS related stuff
# RUN add-apt-repository ppa:kisak/kisak-mesa && apt update
RUN apt-get install -y \
    ros-${ROS_DISTRO}-rmw-cyclonedds-cpp \
    ros-${ROS_DISTRO}-ros-gz \
    ros-${ROS_DISTRO}-gazebo-ros-pkgs \
    ros-${ROS_DISTRO}-gazebo-plugins \
    ros-${ROS_DISTRO}-ros-ign-bridge \
    ros-${ROS_DISTRO}-xacro \
    ros-${ROS_DISTRO}-urdf-tutorial \
    ros-${ROS_DISTRO}-plotjuggler-ros \
    ros-${ROS_DISTRO}-rqt \
    ros-${ROS_DISTRO}-rqt-common-plugins \
    ros-${ROS_DISTRO}-plotjuggler-ros \
    ros-${ROS_DISTRO}-robot-localization \
    ros-${ROS_DISTRO}-rqt-tf-tree
    

# RUN apt upgrade -y
RUN pip3 install setuptools==58.2.0 \
    numpy \
    transformations

# Cleanup
RUN apt autoclean -y \
    && apt autoremove -y \
    && rm -rf /var/lib/apt/lists/*


USER $USERNAME

COPY configs/.zshrc $HOME/.zshrc

RUN sudo chown -R $USERNAME:$USERNAME $HOME/.zshrc
RUN echo 'alias rosdi="rosdep install --from-paths src --ignore-src --rosdistro=${ROS_DISTRO} -y"' >> $HOME/.zshrc
RUN echo "source /opt/ros/${ROS_DISTRO}/setup.zsh" >> $HOME/.zshrc
RUN echo "source /usr/share/gazebo/setup.sh"  >> $HOME/.zshrc
RUN echo "source /home/ros/ws/install/local_setup.zsh" >> $HOME/.zshrc
RUN echo "export _colcon_cd_root=/opt/ros/${ROS_DISTRO}/" >> $HOME/.zshrc
RUN echo 'eval "$(register-python-argcomplete3 ros2)"' >> $HOME/.zshrc
RUN echo 'eval "$(register-python-argcomplete3 colcon)"'  >> $HOME/.zshrc

COPY configs/.p10k.zsh $HOME/.p10k.zsh
COPY configs/.tmux.conf $HOME/.tmux.conf

RUN rosdep update

CMD [ "/bin/zsh"]