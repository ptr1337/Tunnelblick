THE THIRD_PARTY FOLDER                                             Last updated 2019-05-11

CONTENTS
    INTRODUCTION
    PREREQUISITES
    THE THIRD PARTY PROGRAMS
    THE THIRD_PARTY FOLDER CONTENTS
    SOURCES FOLDER DETAILS
    CHANGING THIRD PARTY PROGRAMS


INTRODUCTION

The "third_party" folder contains programs that originate outside of the Tunnelblick
project. It includes a "Makefile" that builds them for use by Tunnelblick. See "THE THIRD
PARTY PROGRAMS", below, for a description of the programs.

Although it is common for software projects that include third-party software to download
the source code at compile time, Tunnelblick does not do that. Instead, all source code is
included in the Tunnelblick source. This is done for several reasons, among them:

 * To ensure that the software combinations work together correctly.

 * To allow older versions of some components (openssl, libressl) to be used with older
   copies of OpenVPN that are included in Tunnelblick to be compatible with older VPN servers. 

 * To protect against a compromise of the network or the servers containing the source
   code or other "supply chain attacks".

Changing the third party programs involves replacing or adding the source code, and,
for some programs, creating patches for the program and/or modifying Makefile (or the
files it "includes") to reflect the name of the new or replacement program. See
"CHANGING THIRD PARTY PROGRAMS", below.

The process of building the third party programs is controlled by an Xcode "Run Script"
that is among the first operation performed by Xcode when building the Tunnelblick application.
(Several Tunnelblick components are built before the application itself is built, so the
third-party programs are not the first binaries that are built.)


PREREQUISITES

Building certain versions of some of the third party programs require that "recent"
versions of the GNU autotools be installed. The "autotools" programs consist of the GNU
"autoconf", "automake", and "libtool" programs. macOS 10.6.8 with Xcode 3 includes older
versions of the autotools programs, and macOS 10.8.5 with Xcode 4 and higher does not
include the autotools programs at all.

The "third_party/ShellScriptToInstallAutotools.sh" script will download recent (possibly not the most
recent) versions of the autotools and install them into /usr/local/bin.

When building Tunnelblick, the first build phase "Run Script" that builds the third party
programs sets the path to look in /usr/local/bin first (before other folders), so any
programs installed there will be used instead of older versions.


THE THIRD PARTY PROGRAMS

Three programs are built and used by Tunnelblick directly:

    OpenVPN:   This is the program that creates VPNs. Tunnelblick launches it when a user
               asks to connect to a VPN.

    openvpn-down-root.so: This is a "helper" program used by OpenVPN. It is launched by
               OpenVPN when invoked with a certain options Tunnelblick passes to OpenVPN.

    tuntaposx: This program consists of tun and tap kexts which are loaded by Tunnelblick
               on demand.

