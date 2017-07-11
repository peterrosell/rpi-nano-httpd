# rpi-nano-httpd

A nano sized web server packed into a Docker Nano Container. Without any payload at all.
Payload is instead volume mounted into the container.

## Step 1: Compile the assembly source code

Compiling the ASM source to a statically linked ARM binary for Raspberry Pi.

The source can be found in `src/` folder. You need a x86/amd64 Linux machine to compile the source assembly code with FASM (can be downloaded from http://arm.flatassembler.net). You only need the statically compiled `fasmarm` binary. I've done it within a Ubuntu 14.04 (64bit) machine running as a Docker Container.

Compile the thing just with `./fasmarm httpd.fasm httpd`
```
cd ./src/
docker run --rm -ti -v $(pwd):/src ubuntu:14.04 bash -c 'cd /src && ./fasmarm httpd.fasm httpd'
flat assembler for ARM  version 1.71.39  (16384 kilobytes memory)
3 passes, 4328 bytes.
```
Now we've got a super small httpd binary with 4kByte only
```
ls -al httpd
-rwxr-xr-x  1 dieter  staff  4328 Jun 28 16:27 httpd
```
And hell yeah, it's statically linked
```
file httpd
httpd: ELF 32-bit LSB executable, ARM, version 1 (SYSV), statically linked, stripped
```

Let's copying it to `./docker/` folder
```
cp httpd ../docker/
```

## Step 2: Create the Docker Nano Image
```
cd ./docker/
make
```

Now we do have a ready-to-run Docker Image with a single statically linked ARM binary for use on a Raspberry Pi.
```
docker images
REPOSITORY                  TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
peterrosell/rpi-nano-httpd  0.1.0               43ba25e1c004        2 hours ago         4.33kB
peterrosell/rpi-nano-httpd  latest              43ba25e1c004        2 hours ago         4.33kB
```
The image size is only 4kByte only! 

## Step 3: Now let's run a container on our Raspberry Pi
To start it run this command. The current directory with be the root of the web site.
```
docker run --name=webserver -v $PWD:/www -p 80:80 peterrosell/rpi-nano-httpd
```
That's it, have fun.


# Acknoledgements

## Forked
This project is forked from hypriot/rpi-nano-httpd. The change is to remove the payload from within
the container and instead volume mount it from the host.

## httpd, original source code
https://www.raspberrypi.org/forums/viewtopic.php?p=320919

I was so happy to find this small piece of source code after hours. It's a minimal web server or httpd written in assembly language to run on an ARM cpu. I did only a minor change and use `index.html` as the default resource to look for.
As the source code is written for FASM you also need to download this tool too.

## FASMARM: Freeware ARM cross assembler for FASM
http://arm.flatassembler.net

Just download the package for a Linux distro and extract the tool `fasmarm` which is a statically linked binary and doesn't need any additional dependencies installed.
