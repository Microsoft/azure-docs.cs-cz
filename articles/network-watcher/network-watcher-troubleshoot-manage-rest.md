---
title: Poradce při potížích s bránou virtuální sítě a připojení – rozhraní AZURE REST API
titleSuffix: Azure Network Watcher
description: Tato stránka vysvětluje, jak řešit potíže s bránami virtuálnísítě a připojení mandatorního sledování sítě pomocí rest
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: ab9f7fd95d7081b66e05dfd3d6a5ef47eb3c4053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840668"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Poradce při potížích s bránou virtuální sítě a připojeními pomocí nástroje Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portál](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [ROZHRANÍ API PRO ODPOČINEK](network-watcher-troubleshoot-manage-rest.md)

Network Watcher poskytuje mnoho funkcí, pokud jde o pochopení vašich síťových prostředků v Azure. Jednou z těchto možností je řešení potíží s prostředky. Řešení potíží s prostředky lze volat prostřednictvím portálu, prostředí PowerShell, ROZHRANÍ API nebo rozhraní REST API. Při volání, Network Watcher zkontroluje stav brány virtuální sítě nebo připojení a vrátí jeho zjištění.

Tento článek vás provede různými úkoly správy, které jsou aktuálně k dispozici pro řešení potíží s prostředky.

- [**Poradce při potížích s bránou virtuální sítě**](#troubleshoot-a-virtual-network-gateway)
- [**Poradce při potížích s připojením**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Než začnete

ARMclient se používá k volání rozhraní REST API pomocí prostředí PowerShell. ARMClient se nachází na chocolatey na [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

Tento scénář předpokládá, že jste již postupovali podle kroků v [části Vytvoření sledovacího programu sítě](network-watcher-create.md) k vytvoření sledovacího programu sítě.

Seznam podporovaných typů bran naleznete na stránce [Podporované typy bran](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Přehled

Řešení potíží s sledováním sítě poskytuje potíže s odstraňováním potíží, které vznikají s bránami virtuální sítě a připojeními. Při požadavku na řešení potíží s prostředky protokoly jsou dotazování a kontrolovány. Po dokončení kontroly jsou vráceny výsledky. Požadavky na řešení potíží s rozhraním API jsou dlouho běžící požadavky, které může trvat několik minut vrátit výsledek. Protokoly jsou uloženy v kontejneru v účtu úložiště.

## <a name="log-in-with-armclient"></a>Přihlášení pomocí klienta ARMClient

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Poradce při potížích s bránou virtuální sítě


### <a name="post-the-troubleshoot-request"></a>ODESLÁNÍ požadavku na řešení potíží

Následující příklad se dotazuje na stav brány virtuální sítě.

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


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" $requestBody -verbose
```

Vzhledem k tomu, že tato operace je již dlouho spuštěna, identifikátor URI pro dotazování na operaci a identifikátor URI pro výsledek je vrácen v hlavičce odpovědi, jak je znázorněno v následující odpovědi:

**Důležité hodnoty**

* **Azure-AsyncOperation** - Tato vlastnost obsahuje identifikátor URI pro dotaz na operaci řešení potíží s Async
* **Umístění** – tato vlastnost obsahuje identifikátor URI, kde jsou výsledky po dokončení operace

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

### <a name="query-the-async-operation-for-completion"></a>Dotaz na asynchronní operaci pro dokončení

Pomocí identifikátoru URI operací můžete dotazovat na průběh operace, jak je vidět v následujícím příkladu:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Zatímco operace probíhá, odpověď ukazuje **InProgress,** jak je vidět v následujícím příkladu:

```json
{
  "status": "InProgress"
}
```

Po dokončení operace se stav změní na **Succeeded**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Načtení výsledků

Jakmile je **vrácený**stav úspěšný , zavolejte metodu GET na operaciVýsledek identifikátoru URI k načtení výsledků.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Následující odpovědi jsou příklady typické degradované odpovědi vrácené při dotazování na výsledky řešení potíží s bránou. Viz [Principy výsledků](#understanding-the-results) získat vysvětlení o tom, co vlastnosti v odpovědi znamená.

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
          "actionUri": "https://azure.microsoft.com/support",
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
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Poradce při potížích s připojením

Následující příklad dotazuje stav připojení.

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
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 $requestBody"
```

> [!NOTE]
> Operaci poradce při potížích nelze spustit paralelně na připojení a jeho odpovídající brány. Operace musí být dokončena před spuštěním na předchozí prostředek.

Vzhledem k tomu, že se jedná o dlouho běžící transakci, je v hlavičce odpovědi vrácen identifikátor URI pro dotazování na operaci a identifikátor URI pro výsledek, jak je znázorněno v následující odpovědi:

**Důležité hodnoty**

* **Azure-AsyncOperation** - Tato vlastnost obsahuje identifikátor URI pro dotaz na operaci řešení potíží s Async
* **Umístění** – tato vlastnost obsahuje identifikátor URI, kde jsou výsledky po dokončení operace

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

### <a name="query-the-async-operation-for-completion"></a>Dotaz na asynchronní operaci pro dokončení

Pomocí identifikátoru URI operací můžete dotazovat na průběh operace, jak je vidět v následujícím příkladu:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Zatímco operace probíhá, odpověď ukazuje **InProgress,** jak je vidět v následujícím příkladu:

```json
{
  "status": "InProgress"
}
```

Po dokončení operace se stav změní na **Succeeded**.

```json
{
  "status": "Succeeded"
}
```

Následující odpovědi jsou příklady typické odpovědi vrácené při dotazování na výsledky řešení potíží s připojením.

### <a name="retrieve-the-results"></a>Načtení výsledků

Jakmile je **vrácený**stav úspěšný , zavolejte metodu GET na operaciVýsledek identifikátoru URI k načtení výsledků.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Následující odpovědi jsou příklady typické odpovědi vrácené při dotazování na výsledky řešení potíží s připojením.

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
          "actionUri": "https://azure.microsoft.com/support",
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
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Pochopení výsledků

Text akce obsahuje obecné pokyny, jak problém vyřešit. Pokud lze pro tento problém přijmout akci, je k dispozici odkaz s dalšími pokyny. V případě, že neexistuje žádné další pokyny, odpověď poskytuje adresu URL otevřít případ podpory.  Další informace o vlastnostech odpovědi a o tom, co je zahrnuto, naleznete v [přehledu řešení potíží s sledováním sítě.](network-watcher-troubleshoot-overview.md)

Pokyny ke stahování souborů z účtů azure storage najdete v článku [Začínáme s úložištěm objektů blob Azure pomocí rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Dalším nástrojem, který lze použít, je Průzkumník úložiště. Další informace o Průzkumníku úložiště naleznete zde na následujícím odkazu: [Průzkumník úložiště](https://storageexplorer.com/)

## <a name="next-steps"></a>Další kroky

Pokud byla změněna nastavení, která zastaví připojení k síti VPN, [přečtěte si](../virtual-network/manage-network-security-group.md) informace o sledování skupiny zabezpečení sítě a pravidel zabezpečení, která se mohou dít, v tématu Správa skupin zabezpečení sítě.
