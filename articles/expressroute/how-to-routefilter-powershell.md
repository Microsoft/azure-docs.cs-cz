---
title: 'Konfigurace filtrů směrování pro partnerský vztah Microsoftu – ExpressRoute: PowerShell: Azure | Dokumentace Microsoftu'
description: Tento článek popisuje postup konfigurace filtrů směrování pro Microsoft Peering pomocí Powershellu
documentationcenter: na
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: fc2cfcce57ad15d2bbad3242351492e184e7fd33
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415292"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Konfigurace filtrů směrování pro partnerský vztah Microsoftu: PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Filtry tras představují způsob, jak spotřebovat dílčí sadu podporovaných služeb přes partnerský vztah Microsoftu. Kroky v tomto článku vám pomůžou nakonfigurovat a spravovat filtrů směrování pro okruhy ExpressRoute.

Služby Dynamics 365 a službám Office 365 Exchange Online, SharePoint Online a Skype pro firmy a veřejné služby Azure, jako jsou úložiště a SQL DB jsou přístupné prostřednictvím partnerského vztahu Microsoftu. Veřejné služby Azure jsou volitelné na základě podle oblasti a nemůže být definovaná na veřejné služby.

Když se filtr tras připojuje partnerského vztahu Microsoftu je nakonfigurovaná na okruh ExpressRoute, jsou všechny předpony, které jsou vybrány pro tyto služby inzerované prostřednictvím relace protokolu BGP, které jsou vytvořeny. Ke každé předponě je připojená hodnota komunity protokolu BGP, která identifikuje službu nabízenou prostřednictvím dané předpony. Tyto hodnoty komunity protokolu BGP a služeb, které jsou mapovány seznam najdete v tématu [komunit protokolu BGP](expressroute-routing.md#bgp).

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
  - [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho povolený podle svého poskytovatele připojení, než budete pokračovat. Okruh ExpressRoute musí být ve stavu zřízený a povolený.
  - [Vytvoření partnerského vztahu Microsoftu](expressroute-circuit-peerings.md) při správě přímo relace protokolu BGP. Nebo váš poskytovatel připojení zřízení partnerského vztahu Microsoftu pro váš okruh.

-  Musíte vytvořit a konfigurovat filtr tras.
    - Určete služby vám využívat prostřednictvím partnerského vztahu Microsoftu
    - Určení seznamu přidruženého ke službám hodnotami komunity protokolu BGP
    - Vytvořit pravidlo povolující seznam předpon odpovídající tyto hodnoty komunity protokolu BGP

-  Je nutné připojit filtr tras k okruhu ExpressRoute.

## <a name="before-you-begin"></a>Před zahájením

Než začnete s konfigurací, ujistěte se, že splňujete následující kritéria:

 - Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních postupů](expressroute-workflows.md) předtím, než začnete s konfigurací.

 - Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve stavu zřízený a povolený.

 - Musíte mít active partnerský vztah Microsoftu. Postupujte podle pokynů [vytvoření a změny konfigurace partnerského vztahu](expressroute-circuit-peerings.md) článku.


### <a name="working-with-azure-powershell"></a>Práce s využitím Azure Powershellu
[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Přihlaste se ke svému účtu Azure.

Před zahájením této konfigurace se musíte přihlásit ke svému účtu Azure. Tato rutina vás vyzve k zadání přihlašovacích údajů k vašemu účtu Azure. Po přihlášení se stáhne nastavení účtu, aby bylo dostupné v Azure PowerShellu.

Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Použijte následující příklad připojení vám usnadní. Pokud používáte Azure Cloud Shell, není nutné ke spuštění této rutiny, jak vám bude automaticky přihlášeni.

```azurepowershell
Connect-AzureRmAccount
```

Pokud máte více předplatných Azure, zkontrolujte předplatná pro daný účet.

```azurepowershell-interactive
Get-AzureRmSubscription
```

Určete předplatné, které chcete použít.

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="prefixes"></a>Krok 1: Získat seznam předpon a hodnotami komunity protokolu BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Získání seznamu sad hodnotami komunity protokolu BGP

Chcete-li získat seznam hodnot komunity protokolu BGP přidružené služby přístupné prostřednictvím partnerského vztahu Microsoftu a seznam předpon, které jsou k nim má přiřazené použijte následující rutinu:

```azurepowershell-interactive
Get-AzureRmBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Vytvořte seznam hodnot, které chcete použít

Zkontrolujte seznam hodnotami komunity protokolu BGP, které chcete použít ve filtru tras. Jako příklad je hodnota komunity protokolu BGP pro služby Dynamics 365 12076:5040.

## <a name="filter"></a>Krok 2: Vytvořit filtr tras a pravidlo filtru

Filtr tras může mít jenom jedno pravidlo a pravidlo musí být typu "Povolit". Toto pravidlo může mít seznam hodnot komunity protokolu BGP s ním spojená.

### <a name="1-create-a-route-filter"></a>1. Vytvořit filtr tras

Nejprve vytvořte filtr tras. Příkaz "New-AzureRmRouteFilter" pouze vytvoří prostředek filtr trasy. Po vytvoření prostředku, musí pak vytvořte pravidlo a připojení k objektu filtru trasy. Spuštěním následujícího příkazu vytvořte prostředek filtr trasy:

```azurepowershell-interactive
New-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Vytvořit pravidlo filtru

Sady komunit protokolu BGP jako seznam oddělený čárkami, můžete určit, jak je znázorněno v příkladu. Spuštěním následujícího příkazu vytvořte nové pravidlo:
 
```azurepowershell-interactive
$rule = New-AzureRmRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList "12076:5010,12076:5040"
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Přidat pravidlo filtru tras

Spusťte následující příkaz pro přidání do filtru tras tomuto pravidlu filtru:
 
```azurepowershell-interactive
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

## <a name="attach"></a>Krok 3: Připojit filtr tras k okruhu ExpressRoute

Spusťte následující příkaz připojit filtr tras k okruhu ExpressRoute, za předpokladu, že máte jenom partnerského vztahu Microsoftu:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="tasks"></a>Běžné úlohy

### <a name="getproperties"></a>Chcete-li získat vlastnosti filtr tras

Pokud chcete získat vlastnosti filtru tras, postupujte následovně:

1. Spuštěním následujícího příkazu získejte filtr prostředek trasy:

  ```azurepowershell-interactive
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  ```
2. Získáte trasu pravidla filtru pro prostředek filtr tras, spuštěním následujícího příkazu:

  ```azurepowershell-interactive
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  $rule = $routefilter.Rules[0]
  ```

### <a name="updateproperties"></a>Aktualizovat vlastnosti filtr tras

Pokud se filtr tras je již připojen k okruhu, aktualizace seznamu komunity protokolu BGP automaticky šířící změny oznámení o inzerovaném programu odpovídající předpona prostřednictvím zavedených relací protokolu BGP. Můžete aktualizovat seznam komunity protokolu BGP vašeho filtru tras pomocí následujícího příkazu:

```azurepowershell-interactive
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

### <a name="detach"></a>Chcete-li odpojit filtr tras z okruhu ExpressRoute

Jakmile se filtr tras se odpojit od okruhu ExpressRoute, jsou bez předpony inzerované prostřednictvím relace protokolu BGP. Filtr tras z okruhu ExpressRoute pomocí následujícího příkazu můžete odpojit:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="delete"></a>Odstranit filtr tras

Pokud není připojen k žádné okruhu můžete ho jenom odstranit filtr tras. Ujistěte se, že filtr tras není připojen k žádné okruh před pokusem o jeho odstranění. Můžete odstranit filtr tras pomocí následujícího příkazu:

```azurepowershell-interactive
Remove-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
