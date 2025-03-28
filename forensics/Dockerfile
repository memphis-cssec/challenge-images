# syntax=docker/dockerfile:1-labs

FROM ubuntu:20.04

SHELL ["/bin/bash", "-ceov", "pipefail"]

ENV DEBIAN_FRONTEND=noninteractive
ENV LC_CTYPE=C.UTF-8

RUN <<EOF
    rm -f /etc/apt/apt.conf.d/docker-clean
    echo 'Binary::apt::APT::Keep-Downloaded-Packages "true";' > /etc/apt/apt.conf.d/keep-cache

    (set +o pipefail; yes | unminimize)

    dpkg --add-architecture i386

    apt-get clean && rm -rf /var/lib/apt/lists/*
EOF

RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    apt-get update && xargs apt-get install --no-install-recommends -yqq <<EOF 
        build-essential
        ca-certificates
        curl
        python-is-python3
        sudo
        wget
        unzip
EOF

RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    apt-get update && xargs apt-get install --no-install-recommends -yqq <<EOF 
        autoconf
        bc
        bison
        cargo
        clang
        cmake
        cpio
        openjdk-17-jdk
        flex
        dwarves
        g++-multilib
        gcc-multilib
        git
        libc6-dev-i386
        libc6:i386
        libedit-dev
        libelf-dev
        libffi-dev
        libglib2.0-dev
        libgmp-dev
        libini-config-dev
        libncurses5:i386
        libpcap-dev
        libpixman-1-dev
        libseccomp-dev
        libssl-dev
        libstdc++6:i386
        libtool-bin
        llvm
        man-db
        manpages-dev
        nasm
        python-is-python3
        python3-dev
        python3-pip
        rubygems
        squashfs-tools
        upx-ucl
EOF

RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    apt-get update && xargs apt-get install --no-install-recommends -yqq <<EOF
        apache2
        arping
        bash-builtins
        bat
        binutils
        binutils-aarch64-linux-gnu
        binwalk
        bsdmainutils
        bsdutils
        debianutils
        diffutils
        ed
        elfutils
        emacs
        ethtool
        exiftool
        expect
        figlet
        findutils
        finger
        firefox
        fish
        fortune
        fortunes
        gcc-aarch64-linux-gnu
        gdb
        gdb-multiarch
        gedit
        genisoimage
        gnupg-utils
        gprolog
        hexedit
        icdiff
        iproute2
        iptables
        iputils-ping
        ipython3
        john
        jq
        keyutils
        kmod
        less
        libapache2-mod-php
        libc6-arm64-cross
        libc6-dev-arm64-cross
        ltrace
        mysql-server
        nano
        neovim
        net-tools
        netcat-openbsd
        nmap
        openssh-server
        p7zip-full
        parallel
        patchelf
        pcaputils
        pcre2-utils
        php-cgi
        php-mysql
        psutils
        python3-ipdb
        python3-magic
        qemu-system-x86
        qemu-user
        qemu-utils
        racket
        rsync
        sagemath
        screen
        silversearcher-ag
        socat
        sqlite3
        strace
        tmux
        toilet-fonts
        valgrind
        vim
        wamerican
        whiptail
        wireshark
        zip
        zsh
EOF

# forensics and progs students asked
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    apt-get update && xargs apt-get install --no-install-recommends -yqq <<EOF
    forensics-full
    bat
    hexyl
EOF

# volatility prereqs (prob have them but just in case)
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    apt-get update && xargs apt-get install --no-install-recommends -yqq <<EOF && \
    apt-get clean && rm -rf /var/lib/apt/lists/*
    python3-pip 
    python-setuptools 
    build-essential 
EOF

# volatility
RUN <<EOF
    pip3 install volatility3
    mkdir -p /opt/volatility3/symbols
    cd /opt/volatility3/symbols
    wget https://downloads.volatilityfoundation.org/volatility3/symbols/windows.zip
    wget https://downloads.volatilityfoundation.org/volatility3/symbols/linux.zip
EOF

# new autopsy
RUN <<EOF
    cd /opt
    mkdir sleuthkit
    cd sleuthkit
    wget https://github.com/sleuthkit/sleuthkit/releases/download/sleuthkit-4.12.1/sleuthkit-java_4.12.1-1_amd64.deb
    apt update
    apt -y install ./sleuthkit-java_4.12.1-1_amd64.deb
    cd /opt
    mkdir autopsy
    cd autopsy
    wget https://github.com/sleuthkit/autopsy/releases/download/autopsy-4.21.0/autopsy-4.21.0.zip
    apt -y install \
        openjdk-17-jdk openjdk-17-jre \
        build-essential autoconf libtool automake git zip wget ant \
        libde265-dev libheif-dev \
        libpq-dev \
        testdisk libafflib-dev libewf-dev libvhdi-dev libvmdk-dev \
        libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad \
        gstreamer1.0-plugins-ugly gstreamer1.0-libav gstreamer1.0-tools gstreamer1.0-x \
        gstreamer1.0-alsa gstreamer1.0-gl gstreamer1.0-gtk3 gstreamer1.0-qt5 gstreamer1.0-pulseaudio
    wget https://raw.githubusercontent.com/sleuthkit/autopsy/refs/heads/develop/linux_macos_install_scripts/install_application.sh
    chmod +x ./install_application.sh
    ./install_application.sh -z ./autopsy-4.21.0.zip -i /opt/autopsy -j $(update-java-alternatives -l | grep java-1.17 | awk '{print $3}')
EOF

RUN rm -f /usr/lib/python3.*/EXTERNALLY-MANAGED

