# SHA-1 GPU near-collision attacks

## This fork

This repository is a fork of the original code from https://github.com/cr-marcstevens/sha1_gpu_nearcollisionattacks
It implements improvements from the following publication:

- [*SHA-1 is a Shambles: First Chosen-Prefix Collision on SHA-1 and Application to the PGP Web of Trust*](https://eprint.iacr.org/2020/014.pdf), Gaëtan Leurent, Thomas Peyrin, USENIX Security '20, to appear.

  See also https://SHA-mbles.github.io

  This code improves the shatterednc2 attack ("Find your own shattered 2nd near-collision block pair" below)

## Building

 - Update the parameter `BLOCKS` at the top of `shattered_nc2/cuda_step14-60.cu` to twice the number of streaming multiprocessors in your GPU (eg 26 for a GTX 970, or 58 for a GTX 1080 Ti).

 - `autoreconf --install`
   (You may need to install the autoconf-archive package from your distribution)

 - `./configure [--with-cuda=/usr/local/cuda-X.X] [--enable-cudagencode=50,52]`
   (Make sure to enable the correct gencode for your GPU, eg 61 for a GTX 1080 Ti)

 - `make`

## Running

 - Expected GPU runtime: *22 years* on a single GTX-970

 - Generate basesolutions (32 base64-encoded per textline)

 `bin/shatterednc2_basesolgen -g -o nc2_basesols.txt -m 64`

 - Run GPU attack:

 `bin/shatterednc2_gpuattack -a -i nc2_basesols.txt -o nc2_q61sols.txt`

 - Check for collision among 61-step solutions:
 
 `bin/shatterednc2_basesolgen -v -i nc2_q61sols.txt | grep Found -B88 -A52`

- Repeat until collision found

## Important note

The code to generate base-solutions is suboptimal.  We need base
solutions with more constraints than for the Shattered attack, so we
just filter the base-solutions that satisfy the extra conditions.  This code should be rewritten.

In the Shambles attack, we actually used a different framework to
generate base-solutions for the differential trails used in the
attack.


# Original README

## Publications

This repository contains the source code belonging to three scientific publications:

- [*Practical free-start collision attacks on 76-step SHA-1*](https://eprint.iacr.org/2015/530), Pierre Karpman, Thomas Peyrin, and Marc Stevens, CRYPTO 2015, Lecture Notes in Computer Science, vol. 9215, Springer, 2015, pp. 623-642.

  This publication introduces the efficient GPU framework for SHA-1 collision finding, improvements of cryptanalytic tools, and a freestart attack on 76-step SHA-1. The runtime cost is roughly 5 days on 1 NVIDIA GTX-970 GPU and was executed on the GPU cluster of Thomas Peyrin.

- [*Freestart collision for full SHA-1*](https://eprint.iacr.org/2015/967), Marc Stevens, Pierre Karpman, Thomas Peyrin, EUROCRYPT 2016, Lecture Notes in Computer Science, vol. 9665, Springer, 2016, pp. 459-483.

  This publication introduces a freestart attack on 80-step SHA-1, further improvements of cryptanalytic tools, and presents a cost analysis for a (normal) collision attack for full SHA-1. The runtime cost is roughly 640 days on 1 NVIDIA GTX-970 GPU and was executed on the GPU cluster of Thomas Peyrin.
  
  See also https://sites.google.com/site/itstheshappening/

- [*The first collision for full SHA-1*](https://eprint.iacr.org/2017/190), Marc Stevens, Elie Bursztein, Pierre Karpman, Ange Albertini, Yarik Markov, CRYPTO 2017, Lecture Notes in Computer Science, vol. 10401, Springer, 2017, pp. 570-596.

  This publication presents a complete collision attack on full SHA-1 and further improvements of cryptanalytic tools. The majority of the runtime cost is roughly 102 years on 1 NVIDIA GTX-970 GPU and was executed on a distributed GPU system of Google. 
  Note that only the second near-collision attack was implemented for GPU and is released here without the changes to adapt it to Google's proprietary build and job systems. 
  The first near-collision attack of this project was already published in EUROCRYPT 2013 and is available at https://github.com/cr-marcstevens/hashclash.

  See also https://shattered.it


## Requirements

 - CUDA SDK

 - C++11 compiler compatible with CUDA SDK

 - autotools

## Building

 - `autoreconf --install`

 - `./configure [--with-cuda=/usr/local/cuda-X.X] [--enable-cudagencode=50,52]`

 - `make`

## Find your own 76-round SHA-1 freestart collision

 - Expected GPU runtime: *5 days* on a single GTX-970

 - `mkdir fs76; cd fs76`

 - `../run_freestart76.sh`

 - Example manual usage of tools:
 
   1. `bin/freestart76_basesolgen --genbasesol --seed 4_23_152443400808031284 --maxbasesols 262144 -o fs76_basesols.bin`
   
   2. `bin/freestart76_gpuattack --cudaattack -i fs76_basesols.bin -o fs76_q56sols.bin`
   
   3. `bin/freestart76_basesolgen --verifyQ56 -i fs76_q56sols.bin | grep Found -B88 -A52`

## Find your own 80-round SHA-1 freestart collision

 - Expected GPU runtime: *640 days* on a single GTX-970

 - Generate basesolutions (32 base64-encoded per textline)

 `bin/freestart80_basesolgen -g -o fs80_basesols.txt -m 1024`

 - Run GPU attack (generates 60-step solutions):

 `bin/freestart80_gpuattack -a -i fs80_basesols.txt -o fs80_q60sols.txt`
 
 - Check for collision among 60-step solutions:
 
 `bin/freestart80_basesolgen -v -i fs80_q60sols.txt | grep Found -B88 -A52`

- Repeat until collision found

## Find your own shattered 2nd near-collision block pair

 - Expected GPU runtime: *102 years* on a single GTX-970

 - Generate basesolutions (32 base64-encoded per textline)

 `bin/shatterednc2_basesolgen -g -o nc2_basesols.txt -m 1024`

 - Run GPU attack:

 `bin/shatterednc2_gpuattack -a -i nc2_basesols.txt -o nc2_q61sols.txt`

 - Check for collision among 61-step solutions:
 
 `bin/shatterednc2_basesolgen -v -i nc2_q61sols.txt | grep Found -B88 -A52`

- Repeat until collision found
