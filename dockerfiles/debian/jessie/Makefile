MAKEFLAGS += --warn-undefined-variables
SHELL := bash
.SHELLFLAGS := -eu -o pipefail -c
.DEFAULT_GOAL := all
.DELETE_ON_ERROR:
.SUFFIXES:

BUILD_PREFIX ?= hacfi/
BUILD_NAME ?= debian
BUILD_VERSION ?= jessie

.PHONY: all
all: run

.PHONY: run
run: check
	docker run -it --rm $(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION) bash

build:
	docker pull debian:jessie
	docker tag debian:jessie $(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION)

release: check
	docker push $(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION)

remove: check
	docker rmi $(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION)

.PHONY: check
check:
	@if ! docker images $(BUILD_PREFIX)$(BUILD_NAME) | awk '{ print $$2 }' | grep -q -F $(BUILD_VERSION); then \
		echo "Image '$(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION)' is not yet built. Please run 'make build'."; false; \
	fi

test: check
	docker run --rm $(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION) bash -c "apt-get update -qq && DEBIAN_FRONTEND=noninteractive apt-get install -y curl && curl https://google.com"
