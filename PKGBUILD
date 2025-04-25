# Maintainer: Evangelos Foutras <foutrelis@archlinux.org>
# Contributor: Pierre Schmitz <pierre@archlinux.de>
# Contributor: Jan "heftig" Steffens <jan.steffens@gmail.com>
# Contributor: Daniel J Griffiths <ghost1227@archlinux.us>

pkgname=libcronet
pkgver=135.0.7049.95
pkgrel=2
_launcher_ver=8
_manual_clone=0
_system_clang=1
pkgdesc="Networking stack of Chromium put into a library"
arch=('x86_64')
url="https://www.chromium.org/Home"
license=('BSD-3-Clause')
depends=('nss' 'libgcrypt' 'libffi')
makedepends=('python' 'gn' 'ninja' 'clang' 'lld' 'gperf'
             'rust' 'rust-bindgen' 'git' 'ccache')
options=('!lto' '!strip') # Chromium adds its own flags for ThinLTO
source=(https://commondatastorage.googleapis.com/chromium-browser-official/chromium-$pkgver-lite.tar.xz
        webrtc-fix-build-with-pipewire-1.4.patch
        skia-only-call-format_message-when-needed.patch
        add-more-CFI-suppressions-for-inline-PipeWire-functions.patch
        compiler-rt-adjust-paths.patch
        increase-fortify-level.patch
        use-oauth2-client-switches-as-default.patch
        0001-cronet-Add-cert-net-fetcher.patch
        0002-cronet-Use-fixed-proxy-resolution-from-experimental-.patch
        0003-cronet-Support-setting-feature-list-from-experimenta.patch
        0004-net-grpc_support-Set-NetworkIsolationKey-from-header.patch
        0005-cronet-Support-setting-socket-limits-from-experiment.patch
        0006-cronet-fix-crash-we-have-no-command-line-arguments.patch
        0007-grpc_support-Fix-CFI-icall-breakage.patch
        0008-net-Allow-overriding-CONNECT-authority-with-header.patch
	0009-net-socket-Allow-higher-limits-for-proxies.patch
        0010-net-Allow-http-proxies-in-proxy-chains.patch
        0011-net-socket-Force-tunneling-for-all-sockets.patch
	0012-net-socket-Use-SO_REUSEPORT-for-server-sockets.patch)
sha256sums=('60a4c97498a8e6d4095931f5cd8821defabcfb67a891e8a0390eae631fce9f5f'
            '74a2d428f7f09132c4a923e816a5a9333803f842003d650cd4a95a35e5457253'
            '271c7a767005b09e212808cfef7261dca00ea28ba7b808f69c3b5b9f202511d1'
            'd3dd9b4132c9748b824f3dcf730ec998c0087438db902bc358b3c391658bebf5'
            'cc8a71a312e9314743c289b7b8fddcc80350a31445d335f726bb2e68edf916d1'
            'd634d2ce1fc63da7ac41f432b1e84c59b7cceabf19d510848a7cff40c8025342'
            'e6da901e4d0860058dc2f90c6bbcdc38a0cf4b0a69122000f62204f24fa7e374'
            'f6064f045155eeb4245493774daedb6f4605759e98752eb99785aea5fc3d2a77'
            '70389a490a2afce79491626fe6c2be5a5df2e4df5a13839e6c2550da4aeb4922'
            '38071d09226e689cb3721f3a269220d9cc630e4fb272aa3b861bbabf2680e541'
            '82744790cd10801b18d973ae0a8c4972c477202c834d031991cfdb561ff9e79b'
            'b659dc9c0e1782866c2c768c02a7e6f15dc1b6f9079add153dcd508a6753348a'
            '5c61b7ee2a51c0db12576c1e2313d09c5f5386685e84f61e22cd28ad7e689dd9'
            'ef82bce034a8cbd19887754b612d6a972f1cbb59663a1ef1730df16046efbc71'
            'fba00739e62852b5e7d71818675aa81318180f600f315afa6119c40841afd0f3'
            'b994d868c2e0578f0ae2871bee2627cc921b468fc9ab7bfd34e7587854a580e1'
            '65097b915baee3c040bf7ee9ca369840bb4388c478b8468e46f4052ebe79baa0'
            'acbc8b8398346b860789b150254913ec6beeedaceb0f094d867236138f9f8e21'
            'e120d16eb3404f116c029c47910d9f5bf50651a4f2cdee9cbc0b082fa27fc215')

