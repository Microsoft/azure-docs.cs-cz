---
title: Ukázky rozhraní příkazového řádku Azure | Microsoft Docs
description: Ukázky rozhraní příkazového řádku Azure
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.openlocfilehash: 7977460f61bfdabd399e45e86d9bbf2e5083992b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864123"
---
# <a name="azure-cli-samples-for-networking"></a>Ukázek Azure CLI pro sítě

Následující tabulka obsahuje odkazy na bash skripty, které jsou vytvořené pomocí rozhraní příkazového řádku Azure.

| | |
|-|-|
|**Připojení mezi prostředky Azure**||
| [Vytvoření virtuální sítě pro vícevrstvé aplikace](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří virtuální síť s podsítí front-end a back-end. Provoz do front-endu podsítě je omezen na protokolu HTTP a SSH, zatímco provozu do podsítě back-end je omezený na MySQL, port 3306. |
| [Peer dvě virtuální sítě](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří a připojí dvou virtuálních sítí ve stejné oblasti. |
| [Směrovat provoz prostřednictvím sítě virtuálního zařízení](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří virtuální síť s podsítí front-end a back-end a virtuální počítač, který je schopen směrovat provoz mezi dvěma podsítěmi. |
| [Filtrovat příchozí a odchozí provoz sítě virtuálních počítačů](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří virtuální síť s podsítí front-end a back-end. Příchozí síťový provoz do front-endu podsítí je omezený na protokolu HTTP, HTTPS a SSH. Odchozí přenosy k Internetu z podsítě back-end není povoleno. |
|**Načíst vyrovnávání a provoz směrem**||
| [Přenosy Vyrovnávání zatížení pro virtuální počítače pro vysokou dostupnost](./scripts/load-balancer-linux-cli-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří několik virtuálních počítačů v s vysokou dostupností a konfigurace skupinu s vyrovnáváním zatížení. |
| [Více webů na virtuálních počítačích můžete vyrovnávat zatížení](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Vytvoří dva virtuální počítače s víc konfigurací IP adres, připojený k Azure skupiny dostupnosti, přístupný prostřednictvím Vyrovnávání zatížení Azure. |
| [Přímé provoz v několika oblastech aplikace vysokou dostupnost](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Vytvoří dvě plány služby app, dva webové aplikace, profil správce provozu a dva koncové body správce provozu. |
| | |
