# Pemaketan Dasar Debian

## Persiapan

Pasang perkakas-perkakas pendukung,
```
sudo apt install debmake build-essential devscripts quilt
```
## Bongkar tarball

```
tar -xvf debhello-0.0.tar.gz
```


```
tree
.
├── README.md
├── debhello-0.0
│   ├── Makefile
│   └── src
│       └── hello.c
└── debhello-0.0.tar.gz
```

## Buat templat paket Debian secara otomatis

Masuk ke direktori tarball yang sudah dibongkar lalu jalankan `debmake`.
```
cd debhello-0.0
debmake
```

Keluaran,
```
I: set parameters
I: =================================================================
I: package_dir     = /usr/lib/python3/dist-packages
I: base_path       = /usr
I: base_lib_path   = /usr/lib/debmake
I: base_share_path = /usr/share/debmake
I: =================================================================
I: sanity check of parameters
I: pkg="debhello", ver="0.0", rev="1"
I: *** start packaging in "debhello-0.0". ***
I: provide debhello_0.0.orig.tar.gz for non-native Debian package
I: pwd = "/home/user/src/pemaketan-dasar-debian"
I: $ ln -sf debhello-0.0.tar.gz debhello_0.0.orig.tar.gz
I: pwd = "/home/user/src/pemaketan-dasar-debian/debhello-0.0"
I: parse binary package settings:
I: binary package=debhello Type=bin / Arch=any M-A=foreign
I: analyze the source tree
I: build_type = make
I: scan source for copyright+license text and file extensions
I:  33 %, ext = yml
I:  33 %, ext = Debian
I:  33 %, ext = c
I: make debian/* template files
I: debmake -x "1" ...
I: skipping :: debian/control (file exists)
I: skipping :: debian/copyright (file exists)
I: substituting => /usr/share/debmake/extra0/rules
I: skipping :: debian/rules (file exists)
I: substituting => /usr/share/debmake/extra0/changelog
I: skipping :: debian/changelog (file exists)
I: substituting => /usr/share/debmake/extra1/watch
I: skipping :: debian/watch (file exists)
I: substituting => /usr/share/debmake/extra1/salsa-ci.yml
I: skipping :: debian/salsa-ci.yml (file exists)
I: substituting => /usr/share/debmake/extra1/README.Debian
I: skipping :: debian/README.Debian (file exists)
I: substituting => /usr/share/debmake/extra1source/format
I: skipping :: debian/source/format (file exists)
I: substituting => /usr/share/debmake/extra1tests/control
I: skipping :: debian/tests/control (file exists)
I: substituting => /usr/share/debmake/extra1upstream/metadata
I: skipping :: debian/upstream/metadata (file exists)
I: substituting => /usr/share/debmake/extra1patches/series
I: skipping :: debian/patches/series (file exists)
I: substituting => /usr/share/debmake/extra1sourcex/patch-header
I: skipping :: debian/source/patch-header (file exists)
I: substituting => /usr/share/debmake/extra1sourcex/local-options
I: skipping :: debian/source/local-options (file exists)
I: substituting => /usr/share/debmake/extra1sourcex/options
I: skipping :: debian/source/options (file exists)
I: run "debmake -x2" to get more template files
I: $ wrap-and-sort
```

## Lakukan tambalan yang diperlukan

Yaitu mengubah tujuan pemasangan dari `/usr/local/bin` ke `/usr/bin`. Dalam kasus ini, aturan Debian terbaru menyarankan untuk menghindari `/usr/local/bin`.

```
export QUILT_PATCHES=debian/patches
quilt push -a     # ensure empty patch stack
quilt new fix-install-path.patch
quilt add Makefile
```

Kemudian lakukan perubahan pada berkas `Makefile` untuk mengubah direktori tujuan pemasangan. Lalu catat perubahan ke file patch.

```
quilt refresh
```

Pada titik ini, berkas `debian/patches/fix-install-path.patch` akan terisi oleh tambalan yang dimaksud.

```
cat debian/patches/fix-install-path.patch
Index: debhello-0.0/Makefile
===================================================================
--- debhello-0.0.orig/Makefile
+++ debhello-0.0/Makefile
@@ -1,4 +1,4 @@
-prefix = /usr/local
+prefix = /usr

 all: src/hello
```

## Catat perubahan

```
dch -i
```

Lalu isikan informasi terkait perubahan. Ikuti panduan penomoran versi dari https://github.com/BlankOn/wiki/blob/master/TimPengembang/Pemaket/PenomoranVersi.md


## Build

```
debuild
```

Keluaran,
```
ls ..
README.md     debhello-0.0.tar.gz               debhello_0.0-1.debian.tar.xz  debhello_0.0-1_amd64.build      debhello_0.0-1_amd64.changes  debhello_0.0-1_source.buildinfo  debhello_0.0.orig.tar.gz
debhello-0.0  debhello-dbgsym_0.0-1_amd64.ddeb  debhello_0.0-1.dsc            debhello_0.0-1_amd64.buildinfo  debhello_0.0-1_amd64.deb      debhello_0.0-1_source.changes    reset.sh
```



## Optional for learning purpose

### Re-unpack and re-patch

```
dpkg-buildpackage -S #
cd ..
rm -rf debhello-0.0
dpkg-source -x debhello_0.0-1.dsc # Extract tarball dengan patch
```
