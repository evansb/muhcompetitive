
Here lies a Scons build script to manage and test competitive programming (problem) easily.

1. Install scons using `brew install scons`
2. To create a new problem, `scons --create {category}/{name}`
   For instance,

        scons --create adhoc/barcode

   This will create `adhoc/barcode.cpp`, `_input/adhoc/barcode`
   , `_output/adhoc/barcode`, and `_expected/adhoc/barcode`.

4. To build and run all modified files on its corresponding input files, then diff
   with expected output simply run

        scons -s               # For all files
        scons -s adhoc/barcode # For one problem

5. Scons will only diff with expected output if it is not empty.
6. To cleanup the `*.o` and executable files.

        scons -c
