### LEDE编译的问题与解决方案
(或可用于OpenWrt)  

**本文的所有问题都是在ArchLinux上编译LEDE时遇到的**  
+ **停在 ./stage1flex -o stage1scan.c ./scan.l**  
默认情况下执行 `make V=s` 会出现以下情况
```
...  
./stage1flex -o stage1scan.c ./scan.l  
...  
(make fail)
```
没有任何错误提示，Google完全没有答案(￣△￣；)  
经过千辛万苦终于找到解决方案：  
将与此MarkDown文件位于同一目录下的 `200-build-AC_USE_SYSTEM_EXTENSIONS-in-configure.ac.patch` 复制到编译目录的 `tools/flex/patches/` 下  
``` shell
cp 200-build-AC_USE_SYSTEM_EXTENSIONS-in-configure.ac.patch $CodePath/tools/flex/patches/
```

+ **在编译 e2fsprogs/debugfs 时 create_inode.o 报错**  
执行 `make V=s` 到 `making all in debugfs` 后报错：
```
...  
making all in debugfs
make[6]: Entering directory '/home/boringcat/BPI/R2/LEDE/bpi-r2_lede/build_dir/host/e2fsprogs-1.43.5/debugfs'
	CC debug_cmds.c
	CC extent_cmds.c
	CC create_inode.o
./../misc/create_inode.c:399:18: error: conflicting types for 'copy_file_range'
 static errcode_t copy_file_range(ext2_filsys fs, int fd, ext2_file_t e2_file,
                  ^~~~~~~~~~~~~~~
In file included from ./../misc/create_inode.c:19:
/usr/include/unistd.h:1110:9: note: previous declaration of 'copy_file_range' was here
 ssize_t copy_file_range (int __infd, __off64_t *__pinoff,
         ^~~~~~~~~~~~~~~
make[6]: *** [Makefile:423: create_inode.o] Error 1
...
```
Google到这是 glibc 升级到 2.27 后导致的  
查看glibc版本的方法：`ldd --version` 或通过包管理器参看  
解决方案：(来源：[tools/e2fsprogs: fix building on a glibc 2.27 host (58a95f0f) · Commits · XetalKinsei / Xetal7688 / Xetal7688-Lede · GitLab](http://xetal.ddns.net:81/Kinsei/Xetal7688/Xetal7688-Lede/commit/58a95f0f8ff768b43d68eed2b6a786e0f40f723b))  
将与此MarkDown文件位于同一目录下的 `005-misc-rename-copy_file_range-to-copy_file_chunk.patch` 复制到编译目录的 `tools/e2fsprogs/patches/` 下  
``` shell
cp 005-misc-rename-copy_file_range-to-copy_file_chunk.patch $CodePath/tools/e2fsprogs/patches/
```
