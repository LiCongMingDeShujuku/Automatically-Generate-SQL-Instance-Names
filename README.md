![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 自动创建实例名称
### Automatically Generate SQL Instance Names
****发布-日期: 2016年10月15日 (评论)**

![##############](images/image0012.png?raw=true "#######")

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
这是一些用于获取所有实例名称的列表的SQL逻辑，并自动创建下一个实例名称。


在这种环境中，我有几个SQL Server安装程序。我想自动获取下一个实例名称，以便将其放入构建脚本中并从安装和配置下一个实例的作业中运行。

这是现有SQL实例的列表。我正在使用SQLSHARE *作为多实例数据库服务器中的所有实例。



## English
Here’s some SQL Logic to get a list of all instance names, and automatically create the next instance name.
In this environment I have several installations of SQL Server. I want to get the next instance name automatically so I can throw that into a build script and run that from a Job which installs and configures the next instance.
Here’s a list of the existing SQL Instances. I’m using the SQLSHARE* as for all the instances in the multi-instance database server.


![#](images/image0012.png?raw=true "#")

我已经有一个SQL Server的默认实例：MSSQLSERVER，然后我还有几个实例：
SQLSHARE01
SQLSHARE02
SQLSHARE03
SQLSHARE04
SQLSHARE05…

I have a default instance already for SQL Server: MSSQLSERVER, then I have several more instances called:
SQLSHARE01
SQLSHARE02
SQLSHARE03
SQLSHARE04
SQLSHARE05…
What I want to do is get the next Instance Name automatically so I use this little piece of logic to produce the next name in the list.

我想要做的是自动获取下一个实例名称，所以我使用这个小逻辑来生成列表中的下一个名称。


![#](images/mikesdatawork_multi_instance_sql_server1.png?raw=true "#")


---
## Logic
```SQL

use master;
set nocount on
 
-- create table to hold instance names --创建表来保存实例名称

declare @sql_instances table
(
    [id]        int identity(0,1)
,   [rootkey]   nvarchar(255)
,   [sql_instances] nvarchar(255)
,   [value]     nvarchar(255)
)
 
-- get instances names using xp_regread  --使用xp_regread获取实例名称

insert into @sql_instances ([rootkey], [sql_instances], [value])
execute xp_regread
    @rootkey    = 'hkey_local_machine'
,   @key        = 'software\microsoft\microsoft sql server'
,   @value_name = 'installedinstances'
 
select [sql_instances] from @sql_instances
 
-- set prefix name for multi-instance environment aka: Shared SQL Environment "SQLSHARE"   --设置多实例环境的前缀名称：共享SQL环境“SQLSHARE”

-- produce the next instance name.   --生成下一个实例名称
declare @prefix     varchar(255) = 'SQLSHARE'
declare @next_instance  varchar(255) = 
(
select
    case   
        when cast(right(max([sql_instances]), 2) as int) < 10 then @prefix + '0' + cast(cast(right(max([sql_instances]), 2) as int) + 1 as varchar)
        else @prefix + cast(cast(right(max([sql_instances]), 2) as int) + 1 as varchar)
    end as [sql_instances]
from
    @sql_instances si
where
    si.[id] > 0
)
 
select (@next_instance)

```

这是结果：
SQLSHARE06
SQLSHARE06代表下一个in-line实例名称。我将把它用于构建脚本，这样就可以通过运行一个作业自动执行。

Here’s the result:
SQLSHARE06
SQLSHARE06 naturally represents the next in-line Instance Name. I’ll work this into a build script so this can be carried out automatically by running a Job.

---


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

