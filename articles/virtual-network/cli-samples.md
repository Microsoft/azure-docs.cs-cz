---
title: Ukázky v Azure CLI pro virtuální síť | Microsoft Docs
description: Ukázky v Azure CLI pro virtuální síť.
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
ms.openlocfilehash: 4caa19ad22faa0a921fb6397e5b376b65a29e519
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249072"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Ukázky v Azure CLI pro virtuální síť

Následující tabulka obsahuje odkazy na skripty Bash s příkazy Azure CLI:

| | |
|----|----|
| [Vytvoření virtuální sítě pro vícevrstvé aplikace](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí. Provoz do front-endové podsítě je omezený na HTTP a SSH, zatímco provoz do back-endové podsítě je omezený na MySQL na portu 3306. |
| [Vytvoření partnerského vztahu mezi dvěma virtuálními sítěmi](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Vytvoří a propojí dvě virtuální sítě ve stejné oblasti. |
| [Směrování provozu přes síťové virtuální zařízení](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí a virtuální počítač, který je schopný směrovat provoz mezi těmito dvěma podsítěmi. |
| [Filtrování příchozího a odchozího síťového provozu virtuálního počítače](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí. Příchozí síťový provoz do front-endové podsítě je omezený na HTTP, HTTPS a SSH. Odchozí provoz do internetu z back-endové podsítě není povolený. |
|[Konfigurace protokolu IPv4 + IPv6 duální zásobník virtuální sítě s Load balancer úrovně Basic](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Nasadí virtuální síť duální sadou protokolů (IPv4 + IPv6) s dva virtuální počítače a Azure Load balancer úrovně Basic s protokoly IPv4 a IPv6 veřejné IP adresy. |
|[Konfigurace protokolu IPv4 + IPv6 duální zásobník virtuální sítě s Load balanceru úrovně Standard](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Nasadí virtuální síť duální sadou protokolů (IPv4 + IPv6) s dva virtuální počítače a Azure Load balancer úrovně Standard s protokoly IPv4 a IPv6 veřejné IP adresy. |