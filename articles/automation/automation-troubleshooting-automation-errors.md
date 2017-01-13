---
title: "Azure 自动化错误处理 | Microsoft Docs"
description: "本文提供了排查并解决常见 Azure 自动化错误的基本错误处理步骤。"
services: automation
documentationcenter: 
author: mgoedtel
manager: stevenka
editor: tysonn
tags: top-support-issue
keywords: "自动化错误, 错误处理"
ms.assetid: 5f3cfe61-70b0-4e9c-b892-d02daaeee07d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: sngun; v-reagie
translationtype: Human Translation
ms.sourcegitcommit: a9e855afcf9da703efd6653b19d9cc8e406f8bd3
ms.openlocfilehash: 0c7e2373a4c3dc5fe878f0761586665bc4fc0a7a


---
# <a name="error-handling-tips-for-common-azure-automation-errors"></a>常见 Azure 自动化错误的错误处理提示
本文介绍你可能会遇到的一些常见 Azure 自动化错误并建议可能的错误处理步骤。

## <a name="troubleshoot-authentication-errors-when-working-with-azure-automation-runbooks"></a>解决使用 Azure 自动化 Runbook 时遇到的身份验证错误
### <a name="scenario-sign-in-to-azure-account-failed"></a>场景：登录 Azure 帐户失败
**错误：**使用 Add-AzureAccount 或 Login-AzureRmAccount cmdlet 时收到“Unknown_user_type: 用户类型未知”错误。

**错误原因：**如果凭据资产名称无效或者用于设置自动化凭据资产的用户名和密码无效，则会出现此错误。

**疑难解答提示：**为了确定具体错误，请执行以下步骤：  

1. 确保在用于连接到 Azure 的自动化凭据资产名称中没有任何特殊字符，包括 **@** 字符。  
2. 查看你是否能够在本地 PowerShell ISE 编辑器中使用存储在 Azure 自动化凭据中的用户名和密码。 为此，你可以在 PowerShell ISE 中运行以下 cmdlet：  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred
3. 如果无法在本地进行身份验证，则意味着你尚未设置好 Azure Active Directory 凭据。 请参阅[使用 Azure Active Directory 向 Azure 进行身份验证](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/)博客文章，了解如何正确设置 Azure Active Directory 帐户。  

### <a name="scenario-unable-to-find-the-azure-subscription"></a>场景：无法找到 Azure 订阅
**错误：**使用 Select-AzureSubscription 或 Select-AzureRmSubscription cmdlet 时收到“未能找到名为 ``<subscription name>`` 的订阅”错误。

**错误原因：**如果订阅名称无效，或者尝试获取订阅详细信息的 Azure Active Directory 用户未配置为订阅的管理员，则会出现此错误。

**提示：**为了确定你是否已正确向 Azure 进行身份验证并有权访问尝试选择的订阅，请执行以下步骤：  

1. 确保先运行 **Add-AzureAccount** cmdlet，然后再运行 **Select-AzureSubscription** cmdlet。  
2. 如果仍显示此错误消息，可通过添加 **Get-AzureSubscription** cmdlet（在 **Add-AzureAccount** cmdlet 后）来修改代码，然后执行代码。  现在，请验证 Get-AzureSubscription 的输出是否包含你的订阅详细信息。  

   * 如果在输出中看不到任何订阅详细信息，则说明该订阅尚未初始化。  
   * 如果在输出中看到了订阅详细信息，请确认你对 **Select-AzureSubscription** cmdlet 使用了正确的订阅名称或 ID。   

### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>场景：无法向 Azure 进行身份验证，因为已启用多重身份验证
**错误：**使用 Azure 用户名和密码向 Azure 进行身份验证时，收到“Add-AzureAccount: AADSTS50079: 需要进行强身份验证注册(验证)”错误。

**错误原因：**如果你对 Azure 帐户设置了多重身份验证，则不能使用 Azure Active Directory 用户向 Azure 进行身份验证。  而只能使用证书或服务主体向 Azure 进行身份验证。

**疑难解答提示：**若要将证书用于 Azure 服务管理 cmdlet，请参阅[创建并添加管理 Azure 服务所需的证书](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)。 若要将服务主体用于 Azure Resource Manager cmdlet，请参阅[使用 Azure 门户创建服务主体](../resource-group-create-service-principal-portal.md)和[通过 Azure Resource Manager 对服务主体进行身份验证](../resource-group-authenticate-service-principal.md)。

## <a name="troubleshoot-common-errors-when-working-with-runbooks"></a>排查使用 Runbook 时发生的常见错误
### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>场景：Runbook 因反序列化的对象而失败
**错误：**Runbook 失败，出现错误“无法绑定参数 ``<ParameterName>``。 无法将反序列化 ``<ParameterType>`` 类型的 ``<ParameterType>`` 值转换成 ``<ParameterType>`` 类型”。

