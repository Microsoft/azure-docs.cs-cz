---
title: Správa protokolů toku nsg – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Tato stránka vysvětluje, jak spravovat protokoly toku skupiny zabezpečení sítě v Azure Network Watcher s PowerShellem
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
ms.openlocfilehash: 9612afdb63c6988c0027f003caeacd456b5e50e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840940"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Konfigurace protokolů toku skupiny zabezpečení sítě pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> - [Portál Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [ROZHRANÍ API PRO ODPOČINEK](network-watcher-nsg-flow-logging-rest.md)

Protokoly toku skupiny zabezpečení sítě jsou funkcí sledovacího programu sítě, která umožňuje zobrazit informace o příchozím přenosu dat a odchozím přenosech IP prostřednictvím skupiny zabezpečení sítě. Tyto protokoly toku jsou zapsány ve formátu json a zobrazit odchozí a příchozí toky na základě pravidla, nic tok se vztahuje na, 5-řazené kolekce členů informace o toku (Zdroj/Cíl IP, Zdroj/cílový port, protokol), a pokud provoz byl povolen nebo odepřen.

## <a name="register-insights-provider"></a>Registrace poskytovatele Insights

Aby protokolování toku fungovalo úspěšně, musí být zaregistrován poskytovatel **Microsoft.Insights.** Pokud si nejste jisti, zda je poskytovatel **Microsoft.Insights** registrovaný, spusťte následující skript.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Povolení protokolů toku skupiny zabezpečení sítě a analýzy provozu

Příkaz pro povolení protokolů toku je uveden v následujícím příkladu:

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

Zadaný účet úložiště pro něj nemůže mít nakonfigurovaná síťová pravidla, která omezují přístup k síti pouze na služby společnosti Microsoft nebo na konkrétní virtuální sítě. Účet úložiště může být ve stejném nebo jiném předplatném Azure, než je skupina zabezpečení zabezpečení, pro kterou povolíte protokol toku. Pokud používáte různá předplatná, musí být obě přidružená ke stejnému tenantovi služby Azure Active Directory. Účet, který používáte pro každé předplatné, musí mít [potřebná oprávnění](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Zakázání protokolů toku traffic analytics a skupiny zabezpečení sítě

Pomocí následujícího příkladu můžete zakázat analýzu provozu a protokoly toku:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Stažení protokolu toku

Umístění úložiště protokolu toku je definováno při vytváření. Pohodlným nástrojem pro přístup k těmto protokolům toku uloženým do účtu úložiště je Průzkumník úložiště Microsoft Azure, který si můžete stáhnout zde:https://storageexplorer.com/

Pokud je zadán účet úložiště, soubory protokolu toku jsou uloženy do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Informace o struktuře protokolu naleznete v části [Přehled toku skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [vizualizovat protokoly toku nsg pomocí PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Přečtěte si, jak [vizualizovat protokoly toku nsg pomocí nástrojů s otevřeným zdrojovým kódem](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
