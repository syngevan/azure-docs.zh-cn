---
title: "通过 Azure CLI 为 Azure 应用创建标识 | Microsoft Docs"
description: "描述如何使用 Azure CLI 创建 Active Directory 应用程序和服务主体，并通过基于角色的访问控制授予其对资源的访问权限。 它演示如何使用密码或证书对应用程序进行身份验证。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c224a189-dd28-4801-b3e3-26991b0eb24d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a99b55c98f29356fb78e053434f6f3fc5c9d0efc
ms.lasthandoff: 03/21/2017


---
# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>使用 Azure CLI 创建服务主体来访问资源
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [Azure CLI](resource-group-authenticate-service-principal-cli.md)
> * [门户](resource-group-create-service-principal-portal.md)
> 
> 

当应用或脚本需访问资源时，可以为应用设置一个标识，然后使用其自己的凭据进行身份验证。 与使用用户自己的凭据运行应用相比，此方法更优，原因在于：

* 可以将权限分配给应用标识，这些权限不同于你自己的权限。 通常情况下，这些权限仅限于应用需执行的操作。
* 你的职责变化时，无需更改应用的凭据。 
* 执行无人参与的脚本时，可使用证书自动进行身份验证。

本主题介绍如何通过[适用于 Mac、Linux 和 Windows 的 Azure CLI](../cli-install-nodejs.md) 为应用程序进行设置，使之能够使用自己的凭据和标识运行。

使用 Azure CLI 时，可以通过&2; 个选项进行 AD 应用程序身份验证：

* password
* 证书

