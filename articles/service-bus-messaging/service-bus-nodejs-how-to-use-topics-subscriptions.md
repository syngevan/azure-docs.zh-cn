---
title: "如何通过 Node.js 使用 Azure 服务总线主题和订阅 | Microsoft Docs"
description: "了解如何在来自 Node.js 应用的 Azure 中使用服务总线主题和订阅。"
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: 
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/12/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 56094202673416320e5a8801ee2275881ccfc8fb
ms.openlocfilehash: 4e28b47a1ce1a3bf69a57382a5ec6c2a8a161efe


---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>如何使用服务总线主题和订阅
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本指南演示如何从 Node.js 应用程序使用服务总线主题和订阅。 涉及的应用场景包括**创建主题和订阅**、**创建订阅筛选器**、**将消息发送到**主题、**从订阅接收消息**以及**删除主题和订阅**。 有关主题和订阅的详细信息，请参阅[后续步骤](#next-steps)一节。

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>创建 Node.js 应用程序
创建一个空的 Node.js 应用程序。 有关创建 Node.js 应用程序的说明，请参阅[创建 Node.js 应用程序并将其部署到 Azure 网站]、[Node.js 云服务][Node.js Cloud Service]（使用 Windows PowerShell），或“使用 WebMatrix 创建网站”。

## <a name="configure-your-application-to-use-service-bus"></a>配置应用程序以使用 Service Bus
若要使用服务总线，请下载 Node.js Azure 包。 此包包括一组用来与服务总线 REST 服务通信的库。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>使用 Node 包管理器 (NPM) 可获取该程序包
1. 使用 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Unix) 等命令行接口导航到在其中创建了示例应用程序的文件夹。
2. 在命令窗口中键入 **npm install azure**，这应会生成以下输出：

   ```
       azure@0.7.5 node_modules\azure
   ├── dateformat@1.0.2-1.2.3
   ├── xmlbuilder@0.4.2
   ├── node-uuid@1.2.0
   ├── mime@1.2.9
   ├── underscore@1.4.4
   ├── validator@1.1.1
   ├── tunnel@0.0.2
   ├── wns@0.5.3
   ├── xml2js@0.2.7 (sax@0.5.2)
   └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
   ```
3. 可以手动运行 **ls** 命令来验证是否创建了 **node\_modules** 文件夹。 在该文件夹中，找到 **azure** 程序包，其中包含访问服务总线主题所需的库。

### <a name="import-the-module"></a>导入模块
使用记事本或其他文本编辑器将以下内容添加到应用程序的 **server.js** 文件的顶部：

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>设置服务总线连接
Azure 模块将读取环境变量 AZURE\_SERVICEBUS\_NAMESPACE and AZURE\_SERVICEBUS\_ACCESS\_KEY 以获取连接到服务总线所需的信息。 如果未设置这些环境变量，则在调用 **createServiceBusService** 时必须指定帐户信息。

有关在 Azure 云服务的配置文件中设置环境变量的示例，请参阅[使用存储的 Node.js 云服务][Node.js Cloud Service with Storage]。

有关在 [Azure 经典门户][Azure classic portal]中为 Azure 网站设置环境变量的示例，请参阅[使用存储的 Node.js Web 应用程序][Node.js Web Application with Storage]。

## <a name="create-a-topic"></a>创建主题
可以通过 **ServiceBusService** 对象处理主题。 以下代码创建 **ServiceBusService** 对象。 将它添加到靠近 **server.js** 文件顶部、用于导入 azure 模块的语句之后的位置：

```javascript
var serviceBusService = azure.createServiceBusService();
```

通过对 **ServiceBusService** 对象调用 **createTopicIfNotExists**，将返回指定的主题（如果存在），否则将使用指定名称创建新主题。 以下代码使用 **createTopicIfNotExists** 创建或连接到名为“MyTopic”的主题：

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

**createServiceBusService** 还支持其他选项，以允许你重写默认主题设置，如消息生存时间或最大主题大小。 以下示例将最大主题大小设置为 5GB，将生存时间设置为 1 分钟：