**错误原因：**如果你的 Runbook 为 PowerShell 工作流，则会将复杂对象以反序列化格式进行存储，以便在工作流暂停的情况下保留 Runbook 状态。  

**疑难解答提示：**  
下述三种解决方案中的任何一种都可以解决此问题：

1. 如果你要将复杂对象从一个 cmdlet 传送到另一个 cmdlet，则可将这两个 cmdlet 包装在 InlineScript 中。  
2. 传递复杂对象中你所需要的名称或值，不必传递整个对象。  
3. 使用 PowerShell Runbook，而不使用 PowerShell 工作流 Runbook。  

### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>场景：Runbook 作业失败，因为超过了分配的配额
**错误：**Runbook 作业失败，出现“已达到此订阅的每月总作业运行时间配额”错误。

**错误原因：**当作业执行时间超过你帐户的 500 分钟免费配额时，就会出现此错误。 此配额适用于所有类型的作业执行任务，例如测试作业、从门户启动作业、使用 Webhook 执行作业，以及通过 Azure 门户或数据中心计划要执行的作业。 若要详细了解自动化的定价，请参阅[自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

**疑难解答提示：**如果你想要每月使用 500 分钟以上的处理时间，则需将订阅从免费层改为基本层。 你可以通过下述步骤升级到基本层：  

1. 登录到 Azure 订阅  
2. 选择要升级的自动化帐户  
3. 单击“设置” > “定价层和使用情况” > “定价层”  
4. 在“选择定价层”边栏选项卡中，选择“基本”    

### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>场景：在执行 Runbook 时无法识别 Cmdlet
**错误：**Runbook 作业失败，出现“``<cmdlet name>``: 无法将 ``<cmdlet name>`` 一词识别为 cmdlet、函数、脚本文件或可运行程序的名称”错误。

**错误原因：**当 PowerShell 引擎找不到你要在 Runbook 中使用的 cmdlet 时，则会导致此错误。  这可能是因为，帐户中缺少包含该 cmdlet 的模块、与 Runbook 名称存在名称冲突，或者该 cmdlet 也存在于其他模块中，而自动化无法解析该名称。

**疑难解答提示：**下述解决方案中的任何一种都可以解决此问题：  

* 检查输入的 cmdlet 名称是否正确。  
* 确保 cmdlet 存在于你的自动化帐户中，且没有冲突。 若要验证 cmdlet 是否存在，请在编辑模式下打开 Runbook，然后搜索希望在库中找到的 cmdlet，或者运行 **Get-Command ``<CommandName>``**。  验证该 cmdlet 可供帐户使用且与其他 cmdlet 或 Runbook 不存在名称冲突以后，可将其添加到画布上，并确保你使用的是 Runbook 中的有效参数集。  
* 如果存在名称冲突且 cmdlet 可在两个不同的模块中使用，则可使用 cmdlet 的完全限定名称来解决此问题。 例如，可以使用 **ModuleName\CmdletName**。  
* 如果你是在本地执行混合辅助角色组中的 Runbook，则请确保模块/cmdlet 已安装在托管混合辅助角色的计算机上。

### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>场景：某个长时间运行的 Runbook 不断失败并出现异常：“该作业无法继续运行，因为它已反复从同一个检查点逐出”。
**错误原因：**这是设计使然。Azure 自动化中对进程的“公平份额”监视会自动暂停执行时间超过 3 小时的 Runbook。 但是，返回的错误消息不会提供“后续措施”选项。 Runbook 可能会出于多种原因而暂停。 发生暂停的主要原因是出错。 例如，Runbook 中出现未捕获到的异常、网络故障、运行 Runbook 的 Runbook 辅助角色崩溃，都会导致 Runbook 暂停，并在恢复时从其最后一个检查点开始运行。

**疑难解答提示：**避免此问题的有记录解决方法是在工作流中使用检查点。  若要了解详细信息，请参阅[了解 PowerShell 工作流](automation-powershell-workflow.md#checkpoints)。  [在 Runbook 中使用检查点](https://azure.microsoft.com/en-us/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/)博客文章中提供了有关“公平份额”和检查点的更全面说明。

## <a name="troubleshoot-common-errors-when-importing-modules"></a>排查导入模块时发生的常见错误
### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>场景：模块无法导入，或者 cmdlet 在导入后无法执行
**错误：**模块无法导入，或者虽然导入成功，但无法提取 cmdlet。

**错误原因：**模块无法成功导入到 Azure 自动化中的一些常见原因是：  

* 结构与自动化所需的模块结构不符。  
* 该模块依赖于其他模块，而后者尚未部署到你的自动化帐户。  
* 该模块的文件夹中缺少依赖项。  
* 使用了 **New-AzureRmAutomationModule** cmdlet 来上载该模块，但你尚未提供完整的存储路径，或者尚未使用可公开访问的 URL 来加载该模块。  

**疑难解答提示：**  
下述解决方案中的任何一种都可以解决此问题：  

* 请确保该模块遵循以下格式：  
  ModuleName.Zip **->** 模块名称或版本号 **->** (ModuleName.psm1, ModuleName.psd1)
* 打开 .psd1 文件，看模块是否有任何依赖项。  如果有，则将这些模块上载到自动化帐户。  
* 确保任何引用的 .dll 都存在于模块文件夹中。  

## <a name="troubleshoot-common-errors-when-working-with-desired-state-configuration-dsc"></a>解决使用所需状态配置 (DSC) 时的常见错误
### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>场景：节点处于失败状态，出现“未找到”错误
**错误：**节点的报告显示“失败”状态，并包含“从服务器 https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction 获取操作的尝试失败，因为未找到有效配置 ``<guid>``”错误。

**错误原因：**当将节点分配到配置名称（例如 ABC）而不是节点配置名称（例如 ABC.WebServer）时，通常会发生此错误。  

**疑难解答提示：**  

* 确保要为节点分配“节点配置名称”，而不是“配置名称”。  
* 可以使用 Azure 门户或 PowerShell cmdlet 将节点配置分配给节点。

  * 若要使用 Azure 门户将节点配置分配给节点，请打开“DSC 节点”边栏选项卡，然后选择一个节点，并单击“分配节点配置”按钮。  
  * 若要使用 PowerShell cmdlet 将节点配置分配给节点，请使用 **Set-AzureRmAutomationDscNode** cmdlet

### <a name="scenario--no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>场景：编译配置时未生成节点配置（MOF 文件）
**错误：**DSC 编译作业暂停，出现错误：“编译已成功完成，但未生成任何节点配置 .mof”。

**错误原因：**如果 DSC 配置中 **Node** 关键字后面的表达式的计算结果为 $null，则不会生成节点配置。    

**疑难解答提示：**  
下述解决方案中的任何一种都可以解决此问题：  

* 确保配置定义中 **Node** 关键字旁边的表达式的计算结果不为 $null。  
* 如果要在编译配置时传递 ConfigurationData，请确保从 [ConfigurationData](automation-dsc-compile.md#configurationdata) 传递配置需要的预期值。

### <a name="scenario--the-dsc-node-report-becomes-stuck-in-progress-state"></a>场景：DSC 节点报告卡在“正在进行”状态
**错误：**DSC 代理输出“未找到具有给定属性值的实例。”

**错误原因：**已升级 WMF 版本，已损坏 WMI。  

**疑难解答提示：**请按照 [DSC 已知问题和限制](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc)中的说明解决此问题。

### <a name="scenario--unable-to-use-a-credential-in-a-dsc-configuration"></a>场景：无法在 DSC 配置中使用凭据
**错误：**DSC 编译作业已暂停，并出现错误：“在处理 ``<some resource name>`` 类型的属性 'Credential' 时出现 System.InvalidOperationException 错误: 仅当 PSDscAllowPlainTextPassword 设置为 true 时，才允许将已加密的密码转换并存储为纯文本”。

**错误原因：**已在配置中使用凭据，但未提供正确 **ConfigurationData**，从而无法将每个节点配置的 **PSDscAllowPlainTextPassword** 设置为 true。  

**疑难解答提示：**  

* 确保传入正确的 **ConfigurationData**，以便将配置中涉及的每个节点配置的 **PSDscAllowPlainTextPassword** 设置为 true。 有关详细信息，请参阅 [Azure Automation DSC 中的资产](automation-dsc-compile.md#assets)。

## <a name="next-steps"></a>后续步骤
如果你在完成上述疑难解答步骤以后仍对本文中的内容存有疑问，你可以：

* 从 Azure 专家那里获取帮助。 向 [MSDN Azure 或 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)提交问题。
* 提出 Azure 支持事件。 转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)，单击“技术和帐单支持”下的“获得支持”。
* 如果正在寻找 Azure 自动化 Runbook 解决方案或集成模块，请在[脚本中心](https://azure.microsoft.com/documentation/scripts/)发布脚本请求。
* 将关于 Azure 自动化的反馈或功能请求发布到[用户之声](https://feedback.azure.com/forums/34192--general-feedback)。



<!--HONumber=Nov16_HO4-->

