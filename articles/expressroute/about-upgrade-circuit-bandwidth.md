---
title: O upgradu šířky pásma okruhu | ExpressRoute Azure
description: V tomto článku se seznámíte s osvědčenými postupy pro upgrade šířky pásma okruhu ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: duau
ms.openlocfilehash: 7831e7944321e074c312853e1534c47970ebdfdb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89397946"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>O upgradu šířky pásma okruhu ExpressRoute

ExpressRoute umožňuje vyhrazené a privátní připojení k globální síti Microsoftu. Připojení usnadňuje síť partnera ExpressRoute nebo přímé připojení k zařízením Microsoft Enterprise Edge (MSEE). Po nakonfigurování a otestování fyzického připojení můžete povolit připojení vrstvy 2 a 3 vytvořením okruhu ExpressRoute a konfigurací partnerského vztahu.

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>Upgradovat šířku pásma okruhu

Aby bylo možné upgradovat šířku pásma okruhu, musí mít partner ExpressRoute Direct nebo ExpressRoute k [dispozici dostatek dostupné šířky pásma](#considerations) pro úspěšné dokončení upgradu.

Pokud je kapacita k dispozici, můžete okruh upgradovat pomocí následujících metod:

* [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Azure CLI](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>Důležité informace o kapacitách

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>Nedostatečná šířka pásma partnera ExpressRoute

Pokud partner ExpressRoute nemá dostatečnou kapacitu, je nutné vytvořit nový okruh nakonfigurovaný na požadovanou šířku pásma. Aby se zachovalo připojení, neodstraňujte starý okruh, dokud se nezřídí nově vytvořený okruh, nakonfigurovali se partnerský vztah a (týká se privátního partnerského vztahu) objekt připojení k bráně virtuální sítě ExpressRoute se zřídil.

Pokud Váš partner ExpressRoute nemá dostatek dostupné kapacity, musíte požádat o dodatečnou kapacitu v požadovaném umístění partnerského vztahu. Po zřízení nové kapacity můžete použít kroky uvedené v článcích v části [Šířka pásma upgradu](#upgrade) , abyste vytvořili nový okruh, nakonfigurovali připojení a odstranili starý okruh.


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>Nedostatečná ExpressRoute přímá šířka pásma

Pokud ExpressRoute Direct nemá dostatečnou kapacitu, můžete buď odstranit okruhy přidružené k prostředku ExpressRoute Direct, který už nepotřebujete, nebo vytvořit nový prostředek ExpressRoute Direct. Pokyny ke správě prostředku ExpressRoute Direct najdete v tématu [How to Configure ExpressRoute Direct](how-to-expressroute-direct-portal.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření a změny okruhu](expressroute-howto-circuit-portal-resource-manager.md)
* [Vytvoření a změny konfigurace partnerského vztahu](expressroute-howto-routing-portal-resource-manager.md)
* [Propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
