---
title: Vytvoření instance Azure Network Watcher | Dokumenty společnosti Microsoft
description: Zjistěte, jak povolit sledování sítě v oblasti Azure.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 77812a3765a027152c957f6dbb7c9b3811a2278f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191179"
---
# <a name="create-an-azure-network-watcher-instance"></a>Vytvoření instance služby Azure Network Watcher

Network Watcher je místní služba, která umožňuje sledovat a diagnostikovat podmínky na úrovni síťového scénáře v Azure, do a z Azure. Monitorování úrovně scénáře umožňuje diagnostikovat problémy v zobrazení na úrovni sítě ukončujícímu až konečnému konci. Nástroje pro diagnostiku a vizualizaci sítě, které jsou k dispozici pomocí nástroje Sledování sítě, vám pomohou pochopit, diagnostikovat a získat přehled o vaší síti v Azure. Sledování sítě je povoleno vytvořením prostředku sledování sítě. Tento prostředek umožňuje využít funkce sledování sítě.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Sledování sítě je automaticky povoleno.
Když ve svém předplatném vytvoříte nebo aktualizujete virtuální síť, automaticky se pro oblast této virtuální sítě povolí Network Watcher. Automatické povolení služby Network Watcher nemá žádný vliv na vaše prostředky ani se s ním nepojí žádné poplatky.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Odhlášení automatického povolení sledování sítě
Pokud se chcete odhlásit z automatického povolení Sledování sítě, můžete tak učinit spuštěním následujících příkazů:

> [!WARNING]
> Odhlášení automatického povolení Sledování sítě je trvalou změnou. Jakmile se odhlásíte, nemůžete se přihlásit, aniž byste [kontaktovali podporu](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Vytvoření sledovacího programu sítě na portálu

Přejděte na **službu All Services** > **Network.** > **Network Watcher** Můžete vybrat všechna předplatná, pro která chcete povolit sledování sítě. Tato akce vytvoří sledovací proces sítě v každé oblasti, která je k dispozici.

![vytvoření sledovacího procesu sítě](./media/network-watcher-create/figure1.png)

Když povolíte sledování sítě pomocí portálu, název instance Network Watcher je automaticky nastaven a *NetworkWatcher_region_name* kde *region_name* odpovídá oblasti Azure, kde je povolena instance. Například sledovací modul sítě povolený v oblasti USA – západ – střed se nazývá *NetworkWatcher_westcentralus*.

Instance Network Watcher je automaticky vytvořena ve skupině prostředků s názvem *NetworkWatcherRG*. Skupina prostředků je vytvořena, pokud ještě neexistuje.

Pokud chcete přizpůsobit název instance Network Watcher a skupiny prostředků, do které je umístěna, můžete použít Powershell, Azure CLI, ROZHRANÍ API REST nebo metody ARMClient popsané v následujících částech. V každé možnosti musí existovat skupina prostředků před vytvořením sledovacího programu sítě v něm.  

## <a name="create-a-network-watcher-with-powershell"></a>Vytvoření sledovacího procesu sítě pomocí prostředí PowerShell

Chcete-li vytvořit instanci sledovacího programu sítě, spusťte následující příklad:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Vytvoření sledovacího procesu sítě pomocí příkazového příkazového příkazu k řešení Azure

Chcete-li vytvořit instanci sledovacího programu sítě, spusťte následující příklad:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Vytvoření sledovacího procesu sítě pomocí rozhraní REST API

ArMclient se používá k volání rozhraní REST API pomocí prostředí PowerShell. ARMClient se nachází na chocolatey na [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Přihlášení pomocí klienta ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Vytvoření sledovacího procesu sítě

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

## <a name="delete-a-network-watcher-in-the-portal"></a>Odstranění sledovacího programu sítě na portálu

Přejděte na **službu All Services** > **Network.** > **Network Watcher**

Pokud ještě nejste k dispozici, vyberte kartu Přehled. Pomocí rozevíracího souboru vyberte předplatné, ve kterém chcete zakázat sledovací modul sítě.
Kliknutím na šipku rozbalte seznam oblastí pro zvolené předplatné. Pro daný, použijte 3 tečky na pravé straně pro přístup k kontextové nabídce.
Klikněte na "Zakázat sledování sítě" pro spuštění zakázání. Budete vyzváni k potvrzení tohoto kroku. Pro pokračování klikněte na Ano.
Na portálu to budete muset udělat individuálně pro každou oblast v každém předplatném.


## <a name="delete-a-network-watcher-with-powershell"></a>Odstranění sledovacího procesu sítě pomocí prostředí PowerShell

Chcete-li odstranit instanci sledovacího programu sítě, spusťte následující příklad:

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>Další kroky

Nyní, když máte instanci sledování sítě, se dozvíte o dostupných funkcích:

* [Topologie](network-watcher-topology-overview.md)
* [Zachytávání paketů](network-watcher-packet-capture-overview.md)
* [Ověření toku protokolu IP](network-watcher-ip-flow-verify-overview.md)
* [Další směrování](network-watcher-next-hop-overview.md)
* [Zobrazení skupin zabezpečení](network-watcher-security-group-view-overview.md)
* [Protokolování toku nsg](network-watcher-nsg-flow-logging-overview.md)
* [Řešení potíží s bránou virtuální sítě](network-watcher-troubleshoot-overview.md)
