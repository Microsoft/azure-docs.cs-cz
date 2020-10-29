---
title: Sítě a konektivita řešení Azure VMware
description: Popis připojení a sítě řešení Azure VMware.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 47b5fab8430f4e462f1e4a0e8cb6ddb69ec38c02
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92925015"
---
<!-- Used in introduction.md and concepts-networking.md -->

Řešení Azure VMware nabízí prostředí privátního cloudu, které je přístupné z místních prostředí a prostředí založeného na Azure nebo prostředků. Připojení poskytují služby, jako je Azure ExpressRoute a VPN. Tyto služby vyžadují pro povolení služeb konkrétní rozsahy síťových adres a porty brány firewall.

Při nasazení privátního cloudu se vytvoří privátní sítě pro správu, zřizování a vMotion. Tyto privátní sítě použijte pro přístup k vCenter a NSX Manageru a vMotion nebo nasazení virtuálních počítačů.  ExpressRoute Global Reach slouží k propojení privátních cloudů s místními prostředími. Připojení vyžaduje virtuální síť s okruhem ExpressRoute v rámci vašeho předplatného.

Prostředky, jako jsou webové servery a virtuální počítače, jsou přístupné z Internetu prostřednictvím funkce veřejné IP adresy Azure Virtual WAN.  Ve výchozím nastavení je přístup k Internetu zakázán pro nové privátní cloudy. Další informace najdete v tématu [Jak používat funkci veřejné IP adresy v řešení Azure VMware](../public-ip-usage.md).