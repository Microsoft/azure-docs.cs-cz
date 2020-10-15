---
title: 'Kurz: konfigurace filtrů tras pro partnerský vztah Microsoftu – Azure Portal'
description: V tomto kurzu se dozvíte, jak nakonfigurovat filtry tras pro partnerský vztah Microsoftu pomocí Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 5d5f46c4f078038b91881000cf8a6b67000683e2
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078625"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-the-azure-portal"></a>Kurz: konfigurace filtrů směrování pro partnerský vztah Microsoftu pomocí Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Filtry tras představují způsob, jak spotřebovat dílčí sadu podporovaných služeb přes partnerský vztah Microsoftu. Kroky v tomto článku vám pomůžou nakonfigurovat a spravovat filtry tras pro okruhy ExpressRoute.

Služby Microsoft 365 Services, jako je Exchange Online, SharePoint Online a Skype pro firmy, jsou přístupné prostřednictvím partnerského vztahu Microsoftu. Když se partnerský vztah Microsoftu nakonfiguruje v okruhu ExpressRoute, budou se všechny předpony související s těmito službami inzerovat prostřednictvím vytvořených relací protokolu BGP. Ke každé předponě je připojená hodnota komunity protokolu BGP, která identifikuje službu nabízenou prostřednictvím dané předpony. Seznam hodnot komunity protokolu BGP a služeb, na které se mapují, najdete v tématu [komunity protokolu BGP](expressroute-routing.md#bgp).

Připojení ke všem službám Azure a Microsoft 365 způsobí, že se prostřednictvím protokolu BGP inzeruje velký počet předpon. Velký počet předpon významně zvyšuje velikost směrovacích tabulek udržovaných směrovači v rámci vaší sítě. Pokud máte v úmyslu využívat jenom podmnožinu služeb nabízených partnerským vztahem Microsoftu, můžete zmenšit velikost směrovacích tabulek dvěma způsoby. Další možnosti:

* Odfiltrujte nechtěné předpony pomocí filtrů směrování v komunitách protokolu BGP. Filtrování tras je standardní postup sítě a často se používá v rámci mnoha sítí.

* Definujte filtry tras a použijte je pro okruh ExpressRoute. Filtr tras je nový prostředek, který umožňuje vybrat seznam služeb, které chcete využívat v partnerském vztahu Microsoftu. Směrovače ExpressRoute odesílají seznam předpon, které patří do služeb identifikovaných ve filtru tras.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Získejte hodnoty komunity protokolu BGP.
> - Vytvořte filtr tras a pravidlo filtru.
> - Přidružte filtr směrování k okruhu ExpressRoute.

### <a name="about-route-filters"></a><a name="about"></a>O filtrech tras

Když v okruhu ExpressRoute nakonfigurujete partnerský vztah Microsoftu, vytvoří směrovače Microsoft Edge dvojici relací protokolu BGP se hraničními směrovači přes poskytovatele připojení. Do vaší sítě se žádné trasy neinzerují. Pokud chcete povolit inzerování tras do vaší sítě, je potřeba k ní přidružit filtr tras.

Filtr tras umožňuje identifikovat služby, které chcete využívat prostřednictvím partnerského vztahu Microsoftu s vaším okruhem ExpressRoute. Je v podstatě povolený seznam všech hodnot komunity protokolu BGP. Jakmile se prostředek filtru tras definuje a připojí k okruhu ExpressRoute, všechny předpony, které se mapují na hodnoty komunity protokolu BGP, se budou inzerovat do vaší sítě.

Chcete-li připojit filtry směrování s Microsoft 365 službami, je nutné mít oprávnění ke využívání služeb Microsoft 365 prostřednictvím ExpressRoute. Pokud nemáte oprávnění ke využívání Microsoft 365ch služeb prostřednictvím ExpressRoute, operace připojení filtrů tras se nezdařila. Další informace o procesu autorizace najdete v tématu [Azure ExpressRoute for Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Partnerské vztahy Microsoftu okruhů ExpressRoute, které byly nakonfigurované před 1. srpna 2017, budou mít všechny předpony služby inzerované prostřednictvím partnerského vztahu Microsoftu, a to i v případě, že nejsou definované filtry směrování. Partnerský vztah Microsoftu pro okruhy ExpressRoute, které jsou nakonfigurované na nebo od 1. srpna 2017, nebudou mít inzerované předpony, dokud není k okruhu připojen filtr tras.
> 

## <a name="prerequisites"></a>Požadavky

- Než začnete s konfigurací, Projděte si [požadavky](expressroute-prerequisites.md) a [pracovní postupy](expressroute-workflows.md) .

- Musíte mít aktivní okruh ExpressRoute s zřízeným partnerským vztahem Microsoftu. K provedení těchto úloh můžete použít následující pokyny:
  - Než budete pokračovat, [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a požádejte ho o povolený okruh poskytovatele připojení. Okruh ExpressRoute musí být ve stavu zřizování a povoleno.
  - [Vytvoření partnerského vztahu Microsoftu](expressroute-howto-routing-portal-resource-manager.md) , pokud spravujete relaci protokolu BGP přímo Nebo můžete nechat poskytovatele připojení zřídit partnerský vztah Microsoftu pro váš okruh.

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Získání seznamu předpon a hodnot komunity protokolu BGP

### <a name="get-a-list-of-bgp-community-values"></a>Získání seznamu hodnot komunity protokolu BGP

Hodnoty komunity protokolu BGP spojené se službami dostupnými prostřednictvím partnerského vztahu Microsoftu jsou k dispozici na stránce [požadavky na směrování ExpressRoute](expressroute-routing.md) .

### <a name="make-a-list-of-the-values-that-you-want-to-use"></a>Vytvořte seznam hodnot, které chcete použít.

Vytvořte seznam [hodnot komunity protokolu BGP](expressroute-routing.md#bgp) , které chcete použít ve filtru tras. 

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Vytvoření filtru tras a pravidla filtru

Filtr tras může mít pouze jedno pravidlo a pravidlo musí být typu Allow. Toto pravidlo může obsahovat seznam hodnot komunity protokolu BGP přidružených k tomuto pravidlu.

1. Vyberte **vytvořit prostředek** a pak vyhledejte *Filtr směrování* , jak je znázorněno na následujícím obrázku:

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter.png" alt-text="Snímek obrazovky zobrazující stránku filtru tras":::

1. Umístěte filtr tras do skupiny prostředků. Zajistěte, aby bylo umístění stejné jako okruh ExpressRoute. Vyberte **zkontrolovat + vytvořit** a pak **vytvořit**.

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter-basic.png" alt-text="Snímek obrazovky zobrazující stránku filtru tras":::

### <a name="create-a-filter-rule"></a>Vytvoření pravidla filtru

1. Chcete-li přidat a aktualizovat pravidla, vyberte kartu spravovat pravidlo pro svůj filtr tras.

    :::image type="content" source="./media/how-to-routefilter-portal/manage-route-filter.png" alt-text="Snímek obrazovky zobrazující stránku filtru tras":::

1. V rozevíracím seznamu vyberte služby, ke kterým se chcete připojit, a po dokončení toto pravidlo uložte.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Snímek obrazovky zobrazující stránku filtru tras":::

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Připojení filtru tras k okruhu ExpressRoute

Připojte filtr směrování k okruhu tak, že vyberete tlačítko **+ Přidat okruh** a v rozevíracím seznamu vyberete okruh ExpressRoute.

:::image type="content" source="./media/how-to-routefilter-portal/add-circuit-to-route-filter.png" alt-text="Snímek obrazovky zobrazující stránku filtru tras":::

Pokud poskytovatel připojení konfiguruje partnerský vztah pro okruh ExpressRoute, aktualizujte okruh ze stránky okruhu ExpressRoute předtím, než vyberete tlačítko **+ Přidat okruh** .

:::image type="content" source="./media/how-to-routefilter-portal/refresh-express-route-circuit.png" alt-text="Snímek obrazovky zobrazující stránku filtru tras":::

## <a name="common-tasks"></a><a name="tasks"></a>Běžné úkoly

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Získání vlastností filtru tras

Vlastnosti filtru tras můžete zobrazit při otevření prostředku na portálu.

:::image type="content" source="./media/how-to-routefilter-portal/view-route-filter.png" alt-text="Snímek obrazovky zobrazující stránku filtru tras":::

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Aktualizace vlastností filtru tras

1. Seznam hodnot komunity protokolu BGP připojených k okruhu můžete aktualizovat tak, že vyberete tlačítko **Spravovat pravidlo** .

    :::image type="content" source="./media/how-to-routefilter-portal/update-route-filter.png" alt-text="Snímek obrazovky zobrazující stránku filtru tras":::

1. Vyberte požadované komunity služby a pak vyberte **Uložit**.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Snímek obrazovky zobrazující stránku filtru tras":::

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Odpojení filtru tras od okruhu ExpressRoute

Chcete-li odpojit okruh od filtru tras, klikněte pravým tlačítkem myši na okruh a vyberte zrušit **přidružení**.

:::image type="content" source="./media/how-to-routefilter-portal/detach-route-filter.png" alt-text="Snímek obrazovky zobrazující stránku filtru tras":::


## <a name="clean-up-resources"></a><a name="delete"></a>Vyčištění prostředků

Filtr tras můžete odstranit tak, že vyberete tlačítko **Odstranit** . Před tím, než to uděláte, zkontrolujte, že filtr tras není spojený s žádnými okruhy.

:::image type="content" source="./media/how-to-routefilter-portal/delete-route-filter.png" alt-text="Snímek obrazovky zobrazující stránku filtru tras":::

## <a name="next-steps"></a>Další kroky

Informace o ukázkách konfigurace směrovače najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Ukázky konfigurace směrovače pro nastavení a správu směrování](expressroute-config-samples-routing.md)
