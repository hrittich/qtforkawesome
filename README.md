# Qt ForkAwesome
Library that bundles ForkAwesome for use within Qt applications

## Build instructions

### Dependencies
The library depends on the following Qt modules (version 5.6 or higher):
`core`, `gui`

At build time `qtutilities` and `c++utilities` are required. This library is
built in the same way as these libraries so checkout the `c++utilities`
repository for detailed instructions.

To generate the header with icon definitions, Perl and the module `YAML::XS` (or
`YAML`) are required. To use a specific Perl binary, one can set the CMake
variable `PERL`.

### Providing the font file
Of course the font file and icon definitions for ForkAwesome need to be
supplied as well.
Just download an archive from https://github.com/ForkAwesome/Fork-Awesome/releases
and specify its path via the CMake variables `FORK_AWESOME_FONT_FILE`
and `FORK_AWESOME_ICON_DEFINITIONS`, e.g. add
`-DFORK_AWESOME_FONT_FILE=/path/to/Fork-Awesome-1.2.0/fonts/forkawesome-webfont.woff2`
and
`-DFORK_AWESOME_ICON_DEFINITIONS=/path/to/Fork-Awesome-1.2.0/src/icons/icons.yml`
to the CMake invocation. The font file will be built into the library and
is hence only required at build time. If none of the variables are specified,
the build system will attempt to download the files from GitHub.

The Web Open Font Format (the `.woff`/`.woff2` file) might not be supported by
the font renderer. Notably, Window's native font rendering which Qt uses by
default does *not* support it. Then it makes sense to use the True Type Font
instead. Alternatively, one can force the platform plugin to use FreeType2 (e.g.
by setting `QT_QPA_PLATFORM=windows:fontengine=freetype`). Recent versions of
FreeType2 even support Web Open Font Format 2 (the `.woff2` file) but this
requires FreeType2 to be configured with Brotli support.

### Examples
Checkout https://github.com/Martchus/PKGBUILDs/tree/master/qtforkawesome for
examples to build for GNU/Linux and Windows against Qt 5 and Qt 6.

### Further remarks
As usual with CMake projects, it is possible to control whether to build as
static or shared library via the `BUILD_SHARED_LIBS` variable. If you build
qtforkawesome as part of another project and only want to affect qtforkawesome,
you can also use the variable `QT_FORK_AWESOME_BUILD_SHARED_LIBS`.

## Usage
The installation provides a CMake find module called `qtforkawesome` which
provides the imported target `qtforkawesome`. A pkg-config file is provided as
well. Once configured, the library is fairly simple to use:

```
#include <qtforkawesome/icon.h>
#include <qtforkawesome/renderer.h>

auto renderer = QtForkAwesome::Renderer();
renderer.pixmap(QtForkAwesome::Icon::Globe, QSize(64, 64), Qt::black);
```

There's also `renderer.render(…)` which takes a `QPainter` directly.

### Icon engine
A `QIconEnginePlugin` is provided as well. When it is loaded one can
create a `QIcon` using a file name with `.fa` extension, e.g.:

```
const auto icon = QIcon(QStringLiteral("qrcode:blue.fa"));
```

The color will be deduced from the applications default color palette unless it
is specified explicitly like in the example above.

To link against the plugin statically, find the CMake module
`qtforkawesomeiconengine`, link against the imported target
`qtforkawesomeiconengine` and add `Q_IMPORT_PLUGIN(ForkAwesomeIconEnginePlugin)`
to one of your source files.

### Bundling
It is also possible to build the library as part of your project. Simply add
it via `add_subdirectory`. Checkout the
[Syncthing Tray's project file](https://github.com/Martchus/syncthingtray/blob/master/CMakeLists.txt)
for an example.
