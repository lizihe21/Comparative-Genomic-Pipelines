# 简介
Cactus是一个使用progressive策略进行多重全基因组比对工具。简单的说，对于一组已知系统发育关系的基因组，将最近缘的两物种互相比对，构建祖先序列，重复这个过程直至构建出整个树的共同祖先。这个策略的优势在于不受参考基因组限制， 同时也能节省计算资源。劣势在于在远源物种的比对实际上是构建出的祖先基因组比对而来，如果构建祖先基因时出问题，会影响所有上游比对的的效果。
# 安装
安装可以选择docker镜像或者是官方repositry下载二进制包。我一般选择下载二进制包，地址在这里https://github.com/ComparativeGenomicsToolkit/cactus/releases
安装过程可以参考release的BIN-INSTALL.md,我习惯使用conda 构建虚拟环境进行:
```
wget https://github.com/ComparativeGenomicsToolkit/cactus/releases/download/v2.9.2/cactus-bin-v2.9.2.tar.gz
tar -xzf cactus-bin-v2.9.2.tar.gz
cd cactus-bin-v2.9.2
conda create -n cactus pip
pip install -U setuptools pip wheel
pip install -U .
pip install -U -r ./toil-requirement.txt
```
这里主体就安装完成了。一些工具需要使用UCSC kent tools，可以使用bioconda安装，用可以从UCSC下载:
```
cd bin && for i in wigToBigWig faToTwoBit bedToBigBed bigBedToBed axtChain pslPosTarget bedSort hgGcPercent mafToBigMaf hgLoadMafSummary hgLoadChain; do wget -q http://hgdownload.cse.ucsc.edu/admin/exe/linux.x86_64/${i}; chmod +x ${i}; done
```


# 输入文件
Cactus的输入文件主要有两部分，一个是基因组，另一个是带枝长的物种树。将树和基因组名字和路径写入一个输入文件中即可。这里我们命名为"cactus.input":
```
((camel:0.024449399999999996,(pig:0.026929899999999993,((xilu:0.0318519,((pronghorn:0.011900899999999999,(giraffe:0.004092180000000001,okapi:0.006236209999999999):0.004874759999999999):7.061509999999986E-4,((muskdeer:0.009617210000000001,(cattle:0.007105599999999997,(sheep:0.0023832800000000015,goat:0.0023733999999999977):0.0058297):0.0019783300000000004):9.002019999999993E-4,(((reev_muntjac:0.0041239,ECEP:0.003908189999999999):9.83137000000002E-4,(TUNLU:0.0027520899999999987,(TL:0.002494690000000001,((mhl:7.166099999999995E-4,ML:7.621969999999992E-4):9.152210000000008E-4,PL:0.0019297900000000007):4.7706499999999874E-4):2.276429999999996E-4):0.0012576899999999988):9.57613000000003E-4,((HJ:0.0041008699999999995,PZ:0.0041008699999999995):0.002634530000000003,(mule_deer:0.0028536499999999992,XL:0.00281675):0.0021085500000000007):9.272520000000034E-4):0.005689299999999998):8.850460000000004E-4):0.011183300000000004):0.010619599999999993,(hippo:0.0192975,(gray_whale:0.007563460000000001,killer_whale:0.009708890000000005):0.010820759999999999):0.002187310000000005):0.004391329999999999):0.0033151899999999956):0.0162236,tapir:0.0162236);
camel /public3/home/a6s000219/lizihe/01.data/01.genome/changename/camel.fa
cattle /public3/home/a6s000219/lizihe/01.data/01.genome/changename/cattle.fa
ECEP /public3/home/a6s000219/lizihe/01.data/01.genome/luke_mask/ECEP.fa
giraffe /public3/home/a6s000219/lizihe/01.data/01.genome/changename/giraffe.fa
goat /public3/home/a6s000219/lizihe/01.data/01.genome/changename/goat.fa
gray_whale /public3/home/a6s000219/lizihe/01.data/01.genome/changename/gray_whale.fa
hippo /public3/home/a6s000219/lizihe/01.data/01.genome/changename/hippo.fa
HJ /public3/home/a6s000219/lizihe/01.data/01.genome/luke_mask/HJ.fa
killer_whale /public3/home/a6s000219/lizihe/01.data/01.genome/changename/killer_whale.fa
mhl /public3/home/a6s000219/lizihe/01.data/01.genome/changename/mhl.fa
ML /public3/home/a6s000219/lizihe/01.data/01.genome/luke_mask/ML.fa
mule_deer /public3/home/a6s000219/lizihe/01.data/01.genome/changename/mule_deer.fa
muskdeer /public3/home/a6s000219/lizihe/01.data/01.genome/changename/muskdeer.fa
okapi /public3/home/a6s000219/lizihe/01.data/01.genome/changename/okapi.fa
pig /public3/home/a6s000219/lizihe/01.data/01.genome/changename/pig.fa
PL /public3/home/a6s000219/lizihe/01.data/01.genome/luke_mask/PL.fa
pronghorn /public3/home/a6s000219/lizihe/01.data/01.genome/changename/pronghorn.fa
PZ /public3/home/a6s000219/lizihe/01.data/01.genome/luke_mask/PZ.fa
reev_muntjac /public3/home/a6s000219/lizihe/01.data/01.genome/changename/reev_muntjac.fa
sheep /public3/home/a6s000219/lizihe/01.data/01.genome/changename/sheep.fa
tapir /public3/home/a6s000219/lizihe/01.data/01.genome/changename/tapir.fa
TL /public3/home/a6s000219/lizihe/01.data/01.genome/luke_mask/TL.fa
TUNLU /public3/home/a6s000219/lizihe/01.data/01.genome/luke_mask/TUNLU.fa
xilu /public3/home/a6s000219/lizihe/01.data/01.genome/changename/xilu.fa
XL /public3/home/a6s000219/lizihe/01.data/01.genome/luke_mask/XL.fa
```
# 运行Cactus
运行Cactus主要有三种选择，一种是直接使用内置的TOIL任务管理器运行，适用于小规模数据集在本地跑或者SLURM集群（v2.61以后，未测试）。第二种是生成WDL，使用cromwell等任务管理器运行，可以适用于大部分集群系统
