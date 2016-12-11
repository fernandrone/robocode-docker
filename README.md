# Robocode-docker
Robocode in an extra-slim container!

## Runnning

1. To persist data accross multiple sessions, create folders where Robocode can store robot data and configuration files. A good place to pick is `$HOME/.robocode` (but you can choose somewhere else). First, set the variables below:

  ```
  $ export $ROBO_BOTS=~/.robocode/robots 
  $ export $ROBO_CONF=~/.robocode/config
  ```
  
  Then, create the folders:
  
  ```
  $ mkdir -p $ROBO_BOTS $ROBO_CONF
  ```

2. Next, run the container passing the chosen volumes:

  ```
  $ docker run -it --rm \
      -u $(id -u):$(id -g) \
      -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
      -v $ROBO_BOTS:/robocode/robots/robots \
      -v $ROBO_CONF:/robocode/config \
      -e DISPLAY \
      fbcbarbosa/robocode
  ```

And you're good to go!

> **Warning**:
> if you run the container *before* creating the volumes, they will be initialized as `root:root`, and Docker won't have write access to it!

### Sharing data

Note that the robot data volume is mapped in the example above as `$ROBO_BOTS:/robocode/robots/robots`. This means that if you want to create, save and/or package robots within the container and make them available to the host, they must be stored within the container's `/robocode/robots/robots` folder. 

Likewise, if you want to import robots from the host to the container, make sure to place their .class files under the host's `$ROBO_BOTS` folder. 

### Packages

Due to the way robots are loaded, if they are saved within the container's `/robocode/robots/robots` folder, then their package name must be `robots`. Of course, you can name the folder differently when running the container, e.g: 

```
-v $ROBO_BOTS:/robocode/robots/human
```

Or, to use a subpackage:

```
-v $ROBO_BOTS:/robocode/robots/robots/human
```

The package name would have to be updated to `human` and `robots.human`, respectively.

## Developing Robots

If you want to use your own IDE to develop robots outside of the container, just make sure that the '.class' files are stored on the host's `$ROBO_BOTS` folder, and that their package has been set accordingly. See [Robocode-maven](https://github.com/fbcbarbosa/robocode-maven) for an example of this implementation.

Alternatively, it's also possible to use Robocode's internal editor, after launching the container, to develop new Robots.

## Building

To build the image yourself, just run:

```
$ docker build -t fbcbarbosa/robocode .
```
