PORTNAME=	sunshine
DISTVERSIONPREFIX=	v
DISTVERSION=	2026.906.222525
CATEGORIES=	multimedia

MAINTAINER=	freebsd@mail.indolore.net
COMMENT=	Self-hosted game/desktop stream host for Moonlight
WWW=	https://github.com/LizardByte/Sunshine

LICENSE=	GPLv3
LICENSE_FILE=	${WRKSRC}/LICENSE

BUILD_DEPENDS=	clang19:devel/llvm19 \
	git:devel/git \
	bash:shells/bash \
	ninja:devel/ninja \
	ccache:devel/ccache \
	uv:devel/uv \
	npm:www/npm-node22 \
	${LOCALBASE}/include/boost/version.hpp:devel/boost-all \
	glslc:graphics/shaderc \
	${LOCALBASE}/include/vulkan/vulkan.h:graphics/vulkan-headers \
	cmake:devel/cmake \
	${LOCALBASE}/lib/libass.so:multimedia/libass \
	${LOCALBASE}/lib/libv4l2.so:multimedia/libv4l \
	${LOCALBASE}/include/linux/videodev2.h:multimedia/v4l_compat \
	${LOCALBASE}/lib/libfreetype.so:print/freetype2 \
	${LOCALBASE}/lib/libgnutls.so:security/gnutls \
	${LOCALBASE}/share/cmake/nlohmann_json/nlohmann_jsonConfig.cmake:devel/nlohmann-json \
	${LOCALBASE}/include/ffnvcodec/nvEncodeAPI.h:multimedia/ffnvcodec-headers
LIB_DEPENDS=	libopus.so:audio/opus \
	libevdev.so:devel/libevdev \
	libcurl.so:ftp/curl \
	libdrm.so:graphics/libdrm \
	libgbm.so:graphics/mesa-libs \
	libpulse.so:audio/pulseaudio \
	libva.so:multimedia/libva \
	libpipewire-0.3.so:multimedia/pipewire \
	libminiupnpc.so:net/miniupnpc \
	libssl.so:security/openssl \
	libwayland-client.so:graphics/wayland \
	libvulkan.so:graphics/vulkan-loader \
	libX11.so:x11/libX11 \
	libxcb.so:x11/libxcb \
	libXfixes.so:x11/libXfixes \
	libXrandr.so:x11/libXrandr \
	libXtst.so:x11/libXtst
RUN_DEPENDS=	pulseaudio:audio/pulseaudio \
	pipewire:multimedia/pipewire \
	bash:shells/bash \
	Xorg:x11-servers/xorg-server

USES=	cmake compiler:c++20-lang pkgconfig python:3.14+,build \
	nodejs:22,build qt:6 shebangfix
