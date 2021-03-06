# :dancers: [Discohash](https://github.com/cris691/discohash)

#### **[2-5GB/s, passed SMHasher](https://github.com/rurban/smhasher/blob/master/doc/BEBB4185.txt)**  [![version](https://img.shields.io/npm/v/bebb4185.svg?label=&color=0080FF)](https://github.com/cris691/discohash/releases/latest) ![npm downloads](https://img.shields.io/npm/dt/bebb4185)

**Discohash** (*also known as* **BEBB4185**) is a super simple and super fast hash that passes all of SMHasher, and runs at 2-5GB/s (depending on hardware) in this naive, portable, serial implementation.

[Link to the SUPERCOP ECRYPT benchmark for bebb4185](https://bench.cr.yp.to/impl-hash/bebb4185.html)

## Cryptanalysis

**Cryptanalysis Open**

Inviting all budding hashbreakers to attack the 128-bit version. Should be easy, right?

If you'd like something a little more challenging, try breaking [beamsplitter-128](https://github.com/cris691/beamsplitter)

Submit your results as PR requests updating the README to a link to your analysis, or [contact Cris directly](mailto:cris@dosycorp.com).

**Important Note** The 128-bit variants must be modified from the existing source C/C++ files. Find the commented out lines at the end of the main hash function, and return 128-bits, instead of 64.

**CLI app included**

------

# Quick Facts

- A super-fast 64-bit hash.
- one of the fastest hashes ever benchmarked at [ecrypt](https://bench.cr.yp.to/impl-hash/bebb4185.html)
- [ECRYPT](https://bench.cr.yp.to/impl-hash/bebb4185.html) benchmark is 4x faster than [BLAKE3](https://bench.cr.yp.to/impl-hash/blake3.html)
- Mix is super simple.
- Tested at ~ 5GB/s @ 3Gz, (Google Cloud Platform, N1 CPU)
- Passes all SMHasher tests. 
- *Also known as*: **BEBB4185**
- Implemented in C++, and also a port to JS
- This repo includes a simple CLI app for hashing files or stdin from the command line.

## Simplicity

The main 128-bit-to-128-bit mixing function is just this:

```c
  mix(const int A)
    {
      const int B = A+1;
      
      ds[A] *= P;
      ds[A] = rot(ds[A], 23);
      ds[A] *= Q;
      
      ds[B] ^= ds[A];

      ds[B] *= P;
      ds[B] = rot(ds[B], 23);
      ds[B] *= Q;
    }
```

which is just multiplications by two 64-bit primes, P and Q, bit rotation, and xor.

P, and Q are:
- P = 0xFFFFFFFFFFFFFFFF - 58
- Q = 13166748625691186689

The standard internal state is 256-bits and the mixing function windows across that.

The standard digest is 64-bits, but you can modify it to yield 128-bits or more if you want a cryptographically secure hash.

## Using

Use the C code from this repository, either in your project or as a CL-app (included):

```console
cd src
./build.sh
./bin/bebbsum < 0xa2a647993898a3df.txt
> 0xa2a647993898a3df
./bin/bebbsum 0xa2a647993898a3df.txt
> 0xa2a647993898a3df
```

or, for a JS implementation:

```console
npm i --save bebb4185
```

Use in Node.JS:

```js
import {discohash} from 'bebb4185'
```

Or using Snowpack as a webmodule:

```js
import {discohash} from './web_modules/bebb4185.js';
```

Then call it:

```js
const hash = discohash(string_or_typed_array_key, optional_seed);
```

## JS Implementation

- The JS Implementation produces the same value hashes as the C++ implementation.
- The JS implementation is ~ 500x slower than the C++ implementation.
- This is probably because of the use of BigInts to stand in for `uint64_t`
- It's possible to implement a 64-bit mult using 32-bit view which would remove the need for BigInt. I have no plan to do this.

## SMHasher verification value

The value is: `BEBB4185`

## Possible future work

Make a parallel version using Merkle tree
