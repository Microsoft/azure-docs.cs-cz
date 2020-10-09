---
title: Seznámení s dalším segmentem směrování v Azure Network Watcher | Microsoft Docs
description: Tento článek poskytuje přehled Network Watcher možnosti dalšího směrování.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76844054"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Použití dalšího směrování k diagnostice problémů s směrováním virtuálních počítačů

Provoz z virtuálního počítače se pošle do cíle na základě efektivních tras přidružených k síťovému rozhraní (NIC). Další segment směrování Získá typ dalšího segmentu směrování a IP adresu paketu z konkrétního virtuálního počítače a síťové karty. Znalost dalšího směrování vám pomůže určit, jestli se má provoz směrovat do zamýšleného cíle, nebo jestli se provoz posílá nikde. Nesprávná konfigurace tras, kde je přenos směrován do místního umístění nebo virtuální zařízení, může vést k problémům s připojením. Další segment směrování také vrátí směrovací tabulku přidruženou k dalšímu segmentu směrování. Pokud je trasa definovaná jako trasa definovaná uživatelem, tato trasa se vrátí. V opačném případě funkce Next Hop vrátí **systémovou trasu**.

![Přehled dalšího segmentu směrování](./media/network-watcher-next-hop-overview/figure1.png)

Další segmenty, které může vrátit funkce dalšího směrování, jsou následující:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Žádné

Další informace o jednotlivých typech dalších segmentů směrování najdete v tématu [Přehled směrování](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak pomocí dalšího směrování diagnostikovat problémy se směrováním sítě virtuálních počítačů, najdete v tématu Diagnostika problémů s směrováním sítě virtuálních počítačů pomocí [Azure Portal](diagnose-vm-network-routing-problem.md), [PowerShellu](diagnose-vm-network-routing-problem-powershell.md)nebo rozhraní příkazového [řádku Azure](diagnose-vm-network-routing-problem-cli.md).
