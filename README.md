# kawpowminer (ethminer fork with ProgPoW implementation)

Kawpowminer is a derivative of Ethash enhanced with [Programmable Proof of Work](https://github.com/ifdefelse/progpow) for ASIC and FPGA resistance.

> kawpowpow miner with OpenCL, CUDA, CPU and stratum support

AMD GPU cards are not currently properly supported.

Remember that you need to have CUDA installed on your system in order for kawpowminer to work. Get it from nVidia.

**kawpowminer** is an ProgPoW GPU mining worker: with kawpowminer you can mine Ravencoin, which relies on an ProgPoW-based Proof of Work thus including Ethereum ProgPoW and others. This is the actively maintained version of kawpowminer. It originates from the [ethminer](https://github.com/ethereum-mining/ethminer) project. Check the original [ProgPoW](https://github.com/ifdefelse/progpow) implementation and [EIP-1057](https://eips.ethereum.org/EIPS/eip-1057) for specification.

## Features

* First commercial ProgPOW Raven miner software for miners.
* OpenCL mining
* Nvidia CUDA mining
* CPU mining
* realistic benchmarking against arbitrary epoch/DAG/blocknumber
* on-GPU DAG generation (no more DAG files on disk)
* stratum mining without proxy
* OpenCL devices picking


## Table of Contents

* [Install](#install)
* [Usage](#usage)
    * [Examples connecting to pools](#examples-of-connections)
    * [Benchmarking](#benchmarking)
* [Build](#build)
    * [Ubuntu / OSX](#ubuntu--osx )
    * [Windows](#windows)
* [F.A.Q.](#faq)
* [Maintainers & Authors](#maintainers--authors)


## Install

Standalone **executables** for *Linux*, *macOS* and *Windows* are provided in
the [Releases] section.
Download an archive for your operating system and unpack the content to a place
accessible from command line. The kawpowminer is ready to go.

If you have trouble with missing .dll or CUDA errors, [please install the latest version of CUDA drivers](https://developer.nvidia.com/cuda-downloads) or report to project maintainers.

## Usage

The **kawpowminer** is a command line program. This means you launch it either
from a Windows command prompt or Linux console, or create shortcuts to
predefined command lines using a Linux Bash script or Windows batch/cmd file.
For a full list of available command, please run:

`kawpowminer --help` Or for specific info about a module `kawpowminer -H <moduleName>`

(has to be before the `-P` flag)
`kawpowminer -U -P ...` = Forced CUDA
`kawpowminer -G -P ...` = Forced OpenCL
`kawpowminer --cpu -P ...` = Forced CPU

### Examples of connections

A typical use of kawpowminer with a pool might look like:

```
kawpowminer -P stratum+tcp://n2QdGzZai5qPfUDuF747kr9juhRZPZEhEz.worker@trvn.evrpool.com:2222
```

With a local stratum solo-mining proxy:

```
kawpowminer -P stratum+tcp://n2QdGzZai5qPfUDuF747kr9juhRZPZEhEz.worker@127.0.0.1:2345
```

To solo mine direct to ravend/raven-qt (specify miningaddress="youraddress" in raven.conf):

```
kawpowminer -P http://mynodeusername:mynodepassword@127.0.0.1:8766
``` 
Dont forget to configure your node if you are solo mining direct to ravend or raven-qt. Before launching kawpowminer, make sure that:

* the node is fully syncd.
* the node is listening on the RPC port. (8766 for mainnet or 18766 for testnet)
* the node has `server=1` set in the raven.conf file.
* the node has `miningaddress="youraddress"` set in the raven.conf file.
* the node has `rpcuser=yourUser` and `rpcpassword=yourPass` set in the raven.conf file.
* optionally the node has `testnet=1` set in the raven.conf file.


For CPU mining run the miner with the `--cpu` before the `-P` flag like so (works for pool, proxy and direct to node):

```
kawpowminer --cpu -P http://mynodeusername:mynodepassword@127.0.0.1:18766
``` 
When running the `--cpu` flag the miner will disable gpu mining.

### Benchmarking

```
kawpowminer -M <Block Height>
```


## Build

After cloning this repository into `kawpowminer`, it can be built with commands like:

### Ubuntu / OSX
```
cd kawpowminer
git submodule update --init --recursive
mkdir build
cd build
cmake .. -DETHASHCUDA=ON -DETHASHCL=ON -DETHASHCPU=ON -DAPICORE=ON
make -sj $(nproc)
```


### Windows

### Prerequisites:
1. Install Visual Studios (2019) (with the additional installation package "C++ Cmake Tools for Windows)
2. Install latest perl to C:\Perl (https://www.perl.org/get.html)
   Follow the steps outlined and the default perl installtion should work

### Building via Visual Studios Command Line:
Open "Developer Command Prompt for VS 2019"
1. Open StartMenu and search for "Developer Command Prompt for VS 2019"
2. Follow these steps:
```
cd C:\Users\USER_NAME\PATH_TO_KAWPOWMINER\kawpowminer
git submodule update --init --recursive
mkdir build
cd build
cmake -G "Visual Studio 16 2019" -A X64 -H. -Bbuild -DETHASHCL=ON -DETHASHCUDA=ON -DETHASHCPU=ON -DAPICORE=ON ..
cd build
cmake --build . --config Release
```
(Yes, two nested build/build directories.)

### Building via Visual Studios GUI (This build doesn't seem to work for some 20XX Nvidia cards)
   1. Open Visual Studios
   2. Open CMakeLists.txt file with File->Open->CMake
   3. Wait for intelligence to build the cache (this can take some time)
   4. Build the project (CTRL+SHIFT+B) or find the build command in the menu

=================

ProgPoW can be tuned using the following parameters.  The proposed settings have been tuned for a range of existing, commodity GPUs:

* `PROGPOW_PERIOD`: Number of blocks before changing the random program
* `PROGPOW_LANES`: The number of parallel lanes that coordinate to calculate a single hash instance
* `PROGPOW_REGS`: The register file usage size
* `PROGPOW_DAG_LOADS`: Number of uint32 loads from the DAG per lane
* `PROGPOW_CACHE_BYTES`: The size of the cache
* `PROGPOW_CNT_DAG`: The number of DAG accesses, defined as the outer loop of the algorithm (64 is the same as Ethash)
* `PROGPOW_CNT_CACHE`: The number of cache accesses per loop
* `PROGPOW_CNT_MATH`: The number of math operations per loop

The value of these parameters has been tweaked to use 0.9.4 specs with a PROGPOW_PEROD of 3 to fit Raven's blocktimes.  See [this medium post](https://medium.com/@ifdefelse/progpow-progress-da5bb31a651b) for details.

| Parameter             | 0.9.2 | 0.9.3 | 0.9.4 |
|-----------------------|-------|-------|--------|
| `PROGPOW_PERIOD`      | `50`  | `10`  |  `3`   |
| `PROGPOW_LANES`       | `16`  | `16`  |  `16`  |
| `PROGPOW_REGS`        | `32`  | `32`  |  `32`  |
| `PROGPOW_DAG_LOADS`   | `4`   | `4`   |  `4`   |
| `PROGPOW_CACHE_BYTES` | `16x1024` | `16x1024` | `16x1024` |
| `PROGPOW_CNT_DAG`     | `64`  | `64`  | `64`  |
| `PROGPOW_CNT_CACHE`   | `12`  | `11`  | `11`  |
| `PROGPOW_CNT_MATH`    | `20`  | `18`  | `18`  |

Epoch length = 7500 blocks

## License

Licensed under the [GNU General Public License, Version 3](LICENSE).


## F.A.Q

### Can I still mine Raven with my 4GB GPU?

Not really, your VRAM must be above the DAG size (Currently about 4 GB.) to get best performance. Without it severe hash loss will occur.

### Can I CPU Mine?



## Maintainers & Authors

[![Discord](https://img.shields.io/badge/discord-join%20chat-blue.svg)](https://discord.gg/uvyuqWm)

The list of current and past maintainers, authors and contributors to the kawpowminer project.
Ordered alphabetically. [Contributors statistics since 2015-08-20].

| Name                  | Contact                                                      |     |
| --------------------- | ------------------------------------------------------------ | --- |
| Hans Schmidt          | [@hans-schmidt](https://github.com/hans-schmidt)             | --- |
| Jeremy Anderson       | [@Blondfrogs](https://github.com/Blondfrogs)                 | --- |
| Traysi                | [@traysi](https://github.com/traysi)                         | --  |
| Andrea Lanfranchi     | [@AndreaLanfranchi](https://github.com/AndreaLanfranchi)     | ETH: 0xa7e593bde6b5900262cf94e4d75fb040f7ff4727 |
| EoD                   | [@EoD](https://github.com/EoD)                               |     |
| Genoil                | [@Genoil](https://github.com/Genoil)                         |     |
| goobur                | [@goobur](https://github.com/goobur)                         |     |
| Marius van der Wijden | [@MariusVanDerWijden](https://github.com/MariusVanDerWijden) | ETH: 0x57d22b967c9dc64e5577f37edf1514c2d8985099 |
| Paweł Bylica          | [@chfast](https://github.com/chfast)                         | ETH: 0x8FB24C5b5a75887b429d886DBb57fd053D4CF3a2 |
| Philipp Andreas       | [@smurfy](https://github.com/smurfy)                         |     |
| Stefan Oberhumer      | [@StefanOberhumer](https://github.com/StefanOberhumer)       |     |
| ifdefelse             | [@ifdefelse](https://github.com/ifdefelse)                   |     |
| Won-Kyu Park          | [@hackmod](https://github.com/hackmod)                       | ETH: 0x89307cb2fa6b9c571ab0d7408ab191a2fbefae0a |
| Ikmyeong Na           | [@naikmyeong](https://github.com/naikmyeong)                 |     |


[cpp-ethereum]: https://github.com/ethereum/cpp-ethereum
[Contributors statistics since 2015-08-20]: https://github.com/kawpoworg/kawpowminer/graphs/contributors?from=2015-08-20
[Genoil's fork]: https://github.com/Genoil/cpp-ethereum
[Releases]: https://github.com/kawpoworg/kawpowminer/releases
