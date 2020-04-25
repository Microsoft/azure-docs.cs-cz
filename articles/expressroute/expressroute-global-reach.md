---
title: 'Azure ExpressRoute: připojení k Microsoft Cloud pomocí Global Reach'
description: Tento článek vysvětluje ExpressRoute Global Reach.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: 4c326a556530fd778c1178f11b79d2aba613b4c1
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82136579"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute je soukromý a odolný způsob, jak připojit vaše místní sítě k Microsoft Cloud. Přístup k mnoha cloudovým službám Microsoftu, jako je Azure a Office 365, můžete získat z privátního datového centra nebo vaší firemní sítě. Můžete mít například pobočku v síti San Francisco s okruhem ExpressRoute v silikonovém sedlu a další pobočkou v Londýně s okruhem ExpressRoute ve stejném městě. Obě pobočky můžou mít vysokou rychlost připojení k prostředkům Azure v USA – západ a Velká Británie – jih. Firemní pobočky ale nemůžou přímo navzájem měnit data. Jinými slovy, 10.0.1.0/24 může odesílat data do 10.0.3.0/24 a 10.0.4.0/24, ale ne do 10.0.2.0/24.

![nedotýkají][1]

Pomocí **ExpressRoute Global REACH**můžete propojit ExpressRoute okruhy a vytvořit tak soukromou síť mezi místními sítěmi. Ve výše uvedeném příkladu s přidáním ExpressRoute Global Reach vaše síť San Francisco Office (10.0.1.0/24) může přímo vyměňovat data s vaší aplikací v Londýně (10.0.2.0/24) prostřednictvím stávajících okruhů ExpressRoute a přes globální síť Microsoftu. 

![řetězce][2]

## <a name="use-case"></a>Případ použití
ExpressRoute Global Reach je navržená tak, aby doplnila implementaci sítě WAN poskytovatele služeb a připojila firemní pobočky po celém světě. Například pokud váš poskytovatel služeb primárně pracuje v USA a má propojení všech větví v USA, ale poskytovatel služeb nefunguje v Japonsku a Hongkong, s ExpressRoute Global Reach můžete pracovat s místním poskytovatelem služeb a společnost Microsoft bude připojit vaše pobočky k těm, které jsou v USA, pomocí ExpressRoute a naší globální sítě.

![případ použití][3]

## <a name="availability"></a>Dostupnost 
ExpressRoute Global Reach v současné době jsou podporovány na následujících místech.

* Austrálie
* Kanada
* Francie
* Německo
* Hongkong – zvláštní správní oblast
* Irsko
* Japonsko
* Jižní Korea
* Nizozemsko
* Nový Zéland
* Singapur
* Švýcarsko
* Spojené království
* Spojené státy

Vaše okruhy ExpressRoute musí být vytvořené v [umístěních partnerských vztahů ExpressRoute](expressroute-locations.md) ve výše uvedených zemích nebo oblastech. Pokud chcete povolit ExpressRoute Global Reach mezi [různými geopolitickými oblastmi](expressroute-locations.md), vaše okruhy musí být SKU úrovně Premium.

## <a name="next-steps"></a>Další kroky
1. [Zobrazit nejčastější dotazy k Global Reach](expressroute-faqs.md#globalreach)
2. [Informace o tom, jak povolit Global Reach](expressroute-howto-set-global-reach.md)
3. [Přečtěte si, jak propojit okruh ExpressRoute s vaší virtuální sítí.](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram bez globálního dosahu"
[2]: ./media/expressroute-global-reach/2.png "diagram s globálním dosahem"
[3]: ./media/expressroute-global-reach/3.png "případ použití globálního dosahu"
