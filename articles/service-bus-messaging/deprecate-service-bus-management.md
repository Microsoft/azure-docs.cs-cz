---
title: Služby zasílání zpráv Azure – Správce služeb správci prostředků
description: Tento článek obsahuje mapování zastaralé ho reprecovaného rozhraní REST API Azure Service Manager & rutiny prostředí PowerShell do rozhraní REST API správce prostředků & rutin prostředí PowerShell.
services: service-bus-messaging, event-hubs, event-grid
documentationcenter: na
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2020
ms.author: spelluru
ms.openlocfilehash: d263381667319b98a28ee6168e2de75c4041b58a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589906"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Vyřazení podpory Azure Service Managerpro Azure Service Bus, Relay a Event Hubs

Resource Manager, náš zásobník cloudové infrastruktury nové generace, plně nahrazuje "klasický" model správy služeb Azure (klasický model nasazení). Výsledkem je, že klasická nasazení modelu REST API a podpora service bus, relé a centra událostí budou vyřazeny v listopadu 1, 2021. Toto vyřazení bylo poprvé oznámeno na [oznámení Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909), ale nedávno jsme se rozhodli prodloužit období vyřazení další dva roky od okamžiku původního oznámení. Pro snadnou identifikaci `management.core.windows.net` mají tato api ve svém identifikátoru URI. Seznam zastaralá rozhraní API a jejich verze rozhraní API Azure Resource Manager, kterou byste teď měli použít, najdete v následující tabulce.

Chcete-li pokračovat v používání služby Service Bus, Relay a Event Hubs, přesuňte se do Správce prostředků do 31. Doporučujeme všem zákazníkům, kteří stále používají stará řešení API, aby brzy přešli na přepínač, aby mohli využít další výhody Správce prostředků, mezi něž patří seskupování zdrojů, značky, zjednodušený proces nasazení a správy a jemně odstupňovaný přístup. pomocí řízení přístupu na základě rolí (RBAC).

Další informace o Azure Resource Manager vs Azure Service Manager, najdete v tématu [Blog TechNet](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/).

Další informace o rozhraních API Správce služeb a Správce prostředků pro Azure Service Bus, Relay a Event Hubs najdete v naší dokumentaci k rozhraní REST API:

- [Azure Service Bus](/rest/api/servicebus/)
- [Centra událostí Azure](/rest/api/eventhub/)
- [Azure Relay](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>Rozhraní REST API správce služeb – rozhraní REST API správce prostředků

| Zastaralá api správce služeb (zastaralé) | Správce prostředků – rozhraní API služby Service Bus | Správce prostředků – rozhraní API centra událostí | Správce prostředků – rozhraní API pro předávání |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **Obory názvů GetNamespaceAsync** <br/>[Služba Service Bus získat obor názvů](/rest/api/servicebus/get-namespace)<br/>[Centrum událostí získat obor názvů](/rest/api/eventhub/get-event-hub)<br/>[Relé získat obor názvů](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **ConnectionDetails-GetConnectionDetails**<br/>Service Bus/Event Hub/Relay GetConnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [seznamové klávesy](/rest/api/servicebus/namespaces/listkeys) | [seznamové klávesy](/rest/api/eventhub/namespaces/listkeys) | [seznamové klávesy](/rest/api/relay/namespaces/listkeys) |
| **Témata-GetTopicsAsync**<br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [list](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **Fronty-GetQueueAsync** <br/>Service Bus<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **Relé-GetRelaysAsync**<br/>[Získat relé](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [list](/rest/api/relay/wcfrelays/listbynamespace) | 
| **Obor názvůAuthorizationRules-GetNamespaceAuthorizationRuleAsync**<br/>Centrum service bus/událostí/relé GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [getauthorizationrule](/rest/api/servicebus/namespaces/getauthorizationrule) |[getauthorizationrule](/rest/api/eventhub/namespaces/getauthorizationrule) | [getauthorizationrule](/rest/api/relay/namespaces/getauthorizationrule) |
| **Obory názvů DeleteNamespaceAsync**<br/>[Obor názvů Odstranění služby Service Bus](/rest/api/servicebus/delete-namespace)<br/>[Centrum událostí odstraňují obor názvů](/rest/api/eventhub/delete-event-hub)<br/>[Reamátá odstranění oboru názvů](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [delete](/rest/api/servicebus/namespaces/delete) | [delete](/rest/api/eventhub/namespaces/delete) | [delete](/rest/api/relay/namespaces/delete) | 
| **MessagingSKUPlan-GetPlanAsync**<br/>Service Bus/Event Hub/Relay Get Namespace<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **MessagingSKUPlan-UpdatePlanAsync**<br/>Service Bus/Event Hub/Relay Get Namespace<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **Obor názvůAuthorizationRules-UpdateOborOborAuthorizationRuleASyncAsynchronizace**<br/>Service Bus/Event Hub/Relay Get Namespace<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **Obor názvůAuthorizationRules-CreateNamespaceAuthorizationRuleAsync**<br/> 
Centrum/přenos sběrnice/události<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **Obor názvůProperties-GetNamespacePropertiesAsync**<br/>[Služba Service Bus získat obor názvů](/rest/api/servicebus/get-namespace)<br/>[Centra událostí získají obor názvů](/rest/api/eventhub/get-event-hub)<br/>[Relé získat obor názvů](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **RegionCodes-GetRegionCodesAsync**<br/>Service Bus/EventHub/Relay získat obor názvů<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/regions/listbysku) | [listbysku](/rest/api/eventhub/regions/listbysku) | &nbsp; | 
| **Vlastnosti oboru názvů-UpdateNamespacePropertyAsync**<br/>Sběrnice/EventHub/Relé<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[Seznam center událostí](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [list](/rest/api/eventhub/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[Získejte centra událostí](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/eventhubs/get) | &nbsp; | 
| **Obor názvůAuthorizationRules-DeleteOborOborAuthorizationRuleASynchronizace**<br/>Centrum/přenos sběrnice/události<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [odstranit autorizační pravidlo](/rest/api/servicebus/namespaces/deleteauthorizationrule) | [odstranit autorizační pravidlo](/rest/api/eventhub/namespaces/deleteauthorizationrule) | [odstranit autorizační pravidlo](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **Obor názvůAuthorizationRules-GetNamespaceAuthorizationRulesAsync**<br/>Sběrnice/EventHub/Relé<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [pravidla oprávnění seznamu](/rest/api/servicebus/namespaces/listauthorizationrules) | [pravidla oprávnění seznamu](/rest/api/eventhub/namespaces/listauthorizationrules) | [pravidla oprávnění seznamu](/rest/api/relay/namespaces/listauthorizationrules) |
| **Obor názvůDostupnost-IsNamespaceK dispozici**<br/>[Dostupnost oboru názvů služby Service Bus](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability checknameavailability checknameavailability checkname](/rest/api/servicebus/namespaces/checknameavailability) | [checknameavailability checknameavailability checknameavailability checkname](/rest/api/eventhub/namespaces/checknameavailability) | [checknameavailability checknameavailability checknameavailability checkname](/rest/api/relay/namespaces/checknameavailability) |
| **Obory názvů CreateOrUpdateNamespaceAsync**<br/>Centrum/přenos sběrnice/události<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **Témata-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Správce služeb PowerShell – PowerShell správce prostředků
| Správce služeb PowerShell , příkaz (zastaralé) | Nové příkazy Správce prostředků | Novější příkaz Správce zdrojů |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure/get-azuresblocation?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusGeoDRKonfigurace](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [Get-AzServiceBusGeoDRKonfigurace](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure/get-azuresbnamespace?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [New-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Nové pravidlo azservicebusauthorization](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure/new-azuresbnamespace?view=azuresmps-4.0.0) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [Nový-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [Odebrat pravidlo autorizace relé AzureRm](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [Remove-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [Odebrat-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure/remove-azuresbnamespace?view=azuresmps-4.0.0) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [Odebrat azServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Set-AzureRmServiceBusAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Pravidlo autorizace azServiceBus](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>Další kroky
Podívejte se na následující dokumentaci: 

- Nejnovější dokumentace rozhraní REST API
    - [Azure Service Bus](/rest/api/servicebus/)
    - [Centra událostí Azure](/rest/api/eventhub/)
    - [Azure Relay](/rest/api/relay/)
- Nejnovější dokumentace k prostředí PowerShell
    - [Azure Service Bus](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Centra událostí Azure](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [Azure Event Grid](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