本主题演示如何在 Azure CLI 中使用这两个选项。 如果想从一个编程框架（例如 Python、Ruby 或 Node.js）登录 Azure，则最好是使用密码身份验证。 在确定是使用密码还是证书之前，请参阅[示例应用程序](#sample-applications)部分，获取在不同框架中进行身份验证的示例。

## <a name="active-directory-concepts"></a>Active Directory 概念
在本文中，你将创建两个对象 - Active Directory (AD) 应用程序和服务主体。 AD 应用程序是应用程序的全局表示形式。 它包含凭据（应用程序 ID 和密码或证书）。 服务主体是应用程序在 Active Directory 中的本地表示形式。 它包含角色分配。 本主题重点介绍单租户应用程序，即应用程序只会在一个组织中运行。 通常会将单租户应用程序作为在组织中运行的业务线应用程序使用。 在单租户应用程序中，有一个 AD 应用和一个服务主体。

我们可能会疑惑 - 为什么需要这两个对象？ 在考虑多租户应用程序时，此方法会更合理。 通常对软件即服务 (SaaS) 应用程序使用多租户应用程序，其中应用程序在许多不同订阅中运行。 对于多租户应用程序，有一个 AD 应用和多个服务主体（授予应用访问权限的每个 Active Directory 中有一个）。 若要设置多租户应用程序，请参阅[使用 Azure Resource Manager API 进行授权的开发人员指南](resource-manager-api-authentication.md)。

## <a name="required-permissions"></a>所需的权限
若要完成本主题，必须在 Azure Active Directory 和 Azure 订阅中均具有足够的权限。 具体而言，必须能够在 Active Directory 中创建应用并向角色分配服务主体。 

检查帐户是否有足够权限的最简方法是使用门户。 请参阅[在门户中检查所需的权限](resource-group-create-service-principal-portal.md#required-permissions)。

现在转到[密码](#create-service-principal-with-password)或[证书](#create-service-principal-with-certificate)身份验证部分。

## <a name="create-service-principal-with-password"></a>使用密码创建服务主体
在本部分中，你将执行以下步骤，创建包含密码的 AD 应用程序并“读取者”角色分配到服务主体。

让我们逐步执行这些步骤。

1. 登录到你的帐户。
   
   ```azurecli
   azure login
   ```
2. 可以使用两个选项来创建 AD 应用程序。 可以通过一个步骤创建 AD 应用程序和服务主体，也可以分开创建。 如果不需要指定应用的主页和标识符 URI，请通过一个步骤创建应用程序和服务主体。 如果需要为 Web 应用设置这些值，请分开创建应用程序和服务主体。 此步骤说明了这两个选项。
   
   * 若要一步创建 AD 应用程序和服务主体，请提供应用名称和密码，如以下命令中所示：
     
     ```azurecli
     azure ad sp create -n exampleapp -p {your-password}
     ```
   * 若要单独创建 AD 应用程序，请提供：

      * 应用的名称
      * 应用主页的 URL
      * 用于标识应用的 URI 的逗号分隔列表
      * password

      如以下命令所示：
     
     ```azurecli
     azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p {Your_Password}
     ```

       上述命令会返回 AppId 值。 若要创建服务主体，请在以下命令中以参数形式提供该值：
     
     ```azurecli
     azure ad sp create -a {AppId}
     ```
     
     如果帐户在 Active Directory 上不具有[所需的权限](#required-permissions)，将看到指示“Authentication_Unauthorized”或“上下文中找不到订阅”的错误消息。
     
     使用这两个选项时，都会返回新的服务主体。 授权时需要使用 `Object Id`。 登录时需要提供随 `Service Principal Names` 列出的 GUID。 此 GUID 的值与应用 ID 相同。 在示例应用程序中，此值称为 `Client ID`。 
     
     ```azurecli
     info:    Executing command ad sp create
     
     Creating application exampleapp
       / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
       data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
       data:    Display Name:            exampleapp
       data:    Service Principal Names:
       data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
       data:                             https://www.contoso.org/example
       info:    ad sp create command OK
      ```

3. 向服务主体授予对订阅的权限。 在此示例中，向“读取者”角色（授予读取订阅中所有资源的权限）添加服务主体。 对于其他角色，请参阅 [RBAC：内置角色](../active-directory/role-based-access-built-in-roles.md)。 对于 `objectid` 参数，请提供创建应用程序时使用的 `Object Id`。 运行此命令之前，必须留出一些时间将新的服务主体传遍 Active Directory。 手动运行这些命令时，任务之间通常已经过足够的时间。 在脚本中，应在命令间添加休眠步骤（如 `sleep 15`）。 如果看到错误称主体不存在于目录中，请重新运行该命令。
   
   ```azurecli
   azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/
   ```
   
     如果帐户没有足够权限来分配角色，将看到一条错误消息。 该消息声明用户的帐户“无权在作用域 '/subscriptions/{guid}' 执行操作 'Microsoft.Authorization/roleAssignments/write'”。

就这么简单！ AD 应用程序和服务主体设置完毕。 下一部分演示如何通过 Azure CLI 使用凭据进行登录。 如果想在代码应用程序中使用凭据，则不需要继续了解本主题。 可以跳到[示例应用程序](#sample-applications)，获取使用应用程序 ID 和密码登录的示例。 

### <a name="provide-credentials-through-azure-cli"></a>通过 Azure CLI 提供凭据
现在，需要以应用程序方式登录以执行相应操作。

1. 以服务主体方式登录时，需提供 AD 应用所在目录的租户 ID。 租户是 Active Directory 的实例。 若要检索当前已经过身份验证的订阅的租户 ID，请使用：
   
   ```azurecli
   azure account show
   ```
   
     将返回：
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
     如果需要获取另一个订阅的租户 ID，请使用以下命令：
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. 如果需要检索用于登录的客户端 ID，请使用：
   
   ```azurecli
   azure ad sp show -c exampleapp --json
   ```
   
     用于登录的值是服务主体名称中列出的 GUID。
   
   ```azurecli
   [
     {
       "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "7132aca4-1bdb-4238-ad81-996ff91d8db4"
       ]
     }
   ]
   ```
3. 以服务主体身份登录。
   
   ```azurecli
   azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}
   ```
   
    系统将提示输入密码。 提供在创建 AD 应用程序时指定的密码。
   
   ```azurecli
   info:    Executing command login
   Password: ********
   ```

现在，用户已作为所创建服务主体的服务主体进行身份验证。

也可从要登录的命令行调用 REST 操作。 可以从身份验证响应中检索访问令牌，将其用于其他操作。 若要通过示例来了解如何通过调用 REST 操作来检索访问令牌，请参阅[生成访问令牌](resource-manager-rest-api.md#generating-an-access-token)。

## <a name="create-service-principal-with-certificate"></a>使用证书创建服务主体
执行本部分中的步骤，以便：

* 创建自签名证书
* 使用证书创建 AD 应用程序，并创建服务主体
* 向服务主体分配“读取者”角色

若要完成这些步骤，必须已安装 [OpenSSL](http://www.openssl.org/)。

1. 创建自签名证书。
   
   ```
   openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'
   ```
2. 将公钥和私钥组合在一起。
   
   ```
   cat privkey.pem cert.pem > examplecert.pem
   ```
3. 打开 **examplecert.pem** 文件并找到 **-----BEGIN CERTIFICATE-----** 与 **-----END CERTIFICATE-----** 之间的长字符序列。 复制证书数据。 创建服务主体时将此数据作为参数传递。
4. 登录到你的帐户。
   
   ```azurecli
   azure login
   ```
5. 可以使用两个选项来创建 AD 应用程序。 可以通过一个步骤创建 AD 应用程序和服务主体，也可以分开创建。 如果不需要指定应用的主页和标识符 URI，请通过一个步骤创建应用程序和服务主体。 如果需要为 Web 应用设置这些值，请分开创建应用程序和服务主体。 此步骤说明了这两个选项。
   
   * 若要一步创建 AD 应用程序和服务主体，请提供应用名称和证书数据，如以下命令中所示：
     
     ```azurecli
     azure ad sp create -n exampleapp --cert-value {certificate data}
     ```
   * 若要单独创建 AD 应用程序，请提供：
      
      * 应用的名称
      * 应用主页的 URL
      * 用于标识应用的 URI 的逗号分隔列表
      * 证书数据

      如以下命令所示：

     ```azurecli
     azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example --cert-value {certificate data}
     ```
     
       上述命令会返回 AppId 值。 若要创建服务主体，请在以下命令中以参数形式提供该值：
     
     ```azurecli
     azure ad sp create -a {AppId}
     ```
     
     如果帐户在 Active Directory 上不具有[所需的权限](#required-permissions)，将看到指示“Authentication_Unauthorized”或“上下文中找不到订阅”的错误消息。
     
     使用这两个选项时，都会返回新的服务主体。 授权时需要使用对象 ID。 登录时需要提供随 `Service Principal Names` 列出的 GUID。 此 GUID 的值与应用 ID 相同。 在示例应用程序中，此值称为 `Client ID`。 
     
     ```azurecli
     info:    Executing command ad sp create
     
     Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
       data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
       data:    Display Name:     exampleapp
       data:    Service Principal Names:
       data:                      4fd39843-c338-417d-b549-a545f584a745
       data:                      https://www.contoso.org/example
       info:    ad sp create command OK
     ```
6. 向服务主体授予对订阅的权限。 在此示例中，向“读取者”角色（授予读取订阅中所有资源的权限）添加服务主体。 对于其他角色，请参阅 [RBAC：内置角色](../active-directory/role-based-access-built-in-roles.md)。 对于 `objectid` 参数，请提供创建应用程序时使用的 `Object Id`。 运行此命令之前，必须留出一些时间将新的服务主体传遍 Active Directory。 手动运行这些命令时，任务之间通常已经过足够的时间。 在脚本中，应在命令间添加休眠步骤（如 `sleep 15`）。 如果看到错误称主体不存在于目录中，请重新运行该命令。
   
   ```azurecli
   azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/
   ```
   
     如果帐户没有足够权限来分配角色，将看到一条错误消息。 该消息声明用户的帐户“无权在作用域 '/subscriptions/{guid}' 执行操作 'Microsoft.Authorization/roleAssignments/write'”。

### <a name="provide-certificate-through-automated-azure-cli-script"></a>通过自动执行的 Azure CLI 脚本提供证书
现在，需要以应用程序方式登录以执行相应操作。

1. 以服务主体方式登录时，需提供 AD 应用所在目录的租户 ID。 租户是 Active Directory 的实例。 若要检索当前已经过身份验证的订阅的租户 ID，请使用：
   
   ```azurecli
   azure account show
   ```
   
     将返回：
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
     如果需要获取另一个订阅的租户 ID，请使用以下命令：
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. 若要检索证书指纹并删除不需要的字符，请使用：
   
   ```
   openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
   ```
   
     它返回的指纹值类似于：
   
   ```
   30996D9CE48A0B6E0CD49DBB9A48059BF9355851
   ```
3. 如果需要检索用于登录的客户端 ID，请使用：
   
   ```azurecli
   azure ad sp show -c exampleapp
   ```
   
     用于登录的值是服务主体名称中列出的 GUID。
     
   ```azurecli
   [
     {
       "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "4fd39843-c338-417d-b549-a545f584a745",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "4fd39843-c338-417d-b549-a545f584a745"
       ]
     }
   ]
   ```
4. 以服务主体身份登录。
   
   ```azurecli
   azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}
   ```

现在，你已作为所创建 Active Directory 应用程序的服务主体进行身份验证。

## <a name="change-credentials"></a>更改凭据

为了保障安全或由于凭据过期，若要更改 AD 应用的凭据，请使用 `azure ad app set`。

若要更改密码，请使用：

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --password p@ssword
```

若要更改证书值，请使用：

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --cert-value {certificate data}
```


## <a name="sample-applications"></a>示例应用程序
以下示例应用程序演示如何以服务主体身份登录。

**.NET**

* [在 .NET 中使用模板部署启用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [使用 .NET 管理 Azure 资源和资源组](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [资源入门 - 在 Java 中使用 Azure Resource Manager 模板部署资源](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [资源入门 - 在 Java 中管理资源组](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [在 Python 中使用模板部署启用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [使用 Python 管理 Azure 资源和资源组](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

* [在 Node.js 中使用模板部署启用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [使用 Node.js 管理 Azure 资源和资源组](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [在 Ruby 中使用模板部署启用 SSH 的 VM](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [使用 Ruby 管理 Azure 资源和资源组](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>后续步骤
* 有关将应用程序集成到 Azure 以管理资源的详细步骤，请参阅 [Developer's guide to authorization with the Azure Resource Manager API](resource-manager-api-authentication.md)（使用 Azure Resource Manager API 进行授权的开发人员指南）。
* 若要获取有关使用证书和 Azure CLI 的详细信息，请参阅[从 Linux 命令行对 Azure 服务主体进行基于证书的身份验证](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)。 


