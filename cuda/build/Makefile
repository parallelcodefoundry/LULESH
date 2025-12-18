#MPI_FLAGS = -DUSE_MPI -I$(MPICH_DIR)/include
MPI_FLAGS =

NVCC		= nvcc
SM_VERSION ?= 90
FLAGS		= -arch=sm_$(SM_VERSION)
DFLAGS	= $(MPI_FLAGS) -lineinfo
RFLAGS 	= $(MPI_FLAGS) -O3 -DNDEBUG -lineinfo

#SILO_INCLUDES := /usr/local/silo-4.8/include
#SILO_LIBS := /usr/local/silo-4.8/lib

LINKFLAGS = $(FLAGS)
#LINKFLAGS += -L$(MPICH_DIR)/lib
#ifneq (,$(findstring mpich, $(MPICH_DIR)))
#   LINKFLAGS += -lmpich
#else ifneq (,$(findstring openmpi, $(MPICH_DIR)))
#	LINKFLAGS += -lmpi
#else
#	$(error Unsupported MPI library: $(MPICH_DIR))
#endif
#LINKFLAGS += -L$(SILO_LIBS) -lsilo

#INC_SILO:= -I$(SILO_INCLUDES)

all: release

release:  LINKFLAGS += $(RFLAGS)
debug:    LINKFLAGS += $(DFLAGS)

release: 	FLAGS += $(RFLAGS)
debug: 		FLAGS += $(DFLAGS)

release: lulesh
debug: lulesh

lulesh: allocator.o lulesh.o lulesh-comms.o lulesh-comms-gpu.o
	$(NVCC) $(LINKFLAGS) allocator.o lulesh.o lulesh-comms.o lulesh-comms-gpu.o -o lulesh

allocator.o: allocator.cu vector.h
	$(NVCC) $(FLAGS) allocator.cu -I ./ -c -o allocator.o

lulesh.o: lulesh.cu util.h vector.h allocator.h
	$(NVCC) $(FLAGS) lulesh.cu -I ./  $(INC_SILO) -c -o lulesh.o

lulesh-comms.o: lulesh-comms.cu
	$(NVCC) $(FLAGS) lulesh-comms.cu -I ./ -c -o lulesh-comms.o

lulesh-comms-gpu.o: lulesh-comms-gpu.cu
	$(NVCC) $(FLAGS) lulesh-comms-gpu.cu -I ./ -c -o lulesh-comms-gpu.o

clean:
	rm -rf allocator.o lulesh-comms.o lulesh-comms-gpu.o lulesh.o lulesh xyz.asc regNumList.txt
