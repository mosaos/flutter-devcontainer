FROM ubuntu:25.04

ARG flutter_ver="3.32.4-stable"
ARG openjdk_ver="21.0.2"
ARG asdf_ver="v0.18.0"

WORKDIR /root
ENV HOME /root

# apt install
RUN apt update
RUN apt install -y \
    curl wget vim git xz-utils unzip \
    clang cmake ninja-build pkg-config libgtk-3-dev \
    pulseaudio libnss3 libxkbfile-dev

# asdf
RUN wget https://github.com/asdf-vm/asdf/releases/download/${asdf_ver}/asdf-${asdf_ver}-linux-amd64.tar.gz \
    && tar -xzf asdf-${asdf_ver}-linux-amd64.tar.gz -C /usr/local/bin \
    && rm asdf-${asdf_ver}-linux-amd64.tar.gz
ENV PATH $PATH:$HOME/.asdf/shims

# asdf flutter, java
RUN asdf plugin add flutter \
    && asdf install flutter ${flutter_ver} \
    && asdf set --home flutter ${flutter_ver} \
    && git config --global --add safe.directory /root/.asdf/installs/flutter/${flutter_ver} \
    && flutter config --no-analytics \
    && asdf plugin add java \
    && asdf install java openjdk-${openjdk_ver} \
    && asdf set --home java openjdk-${openjdk_ver} \
    && bash ~/.asdf/plugins/java/set-java-home.bash

# android sdk
RUN wget https://dl.google.com/android/repository/commandlinetools-linux-13114758_latest.zip \
    && unzip ./commandlinetools-linux-13114758_latest.zip \
    && mkdir -p ~/android-sdk/cmdline-tools/latest \
    && mv ./cmdline-tools/* ~/android-sdk/cmdline-tools/latest \
    && rm ./commandlinetools-linux-13114758_latest.zip
ENV PATH $PATH:$HOME/android-sdk/emulator:$HOME/android-sdk/cmdline-tools/latest/bin

RUN yes | sdkmanager --licenses >/dev/null \
    && sdkmanager --install platform-tools \
    && sdkmanager --install emulator \
    && sdkmanager --install "build-tools;33.0.0" "platforms;android-33" "system-images;android-33;default;x86_64" \
    && sdkmanager --install "ndk;26.3.11579264" \
    && echo no | avdmanager create avd -n android33 -k "system-images;android-33;default;x86_64" \
    && flutter config --android-sdk ~/android-sdk
# RUN emulator @android33 -no-metrics

# chrome
RUN wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb \
    && apt install -y --fix-missing ./google-chrome-stable_current_amd64.deb \
    && rm ./google-chrome-stable_current_amd64.deb
# RUN google-chrome --no-sandbox

# api clear
RUN apt clean \
    && rm -rf /var/lib/apt/lists/*

# add flutter user
# RUN useradd -ms /bin/bash flutter && usermod -aG sudo flutter \
#     && mkdir -p /workspace \
#     && chown -R flutter:flutter /workspace /root

# ENV HOME=/home/flutter
WORKDIR /workspace

# USER flutter

CMD ["bash"]
