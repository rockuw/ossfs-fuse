THIS README.md CONTAINS OUTDATED INFORMATION - please refer to the wiki or --help

OSSFS-Fuse
==========

OSSFS is FUSE (File System in User Space) based solution to mount/unmount an Aliyun OSS storage buckets and use system commands with OSS just like it was another Hard Disk.

In order to compile ossfs, You'll need the following requirements:

* Kernel-devel packages (or kernel source) installed that is the SAME version of your running kernel
* LibXML2-devel packages
* CURL-devel packages (or compile curl from sources at: curl.haxx.se/ use 7.15.X)
* GCC, GCC-C++
* pkgconfig
* FUSE (>= 2.8.4)
* FUSE Kernel module installed and running (RHEL 4.x/CentOS 4.x users - read below)
* OpenSSL-devel (0.9.8)
* Subversion

If you're using YUM or APT to install those packages, then it might require additional packaging, allow it to be installed.

Downloading & Compiling:
------------------------
In order to download ossfs, user the following command:
git clone https://github.com/SnakeHunt2012/ossfs-fuse.git

Go inside the directory that has been created (ossfs-read-only/ossfs) and run: ./autogen.sh
This will generate a number of scripts in the project directory, including a configure script which you should run with: ./configure
If configure succeeded, you can now run: make. If it didn't, make sure you meet the dependencies above.
This should compile the code. If everything goes OK, you'll be greated with "ok!" at the end and you'll have a binary file called "ossfs"
in the src/ directory.

As root (you can use su, su -, sudo) do: "make install" -this will copy the "ossfs" binary to /usr/local/bin.

Congratulations. ossfs is now compiled and installed.

Usage:
------
In order to use ossfs, make sure you have the Access Key and the Secret Key handy. (refer to the wiki)
First, create a directory where to mount the OSS bucket you want to use.
Example (as root): mkdir -p /mnt/oss
Then run: ossfs mybucket[:path] /mnt/oss

This will mount your bucket to /mnt/oss. You can do a simple "ls -l /mnt/oss" to see the content of your bucket.

If you want to allow other people access the same bucket in the same machine, you can add "-o allow _other" to read/write/delete content of the bucket.

You can add a fixed mount point in /etc/fstab, here's an example:

ossfs#mybucket /mnt/oss fuse allow_other 0 0

This will mount upon reboot (or by launching: mount -a) your bucket on your machine.

All other options can be read at: http://www.aliyun.com/product/oss

Known Issues:
-------------
ossfs should be working fine with OSS storage. However, There are couple of limitations:

* There is no full UID/GID support yet, everything looks as "root" and if you allow others to access the bucket, others can erase files. There is, however, permissions support built in.
* Currently ossfs could hang the CPU if you have lots of time-outs. This is *NOT* a fault of ossfs but rather libcurl. This happends when you try to copy thousands of files in 1 session, it doesn't happend when you upload hundreds of files or less.
* CentOS 4.x/RHEL 4.x users - if you use the kernel that shipped with your distribution and didn't upgrade to the latest kernel RedHat/CentOS gives, you might have a problem loading the "fuse" kernel. Please upgrade to the latest kernel (2.6.16 or above) and make sure "fuse" kernel module is compiled and loadable since FUSE requires this kernel module and ossfs requires it as well.
* Moving/renaming/erasing files takes time since the whole file needs to be accessed first. A workaround could be to use ossfs's cache support with the use_cache option.

