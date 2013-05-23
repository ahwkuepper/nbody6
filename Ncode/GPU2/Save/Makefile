FC = gfortran
FFLAGS = -O3 -Wall -march=core2 -mavx
FFLAGS += -fopenmp
CXX = g++ -O3 -Wall -march=core2 -mavx
CUDA_PATH = /usr/local/cuda
SDK_PATH = /usr/local/cuda/samples
NVCC = $(CUDA_PATH)/bin/nvcc -O3 -Xcompiler "-Wall -O3 -fopenmp" -I $(SDK_PATH)/common/inc/
NVCC += -m64 -arch sm_30 -Xptxas -dlcm=cg #-ccbin /opt/local/bin
RUNDIR = ./run

OBJECTS = $(SOURCE:.f=.o)

all: gpu

gpu: gpunb.gpu.o gpupot.gpu.o intgrt.o gpucor.o nbintp.o cnbint.o nbint.o start.o fpoly2a.o adjust.o energy2.o phicor.o cmfirr.o cmfirr2.o checkl2.o kspert.o swap.o scale.o wtime.o cxvpred.o gpuirr.o jpred.o jpred2.o fpcorr2.o repair.o sweep2.o ksres3.o
	cp -f gpunb.gpu.o gpunb.o
	cp -f gpupot.gpu.o gpupot.o
	cp -f *.o ../Ncode
	make gpu -C ../Ncode -f ../GPU2/Makefile_gpu -j4
	mv -f ../Ncode/nbody6 $(RUNDIR)/nbody6.gpu

sse: gpunb.sse.o gpupot.sse.o intgrt.o gpucor.o nbintp.o cnbint.o nbint.o start.o fpoly2a.o adjust.o energy2.o phicor.o cmfirr.o cmfirr2.o checkl2.o kspert.o swap.o scale.o wtime.o cxvpred.o gpuirr.o jpred.o jpred2.o fpcorr2.o repair.o sweep2.o ksres3.o
	cp -f gpunb.sse.o gpunb.o
	cp -f gpupot.sse.o gpupot.o
	cp -f *.o ../Ncode
	make sse -C ../Ncode -f ../GPU2/Makefile_gpu -j8
	mv -f ../Ncode/nbody6 $(RUNDIR)/nbody6.sse

avx: gpunb.avx.o gpupot.avx.o intgrt.o gpucor.o nbintp.o cnbint.o nbint.o start.o fpoly2a.o adjust.o energy2.o phicor.o cmfirr.o cmfirr2.o checkl2.o kspert.o swap.o scale.o wtime.o cxvpred.o gpuirr.o jpred.o fpcorr2.o repair.o
	cp -f gpunb.avx.o gpunb.o
	cp -f gpupot.avx.o gpupot.o
	cp -f *.o ../Ncode
	make sse -C ../Ncode -f ../GPU2/Makefile_gpu -j8
	mv -f ../Ncode/nbody6 $(RUNDIR)/nbody6.avx

cnbint.o: ./lib/cnbint.cpp
	$(CXX) -c $^

wtime.o: ./lib/wtime.cpp
	$(CXX) -c $^

cxvpred.o: ./lib/cxvpred.cpp
	$(CXX) -fopenmp -c $^

GPUIR_CPP=./irrlib/gpuirr.avx.cpp

gpuirr.o: $(GPUIR_CPP)
	$(CXX) -fopenmp -c $^ -o $@
#gpuirr.o: gpuirr.s
#	clang -c $^ -o $@

gpuirr.s: $(GPUIR_CPP)
	$(CXX) -mavx -fopenmp -S $^ -o $@

intgrt.o: intgrt.omp.f
#	$(FC) $(FFLAGS) -fopenmp $^ -c -o $@ 
	$(FC) $(FFLAGS) $^ -c -o $@ 

intgrt.s: intgrt.omp.f
	$(FC) $(FFLAGS) -fopenmp $^ -S -o $@

gpucor.s: gpucor.f
	$(FC) $(FFLAGS) -fopenmp $^ -S -o $@

start.o: start.omp.F
	$(FC) $(FFLAGS) -fopenmp $^ -c -o $@
#	$(FC) $(FFLAGS) $^ -c -o $@

#gpunb.gpu.o: lib/gpunb.reduce.cu
gpunb.gpu.o: lib/gpunb.velocity.cu
	$(NVCC) $^ -c -o $@

gpupot.gpu.o: lib/gpupot.gpu.cu
	$(NVCC) $^ -c -o $@

gpunb.sse.o: lib/gpunb.sse.cpp
	$(CXX) -fopenmp $^ -c -o $@

gpupot.sse.o: lib/gpupot.sse.cpp
	$(CXX) -fopenmp $^ -c -o $@

gpunb.avx.o: lib/gpunb.avx.cpp
	$(CXX) -fopenmp $^ -c -o $@

gpupot.avx.o: lib/gpupot.avx.cpp
	$(CXX) -fopenmp $^ -c -o $@

clean:
	rm -f *.o

clean-all: clean
	make -C ../Ncode clean

archive:
	tar -czvf ../gpu2.tar.gz ../GPU2	
