---
title: Vytvoření instance sledovací proces sítě Azure | Microsoft Docs
description: Zjistěte, jak povolit sledovací proces sítě v oblasti Azure.
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
ms.openlocfilehash: 9d3e579cd58bc6c7d67b29998ea5a48a65548b0a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="create-an-azure-network-watcher-instance"></a>Vytvoření instance sledovací proces sítě Azure

Sledovací proces sítě je místní služba, která umožňuje sledovat a diagnostikovat podmínky na úrovni scénář sítě, do a z Azure. Scénář úrovně monitorování umožňuje diagnostikovat problémy na úrovni zobrazení koncová sítě. Diagnostika sítě a k dispozici sledovací proces sítě vizualizace nástroje vám pomůžou pochopit, diagnostikovat a získáte přehled o k síti v Azure.

## <a name="create-a-network-watcher-in-the-portal"></a>Vytvoření sledovací proces sítě v portálu.

Přejděte na **všechny služby** > **sítě** > **sítě sledovacích procesů**. Můžete vybrat všechny odběry, které chcete povolit sledovací proces sítě pro. Tato akce vytvoří sledovací proces sítě v každé oblasti, která je k dispozici.

![Vytvoření sledovací proces sítě](./media/network-watcher-create/figure1.png)

Když povolíte sledovací proces sítě pomocí portálu, název instance sledovací proces sítě se automaticky nastaví na *NetworkWatcher_region_name* kde *region_name* odpovídá oblasti Azure kde je povolena instance. Například sledovací proces sítě povolené v oblasti západní centrální USA s názvem *NetworkWatcher_westcentralus*.

Instance sledovací proces sítě se automaticky vytvoří ve skupině prostředků s názvem *NetworkWatcherRG*. Skupina prostředků je vytvořen, pokud ještě neexistuje.

Pokud chcete přizpůsobit název instance sledovací proces sítě a skupina prostředků je umístěn do, můžete použít Powershell, rozhraní příkazového řádku Azure, rozhraní API REST nebo ARMClient metody popsané v následujících částech. V každé možnosti musí existovat skupina prostředků předtím, než vytvoříte sledovací proces sítě v ní.  

## <a name="create-a-network-watcher-with-powershell"></a>Vytvoření sledovací proces sítě pomocí prostředí PowerShell

Chcete-li vytvořit instanci sledovací proces sítě, spusťte v následujícím příkladu:

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Vytvoření sledovací proces sítě pomocí rozhraní příkazového řádku Azure CLI

Chcete-li vytvořit instanci sledovací proces sítě, spusťte v následujícím příkladu:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Vytvoření sledovací proces sítě pomocí rozhraní REST API

ARMclient se používá k volání rozhraní REST API pomocí prostředí PowerShell. ARMClient se nachází na chocolatey v [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Přihlaste se pomocí ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Vytvoření sledovací proces sítě

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

Teď, když máte instanci sledovací proces sítě, informace o funkcích, které jsou k dispozici:

* [Topologie](network-watcher-topology-overview.md)
* [Zachytávání paketů](network-watcher-packet-capture-overview.md)
* [Ověření IP toku](network-watcher-ip-flow-verify-overview.md)
* [Další směrování](network-watcher-next-hop-overview.md)
* [Zobrazení skupin zabezpečení](network-watcher-security-group-view-overview.md)
* [Protokolování toku NSG](network-watcher-nsg-flow-logging-overview.md)
* [Řešení potíží virtuální síťová brána](network-watcher-troubleshoot-overview.md)

Jakmile instance sledovací proces sítě, můžete povolit zachytáváním paketů v rámci virtuálních počítačů. Další informace, jak zjistit, [vytvořit zaznamenání výstrahy spouštěná paketu](network-watcher-alert-triggered-packet-capture.md)
