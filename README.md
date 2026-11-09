# jsc

Standalone C++ dictionary compression and varicode utilities extracted from
JS8Call. The library retains the original codec algorithms and dictionary data
without requiring the full JS8Call application.

The JS8Call-derived code in this directory has been relicensed under the
**MIT License by Jordan Sherer (the copyright holder)**.
The bundled CRC++ header retains its BSD-3-Clause license; see [Licensing](#licensing).

## Included utilities

| Files | Purpose |
| --- | --- |
| `jsc.h`, `jsc.cpp` | Dictionary lookup, codeword generation, compression, and decompression. |
| `jsc_list.cpp`, `jsc_map.cpp` | Generated lookup and decoding tables for the 262,144-entry dictionary. |
| `varicode.h`, `varicode.cpp` | Huffman coding, bit conversion, integer packing, checksums, callsign and grid packing, and JS8Call message framing. |
| `crc.h` | CRC++ implementation used by the checksum utilities. |
| `CMakeLists.txt` | Standalone static-library build and the `jsc::jsc` CMake target. |

The synchronous `Varicode::buildMessageFrames` API is included. The GUI dictionary
checker and threaded transmit-preview wrapper are not. No GUI, audio, radio-control,
or Fortran decoder components are required.

This project builds a library, not a command-line program or the JS8Call application.

## Requirements

- CMake 3.16 or newer.
- A C++11-capable compiler.
- Qt 5.8 or newer within the Qt 5 series, with Core development files.

On Debian or Ubuntu, the build dependencies are available as `cmake`, `g++`, and
`qtbase5-dev`. Qt Widgets and Qt Multimedia are not needed. Qt 6 is not currently
supported by this build.

## Build

From the `jsc` project directory:

```sh
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build
```

The dictionary tables are compiled into the library; no dictionary files need to
be loaded at runtime. If Qt is installed outside the system search paths, pass its
installation prefix using `-DCMAKE_PREFIX_PATH=/path/to/Qt` when configuring.

## Use from another CMake project

Place this project in a `jsc` subdirectory of your application and link its target:

```cmake
add_subdirectory(jsc)
add_executable(my_app main.cpp)
target_link_libraries(my_app PRIVATE jsc::jsc)
```

The target supplies the include directory, C++11 requirement, and Qt Core linkage.
Include `jsc.h` for dictionary operations and `varicode.h` for the other utilities.

### Dictionary compression example

```cpp
#include "jsc.h"
#include "varicode.h"

const QString text = QStringLiteral("HELLO WORLD");
const auto codewords = JSC::compress(text);

Codeword bits;
for (const auto &codeword : codewords) {
    bits += codeword.first;
}

const QString binary = Varicode::bitsToStr(bits);
const QString decoded = JSC::decompress(bits); // "HELLO WORLD"
```

`JSC::compress` returns a list of `CodewordPair` values. Each pair contains the
codeword bits and the number of input characters consumed, including any encoded
separator space. Concatenate the bit vectors before passing them to
`JSC::decompress`.

These codecs follow JS8Call's text and framing conventions; they are not a
general-purpose compressor for arbitrary binary or Unicode input. The extraction
preserves the original input handling, dictionary ordering, and protocol behavior
rather than adding normalization or validation.

## Licensing

The extracted JS8Call dictionary and varicode sources are licensed under the
[MIT License](LICENSE), as authorized by Jordan Sherer (their copyright holder).
Original copyright attribution is retained in the source files.
This relicensing applies to the extracted work here; it does not modify the
separate JS8Call source tree.

[`crc.h`](crc.h) is CRC++, copyright (c) 2016 Daniel Bahr, and remains under the
BSD-3-Clause terms reproduced in that file. Preserve those third-party notices
when redistributing it.
