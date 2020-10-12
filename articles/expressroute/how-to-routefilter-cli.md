---
title: 'ExpressRoute: filtry tras – partnerské vztahy Microsoftu: Azure CLI'
description: Tento článek popisuje, jak nakonfigurovat filtry tras pro partnerský vztah Microsoftu pomocí Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/07/2018
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8fbce15b84371b7b7907deff361e2a2e706bec28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89567703"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Konfigurace filtrů tras pro partnerský vztah Microsoftu: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Filtry tras představují způsob, jak spotřebovat dílčí sadu podporovaných služeb přes partnerský vztah Microsoftu. Kroky v tomto článku vám pomůžou nakonfigurovat a spravovat filtry tras pro okruhy ExpressRoute.

Služby Microsoft 365 Services, jako je Exchange Online, SharePoint Online a Skype pro firmy, jsou přístupné prostřednictvím partnerského vztahu Microsoftu. Při konfiguraci partnerského vztahu Microsoftu v okruhu ExpressRoute jsou všechny předpony související s těmito službami inzerovány prostřednictvím vytvořených relací protokolu BGP. Ke každé předponě je připojená hodnota komunity protokolu BGP, která identifikuje službu nabízenou prostřednictvím dané předpony. Seznam hodnot komunity protokolu BGP a služeb, na které se mapují, najdete v tématu [komunity protokolu BGP](expressroute-routing.md#bgp).

Pokud budete potřebovat připojení ke všem službám, je prostřednictvím protokolu BGP inzerován velký počet předpon. Tím se významně zvyšuje velikost směrovacích tabulek udržovaných směrovači v rámci vaší sítě. Pokud máte v úmyslu využívat jenom podmnožinu služeb nabízených partnerským vztahem Microsoftu, můžete zmenšit velikost směrovacích tabulek dvěma způsoby. Můžete:

* Odfiltrujte nechtěné předpony pomocí filtrů směrování v komunitách protokolu BGP. Jedná se o standardní postupy sítě, které se běžně používají v mnoha sítích.

* Definujte filtry tras a použijte je pro okruh ExpressRoute. Filtr tras je nový prostředek, který umožňuje vybrat seznam služeb, které chcete využívat v partnerském vztahu Microsoftu. Směrovače ExpressRoute odesílají seznam předpon, které patří do služeb identifikovaných ve filtru tras.

### <a name="about-route-filters"></a><a name="about"></a>O filtrech tras

Když na okruhu ExpressRoute nakonfigurujete partnerský vztah Microsoftu, vytvoří směrovače Microsoft Edge dvojici relací protokolu BGP s hraničními směrovači (vašimi nebo vaším poskytovatelem připojení). Do vaší sítě se žádné trasy neinzerují. Pokud chcete povolit inzerování tras do vaší sítě, je potřeba k ní přidružit filtr tras.

Filtr tras umožňuje identifikovat služby, které chcete využívat prostřednictvím partnerského vztahu Microsoftu s vaším okruhem ExpressRoute. Je v podstatě povolený seznam všech hodnot komunity protokolu BGP. Po definování prostředku filtru tras a jeho připojení k okruhu ExpressRoute se do vaší sítě budou inzerovat všechny předpony, které se mapují na hodnoty komunity protokolu BGP.

Aby bylo možné připojit filtry směrování s Microsoft 365 službami, je nutné mít autorizaci ke využívání služeb Microsoft 365 prostřednictvím ExpressRoute. Pokud nemáte oprávnění ke využívání Microsoft 365ch služeb prostřednictvím ExpressRoute, operace připojení filtrů tras se nezdařila. Další informace o procesu autorizace najdete v tématu [Azure ExpressRoute for Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Partnerské vztahy Microsoftu okruhů ExpressRoute, které byly nakonfigurované před 1. srpna 2017, budou mít všechny předpony služby inzerované prostřednictvím partnerského vztahu Microsoftu, a to i v případě, že nejsou definované filtry směrování. Partnerský vztah Microsoftu pro okruhy ExpressRoute, které jsou nakonfigurované na nebo od 1. srpna 2017, nebudou mít inzerované předpony, dokud není k okruhu připojen filtr tras.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Pracovní postup

Abyste se mohli úspěšně připojit ke službám prostřednictvím partnerského vztahu Microsoftu, musíte provést následující kroky konfigurace:

* Musíte mít aktivní okruh ExpressRoute s zřízeným partnerským vztahem Microsoftu. K provedení těchto úloh můžete použít následující pokyny:
  * Než budete pokračovat, [vytvořte okruh ExpressRoute](howto-circuit-cli.md) a požádejte ho o okruh povolený poskytovatelem připojení. Okruh ExpressRoute musí být ve stavu zřizování a povoleno.
  * [Vytvoření partnerského vztahu Microsoftu](howto-routing-cli.md) , pokud spravujete relaci protokolu BGP přímo Nebo můžete nechat poskytovatele připojení zřídit partnerský vztah Microsoftu pro váš okruh.

* Musíte vytvořit a nakonfigurovat filtr tras.
  * Identifikujte služby, které využíváte prostřednictvím partnerského vztahu Microsoftu
  * Identifikujte seznam hodnot komunity protokolu BGP přidružených ke službám.
  * Vytvořte pravidlo, které umožní seznamu předpony odpovídat hodnotám komunity protokolu BGP.

* Filtr tras musíte připojit k okruhu ExpressRoute.

## <a name="before-you-begin"></a>Než začnete

Než začnete, nainstalujte si nejnovější verzi příkazů rozhraní příkazového řádku (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématech [Instalace Azure CLI](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).

* Než začnete s konfigurací, Projděte si [požadavky](expressroute-prerequisites.md) a [pracovní postupy](expressroute-workflows.md) .

* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](howto-circuit-cli.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve stavu zřizování a povoleno.

* Musíte mít aktivní partnerský vztah Microsoftu. Postupujte podle pokynů v tématu [Vytvoření a úprava konfigurace partnerského vztahu](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte své předplatné.

Pokud chcete zahájit konfiguraci, přihlaste se ke svému účtu Azure. Pokud používáte "vyzkoušet si", jste přihlášení automaticky a můžete přeskočit krok přihlášení. Následující příklady vám pomůžou s připojením:

```azurecli
az login
```

Zkontrolujte předplatná pro příslušný účet.

```azurecli-interactive
az account list
```

Vyberte předplatné, pro které chcete vytvořit okruh ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Krok 1: získání seznamu předpon a hodnot komunity protokolu BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. získání seznamu hodnot komunity protokolu BGP

Pomocí následující rutiny Získejte seznam hodnot komunity protokolu BGP přidružených k dostupným službám prostřednictvím partnerského vztahu Microsoftu a seznam předpon, které jsou k nim přidružené:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Vytvořte seznam hodnot, které chcete použít.

Vytvořte seznam hodnot komunity protokolu BGP, které chcete použít ve filtru tras.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Krok 2: Vytvoření filtru tras a pravidla filtru

Filtr tras může mít pouze jedno pravidlo a pravidlo musí být typu Allow. Toto pravidlo může obsahovat seznam hodnot komunity protokolu BGP přidružených k tomuto pravidlu.

### <a name="1-create-a-route-filter"></a>1. Vytvoření filtru tras

Nejprve vytvořte filtr tras. Příkaz `az network route-filter create` vytvoří pouze prostředek filtru tras. Po vytvoření prostředku je nutné vytvořit pravidlo a připojit ho k objektu filtru tras. Spuštěním následujícího příkazu vytvořte prostředek filtru tras:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. vytvoření pravidla filtru

Spuštěním následujícího příkazu vytvořte nové pravidlo:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Krok 3: připojení filtru tras k okruhu ExpressRoute

Spuštěním následujícího příkazu připojte filtr tras k okruhu ExpressRoute:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Běžné úkoly

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Získání vlastností filtru tras

Chcete-li získat vlastnosti filtru tras, použijte následující příkaz:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Aktualizace vlastností filtru tras

Pokud je filtr tras již k okruhu připojen, aktualizace seznamu komunity protokolu BGP automaticky šíří příslušné změny předpony prostřednictvím zavedených relací protokolu BGP. Seznam komunity protokolu BGP pro svůj filtr tras můžete aktualizovat pomocí následujícího příkazu:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Odpojení filtru tras od okruhu ExpressRoute

Jakmile se filtr tras odpojí od okruhu ExpressRoute, žádné předpony se Neinzerují prostřednictvím relace protokolu BGP. Filtr tras z okruhu ExpressRoute můžete odpojit pomocí následujícího příkazu:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Odstranění filtru tras

Filtr tras můžete odstranit pouze v případě, že není připojen k žádnému okruhu. Před pokusem o odstranění filtru směrování se ujistěte, že není připojen k žádnému okruhu. Filtr tras můžete odstranit pomocí následujícího příkazu:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).
