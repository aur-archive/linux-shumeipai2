# Maintainer: 謝致邦 <Yeking@Red54.com>

_mach=sun4i
pkgname=linux-shumeipai2
true && pkgname=("linux-shumeipai2" "linux-shumeipai2-headers")
# pkgname=linux-custom			 # Build kernel with a different name
pkgver=3.0.76
pkgrel=1
arch=('armv7h')
url="https://github.com/Red54/linux-shumeipai2"
license=('GPL2')
makedepends=('git' 'uboot-mkimage')
options=('!strip')
PKGEXT=".pkg.tar"
source=('linux-shumeipai2.install')
md5sums=('1f255b0ab81d82f6d9a2512d4a3db054')

build() {
	cd $srcdir
	if [[ -d $pkgname ]]; then
		cd $pkgname && git pull origin
	else
		git clone $url --depth 1
	fi
	rm -rf $srcdir/build
	cp -r $srcdir/$pkgname $srcdir/build
	cd $srcdir/build
	make smp2_defconfig
	#make smp2_nand_defconfig
	#make smp2_server_defconfig
	make ${MAKEFLAGS} uImage modules
}

package_linux-shumeipai2() {
	pkgdesc="The Linux Kernel and modules for Smartfire Shumeipai 2"
	depends=('coreutils' 'linux-firmware' 'module-init-tools>=3.16')
	optdepends=('crda: to set the correct wireless channels of your country')
	provides=("kernel26" "linux=${pkgver}")
	conflicts=('kernel26' 'linux')
	replaces=('kernel26')
	install=${pkgname}.install

	cd $srcdir/build
	mkdir -p "${pkgdir}"/{boot,usr}
	make INSTALL_MOD_PATH="${pkgdir}/usr" modules_install
	cp arch/arm/boot/uImage "${pkgdir}/boot/uImage"
	#objcopy -R .note.gnu.build-id -S -O binary vmlinux ${pkgdir}/boot/bImage
	rm -rf "${pkgdir}"/usr/lib/{firmware,modules/${pkgver}+/{source,build}}
}

package_linux-shumeipai2-headers() {
	pkgdesc="Header files and scripts for building modules for linux kernel for Smartfire Shumeipai 2"
	provides=('kernel26-headers' "linux-headers=${pkgver}")
	conflicts=('kernel26-headers')
	replaces=('kernel26-headers')

	install -dm755 "${pkgdir}/usr/lib/modules/${pkgver}+"

	cd "${pkgdir}/usr/lib/modules/${pkgver}+"
	ln -sf ../../../src/linux-${pkgver}+ build

	cd $srcdir/build
	install -D -m644 Makefile \
		"${pkgdir}/usr/src/linux-${pkgver}+/Makefile"
	install -D -m644 kernel/Makefile \
		"${pkgdir}/usr/src/linux-${pkgver}+/kernel/Makefile"
	install -D -m644 .config \
		"${pkgdir}/usr/src/linux-${pkgver}+/.config"

	mkdir -p "${pkgdir}/usr/src/linux-${pkgver}+/include"

	for i in acpi asm-generic config crypto drm generated linux math-emu \
		media net pcmcia scsi sound trace video xen; do
		cp -a include/${i} "${pkgdir}/usr/src/linux-${pkgver}+/include/"
	done

	 # copy arch includes for external modules
	mkdir -p ${pkgdir}/usr/src/linux-${pkgver}+/arch/arm
	cp -a arch/arm/include ${pkgdir}/usr/src/linux-${pkgver}+/arch/arm/

	## YOUR MACH HERE, this is using the $_mach ##
	mkdir -p ${pkgdir}/usr/src/linux-${pkgver}+/arch/arm/mach-${_mach}	 
	cp -a arch/arm/mach-${_mach}/include ${pkgdir}/usr/src/linux-${pkgver}+/arch/arm/mach-${_mach}/

	# copy files necessary for later builds
	cp Module.symvers "${pkgdir}/usr/src/linux-${pkgver}+"
	cp -a scripts "${pkgdir}/usr/src/linux-${pkgver}+"

	# fix permissions on scripts dir
	chmod og-w -R "${pkgdir}/usr/src/linux-${pkgver}+/scripts"
	mkdir -p "${pkgdir}/usr/src/linux-${pkgver}+/.tmp_versions"

	mkdir -p "${pkgdir}/usr/src/linux-${pkgver}+/arch/arm/kernel"

	cp arch/arm/Makefile "${pkgdir}/usr/src/linux-${pkgver}+/arch/arm/"

	cp arch/arm/kernel/asm-offsets.s "${pkgdir}/usr/src/linux-${pkgver}+/arch/arm/kernel/"

	# add headers for lirc package
	mkdir -p "${pkgdir}/usr/src/linux-${pkgver}+/drivers/media/video"

	cp drivers/media/video/*.h	"${pkgdir}/usr/src/linux-${pkgver}+/drivers/media/video/"

	# add docbook makefile
	install -D -m644 Documentation/DocBook/Makefile \
		"${pkgdir}/usr/src/linux-${pkgver}+/Documentation/DocBook/Makefile"

	# add dm headers
	mkdir -p "${pkgdir}/usr/src/linux-${pkgver}+/drivers/md"
	cp drivers/md/*.h "${pkgdir}/usr/src/linux-${pkgver}+/drivers/md"

	# add inotify.h
	mkdir -p "${pkgdir}/usr/src/linux-${pkgver}+/include/linux"
	cp include/linux/inotify.h "${pkgdir}/usr/src/linux-${pkgver}+/include/linux/"

	# add wireless headers
	mkdir -p "${pkgdir}/usr/src/linux-${pkgver}+/net/mac80211/"
	cp net/mac80211/*.h "${pkgdir}/usr/src/linux-${pkgver}+/net/mac80211/"

	# copy in Kconfig files
	for i in `find . -name "Kconfig*"`; do
		mkdir -p "${pkgdir}"/usr/src/linux-${pkgver}+/`echo ${i} | sed 's|/Kconfig.*||'`
		cp ${i} "${pkgdir}/usr/src/linux-${pkgver}+/${i}"
	done

	chown -R root.root "${pkgdir}/usr/src/linux-${pkgver}+"
	find "${pkgdir}/usr/src/linux-${pkgver}+" -type d -exec chmod 755 {} \;

	# remove unneeded architectures
	rm -rf "${pkgdir}"/usr/src/linux-${pkgver}+/arch/{alpha,arm26,avr32,blackfin,cris,frv,h8300,ia64,m32r,m68k,m68knommu,mips,microblaze,mn10300,parisc,powerpc,ppc,s390,sh,sh64,sparc,sparc64,um,v850,x86,xtensa}
}

pkgdesc="The Linux Kernel and modules for Smartfire Shumeipai 2"
