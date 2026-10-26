# Mira maintenance branch

This is a downstream maintenance fork of [nghttp2/nghttp2](https://github.com/nghttp2/nghttp2),
based on upstream **v1.70.0**. Upstream copyright notices and the MIT license
in `COPYING` remain in place.

## 1.70.0-mira.1

Successful CONNECT responses (every 2xx status, including 204) establish a tunnel.
Their Content-Length is ignored, as required by RFC 9110 section 9.3.6.
Ordinary 204/HEAD responses remain bodyless; failed CONNECT responses retain
normal Content-Length validation. Real encoded response frames and DATA are
covered by the library's regression tests.

Consumers can check `NGHTTP2_HAS_CONNECT_2XX_TUNNEL == 1` in the
public header. The runtime version identifies the downstream patch with
`-mira.1`; the numeric upstream version and ABI stay unchanged.

## Build and test

```sh
git clone --branch mira --recurse-submodules https://github.com/dqsjqian/nghttp2.git
cd nghttp2
cmake -S . -B build/verify -DCMAKE_BUILD_TYPE=Release -DENABLE_LIB_ONLY=ON -DBUILD_SHARED_LIBS=OFF -DBUILD_STATIC_LIBS=ON -DENABLE_FAILMALLOC=OFF -DENABLE_DOC=OFF -DBUILD_TESTING=ON
cmake --build build/verify --config Release --target main --parallel 4
ctest --test-dir build/verify -C Release --output-on-failure
```

## Releases and upstream updates

The `mira` branch carries the downstream changes. Merge a reviewed stable upstream
tag, retain upstream history, rerun the complete library tests and the Mira
integration suite, then publish a new `v1.70.0-mira.N` tag after CI passes.
Do not move published tags. When upstream implements this behavior, compare the
regression tests and remove the local patch once the upstream version passes.

`python3 maintainer/package.py` packages the committed HEAD and the exact recursive
submodule revisions into `build/dist/`. Its archive includes licenses and a
`MIRA-SOURCE.json` provenance manifest, with a separate SHA256 file.
The archive is intended for CMake; the upstream GitHub-generated source archives
omit submodule contents and are not a substitute.

The downstream `mira` workflow builds and tests this exact archive on Linux,
macOS and Windows. Upstream service/deployment workflows are disabled on this fork.
