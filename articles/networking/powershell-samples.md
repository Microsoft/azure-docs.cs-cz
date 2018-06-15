---
title: Ukázky Azure PowerShell | Microsoft Docs
description: Ukázky Azure PowerShell
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
ms.author: georgewallace
ms.openlocfilehash: 0bca4fb6874bd265f0ae9faeb4219abeb4ffb6d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23926351"
---
# <a name="azure-powershell-samples-for-networking"></a>Ukázky pro sítě Azure PowerShell

Následující tabulka obsahuje odkazy na skripty, které jsou vytvořené pomocí Azure PowerShell.

| | |
|-|-|
|**Připojení mezi prostředky Azure**||
| [Vytvoření virtuální sítě pro vícevrstvé aplikace](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří virtuální síť s podsítí front-end a back-end. Provoz do front-endu podsítě je omezený na protokolu HTTP, zatímco provozu do podsítě back-end je omezený na SQL, portu 1433. |
| [Peer dvě virtuální sítě](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří a připojí dvou virtuálních sítí ve stejné oblasti. |
| [Směrovat provoz prostřednictvím sítě virtuálního zařízení](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří virtuální síť s podsítí front-end a back-end a virtuální počítač, který je schopen směrovat provoz mezi dvěma podsítěmi. |
| [Filtrovat příchozí a odchozí provoz sítě virtuálních počítačů](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří virtuální síť s podsítí front-end a back-end. Příchozí síťový provoz do front-endu podsítí je omezený na protokolu HTTP a HTTPS... Odchozí přenosy k Internetu z podsítě back-end není povoleno. |
|**Načíst vyrovnávání a provoz směrem**||
| [Přenosy Vyrovnávání zatížení pro virtuální počítače pro vysokou dostupnost](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří několik virtuálních počítačů v s vysokou dostupností a konfigurace skupinu s vyrovnáváním zatížení. |
| [Více webů na virtuálních počítačích můžete vyrovnávat zatížení](./scripts/load-balancer-windows-powershell-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří dva virtuální počítače s víc konfigurací IP adres, připojený k Azure skupiny dostupnosti, přístupný prostřednictvím Vyrovnávání zatížení Azure. |
| [Přímé provoz v několika oblastech aplikace vysokou dostupnost](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Vytvoří dvě plány služby app, dva webové aplikace, profil správce provozu a dva koncové body správce provozu. |
| | |
