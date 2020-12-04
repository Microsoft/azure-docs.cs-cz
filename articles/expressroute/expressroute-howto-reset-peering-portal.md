---
title: 'Azure ExpressRoute: resetování partnerského vztahu okruhů pomocí Azure Portal'
description: Naučte se, jak zakázat a povolit partnerské vztahy okruhu Azure ExpressRoute pomocí Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: d4d6b0b0cce4f5304f7c5790ef2bda05633be52f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582300"
---
# <a name="reset-expressroute-circuit-peerings-use-the-azure-portal"></a>Resetování partnerských vztahů okruhu ExpressRoute použijte Azure Portal

Tento článek popisuje, jak zakázat a povolit partnerské vztahy okruhu ExpressRoute pomocí Azure Portal. Když zakážete partnerský vztah, vypne se relace protokolu BGP pro primární i sekundární připojení okruhu ExpressRoute. V rámci tohoto partnerského vztahu k Microsoftu ztratíte připojení. Když povolíte partnerský vztah, zaplní se relace protokolu BGP na primárním i sekundárním připojení okruhu ExpressRoute. Prostřednictvím tohoto partnerského vztahu k Microsoftu budete moct znovu připojit. V okruhu ExpressRoute můžete nezávisle povolit a zakázat partnerský partnerský vztah Microsoftu a privátní partnerské vztahy Azure. Při první konfiguraci partnerských vztahů v okruhu ExpressRoute jsou partnerské vztahy ve výchozím nastavení povolené.

Existuje několik scénářů, ve kterých může být užitečné resetovat partnerské vztahy ExpressRoute.
* Otestujte návrh a implementaci zotavení po havárii. Například máte dva okruhy ExpressRoute. Můžete zakázat partnerské vztahy jednoho okruhu a vynutit převzetí služeb při selhání provozu v síti jiným okruhem.
* Povolte rozpoznávání obousměrného předávání (BFD) na privátním partnerském vztahu Azure nebo partnerském vztahu Microsoftu vašeho okruhu ExpressRoute. BFD je ve výchozím nastavení povolený u privátního partnerského vztahu Azure, pokud se váš okruh ExpressRoute vytvoří až od srpna 1 2018 a partnerského vztahu Microsoftu. Pokud se váš okruh ExpressRoute vytvoří po lednu 10 2020. Pokud byl okruh vytvořen dříve, BFD nebyl povolen. BFD můžete povolit zakázáním partnerského vztahu a jeho povolením. 

### <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V prohlížeči přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

## <a name="reset-a-peering"></a>Resetování partnerského vztahu

1. Vyberte okruh, u kterého chcete provést změny konfigurace partnerského vztahu.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Seznam okruhů ExpressRoute":::

1. Vyberte konfiguraci partnerského vztahu, kterou chcete povolit nebo zakázat.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Přehled okruhu ExpressRoute":::

1. Zrušte zaškrtnutí políčka **Povolit partnerský vztah** a výběrem **Uložit** zakažte konfiguraci partnerského vztahu.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Zakázat soukromý partnerský vztah":::

1. Partnerský vztah můžete znovu povolit zaškrtnutím políčka **Povolit partnerský vztah** a výběrem **Uložit**.

## <a name="next-steps"></a>Další kroky
Pokud potřebujete pomoc s řešením problému s ExpressRoute, přečtěte si následující články:
* [Ověření možností připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Řešení potíží s výkonem sítě](expressroute-troubleshooting-network-performance.md)
