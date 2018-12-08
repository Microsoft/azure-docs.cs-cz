---
title: 'Konfigurace filtrů směrování pro partnerský vztah Microsoftu: Azure ExpressRoute – portál | Dokumentace Microsoftu'
description: Tento článek popisuje postup konfigurace filtrů směrování pro partnerský vztah Microsoftu pomocí webu Azure portal.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 0515b5e85c3bcf56f1f238620d6036d1be0bec7e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104199"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Konfigurace filtrů směrování pro partnerský vztah Microsoftu: Azure portal
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Filtry tras představují způsob, jak spotřebovat dílčí sadu podporovaných služeb přes partnerský vztah Microsoftu. Kroky v tomto článku vám pomůžou nakonfigurovat a spravovat filtrů směrování pro okruhy ExpressRoute.

Služby Dynamics 365 a službám Office 365 Exchange Online, SharePoint Online a Skype pro firmy a služeb Azure, jako jsou úložiště a SQL DB jsou přístupné prostřednictvím partnerského vztahu Microsoftu. Po vytvoření partnerského vztahu Microsoftu je nakonfigurované v okruhu ExpressRoute, jsou související s těmito službami všechny předpony inzerované prostřednictvím relace protokolu BGP, které jsou vytvořeny. Ke každé předponě je připojená hodnota komunity protokolu BGP, která identifikuje službu nabízenou prostřednictvím dané předpony. Tyto hodnoty komunity protokolu BGP a služeb, které jsou mapovány seznam najdete v tématu [komunit protokolu BGP](expressroute-routing.md#bgp).

Pokud budete vyžadovat připojení ke všem službám, jsou velké množství předpony inzerované prostřednictvím protokolu BGP. To výrazně zvyšuje velikost tabulky směrování udržována směrovače v rámci vaší sítě. Pokud budete chtít využívat pouze podmnožinu službám nabízeným prostřednictvím partnerského vztahu Microsoftu, můžete snížit velikost tabulek směrování dvěma způsoby. Můžete:

- Filtrování nežádoucí předpony použitím filtry tras na komunit protokolu BGP. Je standardní postup sítě a se běžně používá v rámci více sítí.

- Definujte filtry tras a použít je pro váš okruh ExpressRoute. Filtr tras se nový prostředek, který vám umožní vybrat seznam služeb, které budete chtít využívat prostřednictvím partnerského vztahu Microsoftu. ExpressRoute směrovače pouze odeslat seznam předpon, které patří do služby určené ve filtru tras.

### <a name="about"></a>O filtry tras

Po vytvoření partnerského vztahu Microsoftu je nakonfigurovaná na váš okruh ExpressRoute, hraničním směrovačům Microsoft vytvořit dvojici relací protokolu BGP s hraniční směrovače (té vaší nebo váš poskytovatel připojení). Do vaší sítě se žádné trasy neinzerují. Pokud chcete povolit inzerování tras do vaší sítě, je potřeba k ní přidružit filtr tras.

Filtr tras umožňuje identifikovat služby, které chcete využívat prostřednictvím partnerského vztahu Microsoftu s vaším okruhem ExpressRoute. V podstatě se jedná o seznam všech povolených hodnot komunity protokolu BGP. Po definování prostředku filtru tras a jeho připojení k okruhu ExpressRoute se do vaší sítě budou inzerovat všechny předpony, které se mapují na hodnoty komunity protokolu BGP.

Aby bylo možné připojit filtry tras se službami Office 365 s nimi, musíte mít oprávnění k využívání služeb Office 365 přes ExpressRoute. Pokud nemáte oprávnění k využívání služeb Office 365 přes ExpressRoute, operace připojit filtry tras se nezdaří. Další informace o procesu ověřování najdete v tématu [Azure ExpressRoute pro Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Připojení ke službám Dynamics 365 nevyžaduje žádné předchozí autorizace.

> [!IMPORTANT]
> Partnerský vztah Microsoftu okruhů ExpressRoute, které byly nakonfigurovány před 1. srpna 2017 budou mít všechny služby předpony inzerované prostřednictvím Microsoft partnerský vztah, i když nejsou definovány filtry tras. Partnerský vztah Microsoftu okruhů ExpressRoute, které jsou nakonfigurované 1. srpna 2017 nebo později nebude mít všechny předpony inzerované, dokud se filtr tras je připojen k okruhu.
> 
> 

### <a name="workflow"></a>Pracovní postup

Abyste mohli úspěšně připojit ke službám prostřednictvím partnerského vztahu Microsoftu, třeba provést následující kroky konfigurace:

- Musíte mít aktivní okruh ExpressRoute, který má Microsoft partnerský vztah zřízené. Postupujte podle následujících pokynů můžete použít k provedení následujících úkolů:
  - [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a mějte ho povolený podle svého poskytovatele připojení, než budete pokračovat. Okruh ExpressRoute musí být ve stavu zřízený a povolený.
  - [Vytvoření partnerského vztahu Microsoftu](expressroute-howto-routing-portal-resource-manager.md) při správě přímo relace protokolu BGP. Nebo váš poskytovatel připojení zřízení partnerského vztahu Microsoftu pro váš okruh.

-  Musíte vytvořit a konfigurovat filtr tras.
    - Určete služby vám využívat prostřednictvím partnerského vztahu Microsoftu
    - Určení seznamu přidruženého ke službám hodnotami komunity protokolu BGP
    - Vytvořit pravidlo povolující seznam předpon odpovídající tyto hodnoty komunity protokolu BGP

-  Je nutné připojit filtr tras k okruhu ExpressRoute.

## <a name="before-you-begin"></a>Před zahájením

Než začnete s konfigurací, ujistěte se, že splňujete následující kritéria:

 - Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních postupů](expressroute-workflows.md) předtím, než začnete s konfigurací.

 - Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve stavu zřízený a povolený.

 - Musíte mít active partnerský vztah Microsoftu. Postupujte podle pokynů na adrese [vytvoření a změny konfigurace partnerského vztahu](expressroute-howto-routing-portal-resource-manager.md)


## <a name="prefixes"></a>Krok 1: Získání seznamu předpon a hodnotami komunity protokolu BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Získání seznamu sad hodnotami komunity protokolu BGP

Je k dispozici v související se službami, které jsou přístupné prostřednictvím partnerského vztahu Microsoftu hodnotami komunity protokolu BGP [požadavky na směrování služby ExpressRoute](expressroute-routing.md) stránky.

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Vytvořte seznam hodnot, které chcete použít

Zkontrolujte seznam hodnotami komunity protokolu BGP, které chcete použít ve filtru tras. Jako příklad je hodnota komunity protokolu BGP pro služby Dynamics 365 12076:5040.

## <a name="filter"></a>Krok 2: Vytvoření filtru tras a pravidlo filtru

Filtr tras může mít jenom jedno pravidlo a pravidlo musí být typu "Povolit". Toto pravidlo může mít seznam hodnot komunity protokolu BGP s ním spojená.

### <a name="1-create-a-route-filter"></a>1. Vytvořit filtr tras
Můžete vytvořit filtr tras tak, že vyberete možnost vytvořit nový prostředek. Klikněte na tlačítko **vytvořit prostředek** > **sítě** > **RouteFilter**, jak je znázorněno na následujícím obrázku:

![Vytvořit filtr tras](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Filtr tras musíte umístit do skupiny prostředků. 

![Vytvořit filtr tras](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Vytvořit pravidlo filtru

Můžete přidat a aktualizovat pravidla tak, že vyberete kartu spravovat pravidla pro váš filtr tras.

![Vytvořit filtr tras](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Můžete zvolit služby, které chcete připojit z rozevíracího seznamu a uložíte pravidlo, až budete hotovi.

![Vytvořit filtr tras](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="attach"></a>Krok 3: Připojení filtr tras k okruhu ExpressRoute

Filtr tras k okruhu můžete připojit tak, že vyberete tlačítko "Přidat okruh" a výběrem z rozevíracího seznamu okruh ExpressRoute.

![Vytvořit filtr tras](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Pokud poskytovatel připojení nakonfiguruje partnerský vztah pro aktualizaci okruh ExpressRoute okruh z okna okruh ExpressRoute než vyberete tlačítko "Přidat okruh".

![Vytvořit filtr tras](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>Běžné úlohy

### <a name="getproperties"></a>Chcete-li získat vlastnosti filtr tras

Při otevření prostředku na portálu můžete zobrazit vlastnosti filtr tras.

![Vytvořit filtr tras](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="updateproperties"></a>Aktualizovat vlastnosti filtr tras

Můžete aktualizovat seznam hodnotami komunity protokolu BGP připojit k okruhu tak, že vyberete tlačítko "Spravovat pravidla".


![Vytvořit filtr tras](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Vytvořit filtr tras](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="detach"></a>Chcete-li odpojit filtr tras z okruhu ExpressRoute

Chcete-li odpojit okruh z filtru tras, klikněte pravým tlačítkem na okruh a klikněte na "zrušit".

![Vytvořit filtr tras](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="delete"></a>Odstranit filtr tras

Můžete odstranit filtr tras tak, že vyberete tlačítko Odstranit. 

![Vytvořit filtr tras](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).