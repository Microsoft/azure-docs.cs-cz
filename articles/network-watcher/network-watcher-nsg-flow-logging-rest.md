---
title: Správa protokolů toku nsg – rozhraní AZURE REST API
titleSuffix: Azure Network Watcher
description: Tato stránka vysvětluje, jak spravovat protokoly toku skupiny zabezpečení sítě v Azure Network Watcher s rozhraním REST API
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7cc47414dc985f6fc2fff3c57d809f307b142e30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840923"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Konfigurace protokolů toku skupiny zabezpečení sítě pomocí rozhraní REST API

> [!div class="op_single_selector"]
> - [Portál Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [ROZHRANÍ API PRO ODPOČINEK](network-watcher-nsg-flow-logging-rest.md)

Protokoly toku skupiny zabezpečení sítě jsou funkcí sledovacího programu sítě, která umožňuje zobrazit informace o příchozím přenosu dat a odchozím přenosech IP prostřednictvím skupiny zabezpečení sítě. Tyto protokoly toku jsou zapsány ve formátu json a zobrazit odchozí a příchozí toky na základě pravidla, nic tok se vztahuje na, 5-řazené kolekce členů informace o toku (Zdroj/Cíl IP, Zdroj/cílový port, protokol), a pokud provoz byl povolen nebo odepřen.

## <a name="before-you-begin"></a>Než začnete

ARMclient se používá k volání rozhraní REST API pomocí prostředí PowerShell. ARMClient se nachází na chocolatey na [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

Tento scénář předpokládá, že jste již postupovali podle kroků v [části Vytvoření sledovacího programu sítě](network-watcher-create.md) k vytvoření sledovacího programu sítě.

> [!Important]
> Pro sledování sítě, rozhraní REST API volá název skupiny prostředků v požadavku URI je skupina prostředků, která obsahuje sledovací proces sítě, nikoli prostředky, na kterých provádíte diagnostické akce.

## <a name="scenario"></a>Scénář

Scénář, který je popsán v tomto článku ukazuje, jak povolit, zakázat a protokoly toku dotazu pomocí rozhraní REST API. Další informace o protokolování toku skupiny zabezpečení sítě naleznete v článku [Protokolování toku skupiny zabezpečení sítě – přehled](network-watcher-nsg-flow-logging-overview.md).

V tomto scénáři budete:

* Povolení protokolů toku (verze 2)
* Zakázání protokolů toku
* Stav protokolů toku dotazu

## <a name="log-in-with-armclient"></a>Přihlášení pomocí klienta ARMClient

Přihlaste se k armclient u svých přihlašovacích údajů Azure.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Registrace poskytovatele Insights

Aby protokolování toku fungovalo úspěšně, musí být zaregistrován poskytovatel **Microsoft.Insights.** Pokud si nejste jisti, zda je poskytovatel **Microsoft.Insights** registrovaný, spusťte následující skript.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Povolit protokoly toku skupiny zabezpečení sítě

Příkaz pro povolení protokolů toku verze 2 je uveden v následujícím příkladu. Pro verzi 1 nahraďte pole "verze" polem "1":

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Odpověď vrácená z předchozího příkladu je následující:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="disable-network-security-group-flow-logs"></a>Zakázat protokoly toku skupiny zabezpečení sítě

Pomocí následujícího příkladu zakažte protokoly toku. Volání je stejné jako povolení protokolů toku, s výjimkou **false** je nastavena pro povolenou vlastnost.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Odpověď vrácená z předchozího příkladu je následující:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="query-flow-logs"></a>Protokoly toku dotazu

Následující volání REST se dotazuje na stav protokolů toku ve skupině zabezpečení sítě.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

Následuje příklad vrácené odpovědi:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
   "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="download-a-flow-log"></a>Stažení protokolu toku

Umístění úložiště protokolu toku je definováno při vytváření. Pohodlným nástrojem pro přístup k těmto protokolům toku uloženým do účtu úložiště je Průzkumník úložiště Microsoft Azure, který si můžete stáhnout zde:https://storageexplorer.com/

Pokud je zadán účet úložiště, soubory pro digitalizaci paketů jsou uloženy do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [vizualizovat protokoly toku nsg pomocí PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Přečtěte si, jak [vizualizovat protokoly toku nsg pomocí nástrojů s otevřeným zdrojovým kódem](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
