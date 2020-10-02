---
title: 'ExpressRoute: filtry tras – partnerský vztah Microsoftu: Azure Portal'
description: Tento článek popisuje, jak nakonfigurovat filtry tras pro partnerský vztah Microsoftu pomocí Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/01/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 468c7a0113a4603f4f47bb529145261ff50d96d4
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650540"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Nakonfigurujte filtry tras pro partnerský vztah Microsoftu: Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Filtry tras představují způsob, jak spotřebovat dílčí sadu podporovaných služeb přes partnerský vztah Microsoftu. Kroky v tomto článku vám pomůžou nakonfigurovat a spravovat filtry tras pro okruhy ExpressRoute.

Služby Microsoft 365, jako jsou Exchange Online, SharePoint Online a Skype pro firmy, a služby Azure, jako jsou Storage a SQL DB, jsou přístupné prostřednictvím partnerského vztahu Microsoftu. Při konfiguraci partnerského vztahu Microsoftu v okruhu ExpressRoute jsou všechny předpony související s těmito službami inzerovány prostřednictvím vytvořených relací protokolu BGP. Ke každé předponě je připojená hodnota komunity protokolu BGP, která identifikuje službu nabízenou prostřednictvím dané předpony. Seznam hodnot komunity protokolu BGP a služeb, na které se mapují, najdete v tématu [komunity protokolu BGP](expressroute-routing.md#bgp).

Pokud budete potřebovat připojení ke všem službám, je prostřednictvím protokolu BGP inzerován velký počet předpon. Tím se významně zvyšuje velikost směrovacích tabulek udržovaných směrovači v rámci vaší sítě. Pokud máte v úmyslu využívat jenom podmnožinu služeb nabízených partnerským vztahem Microsoftu, můžete zmenšit velikost směrovacích tabulek dvěma způsoby. Další možnosti:

- Odfiltrujte nechtěné předpony pomocí filtrů směrování v komunitách protokolu BGP. Jedná se o standardní postupy sítě, které se běžně používají v mnoha sítích.

- Definujte filtry tras a použijte je pro okruh ExpressRoute. Filtr tras je nový prostředek, který umožňuje vybrat seznam služeb, které chcete využívat v partnerském vztahu Microsoftu. Směrovače ExpressRoute odesílají seznam předpon, které patří do služeb identifikovaných ve filtru tras.

### <a name="about-route-filters"></a><a name="about"></a>O filtrech tras

Když na okruhu ExpressRoute nakonfigurujete partnerský vztah Microsoftu, vytvoří směrovače Microsoft Edge dvojici relací protokolu BGP s hraničními směrovači (vašimi nebo vaším poskytovatelem připojení). Do vaší sítě se žádné trasy neinzerují. Pokud chcete povolit inzerování tras do vaší sítě, je potřeba k ní přidružit filtr tras.

Filtr tras umožňuje identifikovat služby, které chcete využívat prostřednictvím partnerského vztahu Microsoftu s vaším okruhem ExpressRoute. V podstatě je seznam všech hodnot komunity protokolu BGP, které chcete udělit. Po definování prostředku filtru tras a jeho připojení k okruhu ExpressRoute se do vaší sítě budou inzerovat všechny předpony, které se mapují na hodnoty komunity protokolu BGP.

Aby bylo možné připojit filtry směrování s Microsoft 365 službami, je nutné mít autorizaci ke využívání služeb Microsoft 365 prostřednictvím ExpressRoute. Pokud nemáte oprávnění ke využívání Microsoft 365ch služeb prostřednictvím ExpressRoute, operace připojení filtrů tras se nezdařila. Další informace o procesu autorizace najdete v tématu [Azure ExpressRoute for Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Partnerské vztahy Microsoftu okruhů ExpressRoute, které byly nakonfigurované před 1. srpna 2017, budou mít všechny předpony služby inzerované prostřednictvím partnerského vztahu Microsoftu, a to i v případě, že nejsou definované filtry směrování. Partnerský vztah Microsoftu pro okruhy ExpressRoute, které jsou nakonfigurované na nebo od 1. srpna 2017, nebudou mít inzerované předpony, dokud není k okruhu připojen filtr tras.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Pracovní postup

Abyste se mohli úspěšně připojit ke službám prostřednictvím partnerského vztahu Microsoftu, musíte provést následující kroky konfigurace:

- Musíte mít aktivní okruh ExpressRoute s zřízeným partnerským vztahem Microsoftu. K provedení těchto úloh můžete použít následující pokyny:
  - Než budete pokračovat, [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a požádejte ho o okruh povolený poskytovatelem připojení. Okruh ExpressRoute musí být ve stavu zřizování a povoleno.
  - [Vytvoření partnerského vztahu Microsoftu](expressroute-howto-routing-portal-resource-manager.md) , pokud spravujete relaci protokolu BGP přímo Nebo můžete nechat poskytovatele připojení zřídit partnerský vztah Microsoftu pro váš okruh.

-  Musíte vytvořit a nakonfigurovat filtr tras.
    - Identifikujte služby, které využíváte prostřednictvím partnerského vztahu Microsoftu
    - Identifikujte seznam hodnot komunity protokolu BGP přidružených ke službám.
    - Vytvořte pravidlo, které umožní seznamu předpony odpovídat hodnotám komunity protokolu BGP.

-  Filtr tras musíte připojit k okruhu ExpressRoute.

## <a name="before-you-begin"></a>Než začnete

Než začnete s konfigurací, ujistěte se, že splňujete následující kritéria:

 - Než začnete s konfigurací, Projděte si [požadavky](expressroute-prerequisites.md) a [pracovní postupy](expressroute-workflows.md) .

 - Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve stavu zřizování a povoleno.

 - Musíte mít aktivní partnerský vztah Microsoftu. Postupujte podle pokynů v tématu [Vytvoření a úprava konfigurace partnerského vztahu](expressroute-howto-routing-portal-resource-manager.md)


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Krok 1: získání seznamu předpon a hodnot komunity protokolu BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. získání seznamu hodnot komunity protokolu BGP

Hodnoty komunity protokolu BGP spojené se službami dostupnými prostřednictvím partnerského vztahu Microsoftu jsou k dispozici na stránce [požadavky na směrování ExpressRoute](expressroute-routing.md) .

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Vytvořte seznam hodnot, které chcete použít.

Vytvořte seznam [hodnot komunity protokolu BGP](expressroute-routing.md#bgp) , které chcete použít ve filtru tras. 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Krok 2: Vytvoření filtru tras a pravidla filtru

Filtr tras může mít pouze jedno pravidlo a pravidlo musí být typu Allow. Toto pravidlo může obsahovat seznam hodnot komunity protokolu BGP přidružených k tomuto pravidlu.

### <a name="1-create-a-route-filter"></a>1. Vytvoření filtru tras
Můžete vytvořit filtr tras výběrem možnosti vytvořit nový prostředek. Klikněte na **vytvořit prostředek**  >  **sítě**  >  **RouteFilter**, jak je znázorněno na následujícím obrázku:

![Snímek obrazovky zobrazující stránku "filtr tras".](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Filtr tras musíte umístit do skupiny prostředků. 

![Snímek obrazovky zobrazující stránku vytvořit filtr tras s příklady zadaných hodnot](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. vytvoření pravidla filtru

Pravidla můžete přidat a aktualizovat výběrem karty spravovat pravidlo pro svůj filtr tras.

![Snímek obrazovky zobrazující stránku Přehled s zvýrazněnou akcí spravovat pravidlo](./media/how-to-routefilter-portal/ManageRouteFilter.png)


V rozevíracím seznamu můžete vybrat služby, ke kterým se chcete připojit, a po dokončení toto pravidlo Uložit.

![Snímek obrazovky zobrazující okno Spravovat pravidlo se službami vybranými v rozevíracím seznamu povolené komunity služeb](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Krok 3: připojení filtru tras k okruhu ExpressRoute

Filtr směrování můžete připojit k okruhu tak, že vyberete tlačítko Přidat okruh a v rozevíracím seznamu vyberete okruh ExpressRoute.

![Snímek obrazovky, který zobrazuje stránku Přehled se zvolenou akcí přidat okruh](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Pokud poskytovatel připojení konfiguruje partnerský vztah pro váš okruh ExpressRoute, aktualizujte okruh z okna okruhu ExpressRoute předtím, než vyberete tlačítko Přidat okruh.

![Snímek obrazovky zobrazující stránku "Přehled" se zvolenou akcí aktualizovat](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>Běžné úkoly

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Získání vlastností filtru tras

Vlastnosti filtru tras můžete zobrazit při otevření prostředku na portálu.

![Snímek obrazovky zobrazující stránku Přehled](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Aktualizace vlastností filtru tras

Seznam hodnot komunity protokolu BGP připojených k okruhu můžete aktualizovat tak, že vyberete tlačítko "spravovat pravidlo".


![Snímek obrazovky zobrazující stránku Přehled s zvýrazněnou akcí spravovat pravidlo](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Snímek obrazovky zobrazující okno Spravovat pravidlo s vybranými službami](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Odpojení filtru tras od okruhu ExpressRoute

Pokud chcete odpojit okruh od filtru tras, klikněte pravým tlačítkem myši na okruh a klikněte na zrušit přidružení.

![Snímek obrazovky zobrazující stránku Přehled se zvýrazněnou akcí zrušit přidružení](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Odstranění filtru tras

Filtr tras můžete odstranit tak, že vyberete tlačítko Odstranit. 

![Vytvoření filtru tras](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Další kroky

* Další informace o ExpressRoute najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).

* Informace o ukázkách konfigurace směrovačů najdete v tématu [ukázky konfigurace směrovače pro nastavení a správu směrování](expressroute-config-samples-routing.md). 
