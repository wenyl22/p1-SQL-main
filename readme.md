# Wing

## Clone and create your private repository

1. Create a new empty repository [here](https://github.com/new) and **make sure that the visibility is private**. Note that repositories that are directly forked on Github can not be private. Here we denote the newly created repository as `git@github.com:your-id/your-repo.git`.

2. Clone your repository

```shell
git clone git@github.com:your-id/your-repo.git wing
cd wing
```

3. Pull code from our public repository, and push it to your private repository

```shell
git remote add public git@github.com:iidb/wing.git
git pull public main
git submodule update --init
git push origin
```

If there are any updates to the lab framework, you can `git fetch public && git merge public/main` to merge the updates into your repository.

## Build

### Install dependencies

Besides the basic compilation environment, wing also relies on GTest. Here is the installation guide of GTest for some platforms.

#### Debian

```shell
sudo apt install libgtest-dev
```

#### ArchLinux

```shell
sudo pacman -S gtest
```

#### Nix package manager

One of the main advantages of Nix is that it does not require root permission. However, libraries installed by Nix can only be used in `nix-shell`:

```shell
nix-shell -p gtest cmake
```

Then execute commands in the shell generated by `nix-shell`:

```shell
mkdir build
cd build
cmake ..
make -j8
```

### Compile

Wing requires C++20. You may need to upgrade your compiler.

Windows: [Mingw-w64](https://winlibs.com/)

Linux: Upgrade the version of GCC to 10 or newer. You may also use Clang.

Compile with G++:

```shell
mkdir build
cd build
cmake .. -DBUILD_JIT=OFF -DCMAKE_BUILD_TYPE="release" -DCMAKE_CXX_COMPILER="g++" -DCMAKE_C_COMPILER="gcc"
cmake --build . -j
```

Compile with Clang:

```shell
mkdir build
cd build
cmake .. -DBUILD_JIT=OFF -DCMAKE_BUILD_TYPE="release" -DCMAKE_CXX_COMPILER="clang++" -DCMAKE_C_COMPILER="clang"
cmake --build . -j
```

The compilation results include the command line version of wing and testers. The usage of the command line version of wing is in `docs/pre.md`.

Use `-DCMAKE_BUILD_TYPE="debug"` to compile a version for debugging. We strongly recommend storing the debug compilation results and release compilation results into two separate folders, such as `build-release` and `build-debug` so that you don't have to compile from scratch when switching between two compilation modes.

You may use LLVM JIT with `-DBUILD_JIT=ON`. It would be better for the LLVM version to be 11 or newer, although we are not sure whether wing is compatible with LLVM version 10 or older. The installation guide of LLVM-dev for Linux: <https://apt.llvm.org/>

On Linux, you may specify the number of threads to build: `make -j<thread-count>`, e.g., `make -j8`. You may also set the number of threads to the core number: `make -j$(nproc)`. You may make a command alias for it in `~/.bashrc`: `alias jmake='make -j$(nproc)'`

P.S. `make -j` does not limit the number of threads to use. If there are not enough CPU cores, the compilation may be slower due to the higher contention of CPU resources and higher memory consumption.

If your computer is slow, you may consider using other faster build systems such as ninja: `-G "Ninja"`

## Tests

Wing tests with GTest. The code for testing is under `test/`. The compiled testers will be under `build/test/`.

Here we take `build/test/test_lsm` as an example to introduce the usage of testers using GTest.

```shell
cd build
./test/test_lsm --help
# Run all tests
./test/test_lsm
# Run a specific test
./test/test_lsm --gtest_filter=LSMTest.SSTableTest
# Run all tests whose name matches the pattern
./test/test_lsm --gtest_filter="LSMTest.Mem*Test"
```

You may use `gtest-parallel` to test parallelly: <https://github.com/google/gtest-parallel/>

```shell
# Run all tests
/path/to/gtest-parallel ./test/test_lsm
# Run all tests in multiple files
/path/to/gtest-parallel ./test/test_lsm ./test/test_btree
# Run some tests
/path/to/gtest-parallel ./test/test_lsm --gtest_filter=xxx
# Specify thread number (Default is the core number)
/path/to/gtest-parallel --workers=8 ./test/test_lsm
```

You may make an alias in `~/.bashrc`:

```shell
alias gtp="/path/to/gtest-parallel/gtest-parallel --workers=8"
```

## Documentation

The documentation about the usage of the CLI version, the implementation of wing, and SQL grammar are under `docs/`.

## LICENSE

Except as otherwise noted (below and/or in individual files), this project is licensed under MIT license.
