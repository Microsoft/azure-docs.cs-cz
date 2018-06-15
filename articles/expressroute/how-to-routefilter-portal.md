---
title: 'Nastavit filtry směrování pro partnerský vztah Azure ExpressRoute Microsoft: portál | Microsoft Docs'
description: Tento článek popisuje, jak nastavit filtry tras pro aplikaci Microsoft Peering pomocí portálu Azure
documentationcenter: na
services: expressroute
author: ganesr
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: ganesr
ms.openlocfilehash: ab0cd45334581f6f5c6dd6e86939e018df5de074
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29387723"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Nastavit filtry směrování pro partnerský vztah Microsoftu: portál Azure
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Filtry tras představují způsob, jak využívat podmnožinu podporované služby prostřednictvím partnerského vztahu Microsoftu. Kroky v tomto článku vám pomůžou nakonfigurovat a spravovat filtry tras pro okruhy ExpressRoute.

Dynamics 365 služby a služby Office 365 například Exchange Online, SharePoint Online a Skype pro firmy a službám Azure, jako je například úložiště a databáze SQL jsou přístupné prostřednictvím partnerského vztahu Microsoftu. Pokud partnerský vztah Microsoftu je nakonfigurován v okruhu ExpressRoute, jsou všechny předpony související s těmito službami inzerované prostřednictvím relace protokolu BGP, které jsou vytvořeny. Hodnota komunity protokolu BGP je připojený k každých předpona k identifikaci služby, které nabízí prostřednictvím předponu. Seznam hodnot komunity protokolu BGP a služeb, jsou mapovány najdete v tématu [komunit protokolu BGP](expressroute-routing.md#bgp).

Pokud budete potřebovat připojení ke všem službám, jsou velký počet předpon inzerovaných prostřednictvím protokolu BGP. Tím se výrazně zvyšuje velikost směrovací tabulky udržuje pomocí směrovačů v rámci vaší sítě. Pokud budete chtít využívat jenom podmnožinu službám nabízeným přes partnerský vztah Microsoftu, můžete snížit velikost vašeho směrovací tabulky dvěma způsoby. Můžete:

- Filtrovat nežádoucí předpony použitím filtry tras na komunit protokolu BGP. Tento postup standardní sítě a běžně používá v rámci mnoho sítí.

- Zadejte filtry tras a použít je pro váš okruh ExpressRoute. Filtr trasy je nový prostředek, který umožňuje vybrat seznam služeb, které budete chtít využívat prostřednictvím partnerského vztahu Microsoftu. ExpressRoute směrovače odeslat pouze seznam předpon, které patří na služby určené ve filtru trasy.

### <a name="about"></a>O filtrech směrování

Pokud partnerský vztah Microsoftu je nakonfigurován na váš okruh ExpressRoute, Microsoft hraniční směrovače vytvořte dvojici relací protokolu BGP s hraniční směrovače (váš nebo váš poskytovatel připojení). Žádné trasy inzerovány vaší sítě. Pokud chcete povolit inzerování trasy k síti, je nutné přidružit filtr trasy.

Filtr trasy umožňuje identifikoval služby, které chcete využívat prostřednictvím partnerského vztahu Microsoftu okruhu ExpressRoute. Je v podstatě prázdný seznam všech hodnot komunity protokolu BGP. Po filtru prostředek trasy definované a připojené k okruhu ExpressRoute, jsou všechny předpony, které jsou mapovány na hodnoty komunity protokolu BGP inzerovaný do vaší sítě.

Abyste mohli připojit filtry tras se službami Office 365 na ně, musí mít oprávnění pro využívání služeb Office 365 prostřednictvím ExpressRoute. Pokud nemáte oprávnění k využívání služeb Office 365 prostřednictvím ExpressRoute, operace připojit filtry tras se nezdaří. Další informace o procesu autorizačního najdete v tématu [Azure ExpressRoute pro Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Připojení ke službám Dynamics 365 nevyžaduje žádné předchozí autorizace.

> [!IMPORTANT]
> Okruhy ExpressRoute, které byly nakonfigurovány před 1. srpna 2017 partnerského vztahu Microsoftu bude mít všechny služby předpony inzerované prostřednictvím Microsoft partnerský vztah, i když nejsou definovány filtry tras. Okruhy ExpressRoute, které jsou nakonfigurované na nebo po 1 srpen 2017 partnerského vztahu Microsoftu nebude mít všechny předpony inzerované dokud trasy filtr je připojen k okruhu.
> 
> 

### <a name="workflow"></a>pracovní postup

Abyste mohli úspěšně připojit ke službám prostřednictvím partnerského vztahu Microsoftu, musíte provést následující kroky konfigurace:

- Musí mít aktivní okruh ExpressRoute, který má Microsoft partnerský vztah zřízené. K těmto úkolům můžete použít následující pokyny:
  - [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a mějte ho povolený vaším poskytovatelem připojení, než budete pokračovat. Okruh ExpressRoute musí být ve stavu zřízený a povolený.
  - [Vytvoření partnerského vztahu Microsoftu](expressroute-howto-routing-portal-resource-manager.md) Pokud budete spravovat přímo relaci protokolu BGP. Nebo váš poskytovatel připojení zřídit partnerský vztah Microsoftu pro váš okruh.

-  Musíte vytvořit a konfigurovat filtr trasy.
    - Identifikovat služby vám využívat prostřednictvím partnerského vztahu Microsoftu
    - Určení seznamu hodnot komunity protokolu BGP přidruženého ke službám
    - Vytvořit pravidlo, které umožňují seznamu předponu odpovídající hodnoty komunity protokolu BGP

-  Filtr trasy musí připojit k okruhu ExpressRoute.

## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace, ujistěte se, že splňují následující kritéria:

 - Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních](expressroute-workflows.md) před zahájením konfigurace.

 - Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve stavu zřízený a povolený.

 - Musíte mít active partnerský vztah Microsoftu. Postupujte podle pokynů v [vytvoření a úprava konfigurace partnerského vztahu](expressroute-howto-routing-portal-resource-manager.md)


## <a name="prefixes"></a>Krok 1: Získání seznamu předpon a hodnotami komunity protokolu BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Získat seznam hodnot komunity protokolu BGP

Je k dispozici v souvisejících se službou přístupné prostřednictvím partnerského vztahu Microsoftu hodnotami komunity protokolu BGP [požadavky na směrování služby ExpressRoute](expressroute-routing.md) stránky.

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Zkontrolujte seznam hodnot, které chcete použít

Zkontrolujte seznam hodnotami komunity protokolu BGP, které chcete použít ve filtru trasy. Jako příklad je hodnota komunity protokolu BGP pro služby Dynamics 365 12076:5040.

## <a name="filter"></a>Krok 2: Vytvoření filtr trasy a pravidlo filtru

Filtr trasy může mít pouze jedno pravidlo a pravidlo musí být typu 'Povolit'. Toto pravidlo může mít seznam hodnot komunity protokolu BGP s ním spojená.

### <a name="1-create-a-route-filter"></a>1. Vytvoření filtru trasy
Pokud vyberete možnost vytvořit nový prostředek, můžete vytvořit filtr trasy. Klikněte na tlačítko **vytvořit prostředek** > **sítě** > **RouteFilter**, jak je znázorněno na následujícím obrázku:

![Vytvoření filtru trasy](.\media\how-to-routefilter-portal\CreateRouteFilter1.png)

Je nutné umístit filtru tras ve skupině prostředků. 

![Vytvoření filtru trasy](.\media\how-to-routefilter-portal\CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Vytvořit pravidlo filtru

Můžete přidat a aktualizovat pravidla vyberte na kartě Správa pravidlo filtru trasy.

![Vytvoření filtru trasy](.\media\how-to-routefilter-portal\ManageRouteFilter.png)


Můžete vybrat služby, které chcete připojit se z rozevíracího seznamu a uložíte pravidlo po dokončení.

![Vytvoření filtru trasy](.\media\how-to-routefilter-portal\AddRouteFilterRule.png)


## <a name="attach"></a>Krok 3: Připojení filtr trasy k okruhu ExpressRoute

Filtr trasy můžete připojit k okruhu vyberte tlačítko "Přidat okruh" a tlačítko okruh ExpressRoute z rozevíracího seznamu.

![Vytvoření filtru trasy](.\media\how-to-routefilter-portal\AddCktToRouteFilter.png)

Pokud poskytovatel připojení nakonfiguruje partnerský vztah pro vaše aktualizace okruh ExpressRoute okruh v okně okruh ExpressRoute dřív, než vyberete tlačítko "Přidat okruh".

![Vytvoření filtru trasy](.\media\how-to-routefilter-portal\RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>Běžné úlohy

### <a name="getproperties"></a>Chcete-li získat vlastnosti filtru trasy

Při otevření prostředku na portálu, můžete zobrazit vlastnosti filtr trasy.

![Vytvoření filtru trasy](.\media\how-to-routefilter-portal\ViewRouteFilter.png)


### <a name="updateproperties"></a>Aktualizovat vlastnosti filtr trasy

Můžete aktualizovat seznam hodnot komunity protokolu BGP připojit k okruhu výběrem tlačítko "Správa pravidlo".


![Vytvoření filtru trasy](.\media\how-to-routefilter-portal\ManageRouteFilter.png)

![Vytvoření filtru trasy](.\media\how-to-routefilter-portal\AddRouteFilterRule.png) 


### <a name="detach"></a>Odpojit filtr trasy z okruhu ExpressRoute

Odpojit okruh z filtru tras, klikněte pravým tlačítkem na okruh a klikněte na "zrušit přidružení".

![Vytvoření filtru trasy](.\media\how-to-routefilter-portal\DetachRouteFilter.png) 


### <a name="delete"></a>Odstranění filtru trasy

Výběrem tlačítko Odstranit odstraníte filtr trasy. 

![Vytvoření filtru trasy](.\media\how-to-routefilter-portal\DeleteRouteFilter.png) 

## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).