



根据你的环境和选择，该脚本可以创建所有群集基础结构，包括 Azure 虚拟网络、存储帐户、云服务、域控制器、远程或本地 SQL 数据库、头节点和其他群集节点。 或者，该脚本可以使用预先存在的 Azure 基础结构仅创建 HPC 群集节点。

有关规划 HPC Pack 群集的背景信息，请参阅 HPC Pack 2012 R2 TechNet 库中的[产品评估和规划](https://technet.microsoft.com/library/jj899596.aspx)及[入门](https://technet.microsoft.com/library/jj899590.aspx)内容。

## <a name="prerequisites"></a>先决条件
* **Azure 订阅**：可以使用 Azure 全球或 Azure 中国区服务中的订阅。 订阅限制会影响可部署的群集节点数目和类型。 有关信息，请参阅 [Azure 订阅和服务限制、配额与约束](../articles/azure-subscription-service-limits.md)。
* **安装并配置了 Azure PowerShell 0.8.10 或更高版本的 Windows 客户端计算机** - 有关安装说明和 Azure 订阅的连接步骤，请参阅 [Azure PowerShell 入门](/powershell/azureps-cmdlets-docs)。
* **HPC Pack IaaS 部署脚本**：从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=44949)下载并解压缩最新版本的脚本。 通过运行 `New-HPCIaaSCluster.ps1 –Version` 检查脚本的版本。 本文基于脚本版本 4.5.2。
* **脚本配置文件**：创建供脚本用来配置 HPC 群集的 XML 文件。 有关信息和示例，请参阅本文后面的章节和部署脚本随附的文件 Manual.rtf。

## <a name="syntax"></a>语法
```PowerShell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> 以管理员身份运行该脚本。
> 
> 

### <a name="parameters"></a>parameters
* **ConfigFile**：指定描述 HPC 群集的配置文件的路径。 请参阅本主题中该配置文件的详细信息，也可查看包含脚本的文件夹中的 Manual.rtf 文件。
* **AdminUserName**：指定用户名。 如果域林是由脚本创建的，则此用户名将成为所有 VM 的本地管理员用户名以及域管理员名称。 如果域林已存在，则此参数会将域用户指定为安装 HPC Pack 的本地管理员用户名。
* **AdminPassword**：指定管理员的密码。 如果未在命令行中指定密码，脚本将提示输入密码。
* **HPCImageName**（可选）：指定用于部署 HPC 群集的 HPC Pack VM 映像名称。 它必须是 Microsoft 通过 Azure 应用商店提供的 HPC Pack 映像。 如果未指定（通常不建议指定），脚本将选择最新发布的 [HPC Pack 2012 R2 映像](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)。 最新映像基于装有 HPC Pack 2012 R2 Update 3 的 Windows Server 2012 R2 Datacenter。
  
  > [!NOTE]
  > 指定无效的 HPC Pack 映像会导致部署失败。
  > 
  > 
* **LogFile**（可选）：指定部署日志文件路径。 如果未指定，脚本将在运行脚本的计算机的 temp 目录中创建一个日志文件。
* **Force**（可选）：隐藏所有确认提示。
* **NoCleanOnFailure**（可选）：指定不删除未成功部署的 Azure VM。 在重新运行脚本以继续部署之前，请手动删除这些 VM，否则部署可能失败。
* **PSSessionSkipCACheck**（可选）：对于每个包含此脚本所部署的 VM 的云服务，Azure 将自动生成自签名证书，云服务中的所有 VM 使用此证书作为默认的 Windows 远程管理 (WinRM) 证书。 若要在这些 Azure VM 中部署 HPC 功能，脚本默认会在客户端计算机的“本地计算机\\受信任的根证书颁发机构”存储中临时安装这些证书，以隐藏执行脚本期间发生的“不受信任的 CA”安全错误。 完成脚本后将删除这些证书。 如果指定此参数，将不会在客户端计算机中安装证书，并且会抑制安全警告。
  
  > [!IMPORTANT]
  > 对于生产部署，不建议指定此参数。
  > 
  > 

### <a name="example"></a>示例
以下示例使用配置文件 *MyConfigFile.xml* 创建一个 HPC Pack 群集，并指定用于安装该群集的管理员凭据。

```PowerShell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>其他注意事项
* （可选）该脚本可以启用通过 HPC Pack Web 门户或 HPC Pack REST API 提交作业。
* 如果你想要安装其他软件或配置其他设置，该脚本可以选择在头节点上运行自定义的配置前和配置后脚本。

## <a name="configuration-file"></a>配置文件
部署脚本的配置文件是一个 XML 文件。 架构文件 HPCIaaSClusterConfig.xsd 位于 HPC Pack IaaS 部署脚本文件夹中。 **IaaSClusterConfig** 是配置文件的根元素，其中包含部署脚本文件夹中 Manual.rtf 文件详细描述的子元素。



<!--HONumber=Jan17_HO2-->


