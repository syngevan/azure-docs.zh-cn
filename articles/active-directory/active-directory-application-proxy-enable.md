---
title: "启用 Azure AD 应用程序代理 | Microsoft Docs"
description: "在 Azure 经典门户中，打开应用程序代理并为反向代理安装连接器。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: c979e6328f09618642aa7a432c873c7ce20c072b
ms.lasthandoff: 03/03/2017


---

# <a name="enable-application-proxy-in-the-azure-portal"></a>在 Azure 门户中启用应用程序代理
本文将指导你完成在 Azure AD 中为云目录启用 Microsoft Azure AD 应用程序代理的步骤。

如果你不了解应用程序代理可以做些什么，可了解有关 [如何提供对本地应用程序的安全远程访问](active-directory-application-proxy-get-started.md)的详细信息。

## <a name="application-proxy-prerequisites"></a>应用程序代理先决条件
在启用和使用应用程序代理服务之前，你需要：

* [Microsoft Azure AD 基本或高级版订阅](active-directory-editions.md) 以及你本人为全局管理员的 Azure AD 目录。
* 可以安装应用程序代理连接器，运行 Windows Server 2012 R2 或 Windows 8.1 或更高版本的服务器。 该服务器将请求发送到云中的应用程序代理服务，并且需要与要发布的应用程序建立 HTTP 或 HTTPS 连接。
  
  * 若要单一登录到已发布的应用程序，此计算机应域加入要发布的应用程序所在的同一 AD 域。 相关信息，请参阅[使用应用程序代理进行单一登录](active-directory-application-proxy-sso-using-kcd.md)
* 如果路径中有防火墙，请确保防火墙已打开，以便连接器向应用程序代理发出 HTTPS (TCP) 请求。 连接器将连同属于高级域 msappproxy.net 和 servicebus.windows.net 的子域一起使用这些端口。 确保打开以下用于 **出站** 流量的端口：
  
  | 端口号 | 说明 |
  | --- | --- |
  | 80 |启用出站 HTTP 流量以进行安全验证。 |
  | 443 |针对 Azure AD 启用用户身份验证（只有连接器注册过程才需要） |
  | 10100–10120 |启用发送回代理的 LOB HTTP 响应 |
  | 9352、5671 |为传入请求启用连接器到 Azure 服务之间的通信。 |
  | 9350 |可选，提高传入请求的性能 |
  | 8080 |启用连接器启动序列和连接器自动更新 |
  | 9090 |启用连接器注册（只有连接器注册过程才需要） |
  | 9091 |启用连接器信任证书自动续订 |
  
    如果你的防火墙根据发起用户强制实施流量，请针对来自作为网络服务运行的 Windows 服务的流量打开这些端口。 此外，请确保为 NT Authority\System 启用端口 8080。
* 使用 [Azure AD 应用程序代理连接器端口测试工具](https://aadap-portcheck.connectorporttest.msappproxy.net/)验证连接器能够访问应用程序代理服务。 请至少确保美国中部区域和离你最近的区域有全部绿色复选标记。 绿色复选标记越多表示复原能力越强。 
* 如果组织使用代理服务器连接 Internet，请查看博客文章[使用现有本地代理服务器](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/)，了解配置方法的详细信息。

## <a name="step-1-enable-application-proxy-in-azure-ad"></a>步骤 1：在 Azure AD 中启用应用程序代理
1. 在 [Azure 经典门户](https://manage.windowsazure.com/)中，以管理员身份登录。
2. 转到 Active Directory，并选择你要在其中启用应用程序代理的目录。
   
    ![Active Directory - 图标](./media/active-directory-application-proxy-enable/ad_icon.png)
3. 从目录页中选择“配置”，并向下滚动至“应用程序代理”。
4. 将“为此目录启用应用程序代理服务”切换至“已启用”状态。
   
    ![启用应用程序代理](./media/active-directory-application-proxy-enable/app_proxy_enable.png)
5. 选择“立即下载” 。 随即转到“Azure AD 应用程序代理连接器下载” 。 阅读并接受许可条款，然后单击“下载”  为连接器保存 Windows Installer 文件 (.exe)。

## <a name="step-2-install-and-register-the-connector"></a>步骤 2： 安装并注册连接器
1. 在根据先决条件准备的服务器上运行 **AADApplicationProxyConnectorInstaller.exe** 。
2. 按照向导中的说明进行安装。
3. 在安装期间，系统将提示你将连接器注册到 Azure AD 租户的应用程序代理。
   
   * 提供你的 Azure AD 全局管理员凭据。 全局管理员租户可能不同于 Microsoft Azure 凭据。
   * 确保注册连接器的管理员在你启用应用程序代理服务的同一目录中。 例如，如果租户域为 contoso.com，则管理员应该为 admin@contoso.com 或该域上的任何其他别名。
   * 如果要安装连接器的服务器上将“IE 增强的安全配置”设为“打开”，则可能无法显示注册屏幕。 请按照错误消息中的说明来允许访问。 确保 Internet Explorer 增强的安全性已关闭。
   * 如果连接器注册不成功，请参阅 [故障排除应用程序代理](active-directory-application-proxy-troubleshoot.md)。  
4. 安装完成后，服务器上会添加两个新的服务：
   
   * **Microsoft AAD 应用程序代理连接器** 将启用连接
     
     * **Microsoft AAD 应用程序代理连接器更新程序** 是一项自动更新服务，它会定期检查连接器新版本并根据需要更新连接器。
     
     ![应用代理连接器服务 - 屏幕截图](./media/active-directory-application-proxy-enable/app_proxy_services.png)
5. 在安装窗口中单击“完成”  。

有关连接器的信息，请参阅[了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)。 

为了获得高可用性，应该部署至少两个连接器。 若要部署更多连接器，请重复上面的步骤 2 和 3。 每个连接器必须分开注册。

如果要卸载连接器，请先卸载连接器服务和更新程序服务。 重新启动计算机以完全删除该服务。

## <a name="next-steps"></a>后续步骤
现在，你可以随时 [使用应用程序代理发布应用程序](active-directory-application-proxy-publish.md)。

如果应用程序位于单独网络或其他位置，可以使用连接器组将不同连接器组织到逻辑单元中。 了解有关 [使用应用程序代理连接器](active-directory-application-proxy-connectors.md)的详细信息。


