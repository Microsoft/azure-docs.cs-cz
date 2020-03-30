---
title: 'ExpressRoute: Filtry tras – partnerský vztah Microsoftu:portál Azure'
description: Tento článek popisuje, jak nakonfigurovat filtry tras pro partnerský vztah Microsoftu pomocí portálu Azure.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 07/01/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 0b8e06ad5688374e5ab4aaa72d8485e6da797afe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037445"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Konfigurace filtrů tras pro partnerský vztah Microsoftu: Portál Azure
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Filtry tras představují způsob, jak spotřebovat dílčí sadu podporovaných služeb přes partnerský vztah Microsoftu. Postup v tomto článku vám pomůže nakonfigurovat a spravovat filtry tras pro okruhy ExpressRoute.

Služby Office 365, jako je Exchange Online, SharePoint Online a Skype pro firmy, a služby Azure, jako je úložiště a SQL DB, jsou dostupné prostřednictvím partnerského vztahu Microsoftu. Při konfiguraci partnerského vztahu Microsoftu v okruhu ExpressRoute jsou všechny předpony související s těmito službami inzerovány prostřednictvím vytvořených relací Protokolu BGP. Ke každé předponě je připojená hodnota komunity protokolu BGP, která identifikuje službu nabízenou prostřednictvím dané předpony. Seznam hodnot komunity Protokolu BGP a služeb, na které mapují, naleznete v [tématu komunity Protokolu BGP](expressroute-routing.md#bgp).

Pokud požadujete připojení ke všem službám, velký počet předpon jsou inzerovány prostřednictvím protokolu BGP. Tím se výrazně zvětší velikost směrovacích tabulek udržovaných směrovači v síti. Pokud plánujete využívat pouze podmnožinu služeb nabízených prostřednictvím partnerského vztahu Microsoftu, můžete zmenšit velikost směrovacích tabulek dvěma způsoby. Můžete:

- Odfiltrujte nežádoucí předpony použitím filtrů tras v komunitách Protokolu BGP. Jedná se o standardní síťové postupy a běžně se používá v mnoha sítích.

- Definujte filtry tras a aplikujte je na okruh ExpressRoute. Filtr trasy je nový prostředek, který umožňuje vybrat seznam služeb, které chcete využívat prostřednictvím partnerského vztahu Microsoftu. Směrovače ExpressRoute odesílají pouze seznam předpon, které patří ke službám uvedeným ve filtru trasy.

### <a name="about-route-filters"></a><a name="about"></a>Filtry tras

Když je partnerský vztah Microsoftu nakonfigurován v okruhu ExpressRoute, hraniční směrovače Microsoft vytvoří dvojici relací Protokolu BGP s hraničními směrovači (vy nebo poskytovatele připojení). Do vaší sítě se žádné trasy neinzerují. Pokud chcete povolit inzerování tras do vaší sítě, je potřeba k ní přidružit filtr tras.

Filtr tras umožňuje identifikovat služby, které chcete využívat prostřednictvím partnerského vztahu Microsoftu s vaším okruhem ExpressRoute. Jedná se v podstatě o seznam všech hodnot komunity Protokolu BGP, které chcete povolit. Po definování prostředku filtru tras a jeho připojení k okruhu ExpressRoute se do vaší sítě budou inzerovat všechny předpony, které se mapují na hodnoty komunity protokolu BGP.

Abyste k nim mohli připojit filtry tras se službami Office 365, musíte mít oprávnění využívat služby Office 365 prostřednictvím ExpressRoute. Pokud nemáte oprávnění využívat služby Office 365 prostřednictvím ExpressRoute, operace připojení filtrů postupu se nezdaří. Další informace o procesu autorizace najdete v [tématu Azure ExpressRoute pro Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Microsoft peering obvodů ExpressRoute, které byly nakonfigurovány před srpnem 1, 2017 bude mít všechny předpony služby inzerované prostřednictvím partnerského vztahu Microsoftu, i když nejsou definovány filtry tras. Microsoft peering obvodů ExpressRoute, které jsou nakonfigurovány na nebo po srpnu 1, 2017 nebude mít žádné předpony inzerované, dokud není připojen filtr trasy k okruhu.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Pracovního postupu

Chcete-li se úspěšně připojit ke službám prostřednictvím partnerského vztahu společnosti Microsoft, musíte provést následující kroky konfigurace:

- Musíte mít aktivní okruh ExpressRoute, který má zřízené partnerský vztah Microsoftu. K provedení těchto úkolů můžete použít následující pokyny:
  - Než budete pokračovat, [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a před pokračováním jej povolit váš poskytovatel připojení. Okruh ExpressRoute musí být ve zřízeném a povoleném stavu.
  - Pokud přímo spravujete relaci protokolu BGP, [vytvořte partnerský vztah microsoftu.](expressroute-howto-routing-portal-resource-manager.md) Nebo mají váš poskytovatel připojení zřízení partnerský vztah Microsoft pro váš okruh.

-  Je nutné vytvořit a nakonfigurovat filtr trasy.
    - Identifikujte služby, které využíváte ke spotřebě prostřednictvím partnerského vztahu Microsoftu
    - Určení seznamu hodnot komunity Protokolu BGP přidružených ke službám
    - Vytvoření pravidla umožňujícího, aby seznam předponami odpovídal hodnotám komunity Protokolu BGP

-  Filtr trasy je nutné připojit k okruhu ExpressRoute.

## <a name="before-you-begin"></a>Než začnete

Než začnete s konfigurací, ujistěte se, že splňujete následující kritéria:

 - Před zahájením konfigurace zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovní postupy.](expressroute-workflows.md)

 - Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve zřízeném a povoleném stavu.

 - Musíte mít aktivní partnerský vztah Microsoftu. Postupujte podle pokynů na [vytvoření a úpravě konfigurace partnerského vztahu](expressroute-howto-routing-portal-resource-manager.md)


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Krok 1: Získání seznamu předpon a hodnot komunity Protokolu BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Získejte seznam hodnot komunity Protokolu BGP

Hodnoty komunity protokolu BGP přidružené ke službám přístupným prostřednictvím partnerského vztahu Microsoftu jsou k dispozici na stránce [požadavky na směrování expressroute.](expressroute-routing.md)

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Vytvořte seznam hodnot, které chcete použít

Vytvořte seznam [hodnot komunity Protokolu BGP,](expressroute-routing.md#bgp) které chcete použít ve filtru trasy. 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Krok 2: Vytvoření filtru trasy a pravidla filtru

Filtr trasy může mít pouze jedno pravidlo a pravidlo musí být typu Povolit. Toto pravidlo může mít seznam hodnot komunity Protokolu BGP s ním spojené.

### <a name="1-create-a-route-filter"></a>1. Vytvoření filtru trasy
Filtr trasy můžete vytvořit výběrem možnosti pro vytvoření nového zdroje. Klikněte **na Vytvořit zdroj** > **Networking** > **RouteFilter**, jak je znázorněno na následujícím obrázku:

![Vytvoření filtru trasy](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Filtr postupu je nutné umístit do skupiny prostředků. 

![Vytvoření filtru trasy](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Vytvoření pravidla filtru

Pravidla můžete přidat a aktualizovat výběrem karty Spravovat pravidla pro filtr trasy.

![Vytvoření filtru trasy](./media/how-to-routefilter-portal/ManageRouteFilter.png)


V rozevíracím seznamu můžete vybrat služby, ke kterým se chcete připojit, a po dokončení pravidlo uložit.

![Vytvoření filtru trasy](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Krok 3: Připojení filtru trasy k okruhu ExpressRoute

Filtr trasy můžete připojit k okruhu tak, že vyberete tlačítko "Přidat okruh" a z rozevíracího seznamu vyberete okruh ExpressRoute.

![Vytvoření filtru trasy](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Pokud poskytovatel připojení konfiguruje partnerský vztah pro okruh ExpressRoute, aktualizujte okruh z okna okruhu ExpressRoute před výběrem tlačítka "Přidat okruh".

![Vytvoření filtru trasy](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>Běžné úkoly

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Získání vlastností filtru trasy

Vlastnosti filtru trasy můžete zobrazit při otevření prostředku na portálu.

![Vytvoření filtru trasy](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Aktualizace vlastností filtru trasy

Seznam hodnot komunity Protokolu BGP připojených k okruhu můžete aktualizovat výběrem tlačítka "Spravovat pravidlo".


![Vytvoření filtru trasy](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Vytvoření filtru trasy](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Odpojení filtru trasy od okruhu ExpressRoute

Chcete-li odpojit okruh od filtru trasy, klikněte pravým tlačítkem myši na okruh a klikněte na "disassociate".

![Vytvoření filtru trasy](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Odstranění filtru trasy

Filtr trasy můžete odstranit výběrem tlačítka odstranit. 

![Vytvoření filtru trasy](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Další kroky

* Další informace o expressroute naleznete v [nejčastějších dotazech k expressroute](expressroute-faqs.md).

* Informace o ukázkách konfigurace směrovače naleznete v [tématu Ukažování konfigurace směrovače a nastavení a správa směrování](expressroute-config-samples-routing.md). 
