# ubuntu_docker
This ubuntu_docker package include different versions of ubnuntu which can run with docker.

## Acknowledgment
This charuco marker detection is forked from the [charuco_detector](https://github.com/carlosmccosta/charuco_detector).

## Requirements 
This package requires operating system ubuntu and setup with docker and nvidia-docker2.\
[docker install method](https://docs.docker.com/engine/install/ubuntu/)\
running docker without sudo\
[**run docker without sudo**](https://docs.docker.com/engine/install/linux-postinstall/)\
```bash
sudo chmod 666 /var/run/docker.sock
```

## Building
Note: Choose a environment you want to use, it's not needed to build every environments.\
The **_ws** of workspace folder is needed.

```bash
# build worksapce folder
mkdir <your main workspace>_ws
cd ~<your main workspace>_ws

# clone ubuntu docker repository to your workspace
git clone https://github.com/errrr0501/ubuntu20.04_docker.git

# go to the environment you want
cd ubuntu20.04_docker/<environment you want>/.../docker

# build docker image
./build.sh
```

#### Quick start

```bash
# run docker
./run.sh