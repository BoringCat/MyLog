### LEDE编译的问题与解决方案
(或可用于OpenWrt)  

**本文的所有问题都是在ArchLinux上编译LEDE时遇到的**  
+ **./stage1flex -o stage1scan.c ./scan.l**  
默认情况下执行 `make V=s` 会出现以下情况
>...  
./stage1flex -o stage1scan.c ./scan.l  
...  
_make fail_

没有任何错误提示，Google完全没有答案(￣△￣；)  
经过千辛万苦终于找到解决方案：  
将同一目录下的 `200-build-AC_USE_SYSTEM_EXTENSIONS-in-configure.ac.patch` 复制到 `tools/flex/patches/` 下  
> cp 200-build-AC\_USE\_SYSTEM\_EXTENSIONS-in-configure.ac.patch _$CodePath_/tools/flex/patches/
