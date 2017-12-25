ubuntu下查看cpu信息

查看cpu型号

cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
2  Intel(R) Core(TM)2 Duo CPU     P8600  @ 2.40GHz
(看到有2个逻辑CPU, 也知道了CPU型号)

查看cpu运行模式
getconf LONG_BIT

32

(说明当前CPU运行在32bit模式下, 但不代表CPU不支持64bit)

查看cpu信息概要
lscpu

Architecture:          i686                            #架构686
CPU(s):                2                                   #逻辑cpu颗数是2
Thread(s) per core:    1                           #每个核心线程数是1
Core(s) per socket:    2                           #每个cpu插槽核数/每颗物理cpu核数是2
CPU socket(s):         1                            #cpu插槽数是1
Vendor ID:             GenuineIntel           #cpu厂商ID是GenuineIntel
CPU family:            6                              #cpu系列是6
Model:                 23                                #型号23
Stepping:              10                              #步进是10
CPU MHz:               800.000                 #cpu主频是800MHz
Virtualization:        VT-x                         #cpu支持的虚拟化技术VT-x(对此在下一博文中解释下http://hi.baidu.com/sdusoul/blog/item/5d8e0488def3a998a5c272c0.html)
L1d cache:             32K                         #一级缓存32K（google了下，这具体表示表示cpu的L1数据缓存为32k）
L1i cache:             32K                          #一级缓存32K（具体为L1指令缓存为32K）
L2 cache:              3072K                      #二级缓存3072K


最后来个大而全的：

cat /proc/cpuinfo

processor    : 0
vendor_id    : GenuineIntel
cpu family    : 6
model        : 23
model name    : Intel(R) Core(TM)2 Duo CPU     P8600  @ 2.40GHz
stepping    : 10
cpu MHz        : 800.000
cache size    : 3072 KB
physical id    : 0
siblings    : 2
core id        : 0
cpu cores    : 2
apicid        : 0
initial apicid    : 0
fdiv_bug    : no
hlt_bug        : no
f00f_bug    : no
coma_bug    : no
fpu        : yes
fpu_exception    : yes
cpuid level    : 13
wp        : yes
flags        : fpu vme de pse tsc msr pae mce cx8 apic mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe nx lm constant_tsc arch_perfmon pebs bts pni dtes64 monitor ds_cpl vmx smx est tm2 ssse3 cx16 xtpr pdcm sse4_1 xsave lahf_lm ida tpr_shadow vnmi flexpriority
bogomips    : 4788.60
clflush size    : 64
power management:



