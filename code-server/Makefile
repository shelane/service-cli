-include ../tests/env_make
-include env_make

VERSION ?= 7.3
IMAGE = docksal/cli
FROM_TAG = $(VERSION)-build
TAG = $(FROM_TAG)-ide
NAME = docksal-cli-$(VERSION)-ide
CWD = $(shell pwd)

# Improve write performance for /home/docker by turning it into a volume
VOLUMES += -v /home/docker

.PHONY: build test push shell run start stop logs clean release

build:
	docker build --build-arg FROM_TAG=$(FROM_TAG) -t $(IMAGE):$(TAG) .

test:
	IMAGE=$(IMAGE):$(TAG) NAME=$(NAME) tests/test.bats

push:
	docker push $(IMAGE):$(TAG)

run: clean
	docker run --rm --name $(NAME) -it $(PORTS) $(VOLUMES) $(ENV) $(IMAGE):$(TAG)

start: clean
	docker run -d --name $(NAME) $(PORTS) $(VOLUMES) $(ENV) $(IMAGE):$(TAG)

# Non-interactive and non-tty docker exec (uses LF instead of CRLF line endings)
exec:
	@docker exec -u docker $(NAME) bash -lc "$(CMD)"

# Interactive docker exec
exec-it:
	@docker exec -u docker -it $(NAME) bash -ilc "$(CMD)"

shell:
	@docker exec -u docker -it $(NAME) bash -il

stop:
	docker stop $(NAME)

logs:
	docker logs $(NAME)

logs-follow:
	docker logs -f $(NAME)

clean:
	docker rm -vf $(NAME) >/dev/null 2>&1 || true

release: build
	make push -e TAG=$(TAG)

default: build
