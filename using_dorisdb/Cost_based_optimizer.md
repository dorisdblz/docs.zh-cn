
# CBO 优化器

## 背景介绍

在 1.16.0 版本，DorisDB推出的新优化器，可以针对复杂 Ad-hoc 场景生成更优的执行计划。DorisDB采用cascades技术框架，实现基于成本（Cost-based Optimizer 后面简称CBO）的查询规划框架，新增了更多的统计信息来完善成本估算，也补充了各种全新的查询转换（Transformation）和实现（Implementation）规则，能够在数万级别查询计划空间中快速找到最优计划。

<br>

## 使用说明

### 启用统计信息自动抽样收集

所以开启新优化器前，需要先开启统计信息收集。启用统计信息自动抽样收集方式：

修改 FE Config ：

~~~Apache
enable_statistic_collect = true
~~~

然后重启FE

<br>

### 查询启用新优化器

> 注意：启用新优化器之前，建议先开启统计信息自动抽样收集 1 ~ 2天。

全局粒度开启

~~~SQL
set global enable_cbo = true;
~~~

Session 粒度开启

~~~SQL
set enable_cbo = true;

~~~

单个 SQL 粒度开启

~~~SQL
SELECT /*+ SET_VAR(enable_cbo = true) */ * from table;
~~~

<br>

### 新优化器结果验证

DorisDB提供一个新旧优化器**对比**的工具，用于回放fe中的audit.log，可以检查新优化器查询结果是否有误，在使用新优化器前，**建议使用DorisDB提供的对比工具检查一段时间**：

1. 下载DorisDB [new\_planner\_test.zip](http://dorisdb-public.oss-cn-zhangjiakou.aliyuncs.com/new_planner_test.zip)
2. 解压后的文件夹中有：jar，config
3. 按照README配置DorisDB的端口地址，FE的http_port，以及用户名密码
4. 使用命令`java -jar new_planner_test.jar $fe.audit.log.path`执行测试
5. 执行的结果会记录在`result`文件夹中，如果在result中存在错误日志，可以将result文件夹打包提交给DorisDB，协助我们修复问题
