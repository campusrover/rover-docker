
$ cd rover-ros-environment
$ docker build --platform linux/arm64,linux/amd64 -t cosi119/rover-ros-environment:devel-001 .
$ docker push cosi119/rover-ros-environment:devel-001


docker build --platform linux/arm64,linux/amd64 -t cosi119/rover-ros-environment:devel-006 . &&
docker push cosi119/rover-ros-environment:devel-006