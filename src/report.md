🔹 1. Установка GNS3 Server (на Linux Server)

1.1. Установка зависимостей
    Порядок выполнения задания 
    sudo apt update
    sudo apt install -y python3-pip python3-venv docker.io qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
    sudo usermod -aG libvirt,docker $USER
    newgrp libvirt  # (или перезайдите в систему)


1.2. Установка GNS3 Server
    ython3 -m venv ~/gns3-server
    source ~/gns3-server/bin/activate
    pip install gns3-server