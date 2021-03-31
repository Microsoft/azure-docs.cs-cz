---
title: Ukázky Azure PowerShellu pro virtuální síť
description: Přečtěte si o ukázkách Azure PowerShell pro správu virtuálních sítí, včetně ukázky pro vytvoření virtuální sítě pro vícevrstvé aplikace.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
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
ms.openlocfilehash: b3107d521fb79e3ea6cfe190abd2ec3fb7859cb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288235"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Ukázky Azure PowerShellu pro virtuální síť

Následující tabulka obsahuje odkazy na skripty Azure PowerShellu:

| Skript | Popis |
|----|----|
| [Vytvoření virtuální sítě pro vícevrstvé aplikace](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí. Provoz do front-endové podsítě je omezený na HTTP, zatímco provoz do back-endové podsítě je omezený na SQL na portu 1433. |
| [Vytvoření partnerského vztahu mezi dvěma virtuálními sítěmi](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Vytvoří a propojí dvě virtuální sítě ve stejné oblasti. |
| [Směrování provozu přes síťové virtuální zařízení](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí a virtuální počítač, který je schopný směrovat provoz mezi těmito dvěma podsítěmi. |
| [Filtrování příchozího a odchozího síťového provozu virtuálního počítače](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí. Příchozí síťový provoz do front-endové podsítě je omezený na HTTP a HTTPS. Odchozí provoz do internetu z back-endové podsítě není povolený. |
|[Konfigurace virtuální sítě s duálním zásobníkem IPv4 a IPv6 se základními Load Balancer](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Nasadí virtuální síť s duálním zásobníkem (IPv4 + IPv6) se dvěma virtuálními počítači a Load Balancer Azure Basic s IP adresami IPv4 a IPv6. |
|[Nakonfigurujte virtuální síť s duálním zásobníkem IPv4 a IPv6 pomocí Standard Load Balancer](./scripts/virtual-network-powershell-sample-ipv6-dual-stack-standard-load-balancer.md)|Nasadí virtuální síť s duálním zásobníkem (IPv4 + IPv6) se dvěma virtuálními počítači a Standard Load Balancer Azure pomocí veřejných IP adres IPv4 a IPv6. |
