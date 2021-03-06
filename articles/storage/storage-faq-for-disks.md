---
title: "有关 Azure IaaS VM 磁盘的常见问题解答 (FAQ) | Microsoft Docs"
description: "有关 Azure IaaS VM 磁盘和高级磁盘（托管和非托管）的常见问题解答"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 61610078ad5cefd513fdb758aec45d7489704817
ms.openlocfilehash: b4cb40d81613c16558be1e0e2c10dbfa0265a6b7
ms.lasthandoff: 02/24/2017


---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>有关 Azure IaaS VM 磁盘以及托管和非托管高级磁盘的常见问题解答

本文中，我们将查看有关托管磁盘和高级存储的一些常见问题解答。

## <a name="managed-disks"></a>托管磁盘

**什么是 Azure 托管磁盘？**

托管磁盘是一种通过处理存储帐户管理来简化 Azure IaaS VM 的磁盘管理的功能。 有关详细信息，请参阅[托管磁盘概述](storage-managed-disks-overview.md)。

**如果从现有的 VHD（大小为 80 GB）创建标准托管磁盘，需要多少费用？**

从 80 GB VHD 创建的标准托管磁盘被视为下一个可用的高级磁盘大小（S10 磁盘）。 将根据 S10 磁盘定价收费。 有关详细信息，请查看[定价页](https://azure.microsoft.com/pricing/details/storage)。

**标准托管磁盘是否存在任何事务成本？**

是的，会对每个事务进行收费。 有关详细信息，请查看[定价页] (https://azure.microsoft.com/pricing/details/storage)。

**对于标准托管磁盘，是对磁盘上的数据实际大小收费还是对磁盘的预配容量收费？**

根据磁盘的预配容量收费。 有关详细信息，请查看[定价页](https://azure.microsoft.com/pricing/details/storage)。

**高级托管磁盘与非托管磁盘的定价有何不同？**

高级托管磁盘的定价与高级非托管磁盘的定价相同。

**是否可以更改托管磁盘的存储帐户类型（标准/高级）？**

是的。 可以使用 Azure 门户、PowerShell 或 Azure CLI 更改托管磁盘的存储帐户类型。

**是否可将托管磁盘复制或导出到专用存储帐户？**

可以，可通过 Azure 门户、PowerShell 或 Azure CLI 导出托管磁盘。

**是否可以使用 Azure 存储帐户中的 VHD 文件在不同的订阅中创建托管磁盘？**

否。

**是否可以使用 Azure 存储帐户中的 VHD 文件在不同的区域中创建托管磁盘？**

否。

**客户使用托管磁盘是否存在任何规模限制？**

托管磁盘取消了与存储帐户相关的限制。 但是，每个订阅的托管磁盘数默认限制为 2000 个。 可联系支持人员，提高此限额。

**是否可以生成托管磁盘的增量快照？**

否。 当前的快照功能可提供托管磁盘的完整副本。 但我们计划在将来支持增量快照。

**可用性集中的 VM 是否可以同时包含托管和非托管磁盘？**

不可以，可用性集中的 VM 必须全部使用托管磁盘或全部使用非托管磁盘。 创建可用性集时，可以选择要使用的磁盘类型。

**托管磁盘是否是 Azure 门户中的默认选项？**

目前不是，但将来会成为默认选项。

**是否可以创建一个空托管磁盘？**

可以，可创建空磁盘。 可独立于 VM 创建托管磁盘，也就是说不需要将磁盘附加到 VM。

**什么是使用托管磁盘的可用性集的支持容错域计数？**

使用托管磁盘的可用性集的支持容错域计数为 2 或 3，具体取决于它所在的区域。

**如何设置用于诊断的标准存储帐户？**

设置 VM 诊断的专用存储帐户。 我们计划将来也将诊断切换到托管磁盘。

**托管磁盘有哪些类型的 RBAC 支持？**

托管磁盘支持三个密钥默认角色：

1.  所有者：可管理所有内容，包括访问权限。

2.  参与者：可管理访问权限以外的所有内容。

3.  读者：可查看所有内容，但不能进行更改。

**是否可将托管磁盘复制或导出到专用存储帐户？**

可以为托管磁盘获取只读共享访问签名 (SAS) URI，使用它将内容复制到专用存储帐户或本地存储。

**是否可以创建托管磁盘副本？**

客户可以生成托管磁盘的快照，然后使用快照创建另一个托管磁盘。

**是否仍支持非托管磁盘？**

是，我们支持托管磁盘和非托管磁盘。 但建议对新的工作负荷使用托管磁盘，并将当前的工作负荷迁移到托管磁盘。

**如果创建 128 GB 大小的磁盘，然后将大小增加到 130 GB，是否会针对下一磁盘大小 (512 GB) 进行收费？**

是的。

**是否可以创建 LRS、GRS 和 ZRS 托管磁盘？**

Azure 托管磁盘当前仅支持本地冗余存储 (LRS)。

## <a name="managed-disks-and-port-8443"></a>托管磁盘和端口 8443

**为何客户必需使用 Azure 托管磁盘在端口 8443 上为 VM 解除阻止出站流量？**

Azure VM 代理使用端口 8443 将每个 VM 扩展的状态报告给 Azure 平台。 如果不解除对该端口的阻止，VM 代理将无法报告任何 VM 扩展的状态。 有关 VM 代理的详细信息，请参阅 [Azure 虚拟机代理概述](../virtual-machines/virtual-machines-windows-agent-user-guide.md)。

**如果在部署 VM 时未取消对扩展和端口的阻止，会发生什么情况？**

部署会导致错误。 

**如果在部署 VM 时不带扩展，且端口未取消阻止，会发生什么情况？**

对部署不会有任何影响。 

**如果在 VM 上安装了扩展，且该 VM 已预配并处于运行状态，而 VM 的端口 8443 并未取消阻止，会发生什么情况？**

不会成功部署该扩展。 扩展的状态将为未知。 

**如果使用 ARM 模板来预配多个阻止了端口 8443 的 VM（一个 VM 带扩展，另一个 VM 依赖于第一个 VM），会发生什么情况？**

第一个 VM 会显示部署失败，因为扩展未成功部署。 第二个 VM 不会部署。 

**取消端口阻止这个要求是否适用于所有 VM 扩展？**

是的。

**是否需同时解除对端口 8443 上的入站和出站连接的阻止？**

否。 仅需解除对端口 8443 上的出站连接的阻止。 

**是否需在 VM 的整个生存期解除对端口 8443 上的出站连接的阻止？**

是的。

**取消对该端口的阻止是否影响 VM 的性能？**

不能。

**解决此问题是否有一个估计的日期？我不想再被迫解除对端口 8443 的阻止。**

是的，此问题会在 2017 年 5 月底之前解决。

## <a name="premium-disks--both-managed-and-unmanaged"></a>高级磁盘 - 托管和非托管

**如果 VM 使用支持高级存储的大小系列（比如 DSv2），是否可以同时附加高级和标准数据磁盘？** 

是的。

**是否可以同时将高级和标准数据磁盘附加到不支持高级存储的大小系列，例如 D、Dv2、G 或 F 系列？**

否。 只可以将标准数据磁盘附加到不使用支持高级存储的大小系列的 VM。

**如果从现有的 VHD（大小为 80 GB）创建高级数据磁盘，需要多少费用？**

从 80 GB VHD 创建的高级数据磁盘被视为下一个可用的高级磁盘大小（P10 磁盘）。 我们将根据 P10 磁盘价格收费。

**使用高级存储时是否存在事务成本？**

每个磁盘大小都有固定成本，其根据 IOPS 和吞吐量的特定限制进行预配。 其他成本包括出站带宽和快照容量（如果适用）。 有关详细信息，请查看[定价页](https://azure.microsoft.com/pricing/details/storage)。

**什么是可从磁盘缓存获取的 IOPS 和吞吐量限制？**

DS 系列的缓存和本地 SSD 合并限制是每个核心 4000 IOPS，以及每个核心每秒 33 MB。 GS 系列提供每个核心 5000 IOPS，以及每个核心每秒 50 MB。

**托管磁盘 VM 是否支持本地 SSD？**

本地 SSD 是托管磁盘 VM 随附的临时存储。 临时存储不需要额外的成本。 建议不要使用此本地 SSD 来存储应用程序数据，因为这些数据不会永久保存在 Azure Blob 存储中。

## <a name="what-if-my-question-isnt-answered-here"></a>如果未在此处找到相关问题怎么办？

如果未在此处找到相关问题，请联系我们获取帮助。 可将问题发布在本文末尾的评论中或 MSDN [Azure 存储论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)中，围绕本文内容，与 Azure 存储团队和其他社区成员展开探讨。

若要提出功能请求，请将请求和想法提交到 [Azure 存储反馈论坛](https://feedback.azure.com/forums/217298-storage)。
