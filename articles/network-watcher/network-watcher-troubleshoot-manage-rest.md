---
title: Řešení potíží s brány virtuální sítě a připojení pomocí sledovací proces sítě Azure - REST | Microsoft Docs
description: Tato stránka vysvětluje postup řešení potíží s brány virtuální sítě a připojení s sledovací proces sítě Azure pomocí REST
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e4d5f195-b839-4394-94ef-a04192766e55
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 35762d5781d5437a25eaf9942e50f60846ae1ae2
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779137"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Řešení potíží s Brána virtuální sítě a připojení pomocí sledovací proces sítě Azure

> [!div class="op_single_selector"]
> - [Azure Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Sledovací proces sítě obsahuje řadu funkcí ve vztahu k pochopení síťovým prostředkům v Azure. Jeden z těchto funkcí je prostředek řešení potíží. Řešení potíží s prostředků je možné volat prostřednictvím portálu, prostředí PowerShell, rozhraní příkazového řádku nebo REST API. Při volání, sledovací proces sítě kontroluje stav bránu virtuální sítě nebo připojení a vrátí nalezených výsledcích.

Tento článek vás provede úloh jiný správy, které jsou aktuálně dostupné pro řešení potíží s prostředků.

- [**Řešení potíží s bránu virtuální sítě**](#troubleshoot-a-virtual-network-gateway)
- [**Vyřešte potíže připojením**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Než začnete

ARMclient se používá k volání rozhraní REST API pomocí prostředí PowerShell. ARMClient se nachází na chocolatey v [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě.

Seznam podporovaných brány typy návštěvě [typy podporované brány](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Přehled

Řešení potíží s sledovací proces sítě poskytuje možnost odstraňování problémů, které nastat u brány virtuální sítě a připojení. Po odeslání žádosti k prostředku, řešení potíží s protokoly jsou dotazování a prověřovány. Po dokončení kontroly budou vráceny výsledky. Poradce při potížích rozhraní API žádosti, které jsou dlouhé systémem požadavků, které může trvat několik minut vrácení výsledku. Protokoly se ukládají v kontejneru v účtu úložiště.

## <a name="log-in-with-armclient"></a>Přihlaste se pomocí ARMClient

```PowerShell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Řešení potíží s bránu virtuální sítě


### <a name="post-the-troubleshoot-request"></a>Odeslat požadavek Poradce při potížích

Následující příklad dotazuje se na stav brány virtuální sítě.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$vnetGatewayName = "ContosoVNETGateway"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @"
{
'TargetResourceId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/virtualNetworkGateways/${vnetGatewayName}',
'Properties': {
'StorageId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}',
'StoragePath': 'https://${storageAccountName}.blob.core.windows.net/${containerName}'
}
}
"@


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" -verbose
```

Vzhledem k tomu, že tato operace je dlouho spuštěný, identifikátor URI pro dotazování operaci a identifikátor URI pro výsledek je vrácená v hlavičce odpovědi, jak je znázorněno v následující odpověď:

**Důležité hodnoty**

* **Azure AsyncOperation** -tato vlastnost obsahuje identifikátor URI pro dotaz Async řešení operace
* **Umístění** -tato vlastnost obsahuje identifikátor URI, kde jsou výsledky při dokončení operace

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Dotaz pro dokončení asynchronní operace

Použijte operace URI dotazu pro průběh operace, jak je vidět v následujícím příkladu:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Když probíhá operace, zobrazí odpověď **InProgress** jak je vidět v následujícím příkladu:

```json
{
  "status": "InProgress"
}
```

Po dokončení operace se stav změní na **úspěšné**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Načíst výsledky

Jakmile se stav vrátí je **úspěšné**, volání metody GET na výsledek URI načíst výsledky.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Příklady typických sníženou odpověď vrácená při dotazování na výsledky řešení potíží s bránu, jsou tyto odpovědi. V tématu [seznámení s výsledky](#understanding-the-results) získat vysvětlení na významu vlastnosti v odpovědi.

```json
{
  "startTime": "2017-01-12T10:31:41.562646-08:00",
  "endTime": "2017-01-12T18:31:48.677Z",
  "code": "Degraded",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN Gateway"
        },
        {
          "actionText": "If your VPN gateway isn't up and running by the expected resolution time, contact support",
          "actionUri": "http://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN gateway is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "http://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Odstraňování potíží s připojením

V následujícím příkladu se dotazuje stav připojení.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$connectionName = "VNET2toVNET1Connection"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @{
"TargetResourceId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/connections/${connectionName}",
"Properties": {
"StorageId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}",
"StoragePath": "https://${storageAccountName}.blob.core.windows.net/${containerName}"
}

}
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 "
```

> [!NOTE]
> Poradce při potížích operaci nelze spustit souběžně na jeho odpovídající brány a připojení. Operaci musíte dokončit před spuštěním na dřívější prostředek.

Vzhledem k tomu, že toto je dlouhotrvající transakci, v hlavičce odpověď se vrátí identifikátor URI pro dotazování operaci a identifikátor URI pro výsledek, jak je znázorněno v následující odpověď:

**Důležité hodnoty**

* **Azure AsyncOperation** -tato vlastnost obsahuje identifikátor URI pro dotaz Async řešení operace
* **Umístění** -tato vlastnost obsahuje identifikátor URI, kde jsou výsledky při dokončení operace

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Dotaz pro dokončení asynchronní operace

Použijte operace URI dotazu pro průběh operace, jak je vidět v následujícím příkladu:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Když probíhá operace, zobrazí odpověď **InProgress** jak je vidět v následujícím příkladu:

```json
{
  "status": "InProgress"
}
```

Po dokončení operace se stav změní na **úspěšné**.

```json
{
  "status": "Succeeded"
}
```

Příklady typických odpověď vrácená při dotazování na výsledky řešení potíží s připojení, jsou tyto odpovědi.

### <a name="retrieve-the-results"></a>Načíst výsledky

Jakmile se stav vrátí je **úspěšné**, volání metody GET na výsledek URI načíst výsledky.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Příklady typických odpověď vrácená při dotazování na výsledky řešení potíží s připojení, jsou tyto odpovědi.

```json
{
  "startTime": "2017-01-12T14:09:19.1215346-08:00",
  "endTime": "2017-01-12T22:09:23.747Z",
  "code": "UnHealthy",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This 
is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN gateway"
        },
        {
          "actionText": "If your VPN Connection isn't up and running by the expected resolution time, contact support",
          "actionUri": "http://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN Connection is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "http://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Seznámení s výsledky

Text akce obsahuje obecné pokyny k vyřešení problému. Pokud může být akce pro tento problém, je k dispozici odkaz s další pokyny. V případě, že tam, kde není žádná další pokyny, odpověď obsahuje adresu url pro otevření případu podpory.  Další informace o vlastnostech odpovědi a co je součástí najdete v článku [řešení sledovací proces sítě – přehled](network-watcher-troubleshoot-overview.md)

Pokyny ke stahování souborů z účty azure storage, najdete v části [Začínáme s Azure Blob storage pomocí rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Jiný nástroj, který je možné je Storage Explorer. Další informace o Storage Explorer naleznete zde na následující odkaz: [Storage Explorer](http://storageexplorer.com/)

## <a name="next-steps"></a>Další postup

Pokud nastavení bylo změněno tohoto připojení VPN zastavit, přečtěte si téma [spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md) sledovat pravidla zabezpečení sítě skupiny a zabezpečení, které může být nejistá.