if (( _manual_clone )); then
  source[0]=fetch-chromium-release
  makedepends+=('python-httplib2' 'python-pyparsing' 'python-six' 'npm' 'rsync')
fi

# Possible replacements are listed in build/linux/unbundle/replace_gn_files.py
# Keys are the names in the above script; values are the dependencies in Arch
declare -gA _system_libs=(
  #[brotli]=brotli
  #[dav1d]=dav1d
  #[ffmpeg]=ffmpeg    # YouTube playback stopped working in Chromium 120
  [flac]=flac
  [fontconfig]=fontconfig
  [freetype]=freetype2
  [harfbuzz-ng]=harfbuzz
  #[icu]=icu
  #[jsoncpp]=jsoncpp  # needs libstdc++
  #[libaom]=aom
  #[libavif]=libavif  # needs -DAVIF_ENABLE_EXPERIMENTAL_GAIN_MAP=ON
  [libjpeg]=libjpeg-turbo
  [libpng]=libpng
  #[libvpx]=libvpx
  [libwebp]=libwebp
  [libxml]=libxml2
  [libxslt]=libxslt
  [opus]=opus
  #[re2]=re2          # needs libstdc++
  #[snappy]=snappy    # needs libstdc++
  #[woff2]=woff2      # needs libstdc++
  #[zlib]=minizip
)
_unwanted_bundled_libs=(
  $(printf "%s\n" ${!_system_libs[@]} | sed 's/^libjpeg$/&_turbo/')
)
depends+=(${_system_libs[@]})

# Google API keys (see https://www.chromium.org/developers/how-tos/api-keys)
# Note: These are for Arch Linux use ONLY. For your own distribution, please
# get your own set of keys.
#
# Starting with Chromium 89 (2021-03-02) the OAuth2 credentials have been left
# out: https://archlinux.org/news/chromium-losing-sync-support-in-early-march/
_google_api_key=AIzaSyDwr302FpOSkGRpLlUpPThNTDPbXcIn_FM