################################################################################

# linux kernel


RUN <<EOF
    mkdir /opt/linux
    wget -O - https://mirrors.edge.kernel.org/pub/linux/kernel/v5.x/linux-5.4.tar.gz | tar xzC /opt/linux
    cd /opt/linux/linux-5.4
    make defconfig
EOF

RUN awk '{$1=$1};1' >> /opt/linux/linux-5.4/.config <<EOF
    CONFIG_9P_FS=y
    CONFIG_9P_FS_POSIX_ACL=y
    CONFIG_9P_FS_SECURITY=y
    CONFIG_BALLOON_COMPACTION=y
    CONFIG_CRYPTO_DEV_VIRTIO=y
    CONFIG_DEBUG_FS=y
    CONFIG_DEBUG_INFO=y
    CONFIG_DEBUG_INFO_BTF=y
    CONFIG_DEBUG_INFO_DWARF4=y
    CONFIG_DEBUG_INFO_REDUCED=n
    CONFIG_DEBUG_INFO_SPLIT=n
    CONFIG_DEVPTS_FS=y
    CONFIG_DRM_VIRTIO_GPU=y
    CONFIG_FRAME_POINTER=y
    CONFIG_GDB_SCRIPTS=y
    CONFIG_HW_RANDOM_VIRTIO=y
    CONFIG_HYPERVISOR_GUEST=y
    CONFIG_IKCONFIG=y
    CONFIG_IKCONFIG_PROC=y
    CONFIG_NET_9P=y
    CONFIG_NET_9P_DEBUG=n
    CONFIG_NET_9P_VIRTIO=y
    CONFIG_PARAVIRT=y
    CONFIG_PCI=y
    CONFIG_PCI_HOST_GENERIC=y
    CONFIG_USER_NS=y
    CONFIG_VIRTIO_BALLOON=y
    CONFIG_VIRTIO_BLK=y
    CONFIG_VIRTIO_BLK_SCSI=y
    CONFIG_VIRTIO_CONSOLE=y
    CONFIG_VIRTIO_INPUT=y
    CONFIG_VIRTIO_NET=y
    CONFIG_VIRTIO_PCI=y
    CONFIG_VIRTIO_PCI_LEGACY=y
EOF

RUN <<EOF
    cd /opt/linux/linux-5.4
    make -j$(nproc) bzImage
    ln -sf $PWD/arch/x86/boot/bzImage ../bzImage
    ln -sf $PWD/vmlinux ../vmlinux
EOF

################################################################################

# gdb

RUN <<EOF
    git clone --depth 1 --recurse-submodules --branch 2024.02.14 https://github.com/pwndbg/pwndbg /opt/pwndbg
    cd /opt/pwndbg
    ./setup.sh

    git clone --depth 1 https://github.com/hugsy/gef /opt/gef

    git clone --depth 1 https://github.com/jerdna-regeiz/splitmind /opt/splitmind

    git clone --depth 1 https://github.com/nccgroup/libslub /opt/libslub
    cd /opt/libslub
    pip install -r requirements.txt

    git clone --depth 1 --recurse-submodules https://github.com/zolutal/gdb-pt-dump /opt/pt-dump

    git clone --depth 1 --recurse-submodules https://github.com/zolutal/kropr /opt/kropr
    cd /opt/kropr
    cargo install --path . --root /tmp
    mv /tmp/bin/ropr /usr/bin/kropr
    chmod +x /usr/bin/kropr
    ln -sf /home/hacker/.gdbinit /root/.gdbinit
EOF

################################################################################

RUN <<EOF
    git clone --depth 1 https://github.com/the-tcpdump-group/tcpdump /opt/tcpdump
    cd /opt/tcpdump
    ./autogen.sh
    ./configure
    make install
EOF

RUN <<EOF
    git clone https://github.com/capstone-engine/capstone /opt/capstone
    cd /opt/capstone
    git checkout 0a67596
    make
    make install
    rm -rf /opt/capstone
EOF

RUN <<EOF
    wget -q -O - https://github.com/mozilla/geckodriver/releases/download/v0.35.0/geckodriver-v0.35.0-linux64.tar.gz | tar xvz -C /usr/local/bin
EOF

RUN <<EOF
    VERSION="busybox-1.33.2"
    mkdir /opt/busybox && cd "$_"
    wget -q -O - "https://busybox.net/downloads/${VERSION}.tar.bz2" | tar xvj
    pushd "${VERSION}"
    make defconfig
    sed -e 's/.*CONFIG_STATIC .*/CONFIG_STATIC=y/' -i .config
    make -j$(nproc) install
    popd
    ln -s "${VERSION}/_install" /opt/busybox/fs
