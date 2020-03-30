---
title: 'Azure ExpressRoute: Připojení k Microsoft Cloudu pomocí globálního dosahu'
description: Tento článek vysvětluje globální dosah ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: cherylmc
ms.openlocfilehash: f574576044b7e4ddd34289b5cc45fe5ca353f180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538500"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute je soukromý a odolný způsob připojení místních sítí k Microsoft Cloudu. K mnoha cloudovým službám Microsoftu, jako je Azure a Office 365, máte přístup z privátního datového centra nebo podnikové sítě. Můžete mít například pobočku v San Franciscu s okruhem ExpressRoute v Silicon Valley a další pobočku v Londýně s okruhem ExpressRoute ve stejném městě. Obě pobočky mohou mít vysokorychlostní připojení k prostředkům Azure na jihu USA a Velké Británii. Pobočky si však nemohou vyměňovat data přímo mezi sebou. Jinými slovy, 10.0.1.0/24 může odesílat data do 10.0.3.0/24 a 10.0.4.0/24, ale ne na 10.0.2.0/24.

![Bez][1]

Pomocí **aplikace ExpressRoute Global Reach**můžete propojit okruhy ExpressRoute a vytvořit tak privátní síť mezi místními sítěmi. Ve výše uvedeném příkladu si vaše kancelář v San Franciscu (10.0.1.0/24) může přímo vyměňovat data s vaší londýnskou kanceláří (10.0.2.0/24) prostřednictvím stávajících okruhů ExpressRoute a prostřednictvím globální sítě společnosti Microsoft. 

![S][2]

## <a name="use-case"></a>Případ použití
ExpressRoute Global Reach je navržen tak, aby doplňoval implementaci wan poskytovatele vašich služeb a propojil vaše pobočky po celém světě. Pokud například váš poskytovatel služeb působí především ve Spojených státech a propojil všechny vaše pobočky v USA, ale poskytovatel služeb nepůsobí v Japonsku a Hongkongu, můžete s ExpressRoute Global Reach spolupracovat s místním poskytovatelem služeb a Společnost Microsoft připojí vaše pobočky tam na ty v USA pomocí ExpressRoute a naší globální sítě.

![případ použití][3]

## <a name="availability"></a>Dostupnost 
ExpressRoute Global Reach je aktuálně podporován na následujících místech.

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

Okruhy ExpressRoute musí být vytvořeny v [umístěních partnerského vztahu ExpressRoute](expressroute-locations.md) ve výše uvedených zemích nebo oblastech. Chcete-li povolit globální dosah ExpressRoute mezi [různými geopolitickými oblastmi](expressroute-locations.md), musí být vaše okruhy Premium SKU.

## <a name="next-steps"></a>Další kroky
1. [Další informace o globálním dosahu ExpressRoute](expressroute-faqs.md)
2. [Jak povolit globální dosah ExpressRoute](expressroute-howto-set-global-reach.md)
3. [Propojit okruh ExpressRoute s virtuální sítí Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram bez globálního dosahu"
[2]: ./media/expressroute-global-reach/2.png "diagram s globálním dosahem"
[3]: ./media/expressroute-global-reach/3.png "případ použití globálního dosahu"
