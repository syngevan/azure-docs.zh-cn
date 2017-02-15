---
title: "从 Azure VM 创建 VM 映像 | Microsoft Docs"
description: "了解如何基于 Resource Manager 部署模型中创建的现有 Azure VM 创建通用化 VM 映像"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b6dffec166ffe8e04c5d7b701aef009bf7b72d45


---
# <a name="download-the-template-for-a-vm"></a>下载 VM 模板
使用门户或 PowerShell 在 Azure 中创建 VM 时，系统会自动创建一个 Resource Manager 模板。 可以使用此模板快速复制部署。 该模板包含有关资源组中所有资源的信息。 对于虚拟机而言，这意味着该模板包含为于在该资源组中支持该 VM 而创建的所有资源，包括网络资源。

## <a name="download-the-template-using-the-portal"></a>使用门户下载模板
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在中心菜单中，选择“虚拟机”。
3. 从列表中选择虚拟机。
4. 选择“自动化脚本”。
5. 选择“下载”，将 .zip 文件保存到本地计算机。
6. 打开 .zip 文件，将文件解压缩到某个文件夹。 该 .zip 文件包含：
   
   * deploy.ps1
   * deploy.sh 
   * deployer.rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

.json 文件是模板。

## <a name="download-the-template-using-powershell"></a>使用 PowerShell 下载模板
也可以使用 [Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx) cmdlet 下载 .json 模板文件。 可以使用 `-path` 参数提供 .json 文件的文件名和路径。 本示例演示如何将名为 **myResourceGroup** 的资源组的模板下载到本地计算机上的 **C:\users\public\downloads** 文件夹。

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>后续步骤
若要详细了解如何使用模板部署资源，请参阅 [Resource Manager 模板演练](../resource-manager-template-walkthrough.md)。




<!--HONumber=Nov16_HO3-->

