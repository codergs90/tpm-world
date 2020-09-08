Build LibTPMs Dep in a docker container
====
```
# docker build -t build-libtpms-ubuntu-20.04 -f Dockerfile.ubuntu-20.04 .
# docker run --rm build-libtpms-ubuntu-20.04 | tar --extract --verbose
# docker rmi build-libtpms-ubuntu-20.04
# find build -name '*.deb' | xargs -L1 -i% bash -c 'echo %; dpkg -c %'
```
