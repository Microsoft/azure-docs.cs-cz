---
title: Ukázky Azure PowerShellu | Microsoft Docs
description: Ukázky Azure PowerShellu
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: 38ee43b805f39e1a702033a53eb7bbc13c43aab6
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875492"
---
# <a name="azure-powershell-samples-for-networking"></a>Ukázky Azure PowerShell pro sítě

Následující tabulka obsahuje odkazy na skripty vytvořené pomocí Azure PowerShell.

| | |
|-|-|
|**Připojení mezi prostředky Azure**||
| [Vytvoření virtuální sítě pro vícevrstvé aplikace](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí. Provoz do front-endové podsítě je omezený na HTTP, zatímco provoz do back-endové podsítě je omezený na SQL na portu 1433. |
| [Vytvoření partnerského vztahu mezi dvěma virtuálními sítěmi](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří a propojí dvě virtuální sítě ve stejné oblasti. |
| [Směrování provozu přes síťové virtuální zařízení](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí a virtuální počítač, který je schopný směrovat provoz mezi těmito dvěma podsítěmi. |
| [Filtrování příchozího a odchozího síťového provozu virtuálního počítače](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí. Příchozí síťový provoz do front-endové podsítě je omezený na HTTP a HTTPS.. Odchozí provoz do Internetu z back-endové podsítě není povolený. |
|**Vyrovnávání zatížení a směr provozu**||
| [Vyrovnávání zatížení provozu do virtuálních počítačů pro zajištění vysoké dostupnosti](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří několik virtuálních počítačů v s vysokou dostupností a s vyrovnáváním zatížení konfigurace. |
| [Vyvažovat zatížení u více webů na virtuálních počítačích](./scripts/load-balancer-windows-powershell-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří dva virtuální počítače s několika konfigurací IP, připojený k skupině dostupnosti Azure, přístupné prostřednictvím služby Azure Load Balancer. |
| [Přímý provoz napříč několika oblastmi pro zajištění vysoké dostupnosti aplikací](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Vytvoří dva plány služby App Service, dvě webové aplikace, profil služby Traffic Manager a dva koncové body Traffic Manageru. |
| | |