USE_QT=	base svg
USE_GITHUB=	yes
GH_ACCOUNT=	LizardByte
GH_PROJECT=	Sunshine
# Sunshine's own git submodules are not fetched by a GitHub tarball checkout,
# so each is pulled in explicitly, pinned to the commit this DISTVERSION's
# .gitmodules points at. third-party/build-deps is intentionally omitted:
# ffmpeg.cmake only reads it when FFMPEG_PREPARED_BINARIES is undefined, and
# we always set it. packaging/linux/flatpak/deps/flatpak-builder-tools is
# omitted too: it is only used for flatpak packaging, which this port does
# not build.
GH_TUPLE=	Dav1dde:glad:73db193f853e2ee079bf3ca8a64aa2eaf6459043:glad/third-party/glad \
	LizardByte:libdisplaydevice:6e9722f89103320c948dc1199066c9e17a69e88a:libdisplaydevice/third-party/libdisplaydevice \
	LizardByte:tray:c329d9fd0d39dfb47f0f2fb5467e1db2e8a1d623:tray/third-party/tray \
	LizardByte:doxyconfig:b87f7e4e1dcd8a31c9a550cd7702b7420d89efac:doxyconfig/third-party/doxyconfig \
	LizardByte:libvirtualhid:6fdb8bd4de3b68d96c30e5303ac2ebb333c09746:libvirtualhid/third-party/libvirtualhid \
	LizardByte:lizardbyte-common:f9d91e1d29b7473f58e43acde4579da4e56c4abe:lizardbytecommon/third-party/lizardbyte-common \
	LizardByte:Virtual-Gamepad-Emulation-Client:8d71f6740ffff4671cdadbca255ce528e3cd3fef:vigemclient/third-party/ViGEmClient \
	moonlight-stream:moonlight-common-c:874ac9548f1bd6f095ef2b435c42cdde460e7821:moonlightcommonc/third-party/moonlight-common-c \
	cgutman:enet:aca87840b57f045a1f7f9299e4b1b9b8e2a5e2f1:enet/third-party/moonlight-common-c/enet \
	sleepybishop:nanors:b1e3c22ca0cdc0bb83e3cd6ed1a2fc77869ed99a:nanors/third-party/moonlight-common-c/nanors \
	NVIDIA:nvapi:cd6918f60b3c9a0476fdfe7e89bb32330602049d:nvapi/third-party/nvapi \
	KDE:plasma-wayland-protocols:4c015e90ae6c88f2ffa766e899387ef431eade49:plasmawaylandprotocols/third-party/plasma-wayland-protocols \
	LizardByte-infrastructure:Simple-Web-Server:546895a93a29062bb178367b46c7afb72da9881e:simplewebserver/third-party/Simple-Web-Server \
	michaeltyson:TPCircularBuffer:cc520397504bb72bc6df79ff03eb72988a6dc50d:tpcircularbuffer/third-party/TPCircularBuffer \
	LizardByte-infrastructure:wayland-protocols:ee78491a237eaff9389a0ccf8680521d074407d3:waylandprotocols/third-party/wayland-protocols \
	LizardByte-infrastructure:wlr-protocols:bf4fc79abc359eea5a0edec0ac6d4a2b2955f82a:wlrprotocols/third-party/wlr-protocols

CC=	clang19
CXX=	clang++19

BUILD_DEPS_DIR=	${WRKDIR}/build-deps
BUILD_DEPS_DIST=	${BUILD_DEPS_DIR}/build/dist

# jinja2/markupsafe are installed into WRKDIR (not system site-packages, which
# poudriere's build flags as a filesystem violation) and exposed to whichever
# phase actually invokes the code-generation python scripts via PYTHONPATH.
CONFIGURE_ENV+=	PYTHONPATH=${WRKDIR}/pylibs
MAKE_ENV+=	PYTHONPATH=${WRKDIR}/pylibs

# ccache defaults to $HOME/.ccache, which is unwritable when the builder user
# is nobody (HOME=/nonexistent). Point it at WRKDIR instead of relying on a
# builder-host-specific CCACHE_DIR in poudriere.conf.
CONFIGURE_ENV+=	CCACHE_DIR=${WRKDIR}/.ccache
MAKE_ENV+=	CCACHE_DIR=${WRKDIR}/.ccache

CMAKE_ARGS+=	-DBUILD_DOCS=OFF \
	-DBUILD_TESTS=OFF \
	-DBUILD_WERROR=OFF \
	-DSUNSHINE_ASSETS_DIR=share/assets \
	-DSUNSHINE_EXECUTABLE_PATH=${PREFIX}/bin/sunshine \
	-DSUNSHINE_ENABLE_CUDA=OFF \
	-DSUNSHINE_ENABLE_DRM=OFF \
	-DSUNSHINE_ENABLE_KWIN=ON \
	-DSUNSHINE_ENABLE_PORTAL=ON \
	-DSUNSHINE_ENABLE_WAYLAND=ON \
	-DSUNSHINE_ENABLE_X11=ON \
	-DSUNSHINE_SYSTEM_VULKAN_HEADERS=ON \
	-DCMAKE_C_COMPILER_LAUNCHER=ccache \
	-DCMAKE_CXX_COMPILER_LAUNCHER=ccache \
	-DFFMPEG_PREPARED_BINARIES=${BUILD_DEPS_DIST}/ffmpeg

