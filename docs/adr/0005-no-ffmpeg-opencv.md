# ADR 0005: OpenCV exclusion and private FFmpeg

## Status
Accepted — 2026-09. FFmpeg half superseded — 2026-10: a trimmed LGPL FFmpeg 6.1.1 is bundled with private `*-rocm-vision` SONAMEs. OpenCV stays excluded.

## Context
rocAL upstream supports optional ffmpeg and OpenCV backends for video decoding and image I/O. These are large dependency trees.

## Decision
**Exclude OpenCV by design.** FFmpeg is the shallow submodule `third-party/ffmpeg`, built with `--disable-all` plus the file reader, H.264/HEVC decoders, and `libswscale`. The libraries ship as `libavcodec-rocm-vision`, `libavformat-rocm-vision`, `libavutil-rocm-vision`, and `libswscale-rocm-vision`.

## Rationale
- A full FFmpeg build pulls external codecs (libvorbis, libx264, and the rest). The bundled build enables only libavutil, libavcodec, libavformat, and libswscale, with the native H.264/HEVC decoders and no `--enable-gpl`.
- OpenCV transitive deps: libgtk, libpng, libtiff, libwebp, ... (~100+ MB).

## Consequences

### Positive
- rocAL's software video reader links private SONAMEs shipped in `amdrocm-vision-sysdeps`.
- OpenCV stays out of `runvx`.

### Negative
- rocAL OpenCV augmentation ops are disabled.
- The FFmpeg 6.1.1 pin is a submodule commit. A newer FFmpeg is a new pin, not a nightly bump.

## References
- `third-party/CMakeLists.txt` — `VISION_PACK_BUNDLE_FFMPEG`, shallow submodule `third-party/ffmpeg`
- `CMakeLists.txt` — `-DFFMPEG_PATH` for the private prefix; `-DCMAKE_DISABLE_FIND_PACKAGE_FFmpeg=ON` when the bundle is off
