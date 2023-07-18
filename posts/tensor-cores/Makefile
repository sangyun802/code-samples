################################################################################
#
# Copyright 1993-2015 NVIDIA Corporation.  All rights reserved.
#
# NOTICE TO USER:
#
# This source code is subject to NVIDIA ownership rights under U.S. and
# international Copyright laws.
#
# NVIDIA MAKES NO REPRESENTATION ABOUT THE SUITABILITY OF THIS SOURCE
# CODE FOR ANY PURPOSE.  IT IS PROVIDED "AS IS" WITHOUT EXPRESS OR
# IMPLIED WARRANTY OF ANY KIND.  NVIDIA DISCLAIMS ALL WARRANTIES WITH
# REGARD TO THIS SOURCE CODE, INCLUDING ALL IMPLIED WARRANTIES OF
# MERCHANTABILITY, NONINFRINGEMENT, AND FITNESS FOR A PARTICULAR PURPOSE.
# IN NO EVENT SHALL NVIDIA BE LIABLE FOR ANY SPECIAL, INDIRECT, INCIDENTAL,
# OR CONSEQUENTIAL DAMAGES, OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS
# OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE
# OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE
# OR PERFORMANCE OF THIS SOURCE CODE.
#
# U.S. Government End Users.  This source code is a "commercial item" as
# that term is defined at 48 C.F.R. 2.101 (OCT 1995), consisting  of
# "commercial computer software" and "commercial computer software
# documentation" as such terms are used in 48 C.F.R. 12.212 (SEPT 1995)
# and is provided to the U.S. Government only as a commercial end item.
# Consistent with 48 C.F.R.12.212 and 48 C.F.R. 227.7202-1 through
# 227.7202-4 (JUNE 1995), all U.S. Government End Users acquire the
# source code with only those rights set forth herein.
#
################################################################################
#
# Makefile project only supported on Mac OS X and Linux Platforms)
#
################################################################################

# Location of the CUDA Toolkit
CUDA_INSTALL_PATH=/usr/local/cuda
CUDA_PATH ?= ${CUDA_INSTALL_PATH}
ifeq (${CUDA_PATH},)
	$(error CUDA_INSTALL_PATH is not set)
endif
CUTLASS_PATH := /home/ipa/sangyun/instant-ngp/dependencies/tiny-cuda-nn/dependencies/cutlass

NVCC := nvcc
NVCC_FLAGS := -O3 -std=c++17 -U__CUDA_NO_HALF_OPERATORS__ -U__CUDA_NO_HALF_CONVERSIONS__ -U__CUDA_NO_HALF2_OPERATORS__
CUDA_INCLUDE := ${CUDA_PATH}/include
CUDA_LIB := ${CUDA_PATH}/lib64
CUTLASS_INCLUDE := ${CUTLASS_PATH}/include
CUTLASS_UTIL_INCLUDE := ${CUTLASS_PATH}/tools/util/include

# Gencode arguments
SMS=80

ifeq ($(GENCODE_FLAGS),)
# Generate SASS code for each SM architecture listed in $(SMS)
$(foreach sm,$(SMS),$(eval GENCODE_FLAGS += -gencode arch=compute_$(sm),code=sm_$(sm)))

ifeq ($(SMS),)
# Generate PTX code from SM 35
GENCODE_FLAGS += -gencode arch=compute_35,code=compute_35
endif

endif

ALL_CCFLAGS += -m64 --threads 0 --std=c++17 -O3

LIBRARIES += -lcublas -lcublasLt -lculibos -lcudart -lcudart_static -lrt -lpthread -ldl -lcurand

CXX := g++

################################################################################

# Target rules
all: build

build: simpleTensorCoreGEMM

simpleTensorCoreGEMM.o: simpleTensorCoreGEMM.cu
	$(NVCC) $(NVCC_FLAGS) -I$(CUDA_INCLUDE) -I$(CUTLASS_INCLUDE) -I$(CUTLASS_UTIL_INCLUDE) $(GENCODE_FLAGS) -o $@ -c $< -L$(CUDA_LIB) $(LIBRARIES)

simpleTensorCoreGEMM: simpleTensorCoreGEMM.o 
	$(NVCC) -ccbin $(CXX) $(GENCODE_FLAGS) -o $@ $+ $(LIBRARIES)

clean:
	rm -f *.o simpleTensorCoreGEMM tags

clobber: clean
