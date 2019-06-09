# CIF parsing benchmark

## Goal
Evaluate performance of python libraries for parsing CIF files containing several hundreds of atoms.

Candidates considered:

 * [ASE](https://pypi.org/project/ase/3.17.0) 3.17.0
 * [pymatgen](https://pypi.org/project/pymatgen/2018.12.12/) 2018.12.12
 * [pycifrw](https://pypi.org/project/PyCifRW/4.4.1/) 4.4.1
 * [pycodcif](https://pypi.org/project/pycodcif/2.4/) 2.4

Note: For backwards compatibility, candidates should be python2 compatible.

Note: pymatgen was using pycifrw at some point, but dropped its support in
pymatgen v3.0 (due to "issues with installation").

## Installation

```
conda env create -f environment.yml python=2.7
conda activate cif-benchmark
tar xf structures_0100.tar.gz
```

## ToDo
- [ ] test [computational crystallography toolbox](https://cctbx.github.io) 

## Benchmark

```
./benchmark.sh  # run all benchmarks
snakeviz pycodcif_100.prof  # inspect one output
```
or inspect on the command line 
```
python -m pstats pycofcif_100.prof
```

## Results

### MacBook Pro 2015, Intel Core i7 2.2GHz, 512GB SSD

* `ase_100.prof`: 98.3s spent in `read_cif`
* `pymatgen_100.prof`: 159.0s spent in `from_file`
* `pycifrw_100.prof`: 91.6s spent in `ReadCif`
* `pycifrw-fast_100.prof`: 17.6s spent in `ReadCif`
* `pycodcif_100.prof`: 16.3s spent in `parse`

### Ubuntu 18.04, Intel® Core™ i7-4790 CPU @ 3.60GHz × 8, HDD 
* Note: Extended test set! * Heavy load (both memory and CPU) parallel to benchmark. Means and standard deviations from three runs (except for `pycifrw`, where we only did two runs).  

* `ase_108.prof`: memory error for mil structures (in `return sqrt(add.reduce(s, axis=axis, keepdims=keepdims)`), at time of test approx. 12 GB free memory 
* `pymatgen_108.prof`:  629 +/- 14 s
* `pycifrw_108.prof`: 127 +/- 2 s
* `pycifrw-fast_108.prof`: 20 +/- 1 s 
* `pycodcif_108.prof`: 29 +/- 1 s

ASE problematic for huge structures due to inefficient (memory) representation, can not be recommended for a general purpose tool. `pycodcif` 
seems to be the fasted implementation in this test, also with significant benefit over `pycifrw` when reading really large structures.  

## Conclusion

`pycodcif` and `pycifrw` (with `scan_type='flex'`) parse the CIF files in < 0.2s per structure.

Both ASE and pymatgen take more than 4x as long.
