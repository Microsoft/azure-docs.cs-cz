---
title: O Azure ExpressRoute FastPath
description: Informace o Azure ExpressRoute FastPath pro odesílání síťových přenosů vynecháním brány
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 265004b1171d1df95b3090676d5836b951c28a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282847"
---
# <a name="about-expressroute-fastpath"></a>Informace o službě ExpressRoute FastPath

Brána virtuální sítě ExpressRoute je určena k výměně síťových tras a síťovým přenosům. FastPath je navržen tak, aby zlepšil výkon datové cesty mezi místní sítí a virtuální sítí. Pokud je povoleno, FastPath odešle síťový provoz přímo do virtuálních počítačů ve virtuální síti, obcházet bránu.

## <a name="requirements"></a>Požadavky

### <a name="circuits"></a>Obvody

FastPath je k dispozici na všech obvodech ExpressRoute.

### <a name="gateways"></a>Brány

FastPath stále vyžaduje, aby byla vytvořena brána virtuální sítě pro výměnu tras mezi virtuální sítí a místní sítí. Další informace o branách virtuálních sítí a ExpressRoute, včetně informací o výkonu a virtuálních zařízení brány brány, najdete v tématu [Brány virtuální sítě ExpressRoute](expressroute-about-virtual-network-gateways.md).

Chcete-li nakonfigurovat fastpath, brána virtuální sítě musí být buď:

* Ultra výkon
* Ergw3Az

## <a name="supported-features"></a>Podporované funkce

Zatímco FastPath podporuje většinu konfigurací, nepodporuje následující funkce:

* UDR v podsíti brány: Pokud použijete UDR na podsíť brány virtuální sítě, bude síťový provoz z místní sítě nadále odesílán do brány virtuální sítě.

* Partnerský vztah virtuální sítě: Pokud máte jiné virtuální sítě, které jsou partnerem s tou, která je připojená k ExpressRoute, bude se síťový provoz z místní sítě do jiných virtuálních sítí (tj. takzvaných virtuálních sítí "Paprsky") nadále odesílat do virtuální sítě Brána. Řešení je připojit všechny virtuální sítě k okruhu ExpressRoute přímo.

* Základní nástroj pro vyrovnávání zatížení: Pokud nasadíte základní interní nástroj pro vyrovnávání zatížení ve virtuální síti nebo službu Azure PaaS, kterou nasadíte ve virtuální síti, používá základní interní nástroj pro vyrovnávání zatížení, síťový provoz z místní sítě na virtuální IP adresy hostované na Základní nástroj pro vyrovnávání zatížení bude odeslán do brány virtuální sítě. Řešením je upgrade základního systému vyrovnávání zatížení na [standardní systém vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

* Privátní odkaz: Pokud se připojíte k [privátnímu koncovému bodu](../private-link/private-link-overview.md) ve virtuální síti z místní sítě, připojení bude procházet bránou virtuální sítě.
 
## <a name="next-steps"></a>Další kroky

Informace o povolení programu FastPath najdete [v tématu Propojení virtuální sítě s technologií ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
