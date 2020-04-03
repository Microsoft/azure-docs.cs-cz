---
title: 'ExpressRoute: Filtry tras – partnerský vztah Microsoftu:Azure PowerShell'
description: Tento článek popisuje konfiguraci filtrů směrování pro partnerský vztah Microsoft pomocí prostředí PowerShell
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 3fa53258321b22e1683122edca1816f6d4c291b5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618603"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Konfigurace filtrů tras pro partnerský vztah Microsoftu: PowerShell
> [!div class="op_single_selector"]
> * [Portál Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Filtry tras představují způsob, jak spotřebovat dílčí sadu podporovaných služeb přes partnerský vztah Microsoftu. Postup v tomto článku vám pomůže nakonfigurovat a spravovat filtry tras pro okruhy ExpressRoute.

Služby Office 365, jako je Exchange Online, SharePoint Online a Skype pro firmy, a veřejné služby Azure, jako je úložiště a SQL DB, jsou přístupné prostřednictvím partnerského vztahu Microsoftu. Veřejné služby Azure jsou volitelné pro jednu oblast a nelze je definovat podle veřejné služby.

Pokud je partnerský vztah společnosti Microsoft nakonfigurován v okruhu ExpressRoute a je připojen filtr postupu, jsou všechny předpony vybrané pro tyto služby inzerovány prostřednictvím vytvořených relací Protokolu BGP. Ke každé předponě je připojená hodnota komunity protokolu BGP, která identifikuje službu nabízenou prostřednictvím dané předpony. Seznam hodnot komunity Protokolu BGP a služeb, na které mapují, naleznete v [tématu komunity Protokolu BGP](expressroute-routing.md#bgp).

Pokud požadujete připojení ke všem službám, velký počet předpon jsou inzerovány prostřednictvím protokolu BGP. Tím se výrazně zvětší velikost směrovacích tabulek udržovaných směrovači v síti. Pokud plánujete využívat pouze podmnožinu služeb nabízených prostřednictvím partnerského vztahu Microsoftu, můžete zmenšit velikost směrovacích tabulek dvěma způsoby. Můžete:

- Odfiltrujte nežádoucí předpony použitím filtrů tras v komunitách Protokolu BGP. Jedná se o standardní síťové postupy a běžně se používá v mnoha sítích.

- Definujte filtry tras a aplikujte je na okruh ExpressRoute. Filtr trasy je nový prostředek, který umožňuje vybrat seznam služeb, které chcete využívat prostřednictvím partnerského vztahu Microsoftu. Směrovače ExpressRoute odesílají pouze seznam předpon, které patří ke službám uvedeným ve filtru trasy.

### <a name="about-route-filters"></a><a name="about"></a>Filtry tras

Když je partnerský vztah Microsoftu nakonfigurován v okruhu ExpressRoute, směrovače hraničnísítě Společnosti Microsoft vytvoří dvojici relací Protokolu BGP s hraničními směrovači (vy nebo poskytovatele připojení). Do vaší sítě se žádné trasy neinzerují. Pokud chcete povolit inzerování tras do vaší sítě, je potřeba k ní přidružit filtr tras.

Filtr tras umožňuje identifikovat služby, které chcete využívat prostřednictvím partnerského vztahu Microsoftu s vaším okruhem ExpressRoute. Jedná se v podstatě o seznam všech hodnot komunity BGP. Po definování prostředku filtru tras a jeho připojení k okruhu ExpressRoute se do vaší sítě budou inzerovat všechny předpony, které se mapují na hodnoty komunity protokolu BGP.

Abyste k nim mohli připojit filtry tras se službami Office 365, musíte mít oprávnění využívat služby Office 365 prostřednictvím ExpressRoute. Pokud nemáte oprávnění využívat služby Office 365 prostřednictvím ExpressRoute, operace připojení filtrů postupu se nezdaří. Další informace o procesu autorizace najdete v [tématu Azure ExpressRoute pro Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Microsoft peering obvodů ExpressRoute, které byly nakonfigurovány před srpnem 1, 2017 bude mít všechny předpony služby inzerované prostřednictvím partnerského vztahu Microsoftu, i když nejsou definovány filtry tras. Microsoft peering obvodů ExpressRoute, které jsou nakonfigurovány na nebo po srpnu 1, 2017 nebude mít žádné předpony inzerované, dokud není připojen filtr trasy k okruhu.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Pracovního postupu

Chcete-li se úspěšně připojit ke službám prostřednictvím partnerského vztahu společnosti Microsoft, musíte provést následující kroky konfigurace:

- Musíte mít aktivní okruh ExpressRoute, který má zřízené partnerský vztah Microsoftu. K provedení těchto úkolů můžete použít následující pokyny:
  - Než budete pokračovat, [vytvořte okruh ExpressRoute](expressroute-howto-circuit-arm.md) a před pokračováním jej povolit váš poskytovatel připojení. Okruh ExpressRoute musí být ve zřízeném a povoleném stavu.
  - Pokud přímo spravujete relaci protokolu BGP, [vytvořte partnerský vztah microsoftu.](expressroute-circuit-peerings.md) Nebo mají váš poskytovatel připojení zřízení partnerský vztah Microsoft pro váš okruh.

-  Je nutné vytvořit a nakonfigurovat filtr trasy.
    - Identifikujte služby, které využíváte ke spotřebě prostřednictvím partnerského vztahu Microsoftu
    - Určení seznamu hodnot komunity Protokolu BGP přidružených ke službám
    - Vytvoření pravidla umožňujícího, aby seznam předponami odpovídal hodnotám komunity Protokolu BGP

-  Filtr trasy je nutné připojit k okruhu ExpressRoute.

## <a name="before-you-begin"></a>Než začnete

Než začnete s konfigurací, ujistěte se, že splňujete následující kritéria:

 - Před zahájením konfigurace zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovní postupy.](expressroute-workflows.md)

 - Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve zřízeném a povoleném stavu.

 - Musíte mít aktivní partnerský vztah Microsoftu. Postupujte podle pokynů v článku [Vytvořit a upravit konfigurací partnerského vztahu.](expressroute-circuit-peerings.md)


### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShellem

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Přihlaste se ke svému účtu Azure.

Před zahájením této konfigurace se musíte přihlásit ke svému účtu Azure. Tato rutina vás vyzve k zadání přihlašovacích údajů k vašemu účtu Azure. Po přihlášení se stáhne nastavení účtu, aby bylo dostupné v Azure PowerShellu.

Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Použijte následující příklad, který vám pomůže se připojit. Pokud používáte Azure Cloud Shell, nemusíte spouštět tuto rutinu, protože budete automaticky přihlášeni.

```azurepowershell
Connect-AzAccount
```

Pokud máte více předplatných Azure, zkontrolujte předplatná pro daný účet.

```azurepowershell-interactive
Get-AzSubscription
```

Určete předplatné, které chcete použít.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Krok 1: Získání seznamu předpon a hodnot komunity Protokolu BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Získejte seznam hodnot komunity Protokolu BGP

Pomocí následující rutiny získáte seznam hodnot komunity Protokolu BGP přidružených ke službám přístupným prostřednictvím partnerského vztahu Microsoftu a seznam předpon, které jsou s nimi spojené:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Vytvořte seznam hodnot, které chcete použít

Vytvořte seznam hodnot komunity Protokolu BGP, které chcete použít ve filtru trasy.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Krok 2: Vytvoření filtru trasy a pravidla filtru

Filtr trasy může mít pouze jedno pravidlo a pravidlo musí být typu Povolit. Toto pravidlo může mít seznam hodnot komunity Protokolu BGP s ním spojené.

### <a name="1-create-a-route-filter"></a>1. Vytvoření filtru trasy

Nejprve vytvořte filtr trasy. Příkaz New-AzRouteFilter pouze vytvoří prostředek filtru trasy. Po vytvoření zdroje je nutné vytvořit pravidlo a připojit jej k objektu filtru trasy. Chcete-li vytvořit prostředek filtru trasy, spusťte následující příkaz:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Vytvoření pravidla filtru

Můžete určit sadu komunit Protokolu BGP jako seznam oddělený čárkami, jak je znázorněno v příkladu. Chcete-li vytvořit nové pravidlo, spusťte následující příkaz:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Přidání pravidla do filtru trasy

Chcete-li do filtru trasy přidat pravidlo filtru, spusťte následující příkaz:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Krok 3: Připojení filtru trasy k okruhu ExpressRoute

Spusťte následující příkaz pro připojení filtru trasy k okruhu ExpressRoute za předpokladu, že máte pouze partnerský vztah microsoftu:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Běžné úkoly

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Získání vlastností filtru trasy

Chcete-li získat vlastnosti filtru trasy, postupujte takto:

1. Chcete-li získat prostředek filtru trasy, spusťte následující příkaz:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Pomocí následujícího příkazu získáte pravidla filtru trasy pro prostředek filtru trasy:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Aktualizace vlastností filtru trasy

Pokud je filtr trasy již připojen k okruhu, aktualizace seznamu komunity Protokolu BGP automaticky rozšíří příslušné změny reklamy předpony prostřednictvím zavedených relací protokolu BGP. Seznam komunity protokolu BGP filtru trasy můžete aktualizovat pomocí následujícího příkazu:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Odpojení filtru trasy od okruhu ExpressRoute

Jakmile je filtr trasy odpojen od okruhu ExpressRoute, nejsou prostřednictvím relace Protokolu BGP inzerovány žádné předpony. Filtr trasy můžete odpojit od okruhu ExpressRoute pomocí následujícího příkazu:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Odstranění filtru trasy

Filtr trasy lze odstranit pouze v případě, že není připojen k žádnému okruhu. Před pokusem o jeho odstranění se ujistěte, že filtr trasy není připojen k žádnému okruhu. Filtr trasy můžete odstranit pomocí následujícího příkazu:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Další kroky

Další informace o expressroute naleznete v [nejčastějších dotazech k expressroute](expressroute-faqs.md).
