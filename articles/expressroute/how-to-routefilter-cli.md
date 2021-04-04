---
title: 'Kurz: konfigurace filtrů tras pro partnerský vztah Microsoftu – Azure CLI'
description: V tomto kurzu se dozvíte, jak nakonfigurovat filtry tras pro partnerský vztah Microsoftu pomocí Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: ac7fc5af21f11699331d41a074e88ae757170664
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975990"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-azure-cli"></a>Kurz: konfigurace filtrů tras pro partnerský vztah Microsoftu: Azure CLI

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

Chcete-li se úspěšně připojit ke službám prostřednictvím partnerského vztahu Microsoftu, je nutné provést následující kroky konfigurace:

* Musíte mít aktivní okruh ExpressRoute s zřízeným partnerským vztahem Microsoftu. K provedení těchto úloh můžete použít následující pokyny:
  * Než budete pokračovat, [vytvořte okruh ExpressRoute](howto-circuit-cli.md) a požádejte ho o povolený okruh poskytovatele připojení. Okruh ExpressRoute musí být ve stavu zřizování a povoleno.
  * [Vytvoření partnerského vztahu Microsoftu](howto-routing-cli.md) , pokud spravujete relaci protokolu BGP přímo Nebo můžete nechat poskytovatele připojení zřídit partnerský vztah Microsoftu pro váš okruh.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).

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

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Získání seznamu předpon a hodnot komunity protokolu BGP

1. Pomocí následující rutiny Získejte seznam hodnot komunity protokolu BGP a předpony přidružené k dostupným službám prostřednictvím partnerského vztahu Microsoftu:

    ```azurecli-interactive
    az network route-filter rule list-service-communities
    ```

1. Vytvořte seznam hodnot komunity protokolu BGP, které chcete použít ve filtru tras.

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Vytvoření filtru tras a pravidla filtru

Filtr tras může mít pouze jedno pravidlo a pravidlo musí být typu Allow. Toto pravidlo může obsahovat seznam hodnot komunity protokolu BGP přidružených k tomuto pravidlu. Příkaz `az network route-filter create` vytvoří pouze prostředek filtru tras. Po vytvoření prostředku je nutné vytvořit pravidlo a připojit ho k objektu filtru tras.

1. Chcete-li vytvořit prostředek filtru tras, spusťte následující příkaz:

    ```azurecli-interactive
    az network route-filter create -n MyRouteFilter -g MyResourceGroup
    ```

1. Chcete-li vytvořit pravidlo filtru tras, spusťte následující příkaz:
 
    ```azurecli-interactive
    az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Připojení filtru tras k okruhu ExpressRoute

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

Pokud je filtr tras již k okruhu připojen, aktualizace seznamu komunity protokolu BGP automaticky šíří prefixové změny předpony přes vytvořenou relaci protokolu BGP. Seznam komunity protokolu BGP pro svůj filtr tras můžete aktualizovat pomocí následujícího příkazu:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Odpojení filtru tras od okruhu ExpressRoute

Jakmile se filtr tras odpojí od okruhu ExpressRoute, žádné předpony se Neinzerují prostřednictvím relace protokolu BGP. Filtr tras z okruhu ExpressRoute můžete odpojit pomocí následujícího příkazu:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Filtr tras můžete odstranit pouze v případě, že není připojen k žádnému okruhu. Před pokusem o odstranění filtru směrování se ujistěte, že není připojen k žádnému okruhu. Filtr tras můžete odstranit pomocí následujícího příkazu:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

Informace o ukázkách konfigurace směrovače najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Ukázky konfigurace směrovače pro nastavení a správu směrování](expressroute-config-samples-routing.md)
