# Maintainer: Andrzej Giniewicz <gginiu@gmail.com>
# Contributor: Thomas Dziedzic < gostrc at gmail >
# Contributor: Christofer Bertonha <christoferbertonha at gmail dot com>
# Contributor: leepesjee <lpeschier at xs4all dot nl>
# Contributor: Olivier Medoc
# Contributor: ignotus
# Contributor: Fabian Moser
# Contributor: djscholl

pkgname=vtk
pkgver=5.10.0
pkgrel=2
pkgdesc='A software system for 3D computer graphics, image processing, and visualization which supports a wide variety of visualization algorithms and advanced modeling techniques.'
arch=('i686' 'x86_64')
url='http://www.vtk.org'
license=('BSD')
depends=('libjpeg-turbo' 'boost' 'ffmpeg' 'qt' 'qtwebkit' 'lesstif' 'mysql' 'hdf5')
makedepends=('cmake' 'java-environment' 'doxygen' 'gnuplot' 'mesa' 'python2' 'tk')
optdepends=('python2: python bindings'
            'java-runtime: java bindings'
            'tk: tcl bindings'
            'gnuplot: plotting tools'
            'graphviz: drawing tools')
source=("http://www.vtk.org/files/release/${pkgver:0:4}/vtk-${pkgver}.tar.gz"
        "http://www.vtk.org/files/release/${pkgver:0:4}/vtkdata-${pkgver}.tar.gz"
        ffmpeg-0.8.diff ffmpeg-0.11.diff)
md5sums=('a0363f78910f466ba8f1bd5ab5437cb9'
         '8c4105b221384154421e7a460adc9c0c'
         'e992321c9d1def6034d0fa6607b40c5a'
         '81922ea61989e5591135f233a0400694')

build() {
  cd "${srcdir}"/VTK

  # fix compilation error:
  # remove next pkgver bump
  patch -Np1 -i "${srcdir}/ffmpeg-0.8.diff"
  patch -Np1 -i "${srcdir}/ffmpeg-0.11.diff"

  cd "${srcdir}"

  rm -rf build
  mkdir build
  cd build

  # flags to enable using system libs
  local cmake_system_flags=""
  for lib in HDF5 EXPAT FREETYPE JPEG PNG TIFF ZLIB LIBXML2 OGGTHEORA; do # LIBPROJ4
    cmake_system_flags+="-DVTK_USE_SYSTEM_${lib}:BOOL=ON "
  done

  # flags to use python2 instead of python which is 3.x.x on archlinux
  local cmake_system_python_flags="-DPYTHON_EXECUTABLE:PATH=/usr/bin/python2 -DPYTHON_INCLUDE_DIR:PATH=/usr/include/python2.7 -DPYTHON_LIBRARY:PATH=/usr/lib/libpython2.7.so"

  cmake \
    -Wno-dev \
    -DBUILD_SHARED_LIBS:BOOL=ON \
    -DCMAKE_INSTALL_PREFIX:FILEPATH=/usr \
    -DVTK_DATA_ROOT:FILEPATH=${srcdir}/VTKData \
    -DBUILD_DOCUMENTATION:BOOL=ON \
    -DDOCUMENTATION_HTML_HELP:BOOL=ON \
    -DDOCUMENTATION_HTML_TARZ:BOOL=ON \
    -DBUILD_EXAMPLES:BOOL=ON \
    -DVTK_USE_HYBRID:BOOL=ON \
    -DVTK_USE_PARALLEL:BOOL=ON \
    -DVTK_USE_PATENTED:BOOL=ON \
    -DVTK_USE_BOOST:BOOL=ON \
    -DVTK_USE_INFOVIS:BOOL=ON \
    -DVTK_USE_GL2PS:BOOL=ON \
    -DVTK_USE_MYSQL:BOOL=ON \
    -DVTK_USE_FFMPEG_ENCODER:BOOL=ON \
    -DVTK_USE_TEXT_ANALYSIS:BOOL=ON \
    -DVTK_WRAP_JAVA:BOOL=ON \
    -DVTK_WRAP_PYTHON:BOOL=ON \
    -DVTK_WRAP_TCL:BOOL=ON \
    -DVTK_PYTHON_SETUP_ARGS:STRING="--root='${pkgdir}'" \
    -DVTK_USE_QT:BOOL=ON \
    -DVTK_INSTALL_QT_PLUGIN_DIR:STRING="/usr/lib/qt/plugins" \
    -DVTK_USE_GUISUPPORT:BOOL=ON \
    -DCMAKE_CXX_FLAGS="-D__STDC_CONSTANT_MACROS" \
    ${cmake_system_flags} \
    ${cmake_system_python_flags} \
    "${srcdir}/VTK"

  make
}

package() {
  cd "${srcdir}/build"

  make DESTDIR="${pkgdir}" install

  # Move the vtk.jar to the arch-specific location
  install -dv "${pkgdir}/usr/share/java/vtk"
  mv -v "${pkgdir}/usr/lib/vtk-${pkgver:0:4}/java/vtk.jar" \
    "${pkgdir}/usr/share/java/vtk"
  rm -rf "${pkgdir}/usr/lib/vtk-${pkgver:0:4}/java"

  # Install license
  install -dv "${pkgdir}/usr/share/licenses/vtk"
  install -m644 "${srcdir}/VTK/Copyright.txt" \
    "${pkgdir}/usr/share/licenses/vtk"

  # Put an entry in /etc/ld.so.conf.d
  install -dv "${pkgdir}/etc/ld.so.conf.d"
  echo "/usr/lib/vtk-${pkgver:0:4}" > "${pkgdir}/etc/ld.so.conf.d/vtk.conf"

  sed -i -e "s|#![ ]*/usr/bin/env python$|#!/usr/bin/env python2|" \
    $(find "${pkgdir}" -name '*.py')
}

