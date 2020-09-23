---
title: 'Azure ExpressRoute: připojení k Microsoft Cloud pomocí Global Reach'
description: Přečtěte si, jak může Azure ExpressRoute Global Reach propojit okruhy ExpressRoute a vytvořit tak soukromou síť mezi místními sítěmi.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: e3f9105037c049a53f1b7b99da96dd857070fcc7
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987611"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute je soukromý a odolný způsob, jak připojit vaše místní sítě k Microsoft Cloud. K mnoha cloudovým službám Microsoftu, jako je Azure a Microsoft 365, můžete přistupovat z vašeho privátního datového centra nebo vaší firemní sítě. Můžete mít například pobočku v síti San Francisco s okruhem ExpressRoute v silikonovém sedlu a další pobočkou v Londýně s okruhem ExpressRoute ve stejném městě. Obě pobočky mají vysokorychlostní připojení k prostředkům Azure v USA – západ a Velká Británie – jih. Firemní pobočky se ale nemůžou připojit a odesílat data přímo z sebe. Jinými slovy 10.0.1.0/24 může odesílat data do sítě 10.0.3.0/24 a 10.0.4.0/24, ale ne do sítě 10.0.2.0/24.

![Diagram, který zobrazuje okruhy, které nejsou propojené s Global Reach Express Route][1]

Pomocí **ExpressRoute Global REACH**můžete propojit ExpressRoute okruhy a vytvořit tak soukromou síť mezi místními sítěmi. Ve výše uvedeném příkladu s přidáním ExpressRoute Global Reach vaše síť San Francisco Office (10.0.1.0/24) může přímo vyměňovat data s vaší aplikací v Londýně (10.0.2.0/24) prostřednictvím stávajících okruhů ExpressRoute a přes globální síť Microsoftu. 

![Diagram, který zobrazuje okruhy propojené s Global Reach Express Route.][2]

## <a name="use-case"></a>Případ použití
ExpressRoute Global Reach je navržená tak, aby doplnila implementaci sítě WAN poskytovatele služeb a připojila firemní pobočky po celém světě. Například pokud váš poskytovatel služeb primárně pracuje v USA a má propojení všech větví v USA, ale poskytovatel služeb nefunguje v Japonsku a Hongkong, s ExpressRoute Global Reach můžete pracovat s místním poskytovatelem služeb a společnost Microsoft bude připojit vaše pobočky k těm, které jsou v USA, pomocí ExpressRoute a naší globální sítě.

![Diagram, který zobrazuje případ použití Global Reach Express Route.][3]

## <a name="availability"></a>Dostupnost 
ExpressRoute Global Reach se podporují na následujících místech. 

> [!NOTE] 
> Pokud chcete povolit ExpressRoute Global Reach mezi [různými geopolitickými oblastmi](expressroute-locations-providers.md#locations), vaše okruhy musí být **SKU úrovně Premium**.

* Austrálie
* Kanada
* Francie
* Německo
* Hongkong – zvláštní administrativní oblast
* Irsko
* Japonsko
* Jižní Korea
* Nizozemsko
* Nový Zéland
* Norsko
* Singapur
* Švédsko
* Švýcarsko
* Spojené království
* USA

## <a name="next-steps"></a>Další kroky
- Prohlédněte si [Global REACH Nejčastější dotazy](expressroute-faqs.md#globalreach).
- Naučte se, jak [povolit Global REACH](expressroute-howto-set-global-reach.md).
- Přečtěte si, jak [propojit okruh ExpressRoute s vaší virtuální sítí](expressroute-howto-linkvnet-arm.md).

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram bez globálního dosahu"
[2]: ./media/expressroute-global-reach/2.png "diagram s globálním dosahem"
[3]: ./media/expressroute-global-reach/3.png "případ použití globálního dosahu"