prepare() {
  if (( _manual_clone )); then
    ./fetch-chromium-release $pkgver
  fi
  cd chromium-$pkgver

  # Allow building against system libraries in official builds
  sed -i 's/OFFICIAL_BUILD/GOOGLE_CHROME_BUILD/' \
    tools/generate_shim_headers/generate_shim_headers.py

  # https://crbug.com/893950
  sed -i -e 's/\<xmlMalloc\>/malloc/' -e 's/\<xmlFree\>/free/' \
         -e '1i #include <cstdlib>' \
    third_party/blink/renderer/core/xml/*.cc \
    third_party/blink/renderer/core/xml/parser/xml_document_parser.cc \
    third_party/libxml/chromium/*.cc

  patch -Np1 -i ../0001-cronet-Add-cert-net-fetcher.patch
  patch -Np1 -i ../0002-cronet-Use-fixed-proxy-resolution-from-experimental-.patch
  patch -Np1 -i ../0003-cronet-Support-setting-feature-list-from-experimenta.patch
  patch -Np1 -i ../0004-net-grpc_support-Set-NetworkIsolationKey-from-header.patch
  patch -Np1 -i ../0005-cronet-Support-setting-socket-limits-from-experiment.patch
  patch -Np1 -i ../0006-cronet-fix-crash-we-have-no-command-line-arguments.patch
  patch -Np1 -i ../0007-grpc_support-Fix-CFI-icall-breakage.patch
  patch -Np1 -i ../0008-net-Allow-overriding-CONNECT-authority-with-header.patch
  patch -Np1 -i ../0010-net-Allow-http-proxies-in-proxy-chains.patch
  #patch -Np1 -i ../0011-net-socket-Force-tunneling-for-all-sockets.patch
  patch -Np1 -i ../0012-net-socket-Use-SO_REUSEPORT-for-server-sockets.patch


  # Use the --oauth2-client-id= and --oauth2-client-secret= switches for
  # setting GOOGLE_DEFAULT_CLIENT_ID and GOOGLE_DEFAULT_CLIENT_SECRET at
  # runtime -- this allows signing into Chromium without baked-in values
  patch -Np1 -i ../use-oauth2-client-switches-as-default.patch

  # Upstream fixes
  patch -Np1 -d third_party/webrtc <../webrtc-fix-build-with-pipewire-1.4.patch
  patch -Np1 -d third_party/skia <../skia-only-call-format_message-when-needed.patch
  patch -Np1 -i ../add-more-CFI-suppressions-for-inline-PipeWire-functions.patch

  # Allow libclang_rt.builtins from compiler-rt >= 16 to be used
  patch -Np1 -i ../compiler-rt-adjust-paths.patch

  # Increase _FORTIFY_SOURCE level to match Arch's default flags
  patch -Np1 -i ../increase-fortify-level.patch

  # Fixes for building with libstdc++ instead of libc++

  # Link to system tools required by the build
  mkdir third_party/node/linux/node-linux-x64/bin
  ln -s /usr/bin/node third_party/node/linux/node-linux-x64/bin/
  ln -s /usr/bin/java third_party/jdk/current/bin/

  if (( !_system_clang )); then
    # Use prebuilt rust as system rust cannot be used due to the error:
    #   error: the option `Z` is only accepted on the nightly compiler
    ./tools/rust/update_rust.py

    # To link to rust libraries we need to compile with prebuilt clang
    ./tools/clang/scripts/update.py
  fi

  # Remove bundled libraries for which we will use the system copies; this
  # *should* do what the remove_bundled_libraries.py script does, with the
  # added benefit of not having to list all the remaining libraries
  local _lib
  for _lib in ${_unwanted_bundled_libs[@]}; do
    find "third_party/$_lib" -type f \
      \! -path "third_party/$_lib/chromium/*" \
      \! -path "third_party/$_lib/google/*" \
      \! -path "third_party/harfbuzz-ng/utils/hb_scoped.h" \
      \! -regex '.*\.\(gn\|gni\|isolate\)' \
      -delete
  done

  ./build/linux/unbundle/replace_gn_files.py \
    --system-libraries "${!_system_libs[@]}"
}

build() {
  cd chromium-$pkgver

  export CCACHE_SLOPPINESS=time_macros
  export CCACHE_BASEDIR="$PWD"
  export CCACHE_CPP2=yes
  CCACHE=ccache

  if (( _system_clang )); then
    export CC=clang
    export CXX=clang++
    export AR=ar
    export NM=nm
  else
    local _clang_path="$PWD/third_party/llvm-build/Release+Asserts/bin"
    export CC=$_clang_path/clang
    export CXX=$_clang_path/clang++
    export AR=$_clang_path/llvm-ar
    export NM=$_clang_path/llvm-nm
  fi

  local _flags=(
    "cc_wrapper=\"$CCACHE\""
    'custom_toolchain="//build/toolchain/linux/unbundle:default"'
    'host_toolchain="//build/toolchain/linux/unbundle:default"'
    'is_official_build=true' # implies is_cfi=true on x86_64
    'symbol_level=0' # sufficient for backtraces on x86(_64)
    'treat_warnings_as_errors=false'
    'disable_fieldtrial_testing_config=true'
    'blink_enable_generated_code_formatting=false'
    'ffmpeg_branding="Chrome"'
    'proprietary_codecs=false'
    'rtc_use_pipewire=false'
    'link_pulseaudio=false'
    'use_custom_libcxx=true' # https://github.com/llvm/llvm-project/issues/61705
    'use_sysroot=false'
    'use_system_libffi=true'
    'enable_hangout_services_extension=false'
    'enable_widevine=false'
    'enable_nacl=false'
    'use_qt5=false'
    'use_qt6=false'
    "google_api_key=\"$_google_api_key\""

    'use_udev=false'

    'disable_file_support=true'
    'enable_websockets=false'
    'use_kerberos=false'
    'disable_zstd_filter=false'
    'enable_mdns=false'
    'enable_reporting=false'
    'include_transport_security_state_preload_list=false'
    'enable_device_bound_sessions=false'
    'enable_bracketed_proxy_uris=true'
    'enable_quic_proxy_support=true'

    'enable_backup_ref_ptr_support=false'
    'enable_dangling_raw_ptr_checks=false'
    'enable_shadow_metadata=false'
    'disable_histogram_support=true'
  )

  if [[ -n ${_system_libs[icu]+set} ]]; then
    _flags+=('icu_use_data_file=false')
  fi

  if (( _system_clang )); then
     local _clang_version=$(
       clang --version | grep -m1 version | sed 's/.* \([0-9]\+\).*/\1/')

    _flags+=(
      'clang_base_path="/usr"'
      'clang_use_chrome_plugins=false'
      "clang_version=\"$_clang_version\""
      #'chrome_pgo_phase=0' # needs newer clang to read the bundled PGO profile
    )

    # Allow the use of nightly features with stable Rust compiler
    # https://github.com/ungoogled-software/ungoogled-chromium/pull/2696#issuecomment-1918173198
    export RUSTC_BOOTSTRAP=1

    _flags+=(
      'rust_sysroot_absolute="/usr"'
      'rust_bindgen_root="/usr"'
      "rustc_version=\"$(rustc --version)\""
    )
  fi

  # Facilitate deterministic builds (taken from build/config/compiler/BUILD.gn)
  CFLAGS+='   -Wno-builtin-macro-redefined'
  CXXFLAGS+=' -Wno-builtin-macro-redefined'
  CPPFLAGS+=' -D__DATE__=  -D__TIME__=  -D__TIMESTAMP__='

  # Do not warn about unknown warning options
  CFLAGS+='   -Wno-unknown-warning-option'
  CXXFLAGS+=' -Wno-unknown-warning-option'

  # Let Chromium set its own symbol level
  CFLAGS=${CFLAGS/-g }
  CXXFLAGS=${CXXFLAGS/-g }

  # https://github.com/ungoogled-software/ungoogled-chromium-archlinux/issues/123
  CFLAGS=${CFLAGS/-fexceptions}
  CFLAGS=${CFLAGS/-fcf-protection}
  CXXFLAGS=${CXXFLAGS/-fexceptions}
  CXXFLAGS=${CXXFLAGS/-fcf-protection}

  # This appears to cause random segfaults when combined with ThinLTO
  # https://bugs.archlinux.org/task/73518
  CFLAGS=${CFLAGS/-fstack-clash-protection}
  CXXFLAGS=${CXXFLAGS/-fstack-clash-protection}

  # https://crbug.com/957519#c122
  CXXFLAGS=${CXXFLAGS/-Wp,-D_GLIBCXX_ASSERTIONS}

  gn gen out/Release --args="${_flags[*]}"
  ninja -C out/Release cronet_package 
}

package() {
  cd chromium-$pkgver
  install -Dm755 "out/Release/cronet/libcronet.$pkgver.so" \
    "$pkgdir/usr/lib/libcronet.$pkgver.so"
  ln -s "libcronet.$pkgver.so" "$pkgdir/usr/lib/libcronet.so"

  install -d "$pkgdir/usr/include/cronet"
  install -m644 out/Release/cronet/include/*.h \
    "$pkgdir/usr/include/cronet/"
}
