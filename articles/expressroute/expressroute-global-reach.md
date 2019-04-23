---
title: Připojení místní sítě k Microsoft cloudu s využitím globální dosah – Azure ExpressRoute | Dokumentace Microsoftu
description: Tento článek vysvětluje ExpressRoute globální dosah.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: d2de98fe6cb0fffcc77bb851e3a853475d0f704c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60367378"
---
# <a name="expressroute-global-reach"></a>ExpressRoute globálním dosahem
ExpressRoute je privátní a odolná proti selháním způsob, jak připojit místní sítě k Microsoft Cloud. Mnoho cloudových služeb Microsoftu, jako je například Azure, Office 365 a Dynamics 365 můžete přistupovat z vašeho privátního datového centra nebo vaší podnikové síti. Například může mít firemní pobočky v kalifornském San Franciscu okruh ExpressRoute v Silicon Valley a jinou firemní pobočku v Londýně se okruh ExpressRoute ve stejném městě. Obou poboček může mít vysokorychlostní připojení k prostředkům Azure v oblasti USA – západ a Velká Británie – jih. Firemní pobočky však nelze vyměňovat data navzájem přímo. Jinými slovy 10.0.1.0/24 může odesílat data do 10.0.3.0/24 a 10.0.4.0/24, ale ne k 10.0.2.0/24.

![Bez][1]

S **ExpressRoute globální dosah**, je možné propojit okruhy ExpressRoute tvořit privátní sítě mezi místními sítěmi. V příkladu výše, a uveďte ExpressRoute globální dosah svého pracoviště San Francisku (10.0.1.0/24) přímo vyměňovat data s Londýnské pobočce (10.0.2.0/24) prostřednictvím stávajících okruhů ExpressRoute a pomocí globální síti Microsoftu. 

![s][2]

## <a name="use-case"></a>Případ použití
Globální dosah ExpressRoute slouží k doplnění WAN implementace poskytovatele služeb a připojení firemních pobočkách po celém světě. Například pokud váš poskytovatel služeb primárně funguje ve Spojených státech amerických a jsou připojené všechny větve v USA, ale poskytovatel služeb nepracuje v Japonsku a Hong Kong, s globální dosah ExpressRoute můžete pracovat s poskytovatelem místní služby a Microsoft se připojuje větve došlo k v USA pomocí naší globální sítě a ExpressRoute.

![Případ použití][3]

## <a name="availability"></a>Dostupnost 
Globální dosah ExpressRoute současné době se podporuje v následujících umístěních.

* Austrálie
* Kanada
* Francie
* Hongkong – zvláštní správní oblast
* Irsko
* Japonsko
* Jižní Korea
* Nizozemsko
* Spojené Kindom
* Spojené státy

Okruhů ExpressRoute, musí být vytvořeny na [umístění partnerského vztahu ExpressRoute](expressroute-locations.md) ve výše uvedené země či oblasti. Povolit globální dosah ExpressRoute mezi [různých geopolitických oblastí](expressroute-locations.md), vaší obvody musí být SKU úrovně Premium.

## <a name="next-steps"></a>Další postup
1. [Další informace o ExpressRoute globální dosah](expressroute-faqs.md)
2. [Jak povolit ExpressRoute globální dosah](expressroute-howto-set-global-reach.md)
3. [Propojení okruhu ExpressRoute k virtuální síti Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram bez globálním dosahem"
[2]: ./media/expressroute-global-reach/2.png "diagram s globálním dosahem"
[3]: ./media/expressroute-global-reach/3.png "případu globálním dosahem"