EOF

RUN <<EOF
    VERSION="1.5.1"
    DEB_NAME="glow_${VERSION}_amd64.deb"
    pushd /tmp
    wget -q "https://github.com/charmbracelet/glow/releases/download/v${VERSION}/${DEB_NAME}"
    apt install "./${DEB_NAME}"
    rm "${DEB_NAME}"
    popd
EOF

RUN <<EOF
    mkdir /opt/virtiofsd && cd "$_"
    wget -q -O ./build.zip "https://gitlab.com/virtio-fs/virtiofsd/-/jobs/artifacts/main/download?job=publish"
    unzip -p ./build.zip "$(zipinfo -1 ./build.zip | head -n1)" > ./virtiofsd
    rm -f ./build.zip
    chmod +x ./virtiofsd
EOF

################################################################################

# angr management

COPY desktop/angr-management.desktop /usr/share/applications/
RUN <<EOF
    wget -q -O - https://github.com/angr/angr-management/releases/download/nightly/angr-management-ubuntu-20.04.tar.gz | tar xvz -C /opt
EOF

################################################################################

# binja

COPY desktop/binary-ninja.desktop /usr/share/applications/
RUN <<EOF
    wget -O /tmp/binaryninja_free_linux.zip https://web.archive.org/web/20240625203027/https://cdn.binary.ninja/installers/binaryninja_free_linux.zip
    unzip -d /opt /tmp/binaryninja_free_linux.zip
    wget -O /opt/binaryninja/icon.png  https://binary.ninja/icons/android-chrome-192x192.png
    rm /tmp/binaryninja_free_linux.zip
EOF

################################################################################

# python pip

RUN xargs pip install --force-reinstall <<EOF
    angr
    asteval
    dojjail>=0.1.7
    flask
    git+https://github.com/Gallopsled/pwntools#egg=pwntools
    git+https://github.com/secdev/scapy#egg=scapy
    jupyter
    psutil
    pycryptodome
    r2pipe
    requests
    selenium
    fastecdsa
EOF

RUN ln -sf /usr/bin/ipython3 /usr/bin/ipython

################################################################################

RUN mkdir /opt/pwn.college
COPY docker-initialize.sh /opt/pwn.college/docker-initialize.sh
COPY docker-entrypoint.d /opt/pwn.college/docker-entrypoint.d
COPY docker-entrypoint.sh /opt/pwn.college/docker-entrypoint.sh
COPY bash.bashrc /opt/pwn.college/bash.bashrc
COPY vm /opt/pwn.college/vm
COPY .tmux.conf /opt/pwn.college/.tmux.conf
COPY .gdbinit /opt/pwn.college/.gdbinit
COPY .radare2rc /opt/pwn.college/.radare2rc
COPY .pwn.conf /opt/pwn.college/.pwn.conf

RUN <<EOF
    ln -sf /run/dojo/bin/python-suid /opt/pwn.college/python
    ln -sf /run/dojo/bin/bash-suid /opt/pwn.college/bash
    ln -sf /run/dojo/bin/sh-suid /opt/pwn.college/sh

    ln -sf /opt/pwn.college/vm/vm /usr/local/bin/vm

    ln -sf /home/hacker/.tmux.conf /root/.tmux.conf
    ln -sf /home/hacker/.gdbinit /root/.gdbinit
    ln -sf /home/hacker/.radare2rc /root/.radare2rc
    ln -sf /home/hacker/.pwn.conf /root/.pwn.conf

    mkdir /challenge
    install -m 400 <(echo 'pwn.college{uninitialized}') /flag
EOF

RUN <<EOF
    if [ -f /etc/ssh/ssh_config ]
    then
        echo "StrictHostKeyChecking no" >> /etc/ssh/ssh_config
        echo "UserKnownHostsFile=/dev/null" >> /etc/ssh/ssh_config
        echo "LogLevel ERROR" >> /etc/ssh/ssh_config
    fi

    if [ -f /etc/ssh/sshd_config ]
    then
        sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/g' /etc/ssh/sshd_config
        sed -i 's/#PermitEmptyPasswords no/PermitEmptyPasswords yes/g' /etc/ssh/sshd_config
        sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
    fi

    if id ubuntu; then userdel -f -r ubuntu; fi
    useradd -s /bin/bash -m hacker
    passwd -d hacker

    echo -e '\n[ "$UID" -eq 1000 ] && . /opt/pwn.college/bash.bashrc' >> /etc/bash.bashrc
    ln -s /etc/bash.bashrc /etc/bashrc

    find / -xdev -type f -perm -4000 -exec chmod u-s {} \;

    chmod u+s /opt/pwn.college/vm/vm

    date > /opt/pwn.college/build
EOF

ADD --chown=0:0 --chmod=6755 http://github.com/pwncollege/exec-suid/releases/latest/download/exec-suid /usr/bin/exec-suid

RUN <<EOF
    ln -sf /usr/bin/python /usr/local/bin/python
    ln -sf /usr/bin/python3 /usr/local/bin/python3
EOF

USER hacker
WORKDIR /home/hacker
