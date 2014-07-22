### pickett: make for the docker world 

### How to get it

Assuming you have a modern version of go already installed:

```
mkdir -p /tmp/foo/src
export GOPATH=/tmp/foo
export PATH=$PATH:/tmp/foo/bin
go get github.com/tools/godep
godep get github.com/igneous-systems/pickett/pickett
go install github.com/igneous-systems/pickett/pickett
```
The next to last one may take a while to pull down all the dependencies.

You should end up with the executable `pickett` in `/tmp/foo/bin`.

### How to get a sample project

Assuming you did the above:

```
cd /tmp/foo
git clone git@github.com:igneous-systems/pickett-samples
cd pickett-samples/sample1
```

The file to examine is the `Pickett.json`.

### How to build some stuff

Assuming you 

* have a modern version of docker (at least 1.1.1) already installed 
* have set your DOCKER_HOST if you not on the machine that runs docker or are using boot2docker
* Are willing to wait, this takes several minutes the first time
```
cd /tmp/foo/pickett-samples/sample1/
pickett --debug candidate
```
That will take a while (minutes) the first time because it creates some docker images that require downloading a lot of software (`apt-get install blah blah`).  Once in steady state, pickett does incremental builds.  

You may want to look at the files `/tmp/foo/pickett-samples/sample1/container/runner/Dockerfile` and `/tmp/foo/pickett-samples/sample1/container/builder/Dockerfile` to see the commands that take so long. 

The --debug flag is just to give you some stuff to read while you are waiting. Normally that is only interesting to pickett developers.

The "candidate" in the command above refers to a tag on a docker container called `candidate`.  After this completes, you may want to try some docker commands like this:

```
docker images
docker run -it -v candidate /bin/bash
```

In the former of those commands you'll see "sample1/runner", "sample1/builder", and "sample1/external" which are the containers for running (virgin state), building (has go tools), and the external libraries for the sample system.  In the latter, if you look in `/` you'll see `/wuclient` and `/wuserver`.  You should note that the system installed in `candidate` has no build tools (`build-essential` in ubuntu parlance) nor go installed, _just_ the two executables that are the system to be executed.

For giggles, you may want to to try:
```
time pickett candidate
```

That shows you the elapsed time to *just* do the dependency checking (the pickett overhead).


