---
title: "Windows VM 的计划维护 | Microsoft Docs"
description: "了解什么是 Azure 计划内维护以及它如何影响正在 Azure 中运行的 Windows 虚拟机"
services: virtual-machines-windows
documentationcenter: 
author: drewm
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: drewm
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: fecc7666f80f7a9a5b2f8daaa65fd1766616fc85
ms.lasthandoff: 03/03/2017


---
# <a name="planned-maintenance-for-virtual-machines-in-azure"></a>Azure 中虚拟机的计划内维护
了解什么是 Azure 计划内维护，以及它如何影响 Windows 虚拟机的可用性。 本文也适用于 [Linux 虚拟机](virtual-machines-linux-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

本文提供有关 Azure 计划内维护过程的背景信息。 如果想要排查 VM 重新启动的原因，可以[阅读此博客文章，其中详细说明了如何查看 VM 重新启动日志](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/)。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="why-azure-performs-planned-maintenance"></a>Azure 为何要执行计划内维护
Microsoft Azure 在全球范围内定期执行更新，以提高虚拟机所基于的主机基础结构的可靠性、性能及安全性。 其中许多更新在执行时不会对虚拟机或云服务产生任何影响，其中包括内存保留更新。

不过，有些更新就需要重新启动虚拟机，以便向基础结构应用所需的更新。 虚拟机会在我们修补基础结构时关闭，之后再重新启动。

请注意，有两类维护可能会影响虚拟机的可用性：计划内维护和计划外维护。 本页介绍 Microsoft Azure 如何执行计划内维护。 有关计划外维护的详细信息，请参阅[了解计划内与计划外维护](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

[!INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

