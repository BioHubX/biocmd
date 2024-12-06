# biocmd

一个简单易用的biolab客户端工具

#### 前置条件 - 本地化部署biolab服务端
```text
docker run -d --network=host -v .:/root biohubx/biolab-server:v0.1.0  (始终保持升级到最新版)
```

#### 一、安装biolab客户端
```
pip install biocmd
```

#### 二、使用示例

> 2.1 创建工作流

```commandline
biocmd workflow create --name "单菌分析流程" --code singlestrain --step qcstat
biocmd workflow create --name "单菌分析流程" --code singlestrain --step dataassembly --prestep "qcstat"
```
###### 定义名为"单菌分析流程"包含了两个分析步骤["qcstat","dataassembly"]的新工作流[singlestrain]，其中dataassembly流程需要依赖qcstat分析流程的结果


> 2.2 创建工作流算法

```commandline
biocmd script create --workflow singlestrain --step qcstat --script biohubx/qcstat:v0.1.0
biocmd script create --workflow singlestrain --step dataassembly --script biohubx/dataassembly:v0.1.0
```
###### 创建工作流中qcstat、dataassembly对应的算法，其中qcstat步骤的算法为biohubx/qcstat:v0.1.0,其中dataassembly步骤的算法为biohubx/dataassembly:v0.1.0

> 2.3 创建算法的输入及输出

```commandline
biocmd env create --workflow singlestrain --step qcstat --type Input --key read1 --feature _R1.fq.gz
biocmd env create --workflow singlestrain --step qcstat --type Input --key read2 --feature _R2.fq.gz
biocmd env create --workflow singlestrain --step qcstat --type Output --key file1 --feature qcstat.txt
```
###### 工作流中的qcstat算法需要依赖file1和file2两个输入参数，分析结果是一个qcstat.txt文件，其中及Output模板中的qcstat.txt可用于自动检查是否完成了分析任务

> 2.4 从本地注册样本数据
        
```commandline
biocmd file create --basedir /workspace/20241205
```
###### 从biocmd客户端所在机器的[/workspace/20241205]根路径开始扫描原始测序数据，其中根路径的文件结构应该满足以下格式要求：假设在/workspace/20241205/路径下有两个样本： sample1和sample2，则文件结构如下：
```text
/workspace/20241205/
    -- sample1
        -- unique1
            -- sample1_R1.fq.gz
            -- sample1_R2.fq.gz
    -- sample2
        -- unique2
            -- sample2_R1.fq.gz
            -- sample2_R2.fq.gz
    更多其他样本...
    
    （sample名称可以与unique一样）
    sample1的完整路径视图：
        /workspace/20241205/sample1/unique1/sample1_R1.fq.gz
        /workspace/20241205/sample1/unique1/sample1_R2.fq.gz
    sample2的完整路径视图：
        /workspace/20241205/sample2/unique2/sample2_R1.fq.gz
        /workspace/20241205/sample2/unique2/sample2_R2.fq.gz
```
> 2.5 创建分析任务

```commandline
biocmd task create --workflow singlestrain --step qcstat --uniqueno unique1
```
###### 为sample1样本创建qcstat分析任务（unique1为sample1的唯一识别码） 