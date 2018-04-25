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
