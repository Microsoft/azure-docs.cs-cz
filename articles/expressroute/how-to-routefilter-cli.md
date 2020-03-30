---
title: 'ExpressRoute: Filtry směrování – partnerský vztah Microsoftu:rozhraní příkazového příkazu Azure'
description: Tento článek popisuje, jak nakonfigurovat filtry tras pro partnerský vztah Microsoftu pomocí rozhraní příkazového příkazu Azure
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: c3c50a005e119890fb17fcf7b3114a747bbe34bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033419"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Konfigurace filtrů tras pro partnerský vztah Microsoftu: Rozhraní příkazového příkazu Azure

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Filtry tras představují způsob, jak spotřebovat dílčí sadu podporovaných služeb přes partnerský vztah Microsoftu. Postup v tomto článku vám pomůže nakonfigurovat a spravovat filtry tras pro okruhy ExpressRoute.

Služby Office 365, jako je Exchange Online, SharePoint Online a Skype pro firmy, jsou dostupné prostřednictvím partnerského vztahu Microsoftu. Při konfiguraci partnerského vztahu Microsoftu v okruhu ExpressRoute jsou všechny předpony související s těmito službami inzerovány prostřednictvím vytvořených relací Protokolu BGP. Ke každé předponě je připojená hodnota komunity protokolu BGP, která identifikuje službu nabízenou prostřednictvím dané předpony. Seznam hodnot komunity Protokolu BGP a služeb, na které mapují, naleznete v [tématu komunity Protokolu BGP](expressroute-routing.md#bgp).

Pokud požadujete připojení ke všem službám, velký počet předpon jsou inzerovány prostřednictvím protokolu BGP. Tím se výrazně zvětší velikost směrovacích tabulek udržovaných směrovači v síti. Pokud plánujete využívat pouze podmnožinu služeb nabízených prostřednictvím partnerského vztahu Microsoftu, můžete zmenšit velikost směrovacích tabulek dvěma způsoby. Můžete:

* Odfiltrujte nežádoucí předpony použitím filtrů tras v komunitách Protokolu BGP. Jedná se o standardní síťové postupy a běžně se používá v mnoha sítích.

* Definujte filtry tras a aplikujte je na okruh ExpressRoute. Filtr trasy je nový prostředek, který umožňuje vybrat seznam služeb, které chcete využívat prostřednictvím partnerského vztahu Microsoftu. Směrovače ExpressRoute odesílají pouze seznam předpon, které patří ke službám uvedeným ve filtru trasy.

### <a name="about-route-filters"></a><a name="about"></a>Filtry tras

Když je partnerský vztah Microsoftu nakonfigurován v okruhu ExpressRoute, hraniční směrovače Microsoft vytvoří dvojici relací Protokolu BGP s hraničními směrovači (vy nebo poskytovatele připojení). Do vaší sítě se žádné trasy neinzerují. Pokud chcete povolit inzerování tras do vaší sítě, je potřeba k ní přidružit filtr tras.

Filtr tras umožňuje identifikovat služby, které chcete využívat prostřednictvím partnerského vztahu Microsoftu s vaším okruhem ExpressRoute. V podstatě se jedná o seznam všech povolených hodnot komunity protokolu BGP. Po definování prostředku filtru tras a jeho připojení k okruhu ExpressRoute se do vaší sítě budou inzerovat všechny předpony, které se mapují na hodnoty komunity protokolu BGP.

Abyste k nim mohli připojit filtry tras se službami Office 365, musíte mít oprávnění využívat služby Office 365 prostřednictvím ExpressRoute. Pokud nemáte oprávnění využívat služby Office 365 prostřednictvím ExpressRoute, operace připojení filtrů postupu se nezdaří. Další informace o procesu autorizace najdete v [tématu Azure ExpressRoute pro Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Microsoft peering obvodů ExpressRoute, které byly nakonfigurovány před srpnem 1, 2017 bude mít všechny předpony služby inzerované prostřednictvím partnerského vztahu Microsoftu, i když nejsou definovány filtry tras. Microsoft peering obvodů ExpressRoute, které jsou nakonfigurovány na nebo po srpnu 1, 2017 nebude mít žádné předpony inzerované, dokud není připojen filtr trasy k okruhu.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Pracovního postupu

Chcete-li se úspěšně připojit ke službám prostřednictvím partnerského vztahu společnosti Microsoft, musíte provést následující kroky konfigurace:

* Musíte mít aktivní okruh ExpressRoute, který má zřízené partnerský vztah Microsoftu. K provedení těchto úkolů můžete použít následující pokyny:
  * Než budete pokračovat, [vytvořte okruh ExpressRoute](howto-circuit-cli.md) a před pokračováním jej povolit váš poskytovatel připojení. Okruh ExpressRoute musí být ve zřízeném a povoleném stavu.
  * Pokud přímo spravujete relaci protokolu BGP, [vytvořte partnerský vztah microsoftu.](howto-routing-cli.md) Nebo mají váš poskytovatel připojení zřízení partnerský vztah Microsoft pro váš okruh.

* Je nutné vytvořit a nakonfigurovat filtr trasy.
  * Identifikujte služby, které využíváte ke spotřebě prostřednictvím partnerského vztahu Microsoftu
  * Určení seznamu hodnot komunity Protokolu BGP přidružených ke službám
  * Vytvoření pravidla umožňujícího, aby seznam předponami odpovídal hodnotám komunity Protokolu BGP

* Filtr trasy je nutné připojit k okruhu ExpressRoute.

## <a name="before-you-begin"></a>Než začnete

Než začnete, nainstalujte si nejnovější verzi příkazů rozhraní příkazového řádku (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématech [Instalace Azure CLI](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).

* Před zahájením konfigurace zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovní postupy.](expressroute-workflows.md)

* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](howto-circuit-cli.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve zřízeném a povoleném stavu.

* Musíte mít aktivní partnerský vztah Microsoftu. Postupujte podle pokynů na [vytvoření a úpravě konfigurace partnerského vztahu](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte předplatné.

Chcete-li zahájit konfiguraci, přihlaste se ke svému účtu Azure. Pokud používáte "Try It", jste přihlášeni automaticky a můžete přeskočit přihlašovací krok. Pomocí následujících příkladů, které vám pomohou připojit se:

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

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Krok 1: Získání seznamu předpon a hodnot komunity Protokolu BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Získejte seznam hodnot komunity Protokolu BGP

Pomocí následující rutiny získáte seznam hodnot komunity Protokolu BGP přidružených ke službám přístupným prostřednictvím partnerského vztahu Microsoftu a seznam předpon, které jsou s nimi spojené:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Vytvořte seznam hodnot, které chcete použít

Vytvořte seznam hodnot komunity Protokolu BGP, které chcete použít ve filtru trasy.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Krok 2: Vytvoření filtru trasy a pravidla filtru

Filtr trasy může mít pouze jedno pravidlo a pravidlo musí být typu Povolit. Toto pravidlo může mít seznam hodnot komunity Protokolu BGP s ním spojené.

### <a name="1-create-a-route-filter"></a>1. Vytvoření filtru trasy

Nejprve vytvořte filtr trasy. Příkaz `az network route-filter create` pouze vytvoří prostředek filtru trasy. Po vytvoření zdroje je nutné vytvořit pravidlo a připojit jej k objektu filtru trasy. Chcete-li vytvořit prostředek filtru trasy, spusťte následující příkaz:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Vytvoření pravidla filtru

Chcete-li vytvořit nové pravidlo, spusťte následující příkaz:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Krok 3: Připojení filtru trasy k okruhu ExpressRoute

Spuštěním následujícího příkazu připojte filtr trasy k okruhu ExpressRoute:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Běžné úkoly

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Získání vlastností filtru trasy

Chcete-li získat vlastnosti filtru trasy, použijte následující příkaz:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Aktualizace vlastností filtru trasy

Pokud je filtr trasy již připojen k okruhu, aktualizace seznamu komunity Protokolu BGP automaticky rozšíří příslušné změny reklamy předpony prostřednictvím zavedených relací protokolu BGP. Seznam komunity protokolu BGP filtru trasy můžete aktualizovat pomocí následujícího příkazu:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Odpojení filtru trasy od okruhu ExpressRoute

Jakmile je filtr trasy odpojen od okruhu ExpressRoute, nejsou prostřednictvím relace Protokolu BGP inzerovány žádné předpony. Filtr trasy můžete odpojit od okruhu ExpressRoute pomocí následujícího příkazu:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Odstranění filtru trasy

Filtr trasy lze odstranit pouze v případě, že není připojen k žádnému okruhu. Před pokusem o jeho odstranění se ujistěte, že filtr trasy není připojen k žádnému okruhu. Filtr trasy můžete odstranit pomocí následujícího příkazu:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

Další informace o expressroute naleznete v [nejčastějších dotazech k expressroute](expressroute-faqs.md).
