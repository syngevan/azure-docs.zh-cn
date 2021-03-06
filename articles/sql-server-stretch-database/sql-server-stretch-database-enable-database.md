---
title: "为数据库启用 Stretch Database | Microsoft 文档"
description: "了解如何为 Stretch Database 配置数据库。"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 55fc4142-4be9-4664-8ea9-48a5e177838f
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c6d3a81c3c919e50c123cef00dfb3e4a5ab23a44


---
# <a name="enable-stretch-database-for-a-database"></a>为数据库启用 Stretch Database
若要为 Stretch Database 配置现有数据库，请在 SQL Server Management Studio 中选择该数据库对应的“**任务 | 延伸 | 启用**”，以打开“**启用数据库延伸**”向导。 也可以使用 Transact\-SQL 为数据库启用 Stretch Database。

如果你针对单个表选择了“**T任务 | 延伸 | 启用**”但尚未为数据库启用 Stretch Database，该向导将为 Stretch Database 配置数据库，并让你在此过程中选择表。 请遵循本主题中的步骤，而不要遵循[为表启用 Stretch Database](sql-server-stretch-database-enable-database.md) 中的步骤。

对数据库或表启用 Stretch Database 需要有 db\_owner 权限。 对数据库启用 Stretch Database 还需要有 CONTROL DATABASE 权限。

> [!NOTE]
> 若要在以后禁用 Stretch Database，请记住，针对表或数据库禁用 Stretch Database 不会删除远程对象。 若要删除远程表或远程数据库，必须使用 Azure 管理门户。 远程对象在手动删除之前，会持续产生 Azure 费用。
> 
> 

