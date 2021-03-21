---
title: Správa protokolů toku NSG – Azure REST API
titleSuffix: Azure Network Watcher
description: Tato stránka vysvětluje, jak spravovat protokoly toku skupin zabezpečení sítě v Azure Network Watcher s REST API
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: ea24716dba5e4e824a4fa986602007035be8e365
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018373"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Konfigurace protokolů toku skupiny zabezpečení sítě pomocí REST API

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Protokoly toku skupin zabezpečení sítě jsou funkcí Network Watcher, která vám umožní zobrazit informace o příchozím a odchozím provozu IP přes skupinu zabezpečení sítě. Tyto protokoly toků jsou napsané ve formátu JSON a zobrazují odchozí a příchozí toky na základě jednotlivých pravidel. síťové rozhraní, ke kterému se tok vztahuje, je 5 – informace o toku (zdrojová nebo cílová IP adresa, zdrojový/cílový port, protokol) a pokud byl provoz povolený nebo zakázaný.

## <a name="before-you-begin"></a>Než začnete

ARMclient se používá k volání REST API s využitím PowerShellu. ARMClient se nachází v čokoládě na [ARMClient při čokoládě](https://chocolatey.org/packages/ARMClient) .

V tomto scénáři se předpokládá, že už jste postupovali podle kroků v části [vytvoření Network Watcher](network-watcher-create.md) k vytvoření Network Watcher.

> [!Important]
> Pro Network Watcher REST API volá název skupiny prostředků v identifikátoru URI požadavku skupina prostředků, která obsahuje Network Watcher, ne prostředky, na kterých provádíte diagnostické akce.

## <a name="scenario"></a>Scenario

Scénář popsaný v tomto článku ukazuje, jak povolit, zakázat a dotazovat protokoly toku pomocí REST API. Další informace o protokolování toků skupin zabezpečení sítě najdete v článku [protokolování toku skupin zabezpečení sítě – přehled](network-watcher-nsg-flow-logging-overview.md).

V tomto scénáři provedete tyto kroky:

* Povolit protokoly toku (verze 2)
* Zakázat protokoly toků
* Stav protokolů toku dotazů

## <a name="log-in-with-armclient"></a>Přihlášení pomocí ARMClient

Přihlaste se k armclient s přihlašovacími údaji Azure.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Registrace poskytovatele Insights

Aby protokolování toků fungovalo úspěšně, musí být zaregistrovaný poskytovatel **Microsoft. Insights** . Pokud si nejste jistí, jestli je poskytovatel **Microsoft. Insights** zaregistrovaný, spusťte následující skript.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Povolit protokoly toku skupin zabezpečení sítě

V následujícím příkladu je uveden příkaz pro povolení protokolů Flow verze 2. Pro verzi 1 nahraďte pole ' Version ' ' 1 ':

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

## <a name="disable-network-security-group-flow-logs"></a>Zakázat protokoly toků skupin zabezpečení sítě

Chcete-li zakázat protokoly toku, použijte následující příklad. Volání je stejné jako povolení protokolů toku, s výjimkou **hodnoty false** nastavené pro vlastnost Enabled.

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

## <a name="query-flow-logs"></a>Protokoly toku dotazů

Následující volání REST dotazuje stav protokolů toku ve skupině zabezpečení sítě.

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

Následuje příklad vracené odpovědi:

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

Umístění úložiště protokolu toku je definováno při vytvoření. Pohodlný nástroj pro přístup k těmto protokolům toků uloženým do účtu úložiště je Průzkumník služby Microsoft Azure Storage, který se dá stáhnout tady:  https://storageexplorer.com/

Pokud je zadaný účet úložiště, soubory zachytávání paketů se uloží do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Další kroky

Naučte [se vizualizovat protokoly toku NSG pomocí PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md) .

Naučte se [vizualizovat protokoly toku NSG pomocí nástrojů Open Source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) .
