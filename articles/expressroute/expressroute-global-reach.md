---
title: 'Azure ExpressRoute: připojení k Microsoft Cloud pomocí Global Reach'
description: Tento článek vysvětluje ExpressRoute globální dosah.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: e01b311d10dcf4216bfeea77ce8919f64ea2ef28
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080328"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute je privátní a odolná proti selháním způsob, jak připojit místní sítě k Microsoft Cloud. Přístup k mnoha cloudovým službám Microsoftu, jako je Azure a Office 365, můžete získat z privátního datového centra nebo vaší firemní sítě. Například může mít firemní pobočky v kalifornském San Franciscu okruh ExpressRoute v Silicon Valley a jinou firemní pobočku v Londýně se okruh ExpressRoute ve stejném městě. Obou poboček může mít vysokorychlostní připojení k prostředkům Azure v oblasti USA – západ a Velká Británie – jih. Firemní pobočky však nelze vyměňovat data navzájem přímo. Jinými slovy 10.0.1.0/24 může odesílat data do 10.0.3.0/24 a 10.0.4.0/24, ale ne k 10.0.2.0/24.

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
* Německo
* Hongkong – zvláštní správní oblast
* Irsko
* Japonsko
* Jižní Korea
* Nizozemsko
* Singapur
* Švýcarsko
* Spojené království
* Spojené státy

Okruhů ExpressRoute, musí být vytvořeny na [umístění partnerského vztahu ExpressRoute](expressroute-locations.md) ve výše uvedené země či oblasti. Povolit globální dosah ExpressRoute mezi [různých geopolitických oblastí](expressroute-locations.md), vaší obvody musí být SKU úrovně Premium.

## <a name="next-steps"></a>Další kroky
1. [Další informace o ExpressRoute globální dosah](expressroute-faqs.md)
2. [Jak povolit ExpressRoute globální dosah](expressroute-howto-set-global-reach.md)
3. [Propojení okruhu ExpressRoute k virtuální síti Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram bez globálním dosahem"
[2]: ./media/expressroute-global-reach/2.png "diagram s globálním dosahem"
[3]: ./media/expressroute-global-reach/3.png "případu globálním dosahem"
