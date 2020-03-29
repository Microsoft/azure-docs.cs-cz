---
title: Úvod k dalšímu směrování v Azure Network Watcher | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled funkce dalšího směrování sledování sítě.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 33c22b5b68b6677e8cf271dc185007316ec44500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844054"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Diagnostikovat problémy se směrováním virtuálních strojů pomocí dalšího směrování

Provoz z virtuálního počítače (VM) se odesílá do cíle na základě efektivních tras spojených se síťovým rozhraním (NIC). Další směrování získá další typ směrování a IP adresu paketu z konkrétního virtuálního počítačů a nic. Znalost dalšího směrování vám pomůže určit, zda je provoz směrován do zamýšleného cíle nebo zda je provoz odesílán nikam. Nesprávná konfigurace tras, kde je provoz směrován do místního umístění nebo virtuálního zařízení, může vést k problémům s připojením. Další směrování také vrátí směrovací tabulku přidruženou k dalšímu směrování. Pokud je trasa definována jako uživatelem definovaná trasa, je tato trasa vrácena. V opačném případě vrátí další funkce směrování **systémovou trasu**.

![další přehled směrování](./media/network-watcher-next-hop-overview/figure1.png)

Další směrování, které může být vráceno další možností směrování, je následující:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Žádný

Další informace o jednotlivých typech směrování naleznete v [tématu Přehled směrování](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Další kroky

To learn how to use next hop to diagnose VM network routing problems, see Diagnose VM network routing problems using the [Azure portal](diagnose-vm-network-routing-problem.md), [PowerShell](diagnose-vm-network-routing-problem-powershell.md), or the [Azure CLI](diagnose-vm-network-routing-problem-cli.md).
