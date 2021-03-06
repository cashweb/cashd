# Running the Bitcoin ABC node software inside a Docker container

## Building your Docker image

From this directory:
1. [Download](https://www.bitcoinabc.org/releases/) the release archive for your target system.
   This example assumes that you downloaded the file `bitcoin-abc-0.22.3-x86_64-linux-gnu.tar.gz`
2. Build the image:

```shell
docker build -t bitcoin-abc:0.22.3 --build-arg RELEASE_ARCHIVE=bitcoin-abc-0.22.3-x86_64-linux-gnu.tar.gz .
```

## Running the node software

By default the container will execute `lotusd`:

```shell
docker run bitcoin-abc:0.22.3
```

To pass options to `lotusd`, pass them as arguments to the container:

```shell
docker run bitcoin-abc:0.22.3 lotusd -version
```

You can also run another tool by specifying it as an argument to the container:

```shell
docker run bitcoin-abc:0.22.3 lotus-cli -version
```

## Persistent data directory

The container uses `/data` volume as the default data directory.
To make this directory persistent across container runs, you can bind the
volume to your local filesystem:

```shell
mkdir ~/bitcoin-abc-data
docker run -v ~/bitcoin-abc-data:/data bitcoin-abc:0.22.3
```

**Note: Make sure the container has write access to you local folder.**

## Communication between lotus-cli and lotusd

In order to make `lotus-cli` and `lotusd` communicate together, they need to
use the same network. By using the same data directory, they also share the
authentication cookie:

```shell
# Run the lotusd container in the background
docker run --name lotusd -v ~/bitcoin-abc-data:/data --rm -d bitcoin-abc:0.22.3
docker run --network container:lotusd -v ~/bitcoin-abc-data:/data --rm bitcoin-abc:0.22.3 lotus-cli getnetworkinfo
docker stop lotusd
```
