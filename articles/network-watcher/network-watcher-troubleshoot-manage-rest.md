---
title: Řešení potíží s brány virtuální sítě a připojení pomocí Azure Network Watcher – REST | Dokumentace Microsoftu
description: Tato stránka vysvětluje, jak řešit potíže brány virtuální sítě a připojení pomocí služby Azure Network Watcher pomocí rozhraní REST
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
ms.openlocfilehash: 2c65b595e4c56b8c69d38a6e2f30ae8e57bba3f4
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344100"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Řešení potíží s brány virtuální sítě a připojení pomocí Azure Network Watcher

> [!div class="op_single_selector"]
> - [Azure Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Sledovací proces sítě poskytuje mnoho funkcí, jako má vztah k pochopení síťovým prostředkům v Azure. Jeden z těchto funkcí je prostředek řešení potíží. Řešení potíží s prostředků lze volat na portálu, Powershellu, rozhraní příkazového řádku nebo rozhraní REST API. Při volání, Network Watcheru kontroluje stav brány virtuální sítě nebo připojení a vrátí jeho zjištění.

Tento článek vás provede jiné úlohy, které jsou aktuálně k dispozici pro řešení potíží s prostředků.

- [**Řešení potíží s bránu virtuální sítě**](#troubleshoot-a-virtual-network-gateway)
- [**Řešení potíží s připojení**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Než začnete

ARMclient slouží k volání rozhraní REST API pomocí Powershellu. ARMClient se nachází na chocolatey na [ARMClient v Chocolatey](https://chocolatey.org/packages/ARMClient)

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit Network Watcher](network-watcher-create.md) vytvořit Network Watcher.

Seznam podporovaných brány najdete typy [typy brány nepodporuje](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Přehled

Network Watcher troubleshooting poskytuje možnosti, které vznikají pomocí brány virtuální sítě a připojení k řešení potíží. Po odeslání žádosti k prostředku, řešení potíží s protokoly jsou dotazování a prozkoumat. Po dokončení kontroly výsledky jsou vráceny. Žádosti rozhraní API Poradce při potížích se dlouho trvající požadavky, což může trvat několik minut, než vrácení výsledku. Protokoly se ukládají v kontejneru v účtu úložiště.

## <a name="log-in-with-armclient"></a>Přihlaste se pomocí ARMClient

```PowerShell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Řešení potíží s bránu virtuální sítě


### <a name="post-the-troubleshoot-request"></a>ODESLÁNÍ žádosti o řešení potíží

Následující příklad zadá dotaz na stav brány virtuální sítě.

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

Protože tato operace je dlouho spuštěný, identifikátor URI pro dotazování na operaci a identifikátor URI pro výsledek se vrátí v hlavičce odpovědi, jak je znázorněno v následující odpověď:

**Hodnoty je důležité**

* **Azure-AsyncOperation** – tato vlastnost obsahuje identifikátor URI pro dotaz asynchronní řešení potíží s operace
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

### <a name="query-the-async-operation-for-completion"></a>Dotaz pro dokončení asynchronní operace

Použijte operace identifikátoru URI dotazu pro průběh operace, jak je znázorněno v následujícím příkladu:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Když probíhá operace, odpověď ukazuje **InProgress** jak je znázorněno v následujícím příkladu:

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

Jakmile se vrátí stav **Succeeded**, volání metody GET na identifikátor URI k načtení výsledků výsledek.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Příklady typických degradovaný odpovědi vrácené při dotazování na výsledky brány pro řešení potíží, jsou tyto odpovědi. Zobrazit [Principy výsledky](#understanding-the-results) chcete získat další informace na významu vlastnosti v odpovědi.

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


## <a name="troubleshoot-connections"></a>Řešení potíží s připojením

Následující příklad zadá dotaz na stav připojení.

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
> Poradce při potížích operaci nelze spustit souběžně na jeho odpovídající brány a připojení. Operaci musíte dokončit před spuštěním na předchozí zdroj.

Protože to je dlouhodobá transakce v hlavičce odpovědi se vrátí identifikátor URI pro dotazování na operaci a identifikátor URI pro výsledek, jak je znázorněno v následující odpověď:

**Hodnoty je důležité**

* **Azure-AsyncOperation** – tato vlastnost obsahuje identifikátor URI pro dotaz asynchronní řešení potíží s operace
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

### <a name="query-the-async-operation-for-completion"></a>Dotaz pro dokončení asynchronní operace

Použijte operace identifikátoru URI dotazu pro průběh operace, jak je znázorněno v následujícím příkladu:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Když probíhá operace, odpověď ukazuje **InProgress** jak je znázorněno v následujícím příkladu:

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

Příklady typických odpovědi vrácené při dotazování na výsledky připojení pro řešení potíží, jsou tyto odpovědi.

### <a name="retrieve-the-results"></a>Načtení výsledků

Jakmile se vrátí stav **Succeeded**, volání metody GET na identifikátor URI k načtení výsledků výsledek.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Příklady typických odpovědi vrácené při dotazování na výsledky připojení pro řešení potíží, jsou tyto odpovědi.

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

## <a name="understanding-the-results"></a>Principy výsledky

Text akce obsahuje obecné pokyny k vyřešení daného problému. Pokud mohou se akce pro tento problém, je k dispozici odkaz Další informace. V případě, pokud neexistuje žádné další doprovodné materiály, odpověď obsahuje adresu url pro otevření případu podpory.  Další informace o vlastnostech odpovědi a co je součástí [přehled řešení potíží s sledovací proces sítě](network-watcher-troubleshoot-overview.md)

Pokyny ke stahování souborů z účtů úložiště azure, najdete v tématu [Začínáme s Azure Blob storage pomocí .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Dalším nástrojem, který je možné je Průzkumníka služby Storage. Další informace o Průzkumníku služby Storage najdete tady na následující odkaz: [Průzkumníka služby Storage](http://storageexplorer.com/)

## <a name="next-steps"></a>Další postup

Pokud byly změněny nastavení této možnosti připojení sítě VPN stop, přečtěte si téma [spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md) vysledovat pravidla zabezpečení sítě skupiny a zabezpečení, které mohou být nejistá.
