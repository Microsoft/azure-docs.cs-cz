---
title: Informace o službě Azure ExpressRoute FastPath
description: Přečtěte si o Azure ExpressRoute FastPath pro odesílání síťového provozu obejít bránu.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: c03be46207e7698d0557729c546488412c0cc5dd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437102"
---
# <a name="about-expressroute-fastpath"></a>Informace o službě ExpressRoute FastPath

Brána virtuální sítě ExpressRoute je navržená pro výměnu síťových tras a směrování síťového provozu. FastPath je navržená tak, aby vylepšila výkon datových cest mezi vaší místní sítí a virtuální sítí. Pokud je povoleno, FastPath odesílá síťový provoz přímo virtuálním počítačům ve virtuální síti a vynechá bránu.

## <a name="requirements"></a>Požadavky

### <a name="circuits"></a>Spoj

FastPath je k dispozici na všech okruhech ExpressRoute.

### <a name="gateways"></a>Brány

FastPath pořád vyžaduje vytvoření brány virtuální sítě pro výměnu tras mezi virtuální sítí a místní sítí. Další informace o branách virtuální sítě a ExpressRoute, včetně informací o výkonu a SKU brány, najdete v tématu [ExpressRoute brány virtuální sítě](expressroute-about-virtual-network-gateways.md).

Aby bylo možné konfigurovat FastPath, musí být brána virtuální sítě buď:

* Mimořádný výkon
* ErGw3AZ

#### <a name="aggthroughput"></a>Odhadované výkonů podle SKU brány
Následující tabulka ukazuje typy brány a odhadovanou funkční. Tato tabulka platí pro model nasazení Resource Manager a pro model nasazení Classic.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Výkon aplikace závisí na několika faktorech, například na celkovou latenci a počet přenosové toky, které aplikace se otevře. Čísla v tabulce představují horní mez, které může aplikace dosáhnout teoreticky v prostředí ideální.
>
>

## <a name="supported-features"></a>Podporované funkce

I když FastPath podporuje většinu konfigurací, nepodporuje následující funkce:

* UDR v podsíti brány: Pokud použijete UDR k podsíti brány vaší virtuální sítě, síťový provoz z vaší místní sítě se dál pošle do brány virtuální sítě.

* Partnerský vztah virtuálních sítí: Pokud máte jiné virtuální sítě s partnerským vztahem, který je připojený k ExpressRoute, bude se síťový provoz z vaší místní sítě do ostatních virtuálních sítí (tj. "paprskový" virtuální sítě ") dál posílat do virtuální sítě. brány. Alternativním řešením je připojit všechny virtuální sítě k okruhu ExpressRoute přímo.

* Základní Load Balancer: Pokud nasadíte základní interní nástroj pro vyrovnávání zatížení ve vaší virtuální síti nebo ve službě Azure PaaS, kterou nasadíte ve vaší virtuální síti, použijete základní interní nástroj pro vyrovnávání zatížení, síťový provoz z vaší místní sítě na virtuální IP adresy hostované na Do brány virtuální sítě se pošle základní nástroj pro vyrovnávání zatížení. Řešením je upgradovat základní nástroj pro vyrovnávání zatížení na [standardní nástroj pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

* Privátní odkaz: Pokud se připojíte k [privátnímu koncovému bodu](../private-link/private-link-overview.md) ve vaší virtuální síti z vaší místní sítě, připojení projde bránou virtuální sítě.
 
## <a name="next-steps"></a>Další kroky

Pokud chcete povolit FastPath, přečtěte si téma [propojení virtuální sítě s ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
