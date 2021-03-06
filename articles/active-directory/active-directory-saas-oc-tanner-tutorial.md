---
title: "教程：Azure Active Directory 与 O.C. 集成 Tanner - AppreciateHub | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 O. C. 之间配置单一登录 Tanner - AppreciateHub 的权限，允许其使用 Azure 单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1b5527090a41c274b590ed9d1ac7b561b6f4ed14
ms.openlocfilehash: 10faa27e0c82e59c35e00258a2d90bea977dc28f


---
# <a name="tutorial-azure-active-directory-integration-with-o-c-tanner---appreciatehub"></a>教程：Azure Active Directory 与 O.C. 集成 Tanner - AppreciateHub
本教程的目的是介绍如何将 O.C. Tanner - AppreciateHub 与 Azure Active Directory (Azure AD) 集成。  
将 O.C. Tanner - AppreciateHub 与 Azure AD 集成具有以下优势： 

* 可在 Azure AD 中控制谁有权访问 O.C. Tanner - AppreciateHub 
* 可以让用户通过其 Azure AD 帐户自动登录到 O.C. Tanner - AppreciateHub（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 O.C. Tanner - AppreciateHub 的集成，需要备齐以下项目：

* Azure AD 订阅
* 启用了 O.C. Tanner - AppreciateHub 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。 

## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。  
在本教程中概述的方案包括三个主要构建基块：

1. 从库添加 O.C. Tanner - AppreciateHub 
2. 配置和测试 Azure AD 单一登录

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>从库添加 O.C. Tanner - AppreciateHub
若要通过配置将 O.C. Tanner - AppreciateHub 集成到 Azure AD 中，需要从库将 O.C. Tanner - AppreciateHub 添加到托管式 SaaS 应用的列表中。

**若要从库添加 O.C.Tanner - AppreciateHub，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1] 
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2] 
4. 在页面底部单击“添加”。
   
    ![应用程序][3] 
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4] 
6. 在搜索框中，键入“O.C. **Tanner - AppreciateHub”。**
   
    ![应用程序][5] 
7. 在结果窗格中，选择“O.C.**Tanner - AppreciateHub”，然后单击“完成”添加该应用程序。**
   
    ![应用程序][25] 

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分的目的是介绍如何根据名为“Britta Simon”的测试用户的情况，配置和测试 O.C. Tanner - AppreciateHub 的 Azure AD 单一登录。

若要使用单一登录，Azure AD 需要了解与 Azure AD 中的用户相对应的 O.C. Tanner - AppreciateHub 中的用户是谁。 换句话说，需要建立 Azure AD 用户与 O.C. Tanner - AppreciateHub 中相关用户之间的关联关系。  
将 Azure AD 中“用户名”的值指定为 O.C. Tanner - AppreciateHub 中“用户名”的值， 即可建立此关联关系。

若要使用 O.C. Tanner - AppreciateHub 配置和测试 Azure AD 单一登录，需完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 O.C.Tanner - AppreciateHub 测试用户](#creating-a-halogen-software-test-user)** - 目的是在 O.C. Tanner - AppreciateHub 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分的目的是在 Azure 经典门户中启用 Azure AD 单一登录，并在 O.C. Tanner - AppreciateHub 应用程序中配置单一登录。

**若要通过 O.C.Tanner - AppreciateHub 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“O.C. **Tanner - AppreciateHub”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。**
   
    ![配置单一登录][6]
2. 在“你希望用户如何登录 O.C. **Tanner - AppreciateHub”页上，选择“Azure AD 单一登录”，然后单击“下一步”。**
   
    ![Azure AD 单一登录][7]
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置应用设置][8]
   
     a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 通过以下链接打开元数据文件：[https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata)。
   
     b. 找到 **md:AssertionConsumerService** 节点。 
   
     c. 复制 **Location** 属性的值。 
   
     ![配置应用设置][12]
   
     d.单击“下一步”。 在“登录 URL”文本框中，传递在上一步获得的值。
   
    > [!NOTE]
    > 如果无法从元数据文件获取回复 URL，请通过 [sso@octanner.com](mailto:sso@octanner.com) 联系 O.C. Tanner - AppreciateHub 支持团队。
    > 
    > 
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。

4. 在“在 O.C.Tanner - AppreciateHub 上配置单一登录”页上，单击“下载元数据”，然后将元数据文件保存在计算机本地。**
   
    ![什么是 Azure AD Connect][9]
5. 通过 xyz 联系 O.C. Tanner - AppreciateHub 支持团队，为其提供元数据文件，让他们知道应该为你启用 SSO。
6. 在 Azure 经典门户上，选择“单一登录配置确认”，然后单击“下一步”。 
   
    ![什么是 Azure AD Connect][10]
7. 在“单一登录确认”页上，单击“完成”。  
   
    ![什么是 Azure AD Connect][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。  

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤： 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_05.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“下一步”。
6. 在“用户配置文件”对话框页上，执行以下步骤： 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_06.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
    b. 在“姓氏”文本框中，键入“Simon”。
   
    c. 在“显示名称”文本框中，键入“Britta Simon”。
   
    d.单击“下一步”。 在“角色”列表中，选择“用户”。
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_08.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>创建 O.C. Tanner - AppreciateHub 测试用户
本部分的目的是在 O.C. Tanner - AppreciateHub 中创建名为“Britta Simon”的用户。

**若要在 O.C.Tanner - AppreciateHub 中创建名为“Britta Simon”的用户，请执行以下步骤：**

要求 OC Tanner 支持团队创建一个用户，该用户的 nameID 属性值与 Azure AD 中 Britta Simon 的用户名相同。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过授予 Britta Simon 访问 O.C. Tanner - AppreciateHub 的权限，允许其使用 Azure 单一登录。

![分配用户][200]

**若要将 Britta Simon 分配到 O.C.Tanner - AppreciateHub，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201]
2. 在应用程序列表中，选择“O.C. **Tanner - AppreciateHub”。**
   
    ![分配用户][202]
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用访问面板测试 Azure AD 单一登录配置。  
在访问面板中单击“O.C. Tanner - AppreciateHub”磁贴时，用户会自动登录到 O.C. Tanner - AppreciateHub 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_01.png
[25]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_25.png


[6]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_02.png
[8]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_03.png
[9]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_04.png
[10]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_05.png
[11]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_06.png
[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png
[20]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_07.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_205.png









<!--HONumber=Feb17_HO3-->


