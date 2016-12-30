# mkbootimg-binaries

These are the binaries for ubuntu 16.04 amd64
They are staticaly link but for libc

rcoscali@srjlx0001:/usr/local/src/misc/mkbootimg-binaries$ ldd mkbootimg 
	linux-vdso.so.1 =>  (0x00007ffc7156d000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f1d31693000)
	/lib64/ld-linux-x86-64.so.2 (0x0000557786130000)
rcoscali@srjlx0001:/usr/local/src/misc/mkbootimg-binaries$ 