# Sunshine pins an exact Boost version for find_package(Boost CONFIG EXACT);
# ports' boost-all/boost-libs is 1.91.0, not the 1.89.0 upstream pins, so
# the exact-match lookup fails and falls back to a network FetchContent
# that poudriere's build disallows. Point it at the version we actually have.
post-patch:
	@${REINPLACE_CMD} -e 's|set(BOOST_VERSION "1.89.0")|set(BOOST_VERSION "1.91.0")|' ${WRKSRC}/cmake/dependencies/Boost_Sunshine.cmake
	@${REINPLACE_CMD} -e 's|"$${NPM}" ci $${NPM_INSTALL_FLAGS}|"$${NPM}" install $${NPM_INSTALL_FLAGS} \&\& "$${NPM}" install --no-save @rolldown/binding-wasm32-wasi@1.2.6 esbuild|' ${WRKSRC}/cmake/targets/common.cmake
	@${REINPLACE_CMD} -e 's|outDir: resolve(assetsDstPath),|outDir: resolve(assetsDstPath), cssMinify: "esbuild",|' ${WRKSRC}/vite.config.js

pre-configure:
	@${MKDIR} ${WRKDIR}/pydeps
	@fetch -o ${WRKDIR}/pydeps/jinja2-3.1.6-py3-none-any.whl https://files.pythonhosted.org/packages/62/a1/3d680cbfd5f4b8f15abc1d571870c5fc3e594bb582bc3b64ea099db13e56/jinja2-3.1.6-py3-none-any.whl
	@fetch -o ${WRKDIR}/pydeps/markupsafe-3.0.3.tar.gz https://files.pythonhosted.org/packages/7e/99/7690b6d4034fffd95959cbe0c02de8deb3098cc577c67bb6a24fe5d7caa7/markupsafe-3.0.3.tar.gz
	@fetch -o ${WRKDIR}/pydeps/setuptools-84.0.0-py3-none-any.whl https://files.pythonhosted.org/packages/95/9c/c510029fc6ef33a6275cd2c5d3cecd6613dfd6aa401d57c54f1c18852ccf/setuptools-84.0.0-py3-none-any.whl
	@${SETENV} UV_CACHE_DIR=${WRKDIR}/.uv-cache uv pip install --python ${LOCALBASE}/bin/python3.14 --target ${WRKDIR}/pylibs --no-index --find-links ${WRKDIR}/pydeps ${WRKDIR}/pydeps/jinja2-3.1.6-py3-none-any.whl ${WRKDIR}/pydeps/markupsafe-3.0.3.tar.gz
	@${SETENV} PYTHONPATH=${WRKDIR}/pylibs ${LOCALBASE}/bin/python3.14 -c "import jinja2" || (echo "jinja2 still not importable" && exit 1)
	# EXPERIMENT ONLY (not for the real port/ports-tree submission): fetch
	# the last build-deps release that still published a FreeBSD/aarch64
	# prebuilt archive (v2026.724.203728, 2026-07-25 -- build-deps#757
	# dropped aarch64 after this) instead of compiling FFmpeg/x264/x265/
	# SvtAv1Enc from source. Isolates poudriere's own mechanical overhead
	# (jail/ports-tree setup, Sunshine's own compile+package) from the
	# separate, already-understood cost of building FFmpeg itself from
	# source, for both architectures.
	@${MKDIR} ${BUILD_DEPS_DIST}
	@fetch -o ${WRKDIR}/build-deps-ffmpeg.tar.gz https://github.com/LizardByte/build-deps/releases/download/v2026.724.203728/FreeBSD-${ARCH}-ffmpeg.tar.gz
	@tar -xzf ${WRKDIR}/build-deps-ffmpeg.tar.gz -C ${BUILD_DEPS_DIST}

.include <bsd.port.mk>
