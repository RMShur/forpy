# Copyright (C) 2017-2018  Elias Rabel
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU Lesser General Public License as published by 
# the Free Software Foundation, either version 3 of the License, or 
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of 
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU Lesser General Public License for more details.
#
# You should have received a copy of the GNU Lesser General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.

FC=gfortran
FFLAGS=-Wall -g
PY_VERSION=3

ifeq ($(FC),ifort)
  FFLAGS=-warn all -g
endif

PYTHON=python3

ifeq ($(PY_VERSION),2)
  PYTHON=python2
  FFLAGS+=-DPYTHON2
endif

PYTHON_CONFIG=$(PYTHON)-config

ifeq ($(PY_DEBUG), 1)
  PYTHON_CONFIG=$(PYTHON)-dbg-config
  FFLAGS+= -DPy_DEBUG
endif

LDFLAGS=`$(PYTHON_CONFIG) --ldflags`

.PHONY: tests
tests: test_basics test_cast test_datastructures test_ndarray

.PHONY: runtests
runtests: tests
	./test_basics
	./test_cast
	./test_datastructures
	./test_ndarray

test_basics: test_basics.o test_basics_mod.o forpy_mod.o unittest_mod.o forpy_tests_common_mod.o
	$(FC) $(FFLAGS) -o $@ $^ $(LDFLAGS)
	
test_cast: test_cast.o test_cast_mod.o forpy_mod.o unittest_mod.o forpy_tests_common_mod.o
	$(FC) $(FFLAGS) -o $@ $^ $(LDFLAGS)

test_datastructures: test_datastructures.o test_datastructures_mod.o forpy_mod.o unittest_mod.o forpy_tests_common_mod.o
	$(FC) $(FFLAGS) -o $@ $^ $(LDFLAGS)

test_ndarray: test_ndarray.o test_ndarray_mod.o forpy_mod.o unittest_mod.o forpy_tests_common_mod.o
	$(FC) $(FFLAGS) -o $@ $^ $(LDFLAGS)

unittest_mod.o: unittest_mod.F90 unittest_mod.inc
	$(FC) $(FFLAGS) -c -o $@ $<

%.o: %.F90
	$(FC) $(FFLAGS) -c -o $@ $<

forpy_mod.F90: ../forpy_mod.fypp
	python2 ../fypp.py ../forpy_mod.fypp forpy_mod.F90

.PHONY: clean
clean:
	rm -f *.o
	rm -f *.mod
	rm -f forpy_mod.F90
	rm -f test_basics
	rm -f test_cast
	rm -f test_datastructures
	rm -f test_ndarray
	rm -f *.pyc

# Fortran module dependencies
forpy_tests_common_mod.o: forpy_mod.o unittest_mod.o
test_basics.o: test_basics_mod.o
test_basics_mod.o: unittest_mod.o forpy_mod.o forpy_tests_common_mod.o
test_cast.o: test_cast_mod.o
test_cast_mod.o: unittest_mod.o forpy_mod.o forpy_tests_common_mod.o
test_datastructures.o: test_datastructures_mod.o
test_datastructures_mod.o: unittest_mod.o forpy_mod.o forpy_tests_common_mod.o
test_ndarray.o: test_ndarray_mod.o
test_ndarray_mod.o: unittest_mod.o forpy_mod.o forpy_tests_common_mod.o

