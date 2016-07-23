MAKEFLAGS += --warn-undefined-variables
SHELL := bash
.SHELLFLAGS := -eu -o pipefail -c
.DEFAULT_GOAL := all
.DELETE_ON_ERROR:
.SUFFIXES:

DOCKER_VERSION         = $(shell docker -v | egrep -o '\d+\.\d+\.\d+' || false)
DOCKER_COMPOSE_VERSION = $(shell docker-compose -v | egrep -o '\d+\.\d+\.\d+' || false)

ifeq ($(DOCKER_VERSION),)
$(error Please install Docker - you can get it at https://www.docker.com/products/overview )
endif

ifeq ($(DOCKER_COMPOSE_VERSION),)
$(error Please install Docker Compose - you can get it at https://docs.docker.com/compose/install/ )
endif


.PHONY: all
all: run

.PHONY: run
run: version setup
	docker-compose up

.PHONY: setup
setup:
	@([ -f config/nginx/kibana/htpasswd ] || (echo "Please create a user to access Kibana." && \
		read -p "Enter username: " kibana_username && \
		read -s -p "Enter password (not shown): " kibana_password && \
		printf "%s:%s\n" $$kibana_username $$(openssl passwd -apr1 $$kibana_password) >> config/nginx/kibana/htpasswd))
	@([ -f config/nginx/kibana/kibana.crt ] || (echo "Please create a certificate for Kibana." && \
		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ./config/nginx/kibana/kibana.key -out ./config/nginx/kibana/kibana.crt))

.PHONY: test
test:
	echo "Hello ELK!" | nc -w 1 -u $$(docker-machine ip default) 5000

.PHONY: version
version:
	@printf "Environment: Docker %s - Docker Compose %s\n" $(DOCKER_VERSION) $(DOCKER_COMPOSE_VERSION)
