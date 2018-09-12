---
title: Úvod do dalšího segmentu směrování ve službě Azure Network Watcher | Dokumentace Microsoftu
description: Tento článek obsahuje přehled služby Network Watcher funkce dalšího segmentu směrování.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 28eacdce922e26d391cf34f78cb03ead9c6887a1
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391262"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Diagnostika potíží se směrováním virtuálních počítačů pomocí dalšího segmentu směrování

Přenosy z virtuálního počítače (VM) se odesílají do cílového umístění podle efektivních tras, které jsou přidružené k síťové rozhraní (NIC). Další směrování získá typ dalšího segmentu směrování a IP adresa z paketu z konkrétního virtuálního počítače a síťovou kartu Znalost, další segment směrování vám pomůže určit, zda provoz se směřuje do požadovaného cíle, nebo zda provoz je odesílán nikde. Nesprávná konfigurace trasy, kde provoz se směřuje do místního umístění, nebo virtuální zařízení, může vést k problémům s připojením. Další směrování také vrátí hodnotu směrovací tabulky přidružené k další segment směrování. Pokud trasa je definován jako trasy definované uživatelem, je vrácena danou trasu. V opačném případě vrátí hodnotu dalšího směrování **systémová trasa**.

![Přehled směrování](./media/network-watcher-next-hop-overview/figure1.png)

Další směrování, které se můžou vrátit další segment směrování schopnosti jsou následující:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Žádný

Další informace o jednotlivých typ dalšího segmentu směrování najdete v tématu [Přehled směrování](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Další postup

Další informace o použití dalšího segmentu směrování k diagnostice potíží se směrováním sítě virtuálních počítačů, najdete v článku [Diagnostika virtuálního počítače network problémy se směrováním](diagnose-vm-network-routing-problem.md).