## <a name="before-you-get-started"></a>准备工作
* 在为延伸配置数据库之前，我们建议你运行延伸数据库顾问来识别符合延伸条件的数据库和表。 延伸数据库顾问还可识别阻碍性问题。 有关详细信息，请参阅 [识别符合 Stretch Database 条件的数据库和表](sql-server-stretch-database-identify-databases.md)。
* 请参阅 [Stretch Database 的限制](sql-server-stretch-database-limitations.md)。
* Stretch Database 会将数据迁移到 Azure。 因此，必须拥有一个 Azure 帐户和订阅以供计费。 若要获取 Azure 帐户，请[单击此处](http://azure.microsoft.com/pricing/free-trial/)。
* 使用所需的连接和登录信息创建新的 Azure 服务器，或者选择现有的 Azure 服务器。

## <a name="a-nameenabletsqlserveraprerequisite-enable-stretch-database-on-the-server"></a><a name="EnableTSQLServer"></a>先决条件：在服务器上启用 Stretch Database
在对数据库或表启用 Stretch Database 之前，必须先在本地服务器上启用 Stretch Database。 此操作需要 sysadmin 或 serveradmin 权限。

* 如果你拥有所需的管理权限，则“**启用数据库延伸**”向导将为延伸配置服务器。
* 如果你没有所需的权限，则只有在某位管理员通过运行**sp\_configure** 手动启用该选项之后才可以运行该向导，或者必须由管理员运行该向导。

若要在服务器上手动启用 Stretch Database，请运行 **sp\_configure** 并打开“**远程数据存档**”选项。 以下示例通过将“**远程数据存档**”选项的值设置为 1 来启用该选项。

```
EXEC sp_configure 'remote data archive' , '1';
GO

RECONFIGURE;
GO
```
有关详细信息，请参阅[配置远程数据存档服务器配置选项](https://msdn.microsoft.com/library/mt143175.aspx)和 [sp_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx)。

## <a name="a-namewizardause-the-wizard-to-enable-stretch-database-on-a-database"></a><a name="Wizard"></a>使用向导来对数据库启用 Stretch Database
有关“启用数据库延伸”向导的信息，包括必须输入的信息以及必须选择的选项，请参阅[通过运行“启用数据库延伸”向导开始操作](sql-server-stretch-database-wizard.md)。

## <a name="a-nameenabletsqldatabaseause-transact-sql-to-enable-stretch-database-on-a-database"></a><a name="EnableTSQLDatabase"></a>使用 Transact\-SQL 对数据库启用 Stretch Database
在对各个表启用 Stretch Database 之前，必须先对数据库启用 Stretch Database。

对数据库或表启用 Stretch Database 需要有 db\_owner 权限。 对数据库启用 Stretch Database 还需要有 CONTROL DATABASE 权限。

1. 在开始之前，请选择 Stretch Database 要将数据迁移到的现有 Azure 服务器，或创建一个新的 Azure 服务器。
2. 在该 Azure 服务器上，创建一个允许 SQL Server 与远程服务器通信的防火墙规则，该规则使用 SQL Server 的 IP 地址范围。
   
   你可以通过尝试从 SQL Server Management Studio (SSMS) 的对象资源管理器连接到 Azure 服务器，轻松找到所需的值并创建防火墙规则。 SSMS 通过打开以下对话框（其中已包括所需的 IP 地址值）帮助你创建规则。
   
   ![在 SSMS 中创建防火墙规则][FirewallRule]
3. 若要为 Stretch Database 配置某个 SQL Server 数据库，该数据库必须具有数据库主密钥。 数据库主密钥用于保护 Stretch Database 在连接到远程数据库时所用的凭据。 下面是一个示例，用于创建新的数据库主密钥。
   
   ```tsql
   USE <database>;
   GO
   
   CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>';
   GO
   ```
   
   有关数据库主密钥的详细信息，请参阅 [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) 和[创建数据库主密钥](https://msdn.microsoft.com/library/aa337551.aspx)。
4. 在为 Stretch Database 配置数据库时，必须提供用于在本地 SQL Server 与远程 Azure 服务器之间进行通信的 Stretch Database 凭据。 可以使用两个选项。
   
   * 你可以提供管理员凭据。
     
     * 如果通过运行向导来启用 Stretch Database，则可以在运行向导时创建凭据。
     * 如果你打算通过运行 **ALTER DATABASE** 启用 Stretch Database，则必须在运行 **ALTER DATABASE** 启用 Stretch Database 之前手动创建凭据。
     
     下面是一个示例，用于创建新的凭据。
     
     ```tsql
     CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
         WITH IDENTITY = '<identity>' , SECRET = '<secret>';
     GO
     ```
     
     有关凭据的详细信息，请参阅 [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx)。 创建凭据需要 ALTER ANY CREDENTIAL 权限。
   * 如果符合以下所有条件，则可以使用 SQL Server 的联合服务帐户来与远程 Azure 服务器通信。
     
     * 运行 SQL Server 实例的服务帐户是一个域帐户。
     * 域帐户属于某个域，该域的 Active Directory 与 Azure Active Directory 相联合。
     * 远程 Azure 服务器配置为支持 Azure Active Directory 身份验证。
     * 必须将运行 SQL Server 实例的服务帐户配置为远程 Azure 服务器上的 dbmanager 或 sysadmin 帐户。
5. 若要为 Stretch Database 配置数据库，请运行 ALTER DATABASE 命令。
   
   1. 对于 SERVER 参数，请提供现有 Azure 服务器的名称，包括该名称的 `.database.windows.net` 部分 \- 例如 `MyStretchDatabaseServer.database.windows.net`。
   2. 请提供具有 CREDENTIAL 参数的现有管理员凭据，或指定 FEDERATED\_SERVICE\_ACCOUNT = ON。 以下示例提供了一个现有的凭据。
   
   ```tsql
   ALTER DATABASE <database name>
       SET REMOTE_DATA_ARCHIVE = ON
           (
               SERVER = '<server_name>',
               CREDENTIAL = <db_scoped_credential_name>
           ) ;
   GO
   ```

## <a name="next-steps"></a>后续步骤
* [为表启用 Stretch Database](sql-server-stretch-database-enable-table.md) 来启用其他表。
* 参阅[监视 Stretch Database](sql-server-stretch-database-monitor.md) 来查看数据迁移状态。
* [暂停和恢复 Stretch Database](sql-server-stretch-database-pause.md)
* [Stretch Database 的管理和故障排除](sql-server-stretch-database-manage.md)
* [备份启用了延伸的数据库](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>另请参阅
[标识 Stretch Database 的数据库和表](sql-server-stretch-database-identify-databases.md)

[ALTER DATABASE SET 选项 (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[FirewallRule]: ./media/sql-server-stretch-database-enable-database/firewall.png



<!--HONumber=Nov16_HO3-->