```javascript
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>筛选器
可选的筛选操作可应用于使用 **ServiceBusService** 执行的操作。 筛选操作可包括日志记录、自动重试等。筛选器是实现具有签名的方法的对象：

```javascript
function handle (requestOptions, next)
```

在对请求选项执行预处理后，该方法将调用 `next` 并传递具有以下签名的回调：

```javascript
function (returnObject, finalCallback, next)
```

在此回调中，并且在处理 **returnObject**（来自对服务器请求的响应）后，回调需要调用 next（如果它存在）以便继续处理其他筛选器，或只调用 **finalCallback** 以便结束服务调用。

Azure SDK for Node.js 中附带了两个实现了重试逻辑的筛选器，分别是 **ExponentialRetryPolicyFilter** 和 **LinearRetryPolicyFilter**。 以下代码创建一个 **ServiceBusService** 对象，该对象使用 **ExponentialRetryPolicyFilter**：

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>创建订阅
主题订阅也是使用 **ServiceBusService** 对象创建的。 为订阅命名，并且订阅可以具有可选筛选器，以限制传送到订阅的虚拟队列的消息集。

> [!NOTE]
> 订阅是永久性的，并且除非删除它或删除与之相关的主题，否则订阅将一直存在。 如果你的应用程序包含创建订阅的逻辑，则它应首先使用 **getSubscription** 方法检查该订阅是否已经存在。
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>创建具有默认 (MatchAll) 筛选器的订阅
**MatchAll** 筛选器是默认筛选器，在创建新订阅时未指定筛选器的情况下使用。 使用 **MatchAll** 筛选器时，发布到主题的所有消息都将置于订阅的虚拟队列中。 以下示例创建名为“AllMessages”的订阅，并使用默认的 **MatchAll** 筛选器。

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>创建具有筛选器的订阅
还可以创建筛选器，以确定发送到主题的哪些消息应该在特定主题订阅中显示。

订阅支持的最灵活的一种筛选器是 **SqlFilter**，它实现了一部分 SQL92 功能。 SQL 筛选器将对发布到主题的消息的属性进行操作。 有关可用于 SQL 筛选器的表达式的更多详细信息，请参阅 [SqlFilter.SqlExpression][SqlFilter.SqlExpression] 语法。

可以使用 **ServiceBusService** 对象的 **createRule** 方法向订阅中添加筛选器。 此方法允许向现有订阅中添加新筛选器。

> [!NOTE]
> 由于默认筛选器会自动应用到所有新订阅，因此，必须首先删除默认筛选器，否则 **MatchAll** 会替代你可能指定的任何其他筛选器。 可以使用 **ServiceBusService** 对象的 **deleteRule** 方法删除默认规则。
>
>

以下示例创建了一个名为 `HighMessages` 的订阅（带有只选择自定义 **messagenumber** 属性大于 3 的消息的 **SqlFilter**）：

```javascript
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'HighMessages',
            'HighMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

类似地，以下示例创建一个名为 `LowMessages` 的订阅，其 **SqlFilter** 只选择 **messagenumber** 属性小于或等于 3 的消息：

