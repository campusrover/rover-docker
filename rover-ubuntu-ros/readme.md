- latest ros noetic core is indeed *** 1.5.0-1focal.20220520.065017
- docker build -t cosi119/rover-ubuntu-ros:devel-001 .
- why is chrome needed?

$ cd ubuntu-desktop-lxde-vnc-ros
$ docker build --platform linux/arm64,linux/amd64 -t cosi119/rover-ubuntu-ros:devel-001 .
$ docker push cosi119/rover-ubuntu-ros:devel-001 

