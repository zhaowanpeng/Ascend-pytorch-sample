# Ascend-pytorch-sample
## 华为Ascend部署pytorch模型



#### 步骤综述

1. 获取ATC工具
2. 将pytorch输出为onnx
3. 编写AIPP配置
4. 通过atc转om
5. 基于pyACL运行依赖，编写用例
6. 推理

#### 1、获取ATC工具：

1. ##### 下载软件包

   Ascend-cann-toolkit*_{version}*_linux-*{arch}*.run  

   链接：https://www.hiascend.com/software/cann/community（社区版）

   *{version}*表示软件版本号，*{arch}*表示CPU架构。

   ```shell
   arch#查看cpu架构
   ```

2. ##### 检查root用户的umask

   1. 检查root用户的umask值。

      ```
      umask
      ```

   2. 如果umask不等于0022，请执行如下操作配置，在该文件的最后一行添加umask 0022后保存。

      1. 在任意目录下执行如下命令，打开.bashrc文件：在文件最后一行后面添加umask 0022内容。

         ```
         vi ~/.bashrc 
         ```

      2. 执行**:wq!**命令保存文件并退出。

      3. 执行**source ~/.bashrc**命令使其立即生效。

3. ##### 安装os依赖

   参考链接：https://support.huaweicloud.com/instg-cli-cann/atlascli_03_0154.html

4. ##### 安装开发套件

   1. 将获取到的开发套件包上传到安装环境任意路径（如“/home/package”）。

   2. 进入软件包所在路径。

   3. 增加对软件包的可执行权限。

      **chmod +x** *****.run**

   4. 执行如下命令校验软件包安装文件的一致性和完整性。

      **./\*.run --check**

   5. 执行以下命令安装软件。

      **./\*.run --install**

5. ##### 设置环境变量

   **必选环境变量**（如下环境变量中${install_path}以开发套件包Ascend-cann-toolkit使用默认安装路径/usr/local/Ascend/ascend-toolkit/latest为例进行说明）

   ```shell
   export PATH=/usr/local/python3.7.5/bin:${install_path}/atc/ccec_compiler/bin:${install_path}/atc/bin:$PATH
   export PYTHONPATH=${install_path}/atc/python/site-packages:${install_path}/atc/python/site-packages/auto_tune.egg/auto_tune:${install_path}/atc/python/site-packages/schedule_search.egg:$PYTHONPATH
   export LD_LIBRARY_PATH=${install_path}/atc/lib64:$LD_LIBRARY_PATH
   export ASCEND_OPP_PATH=${install_path}/opp
   ```

#### 4、通过atc转om

```shell
atc 
--model="./model.onnx" 
--framework=5
--input_shape="input_1:1,224,224,3" #input_name需要用Netron查看，batch-1为动态批大小 
--input_format=NHWC
--insert_op_conf=insert_op.cfg 
--output="out_put_name"
--output_type=FP32 
--soc_version=Ascend310 
```

##### 5、基于pyACL运行依赖，编写用例
