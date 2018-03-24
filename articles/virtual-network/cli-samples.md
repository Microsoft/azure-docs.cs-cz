---
title: Ukázek Azure CLI pro virtuální sítě | Microsoft Docs
description: Ukázek Azure CLI pro virtuální síť.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 9459bad1060daa0c9f6c34272cecfbc67463be66
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-cli-samples-for-virtual-network"></a>Ukázek Azure CLI pro virtuální síť

Následující tabulka obsahuje odkazy na bash skripty pomocí rozhraní příkazového řádku Azure:

| | |
|----|----|
| [Vytvoření virtuální sítě pro vícevrstvé aplikace](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Vytvoří virtuální síť s podsítí front-end a back-end. Provoz do front-endu podsítě je omezen na protokolu HTTP a SSH, zatímco provozu do podsítě back-end je omezený na MySQL, port 3306. |
| [Peer dvě virtuální sítě](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Vytvoří a připojí dvou virtuálních sítí ve stejné oblasti. |
| [Směrovat provoz prostřednictvím sítě virtuálního zařízení](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Vytvoří virtuální síť s podsítí front-end a back-end a virtuální počítač, který je schopen směrovat provoz mezi dvěma podsítěmi. |
| [Filtrovat příchozí a odchozí provoz sítě virtuálních počítačů](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Vytvoří virtuální síť s podsítí front-end a back-end. Příchozí síťový provoz do front-endu podsítí je omezený na protokolu HTTP, HTTPS a SSH. Odchozí přenosy k Internetu z podsítě back-end není povoleno. |