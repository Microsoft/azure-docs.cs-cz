---
title: Protokoly toku spravovat skupiny zabezpečení sítě pomocí služby Azure Network Watcher – rozhraní REST API | Dokumentace Microsoftu
description: Tato stránka vysvětluje, jak spravovat protokoly toků skupin zabezpečení sítě ve službě Azure Network Watcher pomocí rozhraní REST API
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2ab25379-0fd3-4bfe-9d82-425dfc7ad6bb
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 58486ab821b6b01f531f62445a9d4ade1336569a
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332009"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Konfigurace skupiny zabezpečení sítě protokolů toku s využitím rozhraní REST API

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Protokoly toku skupin zabezpečení sítě jsou funkce služby Network Watcher, který vám umožní zobrazit informace o příchozí a odchozí provoz IP přes skupinu zabezpečení sítě. Tyto protokoly toku jsou napsané ve formátu json a zobrazení odchozí a příchozí toků na základě pravidel za NIC toku se vztahuje na 5 řazené kolekce členů informace o toku (zdrojová a cílová IP, zdrojový/cílový Port, protokol), a jestli byl povolený nebo zakázaný provoz.

> [!NOTE] 
> Verze protokoly toku 2 jsou k dispozici pouze v centrální oblasti USA – západ. Povolení verze 2 protokoly v nepodporované oblasti výsledkem bude výstupem do vašeho účtu úložiště protokolů verze 1.

## <a name="before-you-begin"></a>Před zahájením

ARMclient slouží k volání rozhraní REST API pomocí Powershellu. ARMClient se nachází na chocolatey na [ARMClient v Chocolatey](https://chocolatey.org/packages/ARMClient)

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit Network Watcher](network-watcher-create.md) vytvořit Network Watcher.

> [!Important]
> Pro volání rozhraní REST API služby Network Watcher, že název skupiny prostředků v identifikátoru URI žádosti je skupina prostředků, který obsahuje sledovací proces sítě ne prostředky provádíte diagnostických úkonů na.

## <a name="scenario"></a>Scénář

Scénáře popsané v tomto článku se dozvíte, jak povolit, zakázat a dotazování protokolů toku s využitím rozhraní REST API. Další informace o toku loggings skupina zabezpečení sítě najdete v tématu [protokolování toků skupin zabezpečení sítě - přehled](network-watcher-nsg-flow-logging-overview.md).

V tomto scénáři provedete následující:

* Povolení protokolů toku (verze 2)
* Zakázat protokolů toku
* Stav protokoly toku dotazu

## <a name="log-in-with-armclient"></a>Přihlaste se pomocí ARMClient

Přihlaste se k armclient pomocí přihlašovacích údajů Azure.

```PowerShell
armclient login
```

## <a name="register-insights-provider"></a>Registrace poskytovatele Insights

Aby se tok protokolování nemusí fungovat správně **Microsoft.Insights** musí být zaregistrovaný poskytovatel. Pokud si nejste jisti Pokud **Microsoft.Insights** poskytovatel je zaregistrovaný, spusťte následující skript.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Povolit skupiny zabezpečení sítě protokolů toku

Příkaz, který umožní tok protokoly verze 2 je znázorněno v následujícím příkladu. Pro verzi 1 nahraďte pole 'version' '1':

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

Odpověď vrácená z předchozího příkladu vypadá takto:

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

## <a name="disable-network-security-group-flow-logs"></a>Protokoly toku zakázat skupina zabezpečení sítě

Chcete-li zakázat protokolů toku použijte následující příklad. Volání je totéž jako povolení protokolů toku s výjimkou **false** je nastavena pro vlastnost enabled.

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

Odpověď vrácená z předchozího příkladu vypadá takto:

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

Následující dotazy volání REST stav toku zaznamená na skupinu zabezpečení sítě.

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

Následuje příklad odpovědi vrácené:

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

## <a name="download-a-flow-log"></a>Stáhnout protokol toku

Umístění úložiště protokolů toku je definován při vytvoření. Praktický nástroj pro přístup k těmto protokolů toku uložit do účtu úložiště je Microsoft Azure Storage Explorer, které je možné stáhnout tady:  http://storageexplorer.com/

Pokud je určen účet úložiště, jsou uloženy soubory zachytávání paketů do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Další postup

Zjistěte, jak [vizualizaci protokolů toku NSG s Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Zjistěte, jak [vizualizovat vaše protokoly toků NSG s open source nástroje](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
