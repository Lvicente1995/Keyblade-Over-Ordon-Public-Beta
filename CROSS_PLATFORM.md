# Cross-platform build notes (v0.2.1)

Targets prepared by this source tree:

- Windows AMD64 (MSVC)
- Linux x86_64 (Steam Deck)
- Android ARM64 / arm64-v8a

## Important audio note

The v0.2.0 custom audio implementation synchronizes with Dusklight's JAS audio thread by resolving MSVC-decorated `JASCriticalSection` constructor/destructor symbols. Those names and that ABI are Windows-specific.

For safety, v0.2.1 keeps custom Kingdom Key WAV playback on MSVC/Windows and uses Twilight Princess' native sword sounds on Linux/Steam Deck and Android. The model, chain physics, KHIII visual effects, hit effects, shield gesture, shadows and reflections remain enabled.

Do not replace the non-Windows fallback with guessed Itanium/Android mangled names. Re-enable custom audio only after Dusklight exposes a platform-neutral synchronization API or after the exact symbol/ABI is verified for each target.

## GitHub Actions build

Push this project to a GitHub repository. `.github/workflows/build.yml` builds all three targets and creates a `mod-combined` artifact containing a single multi-platform `.dusk`.

The combined package should contain native libraries under platform directories such as:

- `lib/windows-amd64/mod.dll`
- `lib/linux-x86_64/mod.so`
- `lib/android-aarch64/mod.so`

## Steam Deck

Use the Linux build of Dusklight. Install the combined `.dusk` in:

`~/.local/share/TwilitRealm/Dusklight/mods`

## Android

Use the ARM64 Dusklight build. Put the combined `.dusk` in the `mods` directory under Dusklight's selected data folder. Prefer Vulkan on supported devices; OpenGL ES is a best-effort fallback and may render custom effects differently.

## Local builds

A local build produces only the current host platform. The project can use an existing Dusklight checkout:

`cmake -B build -DDUSKLIGHT_DIR=/path/to/dusklight`

Then:

`cmake --build build --parallel`

For Android, use the Android NDK CMake toolchain with `ANDROID_ABI=arm64-v8a` and `ANDROID_PLATFORM=android-28`, matching the included CI workflow.
