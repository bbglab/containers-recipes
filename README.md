# containers

> [!WARNING]
> This repository is archived. Please refer to https://github.com/bbglab/containers-recipes for updated version of bbglab containers.

To build a container:
```
docker build -t <container_tag> <what to build>
docker build -t bbglab/bgreference .
```

To test that it works:
```
docker run <container_tag> <command>
docker run bbglab/bgreference ls /bgdatacache/datasets/genomereference
```

To push it to Docker Hub:
```
docker push <container_tag>
docker push bbglab/bgreference
```


To pull it in the form of singularity image:
```
singularity pull --name <container_image_name> <link_to_container>
singularity pull --name docker.io-bbglab-bgreference.img docker://docker.io/bbglab/bgreference
```


