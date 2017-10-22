# Linux-Graphics-Full-Stack

Hi There, 

Here I am showing how to get and build the major Linux graphics software components. If you've wondered and wanted know more about modern graphics software stack, you've come to the right place. :) Along the way, hopefully I can throw some deep details on modern graphics technologies, which are normally hard to come by.  I use mostly Ubuntu so these are based on Ubuntu packages. Also the examples used here are mostly based on the bleeding edge versions, so please keep it in your mind so. I am using Intel Nuc with Kaby Lake for the hardware. Enjoy!



# Get the latese Ubuntu installed
Here is the Ubuntu link: https://www.ubuntu.com/download/desktop

Intel Kaby Lake is a full 64 bits machine, so I use the desktop 64 bits version.

# Install some packages to build Mesa3D and others
sudo apt-get install libxi-dev libxmu-dev x11proto-xf86vidmode-dev git-core autoconf automake libtool build-essential dh-autoreconf libpthread-stubs0-dev libpciaccess-dev xutils-dev libx11-dev x11proto-gl-dev x11proto-dri2-dev libxext-dev libxdamage-dev libxcb-xfixes0-dev libx11-xcb-dev libxcb-glx0-dev libexpat1-dev libdrm-nouveau1a flex llvm bison libxmu-dev

I want the Intel ANV Vulkan driver to be build in Mesa and it requires LLVM. We need to install CLANG and LLVM packages.

sudo apt-get install llvm-3.9 llvm-3.9-dev clang-3.9 clang-3.9-dev libxvmc-dev libxcb-xvmc0-dev libvdpau-dev libomxil-bellagio-dev

sudo ln -s /usr/bin/llvm-config-3.9 /usr/bin/llvm-config

# Build libdrm
You can install libdrm package and let MESA build to grep it, but I find it is a lot easier just to get the libdrm source and build before hand.

git clone git://anongit.freedesktop.org/git/mesa/drm
cd drm
./autogen.sh –prefix=/usr/local/xorg
make
sudo make install
export PKG_CONFIG_PATH=/usr/local/xorg/lib/pkgconfig:$PKG_CONFIG_PATH

Here I am re-directing the install directory to /usr/local/xorg.

# Build Mesa3D
git clone git://anongit.freedesktop.org/mesa/mesa
cd mesa
autoreconf -vfi

./configure --prefix=/usr/local/xorg --build=x86_64-linux-gnu  --includedir=\${prefix}/include --mandir=\${prefix}/share/man --infodir=\${prefix}/share/info --sysconfdir=/etc --localstatedir=/var --disable-silent-rules --libdir=\${prefix}/x86_64 --libexecdir=\${prefix}/x86_64 --disable-maintainer-mode --disable-dependency-tracking --enable-dri "--with-dri-drivers= i965" --with-dri-driverdir=/usr/lib/x86_64-linux-gnu/dri --with-dri-searchpath=/usr/lib/x86_64-linux-gnu/dri:\\\$\${ORIGIN}/dri:/usr/lib/dri "--with-vulkan-drivers= intel" --enable-osmesa --enable-glx-tls --enable-shared-glapi --enable-texture-float --disable-xvmc --enable-driglx-direct --enable-dri3 "--with-egl-platforms=x11 wayland drm" --enable-xa --enable-opencl --enable-opencl-icd --enable-gallium-llvm ac_cv_path_LLVM_CONFIG=llvm-config-3.9 --enable-vdpau --enable-va  --enable-gles1 --enable-gles2 "CFLAGS=-g -O2 -fdebug-prefix-map=/«PKGBUILDDIR»=. -fstack-protector-strong -Wformat -Werror=format-security -Wall" "CPPFLAGS=-Wdate-time -D_FORTIFY_SOURCE=2" "CXXFLAGS=-g -O2 -fdebug-prefix-map=/«PKGBUILDDIR»=. -fstack-protector-strong -Wformat -Werror=format-security -Wall" "FCFLAGS=-g -O2 -fdebug-prefix-map=/«PKGBUILDDIR»=. -fstack-protector-strong" "FFLAGS=-g -O2 -fdebug-prefix-map=/«PKGBUILDDIR»=. -fstack-protector-strong" "GCJFLAGS=-g -O2 -fdebug-prefix-map=/«PKGBUILDDIR»=. -fstack-protector-strong" LDFLAGS=-Wl,-z,relro "OBJCFLAGS=-g -O2 -fdebug-prefix-map=/«PKGBUILDDIR»=. -fstack-protector-strong -Wformat -Werror=format-security" "OBJCXXFLAGS=-g -O2 -fdebug-prefix-map=/«PKGBUILDDIR»=. -fstack-protector-strong -Wformat -Werror=format-security"

make

// WARNING! WARNING!
// This will configure your system to use the newly built Mesa libraries. There is a significant
// risk that your windows system can behave malfunctioning or even fail to come up.
make install

# Boot up!
Now re-boot the system and wait for the window system to come up. To verify it has installed the Mesa build correctly, you can grep the Mesa version and compare to your source tree version.

glxinfo | grep Mesa

The version info for the Mesa source tree is under $your_mesa_location/VERSION. 
