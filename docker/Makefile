DOCKER_IMAGE_VERSION=0.1.0
DOCKER_IMAGE_NAME=peterrosell/rpi-nano-httpd
DOCKER_IMAGE_TAGNAME=$(DOCKER_IMAGE_NAME):$(DOCKER_IMAGE_VERSION)

default: build

build:
	docker build -t $(DOCKER_IMAGE_TAGNAME) .
	docker tag $(DOCKER_IMAGE_TAGNAME) $(DOCKER_IMAGE_NAME):latest

minimal:
	docker build -t $(DOCKER_IMAGE_TAGNAME) -f Dockerfile.minimal .
	docker tag $(DOCKER_IMAGE_TAGNAME) $(DOCKER_IMAGE_NAME):minimal

push:
	docker push $(DOCKER_IMAGE_NAME):latest
	docker push $(DOCKER_IMAGE_TAGNAME)

run:
	docker run -d -p 80:80 -d $DOCKER_IMAGE_NAME
