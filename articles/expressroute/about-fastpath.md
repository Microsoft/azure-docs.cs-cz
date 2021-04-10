---
title: Informace o službě Azure ExpressRoute FastPath
description: Přečtěte si o Azure ExpressRoute FastPath pro odesílání síťového provozu obejít bránu.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: duau
ms.openlocfilehash: c953668d6b2e364e6e703b1769317f1c520317ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654369"
---
# <a name="about-expressroute-fastpath"></a>Informace o službě ExpressRoute FastPath

Brána virtuální sítě ExpressRoute je navržená pro výměnu síťových tras a směrování síťového provozu. FastPath je navržená tak, aby vylepšila výkon datových cest mezi vaší místní sítí a virtuální sítí. Pokud je povoleno, FastPath odesílá síťový provoz přímo virtuálním počítačům ve virtuální síti a vynechá bránu.

## <a name="requirements"></a>Požadavky

### <a name="circuits"></a>Spoj

FastPath je k dispozici na všech okruhech ExpressRoute.

### <a name="gateways"></a>Brány

FastPath pořád vyžaduje vytvoření brány virtuální sítě pro výměnu tras mezi virtuální sítí a místní sítí. Další informace o branách virtuální sítě a ExpressRoute, včetně informací o výkonu a SKU brány, najdete v tématu [ExpressRoute brány virtuální sítě](expressroute-about-virtual-network-gateways.md).

Aby bylo možné konfigurovat FastPath, musí být brána virtuální sítě buď:

* Ultra Performance
* ErGw3AZ

> [!IMPORTANT]
> Pokud plánujete používat FastPath se soukromým partnerským vztahem využívajícím IPv6 přes ExpressRoute, nezapomeňte vybrat ErGw3AZ pro **SKU**. Všimněte si, že je k dispozici pouze pro okruhy pomocí ExpressRoute Direct.
> 
>

## <a name="limitations"></a>Omezení

I když FastPath podporuje většinu konfigurací, nepodporuje následující funkce:

* UDR v podsíti brány: Tento UDR nemá žádný vliv na síťový provoz, který FastPath odesílá přímo z vaší místní sítě do virtuálních počítačů ve službě Azure Virtual Network. 

* Partnerský vztah virtuálních sítí: Pokud máte jiné virtuální sítě s partnerským vztahem, který je připojený k ExpressRoute, bude síťový provoz z vaší místní sítě do ostatních virtuálních sítí (tj. "paprskový" virtuální sítě ") dál odesílán do brány virtuální sítě. Alternativním řešením je připojit všechny virtuální sítě k okruhu ExpressRoute přímo.

* Základní Load Balancer: Pokud nasadíte základní interní nástroj pro vyrovnávání zatížení ve vaší virtuální síti nebo ve službě Azure PaaS, kterou nasadíte ve vaší virtuální síti, se použije základní interní nástroj pro vyrovnávání zatížení, do brány virtuální sítě se pošle síťový provoz z vaší místní sítě do virtuálních IP adres hostovaných na úrovni Basic Load Balancer. Řešením je upgradovat základní nástroj pro vyrovnávání zatížení na [standardní nástroj pro vyrovnávání zatížení](../load-balancer/load-balancer-overview.md).

* Privátní odkaz: Pokud se připojíte k [privátnímu koncovému bodu](../private-link/private-link-overview.md) ve vaší virtuální síti z vaší místní sítě, připojení projde bránou virtuální sítě.
 
## <a name="next-steps"></a>Další kroky

Pokud chcete povolit FastPath, přečtěte si téma [propojení virtuální sítě s ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
