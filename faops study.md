# 一、软件介绍
faops是一款针对fasta或fasta q文件格式的一款软件，以C语言为编写语言，可以在Windows环境和Linux环境运行。对于生物相关领域的工作者来说，faops是一款不可缺少的、简单且实用的基因组学统计分析软件。
# 二、软件安装
可以用 brew直接安装，命令如下：
...brew install wang-q/tap/faops
也可下载软件包后，在运行系统环境自行编译：
...git clone https://github.com/wang-q/faops
...cd faops
...make
#三、软件详情：可用help命令可以获得本软件的基本信息和各命令使用说明
用法：faops help
实例：
输入：$ faops help
输出：
Usage:     faops <command> [options] <arguments>
Version:   0.8.21

Commands:
    help           print this message
    count          count base statistics in FA file(s)
    size           count total bases in FA file(s)
    masked         masked (or gaps) regions in FA file(s)
    frag           extract sub-sequences from a FA file
    rc             reverse complement a FA file
    one            extract one fa record
    some           extract some fa records
    order          extract some fa records by the given order
    replace        replace headers from a FA file
    filter         filter fa records
    split-name     splitting by sequence names
    split-about    splitting to chunks about specified size
    n50            compute N50 and other statistics
    dazz           rename records for dazz_db
    interleave     interleave two PE files
    region         extract regions from a FA file

Options:
    There're no global options.
    Type "faops command-name" for detailed options of each command.
    Options *MUST* be placed just after command.
