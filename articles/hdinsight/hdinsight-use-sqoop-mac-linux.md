---
title: "在基于 Linux 的 HDInsight 中使用 Hadoop Sqoop | Microsoft Docs"
description: "了解如何在 HDInsight 群集上基于 Linux 的 Hadoop 和 Azure SQL 数据库之间运行 Sqoop 导入和导出。"
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 32e86b0c3e7c0091b1a0510aa682419d2d030dd8
ms.lasthandoff: 03/25/2017


---
# <a name="use-sqoop-with-hadoop-in-hdinsight-ssh"></a>将 Sqoop 与 HDInsight 中的 Hadoop 配合使用 (SSH)
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

了解如何使用 Sqoop 在 HDInsight 群集和 Azure SQL 数据库或 SQL Server 数据库之间进行导入和导出。

> [!IMPORTANT]
> 本文档中的步骤仅适用于使用 Linux 的 HDInsight 群集。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight Deprecation on Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)（HDInsight 在 Windows 上即将弃用）。

## <a name="prerequisites"></a>先决条件
开始阅读本教程前的必要准备：

* **HDInsight 中的 Hadoop 群集**和 **Azure SQL 数据库**：本文档中的步骤假设已根据[创建群集和 SQL 数据库](hdinsight-use-sqoop.md#create-cluster-and-sql-database)文档创建了群集和数据库。 如果你已有一个 HDInsight 群集和 SQL 数据库，可以使用它们替代本文档中的值。
* **工作站**：使用 SSH 客户端的计算机。

## <a name="install-freetds"></a>安装 FreeTDS
1. 使用 SSH 连接到基于 Linux 的 HDInsight 群集。 连接时要使用的地址为 `CLUSTERNAME-ssh.azurehdinsight.net`，端口为 `22`。

    有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

2. 使用以下命令安装 FreeTDS：

        sudo apt-get --assume-yes install freetds-dev freetds-bin

    将在多个步骤中使用 FreeTDS 连接到 SQL 数据库。

## <a name="create-the-table-in-sql-database"></a>在 SQL 数据库中创建表
> [!IMPORTANT]
> 如果所用的 HDInsight 群集和 SQL 数据库是使用[创建群集和 SQL 数据库](hdinsight-use-sqoop.md)中的步骤创建的，请忽略本部分中的步骤，因为在执行该文档中的步骤时已创建了数据库和表。
>
>

1. 通过 SSH 连接到 HDInsight 后，使用以下命令连接到 SQL 数据库服务器，并创建要在这些余下步骤中使用的表：

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    你将收到如下输出：

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>
2. 在 `1>` 提示符下，输入以下行：

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
        GO

    输入 `GO` 语句后，将评估前面的语句。 首先会创建 **mobiledata** 表，然后会向其添加聚集索引（根据 SQL 数据库的要求。）

    使用以下命令验证是否已创建该表：

        SELECT * FROM information_schema.tables
        GO

    你应该会看到与下面类似的输出：

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE
3. 在 `1>` 提示符下输入 `exit` 以退出 tsql 实用工具。

## <a name="sqoop-export"></a>Sqoop 导出
1. 通过 SSH 连接到 HDInsight 后，使用以下命令验证 Sqoop 是否可以看到你的 SQL 数据库：

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    此时会返回数据库列表，其中包括此前创建的 **sqooptest** 数据库。
2. 使用以下命令将数据从 **hivesampletable** 导出到 **mobiledata** 表：

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    此命令将引导 Sqoop 连接到 SQL 数据库和 **sqooptest** 数据库，以及将数据从 **wasbs:///hive/warehouse/hivesampletable**（*hivesampletable* 的物理文件）导出到 **mobiledata**表。
3. 该命令完成后，使用以下命令通过 TSQL 连接到数据库：

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    连接成功以后，使用以下语句验证数据是否已导出到 **mobiledata** 表：

        SELECT * FROM mobiledata
        GO

    你会在表中看到一系列数据。 键入 `exit` 退出 tsql 实用程序。

## <a name="sqoop-import"></a>Sqoop 导入
1. 使用以下语句将数据从 SQL 数据库中的 **mobiledata** 表导入 HDInsight 上的 **wasbs:///tutorials/usesqoop/importeddata** 目录：

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    导入的数据的字段将通过制表符进行分隔，相关行将由换行符终止。
2. 完成导入后，可使用以下命令在新目录中列出这些数据：

        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

## <a name="using-sql-server"></a>使用 SQL Server
你还可以使用 Sqoop 通过 SQL Server 来导入和导出数据，不管是在数据中心进行，还是在托管在 Azure 中的虚拟机上进行。 SQL 数据库和 SQL Server 在使用方面的差异是：

* HDInsight 和 SQL Server 必须位于同一 Azure 虚拟网络上

  > [!NOTE]
  > HDInsight 仅支持基于位置的虚拟网络，并且当前不适用于基于地缘组的虚拟网络。
  >
  >

    在数据中心使用 SQL Server 时，必须将虚拟网络配置为“站点到站点”或“点到站点”。

  > [!NOTE]
  > 对于“点到站点”虚拟网络，SQL Server 必须运行 Azure 虚拟网络配置的“仪表板”中提供的 VPN 客户端配置应用程序。
  >
  >

    有关 Azure 虚拟网络的详细信息，请参阅[虚拟网络概述](../virtual-network/virtual-networks-overview.md)。
* 必须将 SQL Server 配置为允许 SQL 身份验证。 有关详细信息，请参阅[选择身份验证模式](https://msdn.microsoft.com/ms144284.aspx)
* 你可能需要将 SQL Server 配置为接受远程连接。 有关详细信息，请参阅[如何解决 SQL Server 数据库引擎的连接问题](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)
* 必须在 SQL Server 中创建 **sqooptest** 数据库，创建时使用 **SQL Server Management Studio** 或 **tsql** 之类的实用程序 - Azure CLI 的使用步骤仅适用于 Azure SQL 数据库

    用于创建 **mobiledata** 表的 TSQL 语句类似于用于 SQL 数据库的语句，区别在于是否创建聚集索引 - 这对于 SQL Server 来说不是必需的：

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])
* 从 HDInsight 连接到 SQL Server 时，你可能需要使用 SQL Server 的 IP 地址，除非你已配置了域名系统 (DNS) 来解析 Azure 虚拟网络上的名称。 例如：

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

## <a name="limitations"></a>限制
* 批量导出 - 在基于 Linux 的 HDInsight 上，用于将数据导出到 Microsoft SQL Server 或 Azure SQL 数据库的 Sqoop 连接器目前不支持批量插入。
* 批处理 - 在基于 Linux 的 HDInsight 上，如果执行插入时使用 `-batch` 开关，Sqoop 将执行多次插入而不是批处理插入操作。

## <a name="next-steps"></a>后续步骤
现在你已经学习了如何使用 Sqoop。 若要了解更多信息，请参阅以下文章：

* [将 Oozie 与 HDInsight 配合使用][hdinsight-use-oozie]：在 Oozie 工作流中使用 Sqoop 操作。
* [使用 HDInsight 分析航班延误数据][hdinsight-analyze-flight-data]：使用 Hive 分析航班延误数据，然后使用 Sqoop 将数据导出到 Azure SQL 数据库。
* [将数据上传到 HDInsight][hdinsight-upload-data]：了解将数据上传到 HDInsight/Azure Blob 存储的其他方法。

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

