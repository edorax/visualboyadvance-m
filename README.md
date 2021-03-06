# Visual Boy Advance - M

Game Boy Advance Emulator

Homepage and Forum: http://vba-m.com

## Building

The basic formula to build vba-m is:

```bash
cd ~ && mkdir src && cd src
git clone https://github.com/visualboyadvance-m/visualboyadvance-m.git
cd visualboyadvance-m
./installdeps

# ./installdeps will give you build instructions, which will be similar to:

mkdir build && cd build
cmake ..
make -j10
```

`./installdeps` is supported on MSys2, Linux (Debian/Ubuntu, Fedora or Arch
Linux) and Mac OS X (homebrew, macports or fink.)

If your OS is not supported, you will need the following:

- c++ compiler and binutils
- make
- cmake
- git
- nasm (for 32 bit builds)

And the following development libraries:

- zlib
- mesa (if using X11 or any OpenGL otherwise)
- ffmpeg (optional, for game recording)
- gettext and gettext tools
- jpeg
- png
- tiff
- SDL2
- SFML (optional, for link)
- OpenAL (optional, a sound interface)
- wxWidgets
- cairo (completely optional)

Support for more OSes/distributions for `./installdeps` is planned.

## Cross Compiling for Win32

`./installdeps` takes one optional parameter for cross-compiling target, which
may be `win32` which is an alias for `mingw-w64-i686` to target 32 bit Windows,
or `mingw-gw64-x86_64` for 64 bit Windows targets.

The target is implicit on MSys2 depending on which MINGW shell you started (the
value of `$MSYSTEM`.) It will not run in the MSys shell.

On Debian/Ubuntu this uses the MXE apt repository and works really well.

On Fedora it can build using the Fedora MinGW packages, albeit with wx 2.8, no
OpenGL support, and no Link support for lack of SFML.

On Arch it currently doesn't work at all because the AUR stuff is completely
broken, I will at some point redo the arch stuff to use MXE as well.

## CMake Options

The CMake code tries to guess reasonable defaults for options, but you can
override them on the cmake command with e.g.:

```bash
cmake .. -DENABLE_LINK=NO
```

Of particular interest is making **RELEASE** or **DEBUG** builds, the default
mode is **RELEASE**, to make a **DEBUG** build use something like:

```bash
cmake .. -DCMAKE_BUILD_TYPE=Debug
```

Here is the complete list:

| **CMake Option**     | **What it Does**                                                     | **Defaults**          |
|----------------------|----------------------------------------------------------------------|-----------------------|
| ENABLE_SDL           | Build the SDL port                                                   | OFF                   |
| ENABLE_WX            | Build the wxWidgets port                                             | ON                    |
| ENABLE_DEBUGGER      | Enable the debugger                                                  | ON                    |
| ENABLE_NLS           | Enable translations                                                  | ON                    |
| ENABLE_ASM_CORE      | Enable x86 ASM CPU cores                                             | ON for 32 bit builds  |
| ENABLE_ASM_SCALERS   | Enable x86 ASM graphic filters                                       | ON for 32 bit builds  |
| ENABLE_MMX           | Enable MMX                                                           | ON for 32 bit builds  |
| ENABLE_LINK          | Enable GBA linking functionality (requires SFML)                     | ON                    |
| ENABLE_LIRC          | Enable LIRC support                                                  | OFF                   |
| ENABLE_FFMPEG        | Enable ffmpeg A/V recording                                          | ON on Linux and MSys2 |
| ENABLE_LTO           | Compile with Link Time Optimization (gcc and clang only)             | ON where works        |
| ENABLE_GBA_LOGGING   | Enable extended GBA logging                                          | ON                    |
| ENABLE_CAIRO         | Enable Cairo rendering for wxWidgets                                 | OFF                   |
| ENABLE_DIRECT3D      | Direct3D rendering for wxWidgets (Windows, **NOT IMPLEMENTED!!!**)   | ON                    |
| ENABLE_XAUDIO2       | Enable xaudio2 sound output for wxWidgets (Windows only)             | ON                    |
| ENABLE_OPENAL        | Enable OpenAL for the wxWidgets port                                 | ON                    |

## MSys2 Notes

To run the resulting binary, you can simply type:

```bash
./visualboyadvance-m
```

in the shell where you built it.

If you built with `-DCMAKE_BUILD_TYPE=Debug`, you will get a console app and
will see debug messages, even in mintty.

If you want to start the binary from e.g. a shortcut or Explorer, you will need
to put `c:\msys64\mingw32\bin` for 32 bit builds and `c:\msys64\mingw64\bin`
for 64 bit builds in your PATH (to edit system PATH, go to Control Panel ->
System -> Advanced system settings -> Environment Variables.)

If you want to package the binary, you will need to include the MinGW DLLs it
depends on, they can install to the same directory as the binary.

For our own builds, we use MXE to make static builds.

## Debug Messages

We have an override for `wxLogDebug()` to make it work even in non-debug builds
of wx and on windows, even in mintty. Using this function for console debug
messages is recommended.

It works like `printf()`, but using `wxString`, so use `wxT()` e.g.:

```cpp
int foo = 42;
wxLogDebug(wxT("the value of foo = %d"), foo);
```

`%s` does not work for `wxString`, so use concatenation instead, e.g.:

```cpp
wxString world = "world";
wxLogDebug(wxT("Hello, ") + world + wxT("!"));
```

## CONTRIBUTING

Please keep in mind that this app needs to run on Windows, Linux and Mac OS X at
the very least, so code should be portable and/or use the appropriate `#ifdef`s
and the like when needed.

Please try to craft a good commit message, this post by the great tpope explains
how to do so:
http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html

If you have multiple small commits for a change, please try to use `git rebase
-i` (interactive rebase) to squash them into one or a few logical commits (with
good commit messages!) See:
https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History if you are new to
this.
