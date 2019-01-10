---
title: Správa protokolů toku skupiny zabezpečení sítě pomocí služby Azure Network Watcher – PowerShell | Dokumentace Microsoftu
description: Tato stránka vysvětluje, jak spravovat protokoly toku skupin zabezpečení sítě ve službě Azure Network Watcher pomocí Powershellu
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 120e9295c7e9bd196f40258e8eb8d8d2503cd086
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187524"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Konfigurace protokolů toku skupiny zabezpečení sítě pomocí Powershellu

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Protokoly toku skupin zabezpečení sítě jsou funkce služby Network Watcher, který vám umožní zobrazit informace o příchozí a odchozí provoz IP přes skupinu zabezpečení sítě. Tyto protokoly toku jsou napsané ve formátu json a zobrazení odchozí a příchozí toků na základě pravidel za NIC toku se vztahuje na 5 řazené kolekce členů informace o toku (zdrojová a cílová IP, zdrojový/cílový Port, protokol), a jestli byl povolený nebo zakázaný provoz.

> [!NOTE] 
> Verze protokoly toku 2 jsou k dispozici pouze v centrální oblasti USA – západ. Povolení verze 2 protokoly v nepodporované oblasti může vést k výstupu do vašeho účtu úložiště protokolů verze 1.

## <a name="register-insights-provider"></a>Registrace poskytovatele Insights

Aby se tok protokolování nemusí fungovat správně **Microsoft.Insights** musí být zaregistrovaný poskytovatel. Pokud si nejste jisti Pokud **Microsoft.Insights** poskytovatel je zaregistrovaný, spusťte následující skript.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Protokoly toku povolit skupiny zabezpečení sítě a analýzy provozu

Příkaz povolení protokolů toku můžete vidět v následujícím příkladu:

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
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $worspaceid -WorkspaceLocation $workspaceRegion

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

Účet úložiště, který zadáte, nemůže mít pravidel sítě nakonfigurována, které omezují přístup k síti jenom na služby nebo konkrétní virtuální sítě. Účet úložiště může být ve stejné nebo jiné předplatné Azure, než skupina zabezpečení sítě, která umožňují v protokolu toku. Pokud používáte různých předplatných, musí být obě přidružené ke stejnému tenantovi Azure Active Directory. Musíte mít účet, který používáte pro každé předplatné [potřebná oprávnění](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Zakázat protokoly analýzy provozu a tok skupiny zabezpečení sítě

Zakažte analýzu provozu a protokoly toků pomocí v následujícím příkladu:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Stáhnout protokol toku

Umístění úložiště protokolů toku je definován při vytvoření. Praktický nástroj pro přístup k těmto protokolů toku uložit do účtu úložiště je Microsoft Azure Storage Explorer, které je možné stáhnout tady:  http://storageexplorer.com/

Pokud je určen účet úložiště, jsou uloženy soubory protokolů toku do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Informace o struktuře protokol [tok skupiny zabezpečení sítě protokolu – přehled](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [vizualizaci protokolů toku NSG s Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Zjistěte, jak [vizualizovat vaše protokoly toků NSG s open source nástroje](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
