---
title: Řešení potíží se službou VNET Gateway a připojeními – Azure REST API
titleSuffix: Azure Network Watcher
description: Tato stránka vysvětluje, jak řešit potíže s Virtual Network branami a připojeními pomocí Azure Network Watcher pomocí REST.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 72f279d90d071b0e5fe7f552a05474bc3b37a82e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011029"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Řešení potíží s Virtual Network bránou a připojením pomocí Azure Network Watcher

> [!div class="op_single_selector"]
> - [Azure Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher poskytuje řadu možností, které se týkají porozumění síťovým prostředkům v Azure. Jednou z těchto funkcí je řešení potíží s prostředky. Řešení potíží s prostředky je možné volat prostřednictvím portálu, PowerShellu, rozhraní příkazového řádku nebo REST API. Při volání Network Watcher zkontroluje stav Virtual Network brány nebo připojení a vrátí své závěry.

Tento článek vás provede různými úlohami správy, které jsou aktuálně k dispozici pro řešení potíží s prostředky.

- [**Řešení potíží s bránou Virtual Network**](#troubleshoot-a-virtual-network-gateway)
- [**Řešení potíží s připojením**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Než začnete

ARMclient se používá k volání REST API s využitím PowerShellu. ARMClient se nachází v čokoládě na [ARMClient při čokoládě](https://chocolatey.org/packages/ARMClient) .

V tomto scénáři se předpokládá, že už jste postupovali podle kroků v části [vytvoření Network Watcher](network-watcher-create.md) k vytvoření Network Watcher.

Seznam podporovaných typů bran najdete v části [podporované typy bran](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Přehled

Řešení potíží s Network Watcher poskytuje možnost řešit problémy, které se týkají Virtual Network bran a připojení. V případě, že se k řešení potíží s prostředky připojí požadavek, protokoly se dotazují a kontrolují. Po dokončení kontroly se vrátí výsledky. Řešení potíží s požadavky rozhraní API jsou dlouho běžící požadavky, což může trvat několik minut, než se výsledek vrátí. Protokoly se ukládají do kontejneru v účtu úložiště.

## <a name="log-in-with-armclient"></a>Přihlášení pomocí ARMClient

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Řešení potíží s bránou Virtual Network


### <a name="post-the-troubleshoot-request"></a>PUBLIKOVÁNÍ žádosti o Poradce při potížích

V následujícím příkladu se dotazuje na stav Virtual Network brány.

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

Vzhledem k tomu, že je tato operace dlouho spuštěná, vrátí se identifikátor URI pro dotazování na operaci a identifikátor URI pro výsledek v hlavičce odpovědi, jak je znázorněno v následující reakci:

**Důležité hodnoty**

* **Azure-AsyncOperation** – Tato vlastnost obsahuje identifikátor URI pro dotaz na operaci asynchronního odstraňování potíží.
* **Umístění** – Tato vlastnost obsahuje identifikátor URI, ve kterém jsou výsledky po dokončení operace.

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

Pomocí identifikátoru URI operací se Dotazujte na průběh operace, jak je vidět v následujícím příkladu:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

I když probíhá operace, odpověď zobrazuje **průběh** , jak je vidět v následujícím příkladu:

```json
{
  "status": "InProgress"
}
```

Po dokončení operace se stav změní na **úspěch**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Načtení výsledků

Po **úspěšném** dokončení stavu volejte metodu GET na identifikátor URI výsledek operace uvnitř a načtěte výsledky.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Následující odpovědi jsou příklady typických odpovědí, které byly vráceny při dotazování na výsledky řešení potíží s bránou. V tématu [vysvětlení výsledků](#understanding-the-results) získáte informace o tom, jaké vlastnosti v odpovědi znamenají.

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


## <a name="troubleshoot-connections"></a>Řešení potíží s připojeními

V následujícím příkladu se dotazuje na stav připojení.

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
> Operace řešení potíží nemůže běžet paralelně na připojení a odpovídajících branách. Operaci je nutné provést před spuštěním v předchozím prostředku.

Vzhledem k tomu, že se jedná o dlouhou spuštěnou transakci, vrátí se v hlavičce odpovědi identifikátor URI pro dotazování na operaci a identifikátor URI pro výsledek, jak je znázorněno v následující reakci:

**Důležité hodnoty**

* **Azure-AsyncOperation** – Tato vlastnost obsahuje identifikátor URI pro dotaz na operaci asynchronního odstraňování potíží.
* **Umístění** – Tato vlastnost obsahuje identifikátor URI, ve kterém jsou výsledky po dokončení operace.

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

Pomocí identifikátoru URI operací se Dotazujte na průběh operace, jak je vidět v následujícím příkladu:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

I když probíhá operace, odpověď zobrazuje **průběh** , jak je vidět v následujícím příkladu:

```json
{
  "status": "InProgress"
}
```

Po dokončení operace se stav změní na **úspěch**.

```json
{
  "status": "Succeeded"
}
```

Následující odpovědi jsou příklady typické odpovědi vracené při dotazování na výsledky řešení potíží s připojením.

### <a name="retrieve-the-results"></a>Načtení výsledků

Po **úspěšném** dokončení stavu volejte metodu GET na identifikátor URI výsledek operace uvnitř a načtěte výsledky.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Následující odpovědi jsou příklady typické odpovědi vracené při dotazování na výsledky řešení potíží s připojením.

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

## <a name="understanding-the-results"></a>Porozumění výsledkům

Text akce poskytuje obecné pokyny k vyřešení tohoto problému. Pokud je možné provést akci pro daný problém, je k dispozici odkaz s dalšími pokyny. V případě, že nejsou k dispozici žádné další doprovodné materiály, odpověď poskytne adresu URL pro otevření případu podpory.  Další informace o vlastnostech odpovědi a o tom, co je zahrnuto, najdete v tématu [Network Watcher řešení potíží – přehled](network-watcher-troubleshoot-overview.md)

Pokyny ke stahování souborů z účtů Azure Storage najdete v tématu [Začínáme s úložištěm objektů BLOB v Azure pomocí .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Průzkumník služby Storage se dá použít jiný nástroj. Další informace o Průzkumník služby Storage najdete na následujícím odkazu: [Průzkumník služby Storage](https://storageexplorer.com/)

## <a name="next-steps"></a>Další kroky

Pokud se změnila nastavení, která zastavují připojení k síti VPN, přečtěte si téma [Správa skupin zabezpečení](../virtual-network/manage-network-security-group.md) sítě a sledujte skupinu zabezpečení sítě a pravidla zabezpečení, která mohou být v dané otázce.