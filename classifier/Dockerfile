FROM  bh.cr/al_s_apps/librosa-aarch64

# Ensure package install won't block for user input during build
ENV DEBIAN_FRONTEND=noninteractive

# Add google repositories (coral-edgetpu-stable for Edge TPU support on Raspberry Pi)
RUN \
    echo "deb https://packages.cloud.google.com/apt coral-edgetpu-stable main" | sudo tee /etc/apt/sources.list.d/coral-edgetpu.list && \
    curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    # Removed Mendel-specific repositories (mendel-bsp-enterprise-day and mendel-day) 
    # as they are not compatible with Raspberry Pi OS and cause APT errors

# Install Coral Edge TPU and audio dependencies (Raspberry Pi OS compatible)
# Removed i.MX specific packages (imx-gpu-viv, weston-imx, imx-board-audio) that are Mendel-only
RUN \
    apt-get update && apt-get install -y wget \
    python3-edgetpu \
    libpulse0 libpulsedsp \
    pulseaudio-utils \
    pulseaudio \
    alsa-utils \
    sqlite3

# python3-edgetpu above installs older version of llvmlite, 
# so uninstall it to revert to version 0.31.0 from the base image
RUN \
    pip uninstall -y llvmlite

WORKDIR /usr/src/app

# Copy galcore.ko driver (for i.MX hardware - not needed on Raspberry Pi)
# RUN \
#     find /lib/modules -type f -name 'galcore.ko' -print0 | xargs -0 -I{} cp {} /usr/src/app/galcore.ko

ENV UDEV=1

COPY labels.txt labels.txt
COPY *.tflite ./
COPY *.py ./
COPY start.sh start.sh

CMD ["bash","start.sh"]
