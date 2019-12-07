---
title: Informace o službě Azure ExpressRoute FastPath
description: Přečtěte si o Azure ExpressRoute FastPath pro odesílání síťového provozu obejít bránu.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: cherylmc
ms.openlocfilehash: 6ff1dac312bcb4df1e1afc9679df09fc8a2b28ff
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897361"
---
# <a name="about-expressroute-fastpath"></a>O ExpressRoute FastPath

Brána virtuální sítě ExpressRoute je navržená pro výměnu síťových tras a směrování síťového provozu. FastPath je navržená tak, aby vylepšila výkon datových cest mezi vaší místní sítí a virtuální sítí. Pokud je povoleno, FastPath odesílá síťový provoz přímo virtuálním počítačům ve virtuální síti a vynechá bránu.

## <a name="requirements"></a>Požadavky

### <a name="circuits"></a>Spoj

FastPath je k dispozici na všech okruhech ExpressRoute.

### <a name="gateways"></a>Brány

FastPath pořád vyžaduje vytvoření brány virtuální sítě pro výměnu tras mezi virtuální sítí a místní sítí. Další informace o branách virtuální sítě a ExpressRoute najdete v tématu [brány virtuální sítě ExpressRoute](expressroute-about-virtual-network-gateways.md).

Brána virtuální sítě musí být buď:

* Mimořádný výkon
* ErGw3AZ

## <a name="supported-features"></a>Podporované funkce

I když FastPath podporuje většinu konfigurací, nepodporuje následující funkce:

* UDR v podsíti brány: Pokud použijete UDR k podsíti brány vaší virtuální sítě, síťový provoz z vaší místní sítě se dál pošle do brány virtuální sítě.

* Partnerský vztah virtuálních sítí: Pokud máte jiné virtuální sítě s partnerským vztahem, který je připojený k ExpressRoute, bude se síťový provoz z vaší místní sítě do ostatních virtuálních sítí (tj. "paprskový" virtuální sítě ") dál posílat do virtuální sítě. brány. Alternativním řešením je připojit všechny virtuální sítě k okruhu ExpressRoute přímo.

* Základní Load Balancer: Pokud nasadíte základní interní nástroj pro vyrovnávání zatížení ve vaší virtuální síti nebo ve službě Azure PaaS, kterou nasadíte ve vaší virtuální síti, použijete základní interní nástroj pro vyrovnávání zatížení, síťový provoz z vaší místní sítě na virtuální IP adresy hostované na Do brány virtuální sítě se pošle základní nástroj pro vyrovnávání zatížení. Řešením je upgradovat základní nástroj pro vyrovnávání zatížení na [standardní nástroj pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

* Privátní odkaz: Pokud se připojíte k [privátnímu koncovému bodu](../private-link/private-link-overview.md) ve vaší virtuální síti z vaší místní sítě, připojení projde bránou virtuální sítě.
 
## <a name="next-steps"></a>Další kroky

Pokud chcete povolit FastPath, přečtěte si téma [propojení virtuální sítě s ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
