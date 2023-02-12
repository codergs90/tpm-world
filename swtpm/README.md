Build SWTPM Dep in a docker container
====
```
# symlinks won't work with docker, so let's just copy the deps from relative folder
# make sure to build things before hand
$ cp -r ../libtpms/build/libtpms .


$ docker build -t build-swtpm-ubuntu-20.04 -f Dockerfile.ubuntu-20.04 .
$ docker run --rm build-swtpm-ubuntu-20.04 | tar --extract --verbose
$ docker rmi build-swtpm-ubuntu-20.04
$ find build -name '*.deb' | xargs -L1 -i% bash -c 'echo %; dpkg -c %'
```

Run the container
====
```
$ docker build -t swtpm-on-20.04 -f Install-Dockerfile.ubuntu-20.04 .
$ docker run -d swtpm-on-20.04:latest
# attach to running container from another terminal
$ docker exec -it <container-id> /bin/bash

# check if swtpm is up or not
$ /opt# netstat -tlnap
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 127.0.0.1:9090          0.0.0.0:*               LISTEN      6/swtpm
tcp        0      0 127.0.0.1:9091          0.0.0.0:*               LISTEN      6/swtpm
```


```
# run simulator (already started in container)
$ /usr/bin/swtpm socket --server type=tcp,port=9090 --tpm2 --tpmstate dir=/opt --ctrl type=tcp,port=9091 -d

# send control commands
$ swtpm_ioctl --tcp localhost:9091 --info 2 | jq .
{
  "TPMAttributes": {
    "manufacturer": "id:00001014",
    "version": "id:20191023",
    "model": "swtpm"
  }
}

$ swtpm_ioctl --tcp localhost:9091 -c
ptm capability is 0xffff
```