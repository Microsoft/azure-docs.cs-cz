---
title: Vytvoření instance Azure Network Watcher | Dokumentace Microsoftu
description: Zjistěte, jak povolit Network Watcher v oblasti Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ea10e83e8a5963c1ea0073179c15b1c2f3230805
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615194"
---
# <a name="create-an-azure-network-watcher-instance"></a>Vytvoření instance Azure Network Watcher

Network Watcher je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v aplikaci, do a z Azure. Scénář úrovně monitorování, můžete diagnostikovat problémy na úrovni pohledu koncové síťové. Pro diagnostiku sítě a vizualizačních nástrojů, které jsou k dispozici pomocí služby Network Watcher pomáhají porozumět, diagnostice a získání přehledu o vaší síti v Azure. Network Watcher je povolená díky vytváření prostředek Network Watcher. Tento prostředek umožňuje využít možnosti Network Watcher.

## <a name="network-watcher-is-automatically-enabled"></a>Network Watcher se automaticky povolí.
Při vytváření nebo aktualizaci virtuální sítě ve vašem předplatném, Network Watcher se automaticky povolí v oblasti virtuální sítě. Neexistuje žádný vliv na prostředky nebo související poplatky za automaticky povoluje se Network Watcher.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Odhlásit z automatického povolení Network Watcher
Pokud chcete vyjádřit výslovný nesouhlas povolení automatické Network Watcher, provedete to spuštěním následujících příkazů:

> [!WARNING]
> Vyjádří svůj nesouhlas povolení automatické Network Watcher změna je trvalá. Jakmile je odhlásit vás nelze vyjádřit výslovný souhlas bez [kontaktovat podporu](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Na portálu vytvořit Network Watcher

Přejděte do **všechny služby** > **sítě** > **Network Watcher**. Můžete vybrat všechna předplatná, které chcete povolit Network Watcher pro. Tato akce vytvoří v každé oblasti, která je k dispozici sledovací proces sítě.

![Vytvořit network watcher](./media/network-watcher-create/figure1.png)

Když povolíte sledovací proces sítě pomocí portálu, název instance Network Watcher se automaticky nastaví na *NetworkWatcher_region_name* kde *region_name* odpovídá na základě oblasti Azure kde je povolena instance. Například s názvem Network Watcher povolena v oblasti střed USA – západ *NetworkWatcher_westcentralus*.

Instance Network Watcher se automaticky vytvoří ve skupině prostředků s názvem *NetworkWatcherRG*. Pokud ještě neexistuje, se vytvoří skupina prostředků.

Pokud chcete upravit název instance Network Watcheru a skupinu prostředků je umístěn do, můžete použít prostředí Powershell, rozhraní příkazového řádku Azure, rozhraní REST API nebo ARMClient metod popsaných v následující části. V každé možnosti skupinu prostředků, musí existovat v něm vytvoříte sledovací proces sítě.  

## <a name="create-a-network-watcher-with-powershell"></a>Vytvoření sledovací proces sítě pomocí Powershellu

K vytvoření instance služby Network Watcher, spusťte v následujícím příkladu:

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Pomocí Azure CLI vytvořit Network Watcher

K vytvoření instance služby Network Watcher, spusťte v následujícím příkladu:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Vytvořit Network Watcher pomocí rozhraní REST API

ARMclient slouží k volání rozhraní REST API pomocí Powershellu. ARMClient se nachází na chocolatey na [ARMClient v Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Přihlaste se pomocí ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Vytvořit network watcher

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>Další postup

Teď, když máte instanci služby Network Watcher, další informace o funkcích, které jsou k dispozici:

* [Topologie](network-watcher-topology-overview.md)
* [Zachytávání paketů](network-watcher-packet-capture-overview.md)
* [Ověření IP toku](network-watcher-ip-flow-verify-overview.md)
* [Další směrování](network-watcher-next-hop-overview.md)
* [Zobrazení skupin zabezpečení](network-watcher-security-group-view-overview.md)
* [Protokolování toků NSG](network-watcher-nsg-flow-logging-overview.md)
* [Řešení potíží virtuální síťová brána](network-watcher-troubleshoot-overview.md)

Po vytvoření instance Network Watcher můžete povolit zachytávání paketů v rámci virtuálních počítačů. Další informace o postupu [vytvořit zachytávání paketů upozornění aktivovaných](network-watcher-alert-triggered-packet-capture.md)
