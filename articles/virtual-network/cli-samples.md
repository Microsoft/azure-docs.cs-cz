---
title: Ukázky v Azure CLI pro virtuální síť
description: Přečtěte si o různých ukázkových skriptech, které můžete použít k dokončení úloh v Azure CLI, včetně vytvoření virtuální sítě pro vícevrstvé aplikace.
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
ms.custom: devx-track-azurecli
ms.openlocfilehash: 539d11205ffead52d7f40526f2c712e8cf8b5cdd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501436"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Ukázky v Azure CLI pro virtuální síť

Následující tabulka obsahuje odkazy na skripty Bash s příkazy Azure CLI:

| Skript | Popis |
|----|----|
| [Vytvoření virtuální sítě pro vícevrstvé aplikace](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí. Provoz do front-endové podsítě je omezený na HTTP a SSH, zatímco provoz do back-endové podsítě je omezený na MySQL na portu 3306. |
| [Vytvoření partnerského vztahu mezi dvěma virtuálními sítěmi](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Vytvoří a propojí dvě virtuální sítě ve stejné oblasti. |
| [Směrování provozu přes síťové virtuální zařízení](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí a virtuální počítač, který je schopný směrovat provoz mezi těmito dvěma podsítěmi. |
| [Filtrování příchozího a odchozího síťového provozu virtuálního počítače](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Vytvoří virtuální síť s front-endovou a back-endovou podsítí. Příchozí síťový provoz do front-endové podsítě je omezený na HTTP, HTTPS a SSH. Odchozí provoz do internetu z back-endové podsítě není povolený. |
|[Konfigurace virtuální sítě s duálním zásobníkem IPv4 a IPv6 se základními Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Nasadí virtuální síť s duálním zásobníkem (IPv4 + IPv6) se dvěma virtuálními počítači a Load Balancer Azure Basic s IP adresami IPv4 a IPv6. |
|[Nakonfigurujte virtuální síť s duálním zásobníkem IPv4 a IPv6 pomocí Standard Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Nasadí virtuální síť s duálním zásobníkem (IPv4 + IPv6) se dvěma virtuálními počítači a Standard Load Balancer Azure pomocí veřejných IP adres IPv4 a IPv6. |
|[Kurz: vytvoření a otestování brány NAT – Azure CLI](../virtual-network/tutorial-create-validate-nat-gateway-cli.md)|Vytvoření a ověření brány NAT pomocí zdrojového a cílového virtuálního počítače. |
