---
title: Správa protokolů toku nsg – azure CLI
titleSuffix: Azure Network Watcher
description: Tato stránka vysvětluje, jak spravovat protokoly toku skupiny zabezpečení sítě v Azure Network Watcher s Azure CLI
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
ms.openlocfilehash: 285d19dbd0e7b8a94eada66f837d33b787006f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840957"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurace protokolů toku skupiny zabezpečení sítě pomocí azure CLI

> [!div class="op_single_selector"]
> - [Portál Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [ROZHRANÍ API PRO ODPOČINEK](network-watcher-nsg-flow-logging-rest.md)

Protokoly toku skupiny zabezpečení sítě jsou funkcí sledovacího programu sítě, která umožňuje zobrazit informace o příchozím přenosu dat a odchozím přenosech IP prostřednictvím skupiny zabezpečení sítě. Tyto protokoly toku jsou zapsány ve formátu json a zobrazit odchozí a příchozí toky na základě pravidla, nic tok se vztahuje na, 5-řazené kolekce členů informace o toku (Zdroj/Cíl IP, Zdroj/cílový port, protokol), a pokud provoz byl povolen nebo odepřen.

Chcete-li provést kroky v tomto článku, je třeba [nainstalovat rozhraní příkazového řádku Azure pro Mac, Linux a Windows (CLI).](/cli/azure/install-azure-cli)

## <a name="register-insights-provider"></a>Registrace poskytovatele Insights

Aby protokolování toku fungovalo úspěšně, musí být zaregistrován poskytovatel **Microsoft.Insights.** Pokud si nejste jisti, zda je poskytovatel **Microsoft.Insights** registrovaný, spusťte následující skript.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Povolit protokoly toku skupiny zabezpečení sítě

Příkaz pro povolení protokolů toku je uveden v následujícím příkladu:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

Zadaný účet úložiště pro něj nemůže mít nakonfigurovaná síťová pravidla, která omezují přístup k síti pouze na služby společnosti Microsoft nebo na konkrétní virtuální sítě. Účet úložiště může být ve stejném nebo jiném předplatném Azure, než je skupina zabezpečení zabezpečení, pro kterou povolíte protokol toku. Pokud používáte různá předplatná, musí být obě přidružená ke stejnému tenantovi služby Azure Active Directory. Účet, který používáte pro každé předplatné, musí mít [potřebná oprávnění](required-rbac-permissions.md). 

Pokud je účet úložiště v jiné skupině prostředků nebo předplatné než skupina zabezpečení sítě, zadejte úplné ID účtu úložiště, nikoli jeho název. Pokud je například účet úložiště ve skupině prostředků s názvem *RG-Storage*, nikoli ve specifikování *storageAccountName* v předchozím příkazu, zadáte */subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Zakázat protokoly toku skupiny zabezpečení sítě

Pomocí následujícího příkladu zakažte protokoly toku:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Stažení protokolu toku

Umístění úložiště protokolu toku je definováno při vytváření. Pohodlným nástrojem pro přístup k těmto protokolům toku uloženým do účtu úložiště je Průzkumník úložiště Microsoft Azure, který si můžete stáhnout zde:https://storageexplorer.com/

Pokud je zadán účet úložiště, soubory protokolu toku jsou uloženy do účtu úložiště v následujícím umístění:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [vizualizovat protokoly toku nsg pomocí PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Přečtěte si, jak [vizualizovat protokoly toku nsg pomocí nástrojů s otevřeným zdrojovým kódem](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
