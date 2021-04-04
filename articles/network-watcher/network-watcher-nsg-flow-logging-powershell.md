---
title: Správa protokolů toku NSG – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Tato stránka vysvětluje, jak spravovat protokoly toku skupin zabezpečení sítě v Azure Network Watcher pomocí prostředí PowerShell.
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
ms.openlocfilehash: 771b4ce2999357d729c3ffe557b778cf62a5c0f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98010978"
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
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

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

Umístění úložiště protokolu toku je definováno při vytvoření. Pohodlný nástroj pro přístup k těmto protokolům toků uloženým do účtu úložiště je Průzkumník služby Microsoft Azure Storage, který se dá stáhnout tady:  https://storageexplorer.com/

Pokud je zadaný účet úložiště, soubory protokolu Flow se uloží do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Informace o struktuře protokolu najdete v článku [Přehled protokolu toku skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md) .

## <a name="next-steps"></a>Další kroky

Naučte [se vizualizovat protokoly toku NSG pomocí PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md) .

Naučte se [vizualizovat protokoly toku NSG pomocí nástrojů Open Source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) .
