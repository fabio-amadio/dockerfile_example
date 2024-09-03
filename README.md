# Getting started with Docker and ROS 2

To build the example Dockerfile, from the *dockerfile_example* folder run:

```bash
docker image build -t my_image .
```

This will build a new image called __my_image__ (verify it by running `docker images`).

To run the image in a new container (called __my_container__) with an interactive terminal, run:

```bash
docker run -it --name my_container my_image
```

To also bind mount a volume (*dummy_code/src* in this case), run (from the *dockerfile_example* folder):

```bash
docker run -it --name my_container -v ./dummy_code/src:/src my_image
```

Finally, to open a new terminal inside the same container, run:

```bash
docker exec -it my_container /bin/bash
```

Run `docker ps` to list all the running containers.

Common arguments for running with different users:

- `--user <uname>`
- `--user <uname>:<gname>`
- `--user <uid>`
- `--user <uid>:<gid>`

Common arguments for setting network:

- `--network=host` to share the networking  with the host
- `--ipc=host` to share network the shared memory (IPC: Inter-Process Communication)

So, for example:

```bash
docker run -it --name my_container --user ros --network=host --ipc=host -v ./dummy_code/src:/src my_image
```

The defined `entrypoint.sh` allows to pass a command to the `docker run` instruction (default is `bash`). For example: by running:

```bash
docker run -it --name my_container --user ros --network=host --ipc=host -v ./dummy_code/src:/src my_image ros2 topic list
```

## Run with graphics enabled

Make sure to run the container with a user with access to X.

- Run `xhost +` to give permission to all users.
- Run `xhost +local:` to give permission to all local users.
- Run `xhost +local:<uname>` to give permission to a specific user.

(use `-` to revoke the permission)

We need to expose the X domain socket in the `docker run` command by bind mounting the following volume: `-v /tmp/.X11-unix:/tmp/.X11-unix:rw`.

Finally, we need to give an X display by setting as display environment variable the same one that the host is using: `--env=DISPLAY`.

So, an example command would be:

```bash
docker run -it --user ros --network=host --ipc=host -v /tmp/.X11-unix:/tmp/.X11-unix:rw --env=DISPLAY my_image
```

### Addittional arguments to work with NVIDIA GPUs

```bash
docker run -it --gpus all --runtime=nvidia my_image
```

Other useful arguments when working with GPUs:

- `--gpus all`
- `--runtime=nvidia`
- `--env="QT_X11_NO_MITSHM=1"`
- `--env="NVIDIA_DRIVER_CAPABILITIES=all"`
- `--env="NVIDIA_VISIBLE_DEVICES=all"`
- `--device=/dev/dri:/dev/dri`
