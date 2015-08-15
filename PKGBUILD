# Maintainer: Nathan Hulse <nat.hulse@gmail.com>

pkgname=compiz-core-dev
pkgseries=0.9.7
pkgver=${pkgseries}.8  
pkgrel=2
pkgdesc="The core package from the last milestone to be marked as stable in the development branch."
url="https://launchpad.net/compiz-core"
arch=('i686' 'x86_64')
license=('GPL' 'LGPL' 'MIT')
depends=('boost' 'xorg-server' 'libxcomposite' 'startup-notification' 'librsvg' 'dbus' 'mesa' 'libxslt' 'fuse' 'glibmm' 'libxrender')
optdepends=(
  'ccsm-dev: CompizConfig Settings Manager'
  'compiz-plugins-main-dev: Main plugins'
  'compiz-plugins-extra-dev: Extra plugins'
  'dconf: for GSettings backend support'
  'gtk2: for GTK window decorator (then rebuild this package)'
  'metacity: Metacity window decoration themes support (then rebuild this package)'
  'gnome-control-center: GNOME keybindings support (then rebuild this package)'
  'kdebase-lib: KDE window decoration support (then rebuild this package)' 
  'automoc: KDE window decoration support (then rebuild this package)'
)
makedepends=('intltool' 'cmake')
provides=('compiz-core')
conflicts=('compiz-core')
groups=('compiz-dev')
install='compiz-core-dev.install'
source=(
  "https://launchpad.net/compiz-core/${pkgseries}/${pkgver}/+download/compiz-${pkgver}.tar.bz2"
)
md5sums=(
  'bf9225ba781e534b9f79193318c5c3f2'
)

# GTK window decorator support
GTKWINDOWDECORATOR="on"
# Metaciy theme support for GTK window decorator
METACITY="on"
# KDE window decorator support
KDEWINDOWDECORATOR="on"
# GConf backend support
GCONF="on"
# GSettings backend support
GSETTINGS="on"

build() {
  cd "compiz-${pkgver}"  
  [[ -d build ]] || mkdir build
  cd build
  cmake .. \
    -DCMAKE_INSTALL_PREFIX="/usr" \
    -DCMAKE_BUILD_TYPE="Release" \
    -DCOMPIZ_DISABLE_SCHEMAS_INSTALL=On \
    -DCOMPIZ_BUILD_WITH_RPATH=FALSE \
    -DBUILD_GTK="${GTKWINDOWDECORATOR}" \
    -DBUILD_METACITY="${METACITY}" \
    -DBUILD_KDE4="${KDEWINDOWDECORATOR}" \
    -DUSE_GCONF="${GCONF}" \
    -DUSE_GSETTINGS="${GSETTINGS}" \
    -DCOMPIZ_BUILD_TESTING=Off \
    -DCOMPIZ_DEFAULT_PLUGINS="composite,opengl,decor,resize,place,move" \
    -DCOMPIZ_DISABLE_PLUGIN_FADE=Off \
    -DCOMPIZ_DISABLE_PLUGIN_SCREENSHOT=Off \
    -DCOMPIZ_DISABLE_PLUGIN_OBS=Off \
    -DCOMPIZ_DISABLE_PLUGIN_DECOR=Off \
    -DCOMPIZ_DISABLE_PLUGIN_SCALE=Off \
    -DCOMPIZ_DISABLE_PLUGIN_RESIZE=Off \
    -DCOMPIZ_DISABLE_PLUGIN_CLONE=Off \
    -DCOMPIZ_DISABLE_PLUGIN_WATER=Off \
    -DCOMPIZ_DISABLE_PLUGIN_ANNOTATE=Off \
    -DCOMPIZ_DISABLE_PLUGIN_COMMANDS=Off \
    -DCOMPIZ_DISABLE_PLUGIN_BLUR=Off \
    -DCOMPIZ_DISABLE_PLUGIN_CUBE=Off \
    -DCOMPIZ_DISABLE_PLUGIN_COMPOSITE=Off \
    -DCOMPIZ_DISABLE_PLUGIN_COPYTEX=Off \
    -DCOMPIZ_DISABLE_PLUGIN_GNOMECOMPAT=Off \
    -DCOMPIZ_DISABLE_PLUGIN_OPENGL=Off \
    -DCOMPIZ_DISABLE_PLUGIN_KDE=Off \
    -DCOMPIZ_DISABLE_PLUGIN_REGEX=Off \
    -DCOMPIZ_DISABLE_PLUGIN_COMPIZTOOLBOX=Off \
    -DCOMPIZ_DISABLE_PLUGIN_SWITCHER=Off \
    -DCOMPIZ_DISABLE_PLUGIN_INOTIFY=Off \
    -DCOMPIZ_DISABLE_PLUGIN_ROTATE=Off \
    -DCOMPIZ_DISABLE_PLUGIN_PLACE=Off \
    -DCOMPIZ_DISABLE_PLUGIN_MOVE=Off \
    -DCOMPIZ_DISABLE_PLUGIN_WOBBLY=Off \
    -DCOMPIZ_DISABLE_PLUGIN_IMGPNG=Off \
    -DCOMPIZ_DISABLE_PLUGIN_DBUS=Off \
    -DCOMPIZ_DISABLE_PLUGIN_IMGSVG=Off 
  make
}

package() {
  cd "compiz-${pkgver}/build"
  make DESTDIR="${pkgdir}" install
  
  # Stupid findcompiz_install needs COMPIZ_DESTDIR and install needs DESTDIR
  #make findcompiz_install
  CMAKE_DIR=$(cmake --system-information | grep '^CMAKE_ROOT' | awk -F\" '{print $2}')
  install -dm755 "${pkgdir}${CMAKE_DIR}/Modules/"
  install -m644 ../cmake/FindCompiz.cmake "${pkgdir}${CMAKE_DIR}/Modules/"
  
  # Install documentation
  install -dm755 "${pkgdir}/usr/share/doc/compiz-core/"
  install ../{AUTHORS,NEWS,README,TODO} "${pkgdir}/usr/share/doc/compiz-core/"

  # Amend XDG .desktop file to load the compizconfig plugin with compiz
  sed -i 's/Exec\=compiz/Exec\=compiz ccp/' "${pkgdir}/usr/share/applications/compiz.desktop"

  # Merge the gconf schema files
  if [ -d "${pkgdir}/usr/share/gconf/schemas" ]; then    
    gconf-merge-schema "${pkgdir}/usr/share/gconf/schemas/compiz-core.schemas.in" "{$pkgdir}"/usr/share/gconf/schemas/*.schemas
    sed -i '/<schemalist\/>/d' "${pkgdir}/usr/share/gconf/schemas/compiz-core.schemas.in"
    rm -f "${pkgdir}"/usr/share/gconf/schemas/*.schemas
    mv "${pkgdir}/usr/share/gconf/schemas/compiz-core.schemas.in" "${pkgdir}/usr/share/gconf/schemas/compiz-core.schemas"
  fi

  # Add the pesky gsettings schema files manually
  ls generated | grep -qm1 .gschema.xml
  if [ $? -eq 0 ]; then
    install -dm755 "${pkgdir}/usr/share/glib-2.0/schemas/" 
    install -m644 generated/*.gschema.xml "${pkgdir}/usr/share/glib-2.0/schemas/" 
  fi
}
