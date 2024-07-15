# ROCm 6.1.3, PyTorch 2.4, Torchvision 0.19 with AMD GFX803 aka AMD Polaris aka AMD RX570/RX580/RX590

This repo provides a docker buildfile based on the original ROCm-Dockerimage to compile PyTorch and Torchvision for the [AMD RX570/RX580/RX590](https://en.wikipedia.org/wiki/Radeon_500_series) generation. PyTorch, Torchvision _and_ rocBLAS-Library are not compiled to use the GPU-Polaris generation in the original PIP repository. And of course not compiled too in the official ROCm-PyTorch Dockerfile. However, if Polaris 20/21 GPU support is to be used in ComfyUI, there is no way around newly compiled PyTorch and Torchvision whl/wheel python files. And in ROCm 6.X you have to recompile rocBLAS-Library too. That what this Docker Buildfile will do for you.

## ROCm-6.1.3 in a Dockerfile

|OS            |linux|Python|ROCm |PyTorch|Torchvision|GPU|
|--------------|-----|------|-----|-----|-----|-----|
|Ubuntu-22.04.2|6.X |3.10.10|6.1.3|2.4.0|0.19.0|RX570/580/590 aka Polaris 20/21 aka GCN 4|

* Used ROCm Docker Version: [Ubuntu 22.04+ROCm6.1.3+Python3.10](https://hub.docker.com/layers/rocm/pytorch/rocm6.1.3_ubuntu22.04_py3.10_pytorch_release-2.1.2/images/sha256-456d74f70687b6684a95c296778192a729dde2bfec51ed12f7240e5aa399b09d?context=explore)     

* PyTorch GIT: [v2.4.0](https://github.com/pytorch/pytorch)
* Torchvison GIT: [v0.19.0](https://github.com/pytorch/vision)
* rocBLAS Library: [latest](https://github.com/ROCm/rocBLAS)

- It is _not_ necessary to install the entire ROCm-Stack on the host system. _Unless_ you want to use something to optimize your GPU via rocm-smi. In my case, I need the rocm stuff to reduce the power consumption of my RX570 GPU to 145 watts with `rocm-smi --setpoweroverdrive 145 && watch -n2 rocm-smi` every time I restart the container.

1. install the docker-subsystem / docker.io on your linux system
2. download the latest file version of this github-repos
3. build your Docker image via `docker build . -t 'rocm61_pt24:latest'`
4. start the container via:
5. `docker run -it --device=/dev/kfd --device=/dev/dri --group-add=video --ipc=host --cap-add=SYS_PTRACE --security-opt seccomp=unconfined  -p 8188:8188 rocm61_pt24:latest`
7. install ComfyUI and download a Model inside the container
8. After installing ComfyUI _reinstall_ pytorch and torchvision wheels into your ComfyUI-Python-Environment. you find the Polaris compiled Python-Wheel-Files into the "/pytorch/dist" and "/torchvision/dist" Directory
