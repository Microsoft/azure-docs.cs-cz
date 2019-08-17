---
title: Správa protokolů toku skupin zabezpečení sítě pomocí Azure Network Watcher-PowerShellu | Microsoft Docs
description: Tato stránka vysvětluje, jak spravovat protokoly toku skupin zabezpečení sítě v Azure Network Watcher pomocí prostředí PowerShell.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: c34ed9c5f1b5e422ba9e4e0b12fbaf833c8a4a7c
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563462"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Konfigurace protokolů toku skupiny zabezpečení sítě pomocí PowerShellu

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Protokoly toku skupin zabezpečení sítě jsou funkcí Network Watcher, která vám umožní zobrazit informace o příchozím a odchozím provozu IP přes skupinu zabezpečení sítě. Tyto protokoly toků jsou napsané ve formátu JSON a zobrazují odchozí a příchozí toky na základě jednotlivých pravidel. síťové rozhraní, ke kterému se tok vztahuje, je 5 – informace o toku (zdrojová nebo cílová IP adresa, zdrojový/cílový port, protokol) a pokud byl provoz povolený nebo zakázaný.

## <a name="register-insights-provider"></a>Registrace poskytovatele Insights

Aby protokolování toků fungovalo úspěšně, musí být zaregistrovaný poskytovatel **Microsoft. Insights** . Pokud si nejste jistí, jestli je poskytovatel **Microsoft. Insights** zaregistrovaný, spusťte následující skript.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Povolit protokoly toku skupin zabezpečení sítě a Analýza provozu

V následujícím příkladu se zobrazí příkaz pro povolení protokolů toku:

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceid -WorkspaceLocation $workspaceRegion

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

Účet úložiště, který zadáte, nemůže mít pro něj nakonfigurovaná Síťová pravidla, která omezují přístup k síti jenom na služby Microsoftu nebo konkrétní virtuální sítě. Účet úložiště může být ve stejném nebo jiném předplatném Azure, než NSG, pro který povolíte protokol toku. Pokud používáte jiné odběry, musí být oba přidruženy ke stejnému Azure Active Directory tenantovi. Účet, který použijete pro každé předplatné, musí mít [potřebná oprávnění](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Zakázat protokoly toku Analýza provozu a skupiny zabezpečení sítě

K zakázání analýzy provozu a protokolů toků použijte následující příklad:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Stažení protokolu toku

Umístění úložiště protokolu toku je definováno při vytvoření. Pohodlný nástroj pro přístup k těmto protokolům toků uloženým do účtu úložiště je Průzkumník služby Microsoft Azure Storage, který se dá stáhnout tady: https://storageexplorer.com/

Pokud je zadaný účet úložiště, soubory protokolu Flow se uloží do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
> [!IMPORTANT]
> V současné době dochází k potížím s [protokolem toku NSG (Network Security Group)](network-watcher-nsg-flow-logging-overview.md) pro Network Watcher se z úložiště objektů BLOB automaticky neodstraňují na základě nastavení zásad uchovávání informací. Pokud máte existující nenulové zásady uchovávání informací, doporučujeme, abyste pravidelně odstranili objekty blob úložiště, které jsou po dobu jejich uchování, a nemuseli se jim účtovat poplatky. Další informace o tom, jak odstranit blog úložiště protokolu toku NSG, najdete v tématu [odstranění objektů BLOB úložiště protokolu toku NSG](network-watcher-delete-nsg-flow-log-blobs.md).

Informace o struktuře protokolu najdete v článku [Přehled protokolu toku skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md) .

## <a name="next-steps"></a>Další kroky

Naučte [se vizualizovat protokoly toku NSG pomocí PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md) .

Naučte se [vizualizovat protokoly toku NSG pomocí nástrojů Open Source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) .
