# Bootstrap Texlive Environment

This process was modified from the process used by
[texlive-modular](https://github.com/ProdriveTechnologies/texlive-modular).   A
successful adoption of this bootstrapping process enables Bazel to build `.tex`
files into `.pdf` files.

## Overview

(Copied from texlive-modular's README.md)

[The official tarballs of
TeXLive](http://ftp.math.utah.edu/pub/tex/historic/systems/texlive/2018/) are
very large in size; approximately 3.5 GB.  Most documents created using TeXLive
only depend on a fraction of this data.  This makes the official tarballs
unsuitable for use in automated build processes that download dependencies on
demand (e.g., [Bazel](https://bazel.build/) with its [LaTeX
rules](https://github.com/ProdriveTechnologies/bazel-latex)).

This repository provides a copy of the data stored in the official tarballs,
decomposed into many (> 5000) small archives.  The decomposition is performed
automatically, based on the directory structure of the offical tarballs.  Every
subtree containing at least one (non-blacklisted) file is stored in a separate
tarball.  Tarballs are named after the path of the subtree, where `/` is
substituted with `--`.



## Download TexLive

TexLive distributions are archived
[here](http://ftp.math.utah.edu/pub/tex/historic/systems/texlive/).  Go into
there and select the most recent year, which, as of this writing, is 2018.

The URL selected is:
    http://ftp.math.utah.edu/pub/tex/historic/systems/texlive/2018/


Download all files listed using this command:
```bash
wget -r -np -R 'index.html*' http://ftp.math.utah.edu/pub/tex/historic/systems/texlive/2018/
```

Brief explanation of the command in use:
  - `-r,--recursive` turns on recursive retrieving with a default maximal depth
      of 5.  The maximal depth can be adjusted using `-l,--level`.
  - `-np,--no-parent` causes `wget` to *not* ascend to the parent directory when
      retrieving recursively, thus guarantees that only files *below* the
      target directory are downloaded.
  - `-R,--reject 'index.html*'` causes `wget` to *not* download the
      auto-generated `index.html*` files.

A total of 3.3GB is downloaded, with the following directory structure:
```bash
$ tree ftp.math.utah.edu/pub/tex/historic/systems/texlive/2018/
ftp.math.utah.edu/pub/tex/historic/systems/texlive/2018/
├── install-tl-unx.tar.gz
├── texlive-20180414-bin.tar.xz
├── texlive-20180414-bin.tar.xz.sha512
├── texlive-20180414-bin.tar.xz.sha512.asc
├── texlive-20180414-devsource.tar.xz
├── texlive-20180414-devsource.tar.xz.sha512
├── texlive-20180414-devsource.tar.xz.sha512.asc
├── texlive-20180414-extra.tar.xz
├── texlive-20180414-extra.tar.xz.sha512
├── texlive-20180414-extra.tar.xz.sha512.asc
├── texlive-20180414-source.tar.xz
├── texlive-20180414-source.tar.xz.sha512
├── texlive-20180414-source.tar.xz.sha512.asc
├── texlive-20180414-texmf.tar.xz
├── texlive-20180414-texmf.tar.xz.sha512
├── texlive-20180414-texmf.tar.xz.sha512.asc
└── texlive-20180414-tlpdb-full.tar.gz

0 directories, 17 files
```

Their sha256 checksums:
```bash
$ sha256sum texlive-20180414-*.tar.*
0b5657c97ff203bd8fadb28986a167c731db9e7947ff65f6680dda57ca4971b7  texlive-20180414-bin.tar.xz
aec4de3255f6b9ed788d57e00fc6449220e8c6c3889261aebdebc5d1f063db39  texlive-20180414-bin.tar.xz.sha512
a5406d79c94967e5304304b8158fee81f8a9bcf768fd23c7592317d97d40604a  texlive-20180414-bin.tar.xz.sha512.asc
e22e748b73bb874abc2a9466812554f0c4c4447114fda38c55b450dbd584bb68  texlive-20180414-devsource.tar.xz
8f2fadb6ea8ff22c3cda7ba75c51acb44e79506c3a80c925e109a509b70e170a  texlive-20180414-devsource.tar.xz.sha512
93c2875504f19ec16f1ebdeef8ea8c2f0a4dc37e492fed0e2836dd670f7f070c  texlive-20180414-devsource.tar.xz.sha512.asc
5b4397854723405f20df7172e73a04cee2d3ab712f78b064a7f523d6ab9f0329  texlive-20180414-extra.tar.xz
b52643220743d4d8000ae38e4f5d24688301e6b6bbdefd93a5924f8944704c50  texlive-20180414-extra.tar.xz.sha512
257026b9e433aeb8c58e7212ff0c2cc85f5a782e8d78f6a10658ded5a8d55db5  texlive-20180414-extra.tar.xz.sha512.asc
fe0036d5f66708ad973cdc4e413c0bb9ee2385224481f7b0fb229700a0891e4e  texlive-20180414-source.tar.xz
c47202e3447cb1d4e58853c5211df7429fd54311e265a3d99f9999ea2afe08cf  texlive-20180414-source.tar.xz.sha512
498122ce78e1a38f732f9219531a5ea39441ae425a91c9927c09d742b42df56b  texlive-20180414-source.tar.xz.sha512.asc
bae2fa05ea1858b489f8138bea855c6d65829cf595c1fb219c5d65f4fe8b1fad  texlive-20180414-texmf.tar.xz
ed4c8bfaedd9c9639232007376e7e98be7672444b325991e8d5a4495437807fe  texlive-20180414-texmf.tar.xz.sha512
5c1650ef4ee0bbb5f7ae1a51aa9b41a705a46d879b3787576bdf29f7577767e7  texlive-20180414-texmf.tar.xz.sha512.asc
7791a7c4afd5fddda785eb989f55f6e8d5ffaea68b08b15838d756626b38cbe1  texlive-20180414-tlpdb-full.tar.gz
```

Then, these files are copied to `mirrors.corp.logiocean.com` and become
downloadable from this [link](http://mirrors.corp.logiocean.com/file-store/texlive/ftp/2018/).

## Create Tarballs

Follow [these
instructions](https://github.com/ProdriveTechnologies/texlive-modular) to create
the tarballs.

```bash
tar xf texlive-20180414-bin.tar.xz
tar xf texlive-20180414-extra.tar.xz
tar xf texlive-20180414-texmf.tar.xz
```

Note that these commands will take a few minutes to complete.

Then use
[create_tarballs.py](https://raw.githubusercontent.com/ProdriveTechnologies/texlive-modular/master/create_tarballs.py)
to create the tarballs:
```bash
# Download the create_tarballs.py script.
wget https://raw.githubusercontent.com/ProdriveTechnologies/texlive-modular/master/create_tarballs.py
chmod +x ./create_tarballs.py

# Create the temporary output directory.
mkdir output

# Create tarballs.
time ./create_tarballs.py texlive-20180414-bin  # ~11 minutes
time ./create_tarballs.py texlive-20180414-extra/tlpkg/TeXLive  # almost instant
time ./create_tarballs.py texlive-20180414-texmf  # ~ 9 minutes

# Compute sha256 checksums for the generated tarballs.
# The generated sha256sums-20180414.txt is about 708KB.
time (cd output; sha256sum *) > sha256sums-20180414.txt
```

Both the `output` directory and the `sha256sums-20180414.txt` file are
considered output of this step.

At this point, the `output` directory contains ~5000 `.tar.xz` files, totaling
1.6GB.


## Upload Tarballs

Upload the tarballs in `output` to `mirrors.corp.logiocean.com` so that they
become downloadable from [this
link](http://mirrors.corp.logiocean.com/file-store/texlive/modules/2018/).
