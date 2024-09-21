# winebash
Patched [GNU Bash](https://www.gnu.org/software/bash/) shell that launches exe files via [Wine](https://www.winehq.org/) for windows cross building in containers lacking [binfmt_misc](https://en.wikipedia.org/wiki/Binfmt_misc) support.

## Installation
Inside the container, configure bash with e.g.
```bash
./configure --prefix=/usr
```
and build and install with 
```bash
make && make install
```
This will overwrite the bash installation in /usr. Alternatively, configure with another `prefix` than `/usr` and adjust the `PATH` environment variable to point this patched version of bash before any other bash installation.

Finally, replace `/bin/sh` with a link to this version of bash. This is important, as `/bin/sh` is a default script interpreter which is launched for unrecognized executable files.
```bash
cd /bin
ln -sf bash sh
```
or
```bash
ln -sf /usr/bin/bash /bin/sh
```
depending on the location of the patched bash binary.

Wine is expected to be installed as `/usr/bin/wine`. Another wine binary path can be defined in [shell.h](https://github.com/v-joe/winebash/blob/main/shell.h) by adjusting the line:
```C
#define WINE "/usr/bin/wine"
```

## How it works
Executable binaries that are not recognized natively as binary executables (i.e. that are not recognized as `ELF` binaries on Linux), are checked for their header and launched via wine, if the header is '[MZ](https://en.wikipedia.org/wiki/DOS_MZ_executable)'.
