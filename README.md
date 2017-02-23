# vivado-docker

Vivado System Edition installed into a docker image for CI purposes.

## Image Build Instructions

### Prerequisite
This docker file assumes the Vivado download is available on a webserver somewhere. This can for example also be the build machine using the webserver in Python.

```shell
cd /path/to/Vivado/download
python3 -m http.server
```

### Building

1. Potentially modify the `install_config.txt` to change the install options.
2. Build the image by specifying the host for downloading, the filename, and the license server

```shell
docker build --build-arg VIVADO_TAR_HOST=<hostip>:<hostport> --build-arg VIVADO_TAR_FILE=<filename> --build-arg LICENSE_SERVER=<license_server> -t <imagename> <builddirectory> 
```

## Running the Image and using Vivado

Running the image works in the standard way (e.g., `docker run --rm -ti <imagename>`). If also the graphical user interfaces should be used, additional volumes and variables have to be provided to make the X server usable for the container. In this case, running the container could for example look as the following: (tested on Ubuntu 14.4)

```shell
docker run --rm -ti -h $(hostname) -e DISPLAY -e XAUTHORITY -v /tmp/.X11-unix:/tmp/.X11-unix -v $XAUTHORITY:$XAUTHORITY <imagename>

```

Vivado requires a custom environment which is acquired by sourcing the settings files which are part of the installation (e.g., `/opt/Xilinx/*/*/settings64.sh`). However, since this environment occasionally breaks system tools, it is not sourced by default in this image. A wrapper script called `run_in_xilinx_env` is provided instead which can be used to execute arbitrary commands within the Vivado environment. Additionally, symlinks for the most important commands (vivado, xdsk, ...) have been set up.

## Upgrading the dockerfile to a new vivado version

If the Vivado version is upgraded also the `install_config.txt` should be regenerated. This can be using the installer of the new version and the following command:

```shell
xsetup -b ConfigGen
```