There may be several different versions of OpenVPN in the third_party/tunnelblick folder.
Each copy is created with the Tunnelblick OpenVPN Xor Patch (see
https://tunnelblick.net/cOpenvpn_xorpatch.html).

Two programs are used slightly differently:

    Sparkle:   This program creates the Sparkle.framework, which Tunnelblick uses to
               manage some updates to VPN configurations and to the Tunnelblick application.

    easy-rsa:  This is a collection of shell scripts which can be used to manage a public
               key infrastructure (PKI). They are "installed" by copying them into a
               user-accessible folder. Tunnelblick has a button on its "Utilities" tab to
               launch a Terminal session to use the scripts. Tunnelblick has a limited
               facility for updating the scripts when a new version of Tunnelblick is
               installed.

Two system extensions (kexts) are included both as the source code and as a notarized binary.
The notarized binaries will be used unless they have been deleted. They are included because
macOS requires that kexts be signed with a special "kext signing" certificate which is not
available to most developers.

Four programs are built as libraries and statically linked to OpenVPN:

    LZO:           A compression/decompression library
    LZ4:           A compression/decompression library
    OpenSSL:       A TLS/SSL library
    LibreSSL:      A TLS/SSL library
    pkcs11-helper: A library for dealing with PKCS#11 devices


THE THIRD_PARTY FOLDER CONTENTS

The third_party folder contains the following files and folders:

    Makefile
        This is the makefile invoked by the first build phase "Run Script" to build the
        third party programs. It builds the third-party programs for Intel 64-bit (only)
        on whatever deployment target is specified by Xcode for the main
        Tunnelblick application. It uses make's "include" directive to include all of the
        files in the "makefiles" subfolder.

    makefiles
        This folder contains a Makefile for each of the third-party programs.

    sources
        This folder contains the source code for each of the third party programs. See
        "SOURCES FOLDER DETAILS", below.

	tun-notarized.kext and tap-notarized.kext
		These files are pre-built, signed and notarized binaries, included for the
		convenience of developers who do not have a "kext signing certificate" from
		Apple. If they have been deleted, the build process will use unsigned binaries
		built from the source code.

	ShellScriptToInstallAutotools.sh
		A script to download and install the "autotools" as described above in PREREQUISITES.

	README.txt
		This document.


The build process results in the creation of the following files and folders in third_party:

    do-not-clean
        The "do-not-clean" file is created by the "Run script" at the end of the third
        party build process to indicate that all of the third party programs have been
        created. If this file does not exist when Tunnelblick goes to build the third
        party programs, then the "Run Script" performs a "make clean" before building the
        third party programs to ensure that build is done from a known state. So you can
        cause a clean build of all of the third party programs by deleting this file and
        then building Tunnelblick.

    built-*
        Files with names starting with "built-" are created as part of the build process
        to indicate which programs have been built. This is particularly useful when
        debugging a problem building a particular program: by deleting this file, the
        problematic program can be built without having to build all it's prerequisites.

    build
        This folder is used while the third party programs are being built. It contains
        a subfolder for each program and a staging folder.

    products
        This folder contains the final third party programs as built and ready for use by
        Tunnelblick. They are copied from this folder into Tunnelblick.app by a
        "Run Script" in Tunnelblick.xcodeproj that is run as the final step in creating
        a Tunnelblick.app.


SOURCES FOLDER DETAILS

The "sources" folder contains the source code for each of the third party programs. _Note
that the version numbers in this document may not match the versions included in the
current Tunnelblick source code._

    libressl-2.5.5.tar.gz
        This is an archive of the source code for LibreSSL 2.5.5, as downloaded from
        https://ftp.openbsd.org/pub/OpenBSD/LibreSSL on 2017-08-11.

    lz4-1.7.5.tar.gz
        This is an archive of the source code for LZ4 1.7.5, as downloaded from
        https://github.com/lz4/lz4/releases.

    lzo-2.10.tar.gz
        This is an archive of the source code for LZO 2.10, as downloaded from
        https://www.oberhumer.com/opensource/lzo on 2017-08-11.

    openssl-1.0.2l.tar.gz
        This is an archive of the source code for OpenSSL 1.0.2l, as downloaded from
        https://www.openssl.org/source on 2017-08-11.

    pkcs11-helper-1.22.tar.bz2
        This is an archive of the source code for pkcs11-helper 1.22, as downloaded from
        https://github.com/OpenSC/pkcs11-helper on 2017-08-11.

    Sparkle-1.23.0-with-submodules.tar.gz
        This is an archive of the source code for Sparkle release 1.23.0, as downloaded
        from https://github.com/sparkle-project/Sparkle. See "To replace Sparkle", below,
        for details about how the downloaded source code has been supplemented with the
        submodules which it requires.

    patches
        This folder may contain patches for OpenSSL and Sparkle. tuntap and OpenVPN each have
        a similar folder of patches for each version. The process of creating the third
        party programs expands the source code into the third_party/build folder, patches
        the source code, and then builds the patched source code. As of 2016-07-31, only
        Sparkle has patches. (If easy-rsa, LZO, LZ4, or pkcs11-helper need patches, it
        will be necessary to modify their respective makefiles to implement the patching process.)

    tuntap
        This folder contains source code and patches to create the version(s)s of
        tuntap that are used by Tunnelblick. If additional newer versions are required,
        they should be put in this folder and the makefile modified to build them.

            tuntap-20111101
            tuntap-20141104
                These folders each contain an archive with the source code for a version
                of tuntap, along with a folder containing patches for that version.

    openvpn
        This folder contains source code and patches to create versions of
        OpenVPN that are used by Tunnelblick. The following structure is assumed for each
        version of OpenVPN that is to be created:

        openvpn-x.y.z
            This folder contains the source code and patches for version x.y.z of OpenVPN,
            along with an optional file containing configuration parameters for building
            OpenVPN.

            openvpn-x.y.z.tar.gz
            or
            openvpn-x.y.z.tar.xz
                This archive contains the source code for this version of OpenVPN, as
                downloaded from https://openvpn.net/index.php/open-source/downloads.html.

            patches
                This folder contains patches for this version of OpenVPN. All versions
                include the Tunnelblick OpenVPN Xor Patch (see
                https://code.google.com/p/tunnelblick/wiki/cOpenvpn_xorpatch).

            configure-options.txt (optional)
                If present, this ASCII text file should contain a single line with the
                options used to configure this version of OpenVPN for Tunnelblick. This
                file should not contain any CR or LF characters. The options should be
                separated by spaces.

	Note: When launched, Tunnelblick checks that the version identifier reported by
	each copy of OpenVPN is the same as the name of the folder that encloses it. The check
	ignores any suffix beginning with a hyphen ("-") in the folder name.

    easy-rsa

        This folder contains three folders that are used to create the final
        "easy-rsa-tunnelblick" folder that is contained within Tunnelblick.app.

        easy-rsa-3.0.1
            This is the source code for easy-rsa-3.0.1.

        easy-rsa-from-openvpn-2.2.1
            This is the source code for easy-rsa 2 taken from the OpenVPN 2.2.1 source
            code.

        easy-rsa-2-tunnelblick
            This is the easy-rsa 2 source code modified for use by Tunnelblick. Changes
            include quoting paths so that paths with spaces work properly.


CHANGING THIRD PARTY PROGRAMS

    When a new version of a third party program is released, it is usually appropriate
    to include the new version in Tunnelblick.

To replace an older version of LibreSSL, LZ4, LZO, OpenSSL, or pkcs11-helper:

    1. Download an archive containing the source code and copy it to the "sources"
       subfolder. Download the same type of archive (".tar.gz", ".tar.bz2", or ".zip") as
       the original archive that is being replaced.
    2. Delete the archive of the older version.
    3. If necessary, create or modify the .diff file for each patch that is needed and
       copy the .diff file into the appropriate subfolder in third_party/patches.
    4. Modify the beginning of Makefile to reflect the name of the newer version.
    5. If the format of the archive has changed (from ".tar.gz" to ".zip", for example),
       it may be necessary to modify the makefile for the program to expand the archive
        with a different program.

To add a new version of OpenVPN:
    1. Download an archive containing the source code and copy it to a new subfolder in
       third_party/openvpn. (The subfolder must be named "openvpn-x.y.z", where x.y.z is
       the version of OpenVPN that it contains.) Download the archive as a ".tar.gz" or
       ".tar.xz" file.
    2. If necessary, create or modify a .diff file for each patch that is needed and
       copy the .diff file into the subfolder.
    3. If necessary, or create or modify a 'configuration-options.txt' file to specify
       the options used to configure the version of OpenVPN, and copy the file into the
       subfolder.

To remove a version of OpenVPN:
    Remove the version's folder from /third_party/openvpn.

To add a new version of tuntap:
    1. Download an archive containing the source code and copy it to a new subfolder in
       third_party/sources/tuntap. Download the archive as a ".tar.gz" file.
    2. Delete the archive of the older version.
    3. If necessary, create or modify a .diff file for each patch that is needed and
       copy the .diff file into the appropriate subfolder in third_party/patches.
    4. Modify third_party/Makefile-tuntap to create the newer version. This will involve changes in
       several places.
    5. Other changes may be needed in the Tunnelblick source code to use the new version
       (for example, if it is only for specific versions of macOS).

To replace Sparkle:

	Note: Using the .tar.gz or .zip of a Sparkle release does not work because of a
	bug/feature of GitHub. The problem is that the downloaded archive does not include
	the source code of the ed25519 submodule. So getting a .tar.gz with the full Sparkle
	source code for a branch is a multi-step process.

	Here is an example of getting the source of Sparkle release 1.23.0:

	First, in Terminal, cd to a folder in which to work. Then execute the following commands:

		git clone --depth 1 --branch 1.23.0 --recursive https://github.com/sparkle-project/Sparkle
		mv Sparkle Sparkle-1.23.0-with-submodules
		tar -czf Sparkle-1.23.0-with-submodules.tar.gz Sparkle-1.23.0-with-submodules
		rm -fr Sparkle-1.23.0-with-submodules

	Second, copy the new .tar.gz into the third_party/sources folder.

	Third, remove the old Sparkle .tar.gz or .zip file.

	Finally, change SPARKLE_NAME in third_party/Makefile to the name of the newly created .tar.gz but
	without the ".tag.gz". In the example above, set SPARKLE_NAME to Sparkle-1.23.0-with-submodules.

	Detailed explanation of the commands:
		git clone... downloads a clone of the source code from GitHub.
					"--depth 1" causes the clone to include only the last revision .
					"--branch 1.23.0" causes the commit with tag "1.23.0" to be cloned.
					"--recursive" causes all submodule source code to be included.
					"https://github.com/sparkle-project/Sparkle" is the GitHub URL for the Sparkle project.

		tar -czf...	creates the .tar.gz
					"-czf Sparkle-1.23.0-with-submodules.tar.gz" causes tar to create a new .tar.gz archive with that name
					"Sparkle" specifies the folder from which the .tar.gz is to be made

		rm -fr Sparkle removes the downloaded source code
