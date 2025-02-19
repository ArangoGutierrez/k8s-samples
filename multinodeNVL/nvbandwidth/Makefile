# Copyright (c) 2025, NVIDIA CORPORATION.  All rights reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# You may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

BUILD_MULTI_ARCH_IMAGES ?= no
DOCKER ?= docker
BUILDX  =
ifeq ($(BUILD_MULTI_ARCH_IMAGES),true)
BUILDX = buildx
endif
MKDIR    ?= mkdir

##### Global variables #####
include $(CURDIR)/versions.mk

CUDA_VERSION ?= 12.5.0
NVBANDWIDTH_VERSION ?= v0.7
OPENMPI_VERSION ?= v0.6.0

ifeq ($(IMAGE_NAME),)
IMAGE_NAME := $(REGISTRY)/nvbandwidth
endif

GIT_COMMIT := $(shell git describe --match="" --dirty --long --always --abbrev=40 2> /dev/null || echo "")
IMAGE_VERSION := $(shell echo $(GIT_COMMIT) | cut -c1-8)
IMAGE_TAG ?= $(NVBANDWIDTH_VERSION)-$(IMAGE_VERSION)
IMAGE = $(IMAGE_NAME):$(IMAGE_TAG)

OUT_IMAGE_NAME ?= $(IMAGE_NAME)
OUT_IMAGE_VERSION ?= $(IMAGE_VERSION)
OUT_IMAGE_TAG = $(OUT_IMAGE_VERSION)
OUT_IMAGE = $(OUT_IMAGE_NAME):$(OUT_IMAGE_TAG)

##### Public rules #####

.PHONY: build push clean

build:
	@echo "Building Docker image: $(IMAGE)"
	DOCKER_BUILDKIT=1 \
		$(DOCKER) $(BUILDX) build --pull \
		--provenance=false --sbom=false \
		$(DOCKER_BUILD_OPTIONS) \
		--tag $(IMAGE) \
		--build-arg CUDA_VERSION="$(CUDA_VERSION)" \
		--build-arg NVBANDWIDTH_VERSION="$(NVBANDWIDTH_VERSION)" \
		--build-arg OPENMPI_VERSION="$(OPENMPI_VERSION)" \
		-f $(CURDIR)/multinodeNVL/nvbandwidth/Dockerfile \
		$(CURDIR)

push:
	@echo "Pushing Docker image: $(IMAGE)"
	$(DOCKER) push $(IMAGE)

clean:
	@echo "Removing Docker image: $(IMAGE)"
	$(DOCKER) rmi $(IMAGE)
