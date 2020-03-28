---
title: Ukázky Azure PowerShellu pro virtuální síť
description: Ukázky Azure PowerShellu pro virtuální síť.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: de752cdacf17193d5be95b2b9f887938ace2d50f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74091873"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Ukázky Azure PowerShellu pro virtuální síť

Následující tabulka obsahuje odkazy na skripty Azure PowerShellu:

| | |
|----|----|
| [Vytvoření virtuální sítě pro vícevrstvé aplikace](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí. Provoz do front-endové podsítě je omezený na HTTP, zatímco provoz do back-endové podsítě je omezený na SQL na portu 1433. |
| [Vytvoření partnerského vztahu mezi dvěma virtuálními sítěmi](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Vytvoří a propojí dvě virtuální sítě ve stejné oblasti. |
| [Směrování provozu přes síťové virtuální zařízení](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí a virtuální počítač, který je schopný směrovat provoz mezi těmito dvěma podsítěmi. |
| [Filtrování příchozího a odchozího síťového provozu virtuálního počítače](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí. Příchozí síťový provoz do front-endové podsítě je omezený na HTTP a HTTPS. Odchozí provoz do internetu z back-endové podsítě není povolený. |
|[Konfigurace virtuální sítě iPv4 + IPv6 se dvěma zásobníky pomocí základního nástroje pro vyrovnávání zatížení](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Nasazuje virtuální síť s dvěma zásobníky (IPv4+IPv6) se dvěma virtuálními počítači a nástrojem Pro vyrovnávání zatížení Azure Basic s veřejnými IP adresami IPv4 a IPv6. |
|[Konfigurace virtuální sítě iPv4 + IPv6 se dvěma zásobníky se standardním nástrojem pro vyrovnávání zatížení](./scripts/virtual-network-powershell-sample-ipv6-dual-stack-standard-load-balancer.md)|Nasazuje virtuální síť s dvěma zásobníky (IPv4+IPv6) se dvěma virtuálními počítači a standardním nástrojem pro vyrovnávání zatížení Azure s veřejnými IP adresami IPv4 a IPv6. |
