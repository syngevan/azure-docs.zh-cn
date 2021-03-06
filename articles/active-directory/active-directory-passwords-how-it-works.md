---
title: "工作原理：Azure AD 密码管理 | Microsoft 文档"
description: "了解 Azure AD 密码管理的各个组件 -- 包括用户可在何处注册、重置和更改其密码，以及管理员可在何处配置、报告和启用本地 Active Directory 密码管理。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 24c94f80afdb3d3330a835fae2f77ecb213a9ae5
ms.lasthandoff: 03/28/2017


---
# <a name="how-password-management-works-in-azure-active-directory"></a>Azure Active Directory 中的密码管理的工作原理
> [!IMPORTANT]
> **你是否因登录时遇到问题而浏览至此？** 如果是这样， [可按以下方式更改和重置你的密码](active-directory-passwords-update-your-own-password.md#reset-your-password)。

Azure Active Directory (Azure AD) 中的密码管理包括以下逻辑组件：

* **密码管理配置门户**：可以通过导航到 [Azure 门户](https://manage.windowsazure.com)中相应目录的“配置”选项卡来控制如何在租户中管理密码的不同方面。
* **用户注册门户**：用户可以通过此 Web 门户自助注册密码重置。
* **用户密码重置门户**：用户可以使用各种符合管理员控制的密码重置策略的质询来重置其自己的密码。
* **用户密码更改门户**：用户可以通过使用此 Web 门户输入旧密码并选择一个新密码来随时更改其自己的密码。
* **密码管理报告**：可以通过导航到 [Azure 门户](https://manage.windowsazure.com)中相应目录的“报告”选项卡的“活动报告”部分来查看和分析租户中的密码重置和注册活动。
* **Azure AD Connect 的密码写回组件**：可以选择在安装 Azure AD Connect 时启用“密码写回”功能，以便从云中管理联合或密码同步用户的密码。

## <a name="password-management-configuration-portal"></a>密码管理配置门户
可以使用 [Azure 门户](https://manage.windowsazure.com)为特定目录配置密码管理策略，此操作可通过导航到目录的“配置”选项卡中的“用户密码重置策略”部分来完成。 从此配置页面，可以控制如何在你的组织中管理密码的许多方面，其中包括：

* 针对目录中的所有用户启用和禁用密码重置。
* 设置用户为重置其密码必须通过的质询数量（一个或两个）。
* 根据以下选择设置想要针对所在组织的用户启用的特定类型的质询：
  * 移动电话（通过短信或语音呼叫接收验证码）
  * 办公电话（语音呼叫）
  * 备用电子邮件（电子邮件验证码）
  * 安全问题（基于知识的身份验证）
* 设置用户为了使用安全问题身份验证方法而必须注册的问题数（仅在启用了安全问题时可见）。
* 设置用户为了使用安全问题身份验证方法而在重置期间必须提供的问题数（仅在启用了安全问题时可见）。
* 使用用户在注册密码重置时可以选择使用的预先编写的、已本地化的安全提问（仅在启用了安全问题时可见）。
* 定义用户在注册密码重置时可以选择使用的自定义安全提问（仅在启用了安全问题时可见）。
* 要求用户在转到应用程序[访问面板](http://myapps.microsoft.com)时注册以便进行密码重置。
* 要求用户在经过可配置的天数后再次确认其之前注册的数据（仅在启用了强制注册时可见）。
* 提供在用户重置密码遇到问题时将向其显示的自定义帮助服务台电子邮件或 URL。
* 启用或禁用密码写回功能（使用 Azure AD Connect 部署了写回时）。
* 查看密码写回代理的状态（使用 Azure AD Connect 部署了写回时）。
* 用户自己的密码被重置时向用户发送电子邮件通知（在 [Azure 门户](https://manage.windowsazure.com)的“通知”部分）。
* 其他管理员重置管理员自己的密码时向管理员发送电子邮件通知（在 [Azure 门户](https://manage.windowsazure.com)的“通知”部分）。
* 通过使用租户品牌自定义功能在用户密码重置门户和密码重置电子邮件中标注你所在组织的徽标和名称（在 [Azure 门户](https://manage.windowsazure.com)的“目录属性”部分）。

若要详细了解有关在组织中配置密码管理的信息，请参阅[入门：Azure AD 密码管理](active-directory-passwords-getting-started.md)。

## <a name="user-registration-portal"></a>用户注册门户
在用户可以使用密码重置之前，必须使用正确的身份验证数据更新其云用户帐户，确保他们可以通过定义的适当数量的密码重置质询。 还可以使用 Azure 或 Office 网络门户、DirSync/Azure AD Connect 或 Windows PowerShell 代表用户定义此身份验证信息。

但是，如果你更希望让用户注册其自己的数据，我们也可以提供一个网页，用户可以转至该网页以提供此信息。 利用此页面，用户可以根据在其组织中启用的密码重置策略指定身份验证信息。 此数据经过验证后，将存储在其云用户帐户中，以便日后进行帐户恢复。

注册门户的外观如下：

  ![][001]

有关详细信息，请参阅[入门：Azure AD 密码管理](active-directory-passwords-getting-started.md)和[最佳做法：Azure AD 密码管理](active-directory-passwords-best-practices.md)。

## <a name="user-password-reset-portal"></a>用户密码重置门户
启用自助密码重置、设置所在组织的自助密码重置策略并确保用户在目录中具有适当的联系人数据后，所在组织中的用户可以从使用工作或学校帐户进行登录的任何网页（如 [portal.microsoftonline.com](https://portal.microsoftonline.com)）自动重置他们自己的密码。 在这些网页上，用户会看到“无法访问你的帐户?”链接。

  ![][002]

单击此链接将打开自助服务密码重置门户。

  ![][003]

若要详细了解有关用户如何重置其密码的信息，请参阅[入门：Azure AD 密码管理](active-directory-passwords-getting-started.md)。

## <a name="user-password-change-portal"></a>用户密码更改门户
如果用户想要更改其密码，可以随时使用密码更改门户实现此目的。 用户可以通过访问面板配置文件页来访问密码更改门户，也可以通过单击 Office 365 应用程序内的“更改密码”链接进行访问。 如果用户的密码过期，用户登录时会自动收到更改其密码的请求。

  ![][004]

在这两种情况下，如果已启用密码写回且用户为联合用户或密码同步用户，这些更改的密码都将写回到你的本地 Active Directory。

密码更改门户的外观如下：

  ![][005]

若要详细了解有关用户如何更改自己的本地 Active Directory 密码的信息，请参阅[入门：Azure AD 密码管理](active-directory-passwords-getting-started.md)。

## <a name="password-management-reports"></a>密码管理报告
导航到“报告”选项卡并在“活动日志”部分下查找，将看到两个密码管理报告：“密码重置活动”和“密码重置注册活动”。 可以使用这两个报告查看在组织中注册和使用密码重置的用户。

[Azure 门户](https://manage.windowsazure.com)中的这些报告外观如下：

  ![][006]

有关详细信息，请参阅[深入分析：Azure AD 密码管理报告](active-directory-passwords-get-insights.md)。

## <a name="password-writeback-component-of-azure-ad-connect"></a>Azure AD Connect 的密码写回组件
如果你所在组织中的用户密码源自你的本地环境（通过联合或密码同步），你可以安装 Azure AD Connect 的最新版本以便直接从云中更新这些密码。 这意味着，当用户忘记或要修改其 Azure AD 密码时，可以直接从 Web 执行此操作。 密码写回在 Azure AD Connect 安装向导中的位置如下：

  ![][007]

有关 Azure AD Connect 的详细信息，请参阅[入门：Azure AD Connect](active-directory-aadconnect.md)。 有关密码写回的详细信息，请参阅[入门：Azure AD 密码管理](active-directory-passwords-getting-started.md)。


## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅以下 Azure AD 密码重置页：

* **你是否因登录时遇到问题而浏览至此？** 如果是这样，请了解如何[更改和重置自己的密码](active-directory-passwords-update-your-own-password.md#reset-your-password)。
* [**入门**](active-directory-passwords-getting-started.md)：了解如何让用户重置和更改云密码或本地密码。
* [**自定义**](active-directory-passwords-customize.md)：了解如何根据组织的需求自定义服务的外观和行为。
* [**最佳做法**](active-directory-passwords-best-practices.md)：了解如何快速部署且有效管理组织的密码。
* [**深入分析**](active-directory-passwords-get-insights.md)：了解集成式报告功能。
* [**常见问题**](active-directory-passwords-faq.md)：获取常见问题的解答。
* [**故障排除**](active-directory-passwords-troubleshoot.md)：了解如何快速排查服务的问题。
* [**了解更多**](active-directory-passwords-learn-more.md)：深入探索服务工作原理的技术详细信息。

[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"

