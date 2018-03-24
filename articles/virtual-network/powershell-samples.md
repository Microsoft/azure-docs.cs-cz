---
title: Ukázky pro virtuální síť Azure PowerShell | Microsoft Docs
description: Ukázek Azure PowerShell pro virtuální síť.
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
ms.openlocfilehash: c9cb8da2a6a7512daa6721af90d5b21c6ba5e8e5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Ukázky pro virtuální síť Azure PowerShell

Následující tabulka obsahuje odkazy na skripty prostředí Azure Powershell:

| | |
|----|----|
| [Vytvoření virtuální sítě pro vícevrstvé aplikace](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Vytvoří virtuální síť s podsítí front-end a back-end. Provoz do front-endu podsítě je omezený na protokolu HTTP, zatímco provozu do podsítě back-end je omezený na SQL, portu 1433. |
| [Peer dvě virtuální sítě](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Vytvoří a připojí dvou virtuálních sítí ve stejné oblasti. |
| [Směrovat provoz prostřednictvím sítě virtuálního zařízení](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Vytvoří virtuální síť s podsítí front-end a back-end a virtuální počítač, který je schopen směrovat provoz mezi dvěma podsítěmi. |
| [Filtrovat příchozí a odchozí provoz sítě virtuálních počítačů](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Vytvoří virtuální síť s podsítí front-end a back-end. Příchozí síťový provoz do front-endu podsítí je omezený na protokolu HTTP a HTTPS. Odchozí přenosy k Internetu z podsítě back-end není povoleno. |
