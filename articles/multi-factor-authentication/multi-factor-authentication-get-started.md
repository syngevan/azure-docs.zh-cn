---
title: "在 Azure MFA 云和服务器之间选择 | Microsoft 文档"
description: "通过询问自己想要保护什么以及用户的位置，选择适合你的多重身份验证安全解决方案。  然后选择云、MFA 服务器或 AD FS。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: ec2270ea-13d7-4ebc-8a00-fa75ce6c746d
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 92c552f42f2645281f5039242825cfdaf6b683c9
ms.openlocfilehash: 1a42c137204c2d2f6fff9a005b2b0e885c2002b2
ms.lasthandoff: 01/25/2017


---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>选择适合自己的 Azure 多重身份验证解决方案
因为 Azure 多重身份验证 (MFA) 的种类繁多，我们必须从以下问题着手，以便找出最合适的版本。  这些问题包括：

* [我要保护什么](#what-am-i-trying-to-secure)
* [用户位于何处](#where-are-the-users-located)
* [需要哪些功能？](#what-featured-do-i-need)

以下部分提供有关如何确定这些答案的指导。

## <a name="what-am-i-trying-to-secure"></a>我要保护什么？
为了确定适当的多重验证解决方案，首先我们必须回答一个问题：试图使用第二种身份验证方法来保护什么？  它是 Azure 中的应用程序，  还是远程访问系统？  通过判断我们尝试保护的项目，就可以回答要在何处启用多重身份验证。  

| 你想要保护什么 | 云中的 MFA | MFA 服务器 |
| --- |:---:|:---:|
| 第一方 Microsoft 应用 |● |● |
| 应用库中的 SaaS 应用 |● |● |
| 通过 Azure AD 应用代理发布的 Web 应用程序 |● |● |
| 不是通过 Azure AD 应用代理发布的 IIS 应用程序 | |● |
| VPN、RDG 等远程访问系统 | |● |

## <a name="where-are-the-users-located"></a>用户位于何处
接下来，了解用户位于何处有助于确定要用的正确解决方案，不论是云中的多重身份验证还使用 MFA 服务器的本地多重身份验证。

| 用户位置 | 云中的 MFA | MFA 服务器 |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD 和使用 AD FS 联合身份验证的本地 AD |● |● |
| Azure AD 和使用 DirSync、Azure AD Sync、Azure AD Connect 的本地 AD - 没有密码同步 |● |● |
| Azure AD 和使用 DirSync、Azure AD Sync、Azure AD Connect 的本地 AD - 包含密码同步 |● | |
| 本地 Active Directory | |● |

## <a name="what-features-do-i-need"></a>需要哪些功能？
下表是云中多重身份验证和多重身份验证服务器的功能比较。

| 功能 | 云中的 MFA | MFA 服务器 |
| --- |:---:|:---:|
| 将移动应用通知用作第二个因素 | ● | ● |
| 将移动应用验证码用作第二个因素 | ● | ● |
| 将电话呼叫用作第二个因素 | ● | ● |
| 将单向短信用作第二个因素 | ● | ● |
| 将双向短信用作第二个因素 | | ● |
| 将硬件令牌用作第二个因素 | | ● |
| 不支持 MFA 的 Office 365 客户端的应用密码 | ● | |
| 管理员控制身份验证方法 | ● | ● |
| PIN 模式 | | ● |
| 欺诈警报 |● | ● |
| MFA 报告 |● | ● |
| 一次性跳过 | | ● |
| 通话的自定义问候语 | ● | ● |
| 可自定义的来电者 ID | ● | ● |
| 受信任的 IP | ● | ● |
| 记住受信任的设备的 MFA | ● | |
| 条件性访问 | ● | ● |
| 缓存 |  | ● |

在确定是要使用云 Multi-Factor Authentication 还是本地 MFA 服务器后，接下来可以开始设置并使用 Azure Multi-Factor Authentication。 **选择代表你的方案的图标！**

<center>




[![云](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>