#四、软件功能：
1.【count】统计一个或多个基因序列文件中不同序列的总长和各碱基数量
用法：faops count <in.fa> [more_files.fa]
实例：
输入：faops count a1.fa
a1.fa文件内容:
>read0
ATGATGGGGtcatgCCCTCagctggcgggcggctatttattcg
>read1
cattcgAGTCATGCTatgcatgctagctCTAGCTAGCTAGCTGATCGATGCTAGCTAGCTGTGCTAGCTAGTCGTAGCTACGATCGATCGATGCAGCTAGC
>read2
TCGTAGCcgtacgtaTTGCCAACCGGCGGCGAAACTGGCGCGCGCTACGATGCTAGCTAttattacattcttcGCTCGCGATGCTAGCACGTAcggttgcgt
输出：
#seq    len     A       C       G       T       N
read0   43      6       10      15      12      0
read1   101     22      25      26      28      0
read2   102     19      29      28      26      0
total   246     47      64      69      66      0
2.【size】统计序列全长
用法：faops size <in.fa> [more_files.fa]
实例：
输入：faops size a1.fa a2.fa
a1.fa文件内容与1中相同 
a2.fa文件内容：
>read3
actgtgtgaTGTGTGCGAGCAGCGAGCAGCTAGCGATGCATGC
>read4
cgcatgctgAGCTTTTTTTTATGTAGCTGATCGGACTGATCGATGCTAGCTGATGCTAGCTAGCGTAGCTAGCTGATCGATGCAGCGAGCGATGCTAGCGATGCGATCGTAC
>read5
AGCTGATCGATCGATCGTAGCTAcgatcgtagctagctagctgatcgaCGATCGTGATCGTA
输出：
read0   43
read1   101
read2   102
read3   43
read4   112
read5   62
3.【masked】屏蔽(或跳过)fasta文件中的区域
用法：faops masked [options] <in.fa> [more_files.fa]
可选：
-g      只记录N/n的区域
实例：
输入：faops masked -g a4.fa
a4.fa文件内容：
>read8
ATGTCGTAGCTAGNCATGntacgnnctagctgactagctagcCNNNNATTTGGCCGATCGACT
输出：
read8:14
read8:19
read8:24-25
read8:44-47
4.【frag】提取指定片段
若一个序列文件中包含多个序列，则只对第一个序列进行指定位置的提取操作。可以通过手动添加参数-l来指定提出片段的分行宽度。
用法：	faops frag [options] <in.fa> start end <out.fa>
选项：	-l	INT		输出时序列分行的宽度，默认为80
实例：
输入：faops frag -l 10 a1.fa 11 40 a1-out.fa 
a1.fa文件内容与4中相同 
输出：
>read0:11-40
catgCCCTCa
gctggcgggc
ggctatttat
5.【rc】获得反向或互补序列
用法：faops rc [options] <in.fa> <out.fa>
选项：
-n			保持序列名相同（不添加RC_）
-r			仅反向，添加R_
-c			仅互补，添加C_
-f 	  STR	仅对给定列表中的序列进行反向或互补操作
-l	  INT   输出时序列分行的宽度，默认为80
实例：
输入：faops rc -l 20 -n -f a3.fa a2.fa out.fa
a2.fa文件内容与4中相同 
a3.fa文件内容：
>read 6
ATTTTTTCGGGGGGGGGATCCTGttgctGGgggacgggagctagCGTGTACGGGGGGT
>read 7
acgtGGGGGAGTGTGTGCTGACTGCGATGGGGGCAcaagtggggcgattttTTCACGGGGGgTGCAAAAAGgtagctga
输出：
>read3
actgtgtgaTGTGTGCGAGC
AGCGAGCAGCTAGCGATGCA
TGC
>read4
cgcatgctgAGCTTTTTTTT
ATGTAGCTGATCGGACTGAT
CGATGCTAGCTGATGCTAGC
TAGCGTAGCTAGCTGATCGA
TGCAGCGAGCGATGCTAGCG
ATGCGATCGTAC
>read5
AGCTGATCGATCGATCGTAG
CTAcgatcgtagctagctag
ctgatcgaCGATCGTGATCG
TA
6.【one】只提取一个fa序列
用法：faops some [options] <in.fa> <name> <out.fa>
选项:
-l	INT		输出时序列分行的宽度，默认为80
实例：
输入：faops one a1.fa read1 out.fa
a1.fa文件内容同1
输出：
>read1
cattcgAGTCATGCTatgcatgctagctCTAGCTAGCTAGCTGATCGATGCTAGCTAGCTGTGCTAGCTAGTCGTAGCTA
CGATCGATCGATGCAGCTAGC
7.【some】按照提供的序列名列表提取指定的序列
用法：faops some [options] <in.fa> <list.file> <out.fa>
选项：
-i			反向提取名称不在列表中的序列
-l	INT		输出时序列分行的宽度，默认为80
实例：
输入：faops some -i -l 20 out.fa list.file out1.fa 
out.fa文件内容与4中相同
list.file文件内容：
>read4
输出：
>read3
actgtgtgaTGTGTGCGAGC
AGCGAGCAGCTAGCGATGCA
TGC
>read5
AGCTGATCGATCGATCGTAG
CTAcgatcgtagctagctag
ctgatcgaCGATCGTGATCG
TA
8.【order】给定顺序重排序列
本功能类似some，但内存调度策略存在差别：order会一次性占用更高的内存。
用法：	faops order [options] <in.fa> <list.file> <out.fa>
选项：
-l	INT		输出时序列分行的宽度，默认为80
实例：
输入：
$ faops order -l 20 a1.fa \
 	<(faops size a1.fa | sort -n -r -k2,2 | cut -f 1) \
