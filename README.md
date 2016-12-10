# Robocode-docker
Robocode in an extra-slim container!

## Run

1. To persist data accross multiple sessions, create folders where Robocode can store user configuration and robot data. A good place to pick is `$HOME/.robocode` (but you can choose somewhere else):

  ```
  $ mkdir -p ~/.robocode/robots ~/.robocode/config
  ```

2. Then, run the container passing the chosen volumes, mapped as below:

  ```
  $ docker run -it --rm \
      -u $(id -u):$(id -g) \
      -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
      -v $HOME/.robocode/robots:/robocode/robots/custom \
      -v $HOME/.robocode/config:/robocode/config \
      -e DISPLAY \
      fbcbarbosa/robocode
  ```

And you're good to go!

> **Warning**:
> If you run the container *before* creating the volumes, they will be initialized as `root:root`, and Docker won't have write access to it!

### Saving robots

Note that the robot data volume is mapped to `/robocode/robots/custom`. This means that if you want to create, save and/or package robots they must be stored within the container's `/robocode/robots/custom` folder.

Due to the way robots are loaded, if they are saved within the `/robots/custom` folder, then their package name must start with `custom`. Of course, you can name the folder differently when running the container, e.g: 

```
-v $HOME/.robocode/robots:/robocode/robots/player
```

The package name, however, will have to be updated accordingly to `player`.

If you prefer to use your own IDE to develop robots outside of the container, just make sure that the '.class' files are stored on the host's `~/.robocode/robots` folder, and their package has been set accordingly.

### Run without sharing data

Alternatively, you may choose to not share the data folders, and supress the `--rm` parameter so the container is not removed when execution stops: 

```
$ docker run -it \
    -u $(id -u):$(id -g) \
    -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
    -e DISPLAY \
    --name robocode \
    fbcbarbosa/robocode
```

This allows it to be started again at a later time:

```
$ docker start -a robocode
```

However, containers are volatile things, so it's usually better idea to persist data in your host as described above.

### Notes about GUI

Robocode has a GUI, so it is necessary to connect the container to the host's X server for display. This is why it runs as `$USER`, so it has permissions to read and write though the X11 unix socket, and why the parameters below are passed:

```
    -u $(id -u):$(id -g)
    -v /tmp/.X11-unix:/tmp/.X11-unix:rw
    -e DISPLAY
```

## Build

To build the image yourself, just run:

```
$ docker build -t fbcbarbosa/robocode .
```
