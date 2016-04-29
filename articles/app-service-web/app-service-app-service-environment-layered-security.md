<!-- not suitable for Mooncake -->

<properties 
	pageTitle="使用 Azure 环境实现分层的安全体系结构" 
	description="使用 Azure 环境实现分层的安全体系结构。" 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="wpickett" 
	editor=""/>

<tags
	ms.service="app-service"
	ms.date="01/05/2016"
	wacn.date=""/>	

# 使用 Azure 环境实现分层的安全体系结构

## 概述 ##
 
由于 Azure 环境提供部署到虚拟网络的隔离运行时环境，因此开发人员能够创建分层的安全体系结构，针对每个物理应用层提供不同级别的网络访问权限。

常见的需求之一是要隐藏对 API 后端的常规 Internet 访问，而只允许由上游 Web 应用调用 API。可以在包含 Azure 环境的子网上使用[网络安全组 (NSG)][NetworkSecurityGroups]，来限制对 API 应用程序的公开访问。

下图显示的示例体系结构具有部署在 Azure 环境上的基于 WebAPI 的应用。三个不同的 Web 应用实例部署在三个不同的 Azure 环境上，使后端调用相同的 WebAPI 应用。

![概念体系结构][ConceptualArchitecture]

绿色加号指示包含“apiase”的子网上的网络安全组允许来自于上游 Web 应用的入站调用及其本身的调用。但是，相同的网络安全组显式拒绝 Internet 对一般入站流量的访问。

此主题的其余部分将逐步解说在包含“apiase”的子网上配置网络安全组所需的步骤。

## 确定网络行为 ##
若要知道需要哪些网络安全规则，需要确定哪些网络客户端能够访问包含 API 应用的 Azure 环境，而哪些客户端将遭到阻止。

由于[网络安全组 (NSG)][NetworkSecurityGroups] 将应用到子网，而 Azure 环境部署在子网中，因此 NSG 中包含的规则将应用到所有在 Azure 环境上运行的应用。就本文的示例体系结构而言，将网络安全组应用到包含“apiase”的子网后，所有在“apiase”Azure 环境上运行的应用都将受到相同安全规则集的保护。

- **确定上游调用方的出站 IP 地址：**上游调用方的 IP 地址是什么？ 需要在 NSG 中显式允许访问这些地址。由于 Azure 环境之间的调用被视为“Internet”调用，因此这意味着分配到这三个上游 Azure 环境的出站 IP 地址在“apiase”子网的 NSG 中需是允许访问的。有关确定在 Azure 环境中运行的应用的出站 IP 地址的详细信息，请参阅[网络体系结构][NetworkArchitecture]概述文章。
- **后端 API 应用是否需要调用本身？** 后端应用程序有时候需要调用本身，这是常被忽略且难以察觉的情况。如果 Azure 环境上的后端 API 应用程序需要调用本身，我们也将其视为“Internet”调用。在示例体系结构中，这必须允许“apiase”Azure 环境的出站 IP 地址进行访问。

## 设置网络安全组 ##
知道出站 IP 地址集后，下一步是构建网络安全组。由于目前只有“v1”虚拟网络支持 Azure 环境，因此需要使用 Powershell 中的经典 NSG 支持来完成 [NSG 配置][NetworkSecurityGroupsClassic]。

在示例体系结构中，环境位于中国东部，因此请在该区域中创建空的 NSG：

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "China East" -Label "Only allow web frontend and loopback traffic"

首先要为 Azure 管理基础结构添加显式允许规则，如 Azure 环境的[入站流量][InboundTraffic]相关文章中所述。

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    
接下来，添加两个规则以允许从第一个上游 Azure 环境（“fe1ase”）进行 HTTP 和 HTTPS 调用。

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

对第二个和第三个上游 Azure 环境（“fe2ase”和“fe3ase”）重复相同步骤。

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

最后，授予后端 API 对 Azure 环境出站 IP 地址的访问权限，使其能够回调本身。

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

无需配置其他网络安全规则，因为默认情况下每个 NSG 都有一组默认规则阻止来自 Internet 的入站访问。

网络安全组中的完整规则列表显示如下。请注意最后一个规则（已突出显示）如何阻止未被显式授予访问权限的所有调用方进行的入站访问。

![NSG 配置][NSGConfiguration]

最后一步是将 NSG 应用到包含“apiase”Azure 环境的子网。

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

将 NSG 应用到子网后，只有三个上游 Azure 环境以及包含 API 后端的 Azure 环境能够调用“apiase”环境。


## 其他链接和信息 ##
经典虚拟网络上的网络[安全组配置][NetworkSecurityGroupsClassic]。

了解[出站 IP 地址][NetworkArchitecture]和 Azure 环境。

Azure 环境使用的[网络端口][InboundTraffic]。

[AZURE.INCLUDE [app-service-web-whats-changed](../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: /documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]: /documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkSecurityGroupsClassic]: /documentation/articles/virtual-networks-create-nsg-classic-ps/
[InboundTraffic]: /documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]: ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png

<!---HONumber=Mooncake_0328_2016-->