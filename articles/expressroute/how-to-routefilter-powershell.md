---
title: 'Nastavit filtry směrování pro partnerský vztah Azure ExpressRoute Microsoftu: prostředí PowerShell | Microsoft Docs'
description: Tento článek popisuje, jak nastavit filtry tras pro Microsoft Peering pomocí prostředí PowerShell
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
ms.openlocfilehash: 6e767166ecf248aa0e7fc16dc21361394e03107d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Nastavit filtry směrování pro partnerský vztah Microsoftu: prostředí PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Filtry tras představují způsob, jak využívat podmnožinu podporované služby prostřednictvím partnerského vztahu Microsoftu. Kroky v tomto článku vám pomůžou nakonfigurovat a spravovat filtry tras pro okruhy ExpressRoute.

Dynamics 365 služeb a služeb Office 365 například Exchange Online, SharePoint Online a Skype pro firmy s Azure veřejné služby, jako je například úložiště a databáze SQL jsou přístupné prostřednictvím partnerského vztahu Microsoftu. Azure veřejné služby jsou vybrat na základě za oblast a nemůže být definovaný na veřejné služby. 

Když partnerský vztah Microsoftu je nakonfigurovaná na okruh ExpressRoute a je připojen filtr trasy, všech předpon, které jsou vybrány pro tyto služby jsou inzerované prostřednictvím relace protokolu BGP, které jsou vytvořeny. Hodnota komunity protokolu BGP je připojený k každých předpona k identifikaci služby, které nabízí prostřednictvím předponu. Seznam hodnot komunity protokolu BGP a služeb, jsou mapovány najdete v tématu [komunit protokolu BGP](expressroute-routing.md#bgp).

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
  - [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho povolený vaším poskytovatelem připojení, než budete pokračovat. Okruh ExpressRoute musí být ve stavu zřízený a povolený.
  - [Vytvoření partnerského vztahu Microsoftu](expressroute-circuit-peerings.md) Pokud budete spravovat přímo relaci protokolu BGP. Nebo váš poskytovatel připojení zřídit partnerský vztah Microsoftu pro váš okruh.

-  Musíte vytvořit a konfigurovat filtr trasy.
    - Identifikovat služby vám využívat prostřednictvím partnerského vztahu Microsoftu
    - Určení seznamu hodnot komunity protokolu BGP přidruženého ke službám
    - Vytvořit pravidlo, které umožňují seznamu předponu odpovídající hodnoty komunity protokolu BGP

-  Filtr trasy musí připojit k okruhu ExpressRoute.

## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace, ujistěte se, že splňují následující kritéria:

 - Nainstalujte nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace najdete v článku [Instalace a konfigurace Azure PowerShellu](/powershell/azure/install-azurerm-ps).

  > [!NOTE]
  > Stáhněte nejnovější verzi z Galerie prostředí PowerShell, nikoli pomocí Instalační služby. Instalační program aktuálně nepodporuje požadovaných rutin.
  > 

 - Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních](expressroute-workflows.md) před zahájením konfigurace.

 - Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve stavu zřízený a povolený.

 - Musíte mít active partnerský vztah Microsoftu. Postupujte podle pokynů v [vytvoření a úprava konfigurace partnerského vztahu](expressroute-circuit-peerings.md)

### <a name="log-in-to-your-azure-account"></a>Přihlaste se ke svému účtu Azure.

Před zahájením této konfigurace se musíte přihlásit ke svému účtu Azure. Tato rutina vás vyzve k zadání přihlašovacích údajů k vašemu účtu Azure. Po přihlášení se stáhne nastavení účtu, aby bylo dostupné v Azure PowerShellu.

Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzureRmAccount
```

Pokud máte více předplatných Azure, zkontrolujte předplatná pro daný účet.

```powershell
Get-AzureRmSubscription
```

Určete předplatné, které chcete použít.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="prefixes"></a>Krok 1: Získání seznamu předpon a hodnotami komunity protokolu BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Získat seznam hodnot komunity protokolu BGP

Chcete-li získat seznam hodnot komunity protokolu BGP souvisejících se službou přístupné prostřednictvím partnerského vztahu Microsoftu a seznam předpon, které jsou s nimi spojených použijte následující rutinu:

```powershell
Get-AzureRmBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Zkontrolujte seznam hodnot, které chcete použít

Zkontrolujte seznam hodnotami komunity protokolu BGP, které chcete použít ve filtru trasy. Jako příklad je hodnota komunity protokolu BGP pro služby Dynamics 365 12076:5040.

## <a name="filter"></a>Krok 2: Vytvoření filtr trasy a pravidlo filtru

Filtr trasy může mít pouze jedno pravidlo a pravidlo musí být typu 'Povolit'. Toto pravidlo může mít seznam hodnot komunity protokolu BGP s ním spojená.

### <a name="1-create-a-route-filter"></a>1. Vytvoření filtru trasy

Nejprve vytvořte filtr trasy. Příkaz 'New-AzureRmRouteFilter, pouze vytvoří prostředek filtru trasy. Po vytvoření prostředku, musí pak vytvořit pravidlo a připojte ji k objektu filtru trasy. Spusťte následující příkaz pro vytvoření filtru prostředku trasy:

```powershell
New-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Vytvořit pravidlo filtru

Sadu komunit protokolu BGP jako seznam oddělený čárkami, můžete zadat, jak je znázorněno v příkladu. Spusťte následující příkaz pro vytvoření nového pravidla:
 
```powershell
$rule = New-AzureRmRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList "12076:5010,12076:5040"
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Přidat pravidlo filtru trasy

Spusťte následující příkaz pro přidání tomuto pravidlu filtru do filtru tras:
 
```powershell
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

## <a name="attach"></a>Krok 3: Připojení filtr trasy k okruhu ExpressRoute

Spusťte následující příkaz pro připojení filtru trasy k okruhu ExpressRoute, za předpokladu, že máte jenom partnerského vztahu Microsoftu:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="tasks"></a>Běžné úlohy

### <a name="getproperties"></a>Chcete-li získat vlastnosti filtru trasy

Vlastnosti filtru trasy, použijte následující kroky:

1. Spusťte následující příkaz, který vám daný prostředek trasy filtru:

  ```powershell
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  ```
2. Získáte trasy pravidla filtru pro prostředek trasy filtru, tak, že spustíte následující příkaz:

  ```powershell
  $routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
  $rule = $routefilter.Rules[0]
  ```

### <a name="updateproperties"></a>Aktualizovat vlastnosti filtr trasy

Pokud trasa filtru je již připojen k okruhu, aktualizace do seznamu komunity protokolu BGP automaticky rozšířit příslušné předpony oznámení o inzerovaném programu změny prostřednictvím zavedených relací protokolu BGP. Můžete aktualizovat seznam komunity protokolu BGP filtru trasu pomocí následujícího příkazu:

```powershell
$routefilter = Get-AzureRmRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzureRmRouteFilter -RouteFilter $routefilter
```

### <a name="detach"></a>Odpojit filtr trasy z okruhu ExpressRoute

Jakmile trasy filtr je odpojená od okruhu ExpressRoute, jsou bez předpony inzerované prostřednictvím relace protokolu BGP. Můžete odpojit filtr trasy z okruhu ExpressRoute pomocí následujícího příkazu:
  
```powershell
$ckt.Peerings[0].RouteFilter = $null
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="delete"></a>Odstranění filtru trasy

Filtr trasy může odstranit pouze, pokud není připojený k žádné okruh. Zkontrolujte, že filtr trasy není připojen k žádné okruh před pokusem o jeho odstranění. Odstraněním trasy filtr, pomocí následujícího příkazu:

```powershell
Remove-AzureRmRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