```javascript
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'LowMessages',
            'LowMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

现在，当消息发送到 `MyTopic` 时，它始终会传送给订阅了 `AllMessages` 主题订阅的接收者，并且选择性地传送给订阅了 `HighMessages` 和 `LowMessages` 主题订阅的接收者（具体取决于消息内容）。

## <a name="how-to-send-messages-to-a-topic"></a>如何将消息发送到主题
若要将消息发送到服务总线主题，你的应用程序必须使用 **ServiceBusService** 对象的 **sendTopicMessage** 方法。
发送到服务总线主题的消息是 **BrokeredMessage** 对象。
**BrokeredMessage** 对象具有一组标准属性（如 **Label** 和 **TimeToLive**）、一个用来保存自定义的特定于应用程序的属性的字典，以及一段字符串数据正文。 应用程序可以通过将字符串值传递给 **sendTopicMessage** 来设置消息正文，并且任何必需的标准属性将用默认值填充。

下面的示例演示如何向“MyTopic”发送五条测试消息。 请注意，每条消息的 **messagenumber** 属性值因循环迭代而异（这将确定接收它的订阅）：

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

服务总线主题在[标准层](service-bus-premium-messaging.md)中支持的最大消息容量为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 标头最大为 64 KB，其中包括标准和自定义应用程序属性。 一个主题中包含的消息数量不受限制，但消息的总大小受限制。 此主题大小是在创建时定义的，上限为 5 GB。

## <a name="receive-messages-from-a-subscription"></a>从订阅接收消息
对 **ServiceBusService** 对象使用 **receiveSubscriptionMessage** 方法可从订阅接收消息。 默认情况下，在读取消息后将从订阅中删除它们；不过，通过将可选参数 **isPeekLock** 设置为 **true**，可以读取（扫视）并锁定消息，以避免将其从订阅中删除。

在接收过程中读取并删除消息的默认行为是最简单的模式，并且最适合在发生故障时应用程序可以容忍不处理消息的情况。 为了理解这一点，可以考虑这样一种情形：使用方发出接收请求，但在处理该请求前发生了崩溃。 由于 Service Bus 会将消息标记为“将使用”，因此当应用程序重启并重新开始使用消息时，它会丢失在发生崩溃前使用的消息。

如果将 **isPeekLock** 参数设置为 **true**，则接收将变成一个两阶段操作，这样就可以支持无法允许遗漏消息的应用程序。 当 Service Bus 收到请求时，它会查找下一条要使用的消息，锁定该消息以防其他使用者接收，然后将该消息返回到应用程序。
应用程序处理完该消息（或将它可靠地存储起来留待将来处理）后，通过调用 **deleteMessage** 方法并提供要删除的消息作为参数来完成接收过程的第二阶段。 **deleteMessage** 方法会将消息标记为已使用，并将其从订阅中删除。

以下示例演示如何使用 **receiveSubscriptionMessage** 接收和处理消息。 该示例先从“LowMessages”订阅接收并删除一条消息，然后将 **isPeekLock** 设置为 true，从“HighMessages”订阅接收一条消息。 最后使用 **deleteMessage** 删除该消息：

```javascript
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        }
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何处理应用程序崩溃和不可读消息
Service Bus 提供了相关功能来帮助你轻松地从应用程序错误或消息处理问题中恢复。 如果接收方应用程序因某种原因无法处理消息，则它可以对 **ServiceBusService** 对象调用 **unlockMessage** 方法。 这会导致服务总线解锁订阅中的消息并使其能够重新被同一个正在使用的应用程序或其他正在使用的应用程序接收。

另外，还存在与订阅中已锁定消息关联的超时，并且如果应用程序无法在锁定超时到期之前处理消息（例如，如果应用程序崩溃），则服务总线将自动解锁该消息并使其可再次被接收。

如果应用程序在处理消息之后，调用 **deleteMessage** 方法之前崩溃，则在应用程序重新启动时会将该消息重新传送给它。 此情况通常称作**至少处理一次**，即每条消息将至少被处理一次，但在某些情况下，同一消息可能会被重新传送。 如果方案无法容忍重复处理，则应用程序开发人员应向其应用程序添加更多逻辑以处理重复消息传送。 这通常可以通过使用消息的 **MessageId** 属性来实现，该属性在多次传送尝试中保持不变。

## <a name="delete-topics-and-subscriptions"></a>删除主题和订阅
主题和订阅具有持久性，必须通过 [Azure 经典门户][Azure classic portal]或以编程方式显式删除。
下面的示例演示如何删除名为 `MyTopic` 的主题：

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

删除某个主题也会删除向该主题注册的所有订阅。 也可以单独删除订阅。 以下示例说明如何从 `MyTopic` 主题删除名为 `HighMessages` 的订阅：

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

## <a name="next-steps"></a>后续步骤
现在，你已了解有关 Service Bus 主题的基础知识，单击下面的链接可了解更多信息。

* 请参阅[队列、主题和订阅][Queues, topics, and subscriptions]。
* [SqlFilter][SqlFilter] 的 API 参考。
* 访问 GitHub 上的 [Azure SDK for Node][Azure SDK for Node] 存储库。

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure classic portal]: https://manage.windowsazure.com
[SqlFilter.SqlExpression]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sqlfilter#Microsoft_ServiceBus_Messaging_SqlFilter_SqlExpression
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[创建 Node.js 应用程序并将其部署到 Azure 网站]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Node.js Web Application with Storage]: ../storage/storage-nodejs-use-table-storage-cloud-service-app.md



<!--HONumber=Jan17_HO2-->


