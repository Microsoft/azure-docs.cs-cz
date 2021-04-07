---
title: Sítě a konektivita řešení Azure VMware
description: Popis připojení a sítě řešení Azure VMware.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 96dd93f1db5dc3ddcbb883313e19c6aed8a256da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462574"
---
<!-- Used in introduction.md and concepts-networking.md -->

Řešení Azure VMware nabízí prostředí privátního cloudu přístupné z místních a prostředků založených na Azure. Služby, jako je Azure ExpressRoute, připojení VPN nebo Azure Virtual WAN, dodávají připojení. Tyto služby vyžadují pro povolení služeb konkrétní rozsahy síťových adres a porty brány firewall.

Při nasazení privátního cloudu se vytvoří privátní sítě pro správu, zřizování a vMotion. Tyto privátní sítě použijte pro přístup k vCenter a NSX Manageru a vMotion nebo nasazení virtuálních počítačů.  

ExpressRoute Global Reach slouží k propojení privátních cloudů s místními prostředími. Připojení vyžaduje virtuální síť s okruhem ExpressRoute v místním prostředí v rámci vašeho předplatného.

Virtuální počítače nasazené v privátním cloudu jsou přístupné pro Internet prostřednictvím funkce veřejné IP adresy virtuální sítě WAN Azure.  Ve výchozím nastavení je přístup k Internetu zakázán pro nové privátní cloudy. Další informace najdete v tématu [Jak používat funkci veřejné IP adresy v řešení Azure VMware](../public-ip-usage.md).

