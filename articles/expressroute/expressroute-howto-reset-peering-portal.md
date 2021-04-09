---
title: 'Azure ExpressRoute: resetování partnerského vztahu okruhů pomocí Azure Portal'
description: Naučte se, jak zakázat a povolit partnerské vztahy okruhu Azure ExpressRoute pomocí Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680285"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>Resetování partnerského okruhu ExpressRoute pomocí Azure Portal

Tento článek popisuje, jak zakázat a povolit partnerské vztahy okruhu Azure ExpressRoute pomocí Azure Portal. Když zakážete partnerský vztah, dojde k vypnutí relace Border Gateway Protocol (BGP) pro primární i sekundární připojení okruhu ExpressRoute. Když povolíte partnerský vztah, obnoví se relace protokolu BGP v primárním i sekundárním připojení okruhu ExpressRoute.

> [!Note]
> Při první konfiguraci partnerských vztahů v okruhu ExpressRoute jsou partnerské vztahy ve výchozím nastavení povolené.

Resetování partnerských vztahů ExpressRoute může být užitečné v následujících scénářích:

* Testujete návrh a implementaci zotavení po havárii. Předpokládejme například, že máte dva okruhy ExpressRoute. Můžete zakázat partnerské vztahy jednoho okruhu a vynutit, aby síťový provoz používal druhý okruh.

* Chcete povolit rozpoznávání obousměrného předávání (BFD) ve veřejném partnerském vztahu Azure nebo partnerském vztahu Microsoftu. Pokud byl váš okruh ExpressRoute vytvořen před 1. srpna 2018, v privátním partnerském vztahu Azure nebo dřív od 10. ledna 2020 v partnerském vztahu Microsoftu, BFD nebyl ve výchozím nastavení povolen. Resetování partnerského vztahu na Povolit BFD.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

V prohlížeči přejdete na [Azure Portal](https://portal.azure.com)a potom se přihlásíte pomocí svého účtu Azure.

## <a name="reset-a-peering"></a>Resetování partnerského vztahu

Partnerský vztah Microsoftu a soukromý partnerský vztah Azure na okruhu ExpressRoute můžete resetovat nezávisle.

1. Vyberte okruh, který chcete změnit.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Snímek obrazovky, který zobrazuje výběr okruhu v seznamu okruhů ExpressRoute.":::

1. Vyberte konfiguraci partnerského vztahu, kterou chcete resetovat.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Snímek obrazovky, který ukazuje výběr partnerského vztahu v přehledu okruhu ExpressRoute.":::

1. Zrušte zaškrtnutí políčka **Povolit partnerský vztah** a pak výběrem **Uložit** zakažte konfiguraci partnerského vztahu.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Snímek obrazovky, který ukazuje zrušení zaškrtnutí políčka Povolit partnerský vztah.":::

1. Zaškrtněte políčko **Povolit partnerský vztah** a potom vyberte **Uložit** pro opětovné povolení konfigurace partnerského vztahu.

## <a name="next-steps"></a>Další kroky

Pokud chcete řešit problémy s ExpressRoute, přečtěte si následující články:

* [Ověření možností připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Řešení potíží s výkonem sítě](expressroute-troubleshooting-network-performance.md)
