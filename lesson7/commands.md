docker create -v /config --name dataContainer busybox
docker cp config.conf dataContainer:/config/