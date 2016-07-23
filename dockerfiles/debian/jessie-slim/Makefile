MAKEFLAGS += --warn-undefined-variables
SHELL := bash
.SHELLFLAGS := -eu -o pipefail -c
.DEFAULT_GOAL := all
.DELETE_ON_ERROR:
.SUFFIXES:

BUILD_PREFIX ?= hacfi/
BUILD_NAME ?= debian
BUILD_VERSION ?= jessie-slim

.PHONY: all
all: run

.PHONY: run
run: check
	docker run -it --rm $(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION) bash

.PHONY: build
build:
	docker build -f "Dockerfile.1" -t $(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION)-build .

	docker create --name $(BUILD_NAME)-$(BUILD_VERSION)-build $(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION)-build
	docker export $(BUILD_NAME)-$(BUILD_VERSION)-build > build.tar

	docker rm -v $(BUILD_NAME)-$(BUILD_VERSION)-build
	docker rmi $(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION)-build

	docker build -f "Dockerfile.2" -t $(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION) .
	@rm build.tar

	@echo "\n$(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION) built successfully!"

release: check
	docker push $(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION)

remove: check
	docker rmi $(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION)

check:
	@if ! docker images $(BUILD_PREFIX)$(BUILD_NAME) | awk '{ print $$2 }' | grep -q -F $(BUILD_VERSION); then \
		echo "Image '$(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION)' is not yet built. Please run 'make build'."; false; \
	fi

test: check
	docker run --rm $(BUILD_PREFIX)$(BUILD_NAME):$(BUILD_VERSION) bash -c "apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y curl && curl https://google.com"