out.fa  
a1.fa文件内容与1中相同。
此处列表为根据序列长度由大到小的排列。从而实现了根据长短顺序重排序列。
输出：out.fa文件内容：
>read2
TCGTAGCcgtacgtaTTGCC
AACCGGCGGCGAAACTGGCG
CGCGCTACGATGCTAGCTAt
tattacattcttcGCTCGCG
ATGCTAGCACGTAcggttgc
gt
>read1
cattcgAGTCATGCTatgca
tgctagctCTAGCTAGCTAG
CTGATCGATGCTAGCTAGCT
GTGCTAGCTAGTCGTAGCTA
CGATCGATCGATGCAGCTAG
C
>read0
ATGATGGGGtcatgCCCTCa
gctggcgggcggctatttat
tcg
9.【replace】对特定序列名的替换。也可以仅对指定序列的提取、改名并输出。
用法：faops replace [options] <in.fa> <replace.tsv> <out.fa>
选项：	
-s			仅将存在于列表里面的序列改名后输出到指定位置
-l	INT		输出时序列分行的宽度，默认为80
实例：
输入：
$ faops replace -l 30 -s a1.fa replace.tsv out.fa
in1.fa文件内容与1中相同。
replace.tsv文件内容：
read1	read2
read2	read1
输出：
out.fa文件内容：
>read2
cattcgAGTCATGCTatgcatgctagctCT
AGCTAGCTAGCTGATCGATGCTAGCTAGCT
GTGCTAGCTAGTCGTAGCTACGATCGATCG
ATGCAGCTAGC
>read1
TCGTAGCcgtacgtaTTGCCAACCGGCGGC
GAAACTGGCGCGCGCTACGATGCTAGCTAt
tattacattcttcGCTCGCGATGCTAGCAC
GTAcggttgcgt
10.【filter】进行长度条件筛选。
用法：faops filter [options] <in.fa> <out.fa>
选项：	
-a	INT		设置序列大小的下限
-z	INT		设置序列大小的上限
-n	INT		设置N出现次数的上限
-u			去除名称重复的序列，保留第一个
-U			将序列统一转为大写
-b			将同一序列取消换行且合并为一行
-N			将IUPAC模糊碱基用N替换
-s 			将序列名简化
-l	INT		输出时序列分行的宽度，默认为80
实例：
输入：
$ faops filter -l 30 -a 20 -z 200 -U a2.fa out.fa  
in2.fa文件内容与3.3中相同。
输出：
out.fa文件内容：
>read3
ACTGTGTGATGTGTGCGAGCAGCGAGCAGC
TAGCGATGCATGC
>read4
CGCATGCTGAGCTTTTTTTTATGTAGCTGA
TCGGACTGATCGATGCTAGCTGATGCTAGC
TAGCGTAGCTAGCTGATCGATGCAGCGAGC
GATGCTAGCGATGCGATCGTAC
>read5
AGCTGATCGATCGATCGTAGCTACGATCGT
AGCTAGCTAGCTGATCGACGATCGTGATCG
TA
11.【split-name】按序列名称对序列数据文件进行切割
用法：faops split-name [options] <in.fa> <outdir>
选项：	
-l	INT		输出时序列分行的宽度，默认为80
实例：
输入：faops split-name -l 20 a1.fa out.fa a.a
a1.fa文件内容同1
输出：
read3.fa文件内容：
>read3
actgtgtgaTGTGTGCGAGCAGCGAGCAGCTAGCGATGCATGC
输出：read4.fa文件内容：
>read4
cgcatgctgAGCTTTTTTTTATGTAGCTGATCGGACTGATCGATGCTAGCTGATGCTAGCTAGCGTAGCTAGCTGATCGA
TGCAGCGAGCGATGCTAGCGATGCGATCGTAC
输出：read5.fa文件内容：
>read5
AGCTGATCGATCGATCGTAGCTAcgatcgtagctagctagctgatcgaCGATCGTGATCGTA
12.【split-about】按byte大小对序列数据文件进行切割
用法：faops split-about [options] <in.fa> <approx_size> <outdir>
选项：
-e			每个分割文件的序列要均匀
-m	INT		最多分割的份数
-l	INT		输出时序列分行的宽度，默认为80		
实例：
输入：faops split-about -m 2 a2.fa 20 . 
a2.fa文件内容同2
输出：
000.fa文件内容：
>read3
actgtgtgaTGTGTGCGAGCAGCGAGCAGCTAGCGATGCATGC
001.fa文件内容：
>read4
cgcatgctgAGCTTTTTTTTATGTAGCTGATCGGACTGATCGATGCTAGCTGATGCTAGCTAGCGTAGCTAGCTGATCGA
TGCAGCGAGCGATGCTAGCGATGCGATCGTAC
13.【n50】计算N50及其他一些统计值，N50类似于长度的平均值或中值，但对于较长的contig具有更重要的意义。
用法：faops n50 [options] <in.fa> [more_files.fa]
选项：
-H			只返回统计值，没有统计值名
-N	INT		计算Nx统计值，默认值50
-S			同时显示碱基总数
-A			同时显示平均碱基数
-E			计算E值大小
-C			同时显示总序列数
-g	INT		选取基因序列大小，而不是使用全部文件数据
实例：
输入：
$ faops n50 -S -A -E -C a2.fa
a2.fa文件内容同2
输出：
N50     112
S       217
A       72.33
E       84.04
C       3
14.【dazz】对序列信息命名进行新的标准化，重复名称的序列仅保留第一个。
用法：faops dazz [options] <in.fa> <out.fa>
选项：	
-p	STR	设置新的序列名前缀，默认为read
-s	INT		起始序号，默认为1
-a			不删除重复序列
-l	INT		输出时序列分行的宽度，默认为80
实例：
输入：
faops dazz -l 30 -p test a1.fa out.fa  
输出：
>test/1/0_43
ATGATGGGGtcatgCCCTCagctggcgggc
ggctatttattcg
>test/2/0_101
cattcgAGTCATGCTatgcatgctagctCT
AGCTAGCTAGCTGATCGATGCTAGCTAGCT
GTGCTAGCTAGTCGTAGCTACGATCGATCG
ATGCAGCTAGC
>test/3/0_102
TCGTAGCcgtacgtaTTGCCAACCGGCGGC
GAAACTGGCGCGCGCTACGATGCTAGCTAt
tattacattcttcGCTCGCGATGCTAGCAC
GTAcggttgcgt
15.【interleave】将双端测序的两个文件交错合并。可以只输入一个文件，此时另一端会以N为序列内容。
用法：faops interleave [options] <R1.fa> [R2.fa]
选项：	
-p	STR	设置新的序列名前缀，默认为read
-s	INT		起始序号，默认为0
-l	INT		输出时序列分行的宽度，默认为0
实例：
输入：
faops interleave -p test a2.fa
a2.fa文件内容同2
输出：
>test0/1
actgtgtgaTGTGTGCGAGCAGCGAGCAGCTAGCGATGCATGC
>test0/2
N
>test1/1
cgcatgctgAGCTTTTTTTTATGTAGCTGATCGGACTGATCGATGCTAGCTGATGCTAGCTAGCGTAGCTAGCTGATCGATGCAGCGAGCGATGCTAGCGATGCGATCGTAC
>test1/2
N
>test2/1
AGCTGATCGATCGATCGTAGCTAcgatcgtagctagctagctgatcgaCGATCGTGATCGTA
>test2/2
N
16.【region】在一个FASTA文件中摘取一个或多个指定的序列片段。
本功能可以使用参数标注摘取的正反方向。
用法：	
faops region [options] <in.fa> <region.txt> <out.fa>
选项：	
-s			在序列名中标注提取方向
-l	INT		输出时序列分行的宽度，默认为80
实例：
输入：
$ faops region -s -l 30 a1.fa region.txt out.fa
region.txt文件内容:
read0:1-10,15-16
read1:1-10
a1.fa文件内容同1
输出：
>read0(+):1-10
ATGATGGGGt
>read0(+):15-16
CC
>read1(+):1-10
cattcgAGTC
#四、小结
Faops软件是南京大学生科院王强老师编写的一款软件，可以帮助生物工作者更好更高效的处理信息。且该软件是开源的，可以从https://github.com/wang-q/faops下载安装。


















