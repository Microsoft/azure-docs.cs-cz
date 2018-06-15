---
title: 'Nastavit filtry směrování pro partnerský vztah Azure ExpressRoute Microsoftu: rozhraní příkazového řádku | Microsoft Docs'
description: Tento článek popisuje, jak nastavit filtry tras pro aplikaci Microsoft Peering pomocí rozhraní příkazového řádku Azure
documentationcenter: na
services: expressroute
author: anzaman
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anzaman
ms.openlocfilehash: a85a68393f3dc946db651791de9efff0694f9989
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23850816"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Nastavit filtry směrování pro partnerský vztah Microsoftu: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Filtry tras představují způsob, jak využívat podmnožinu podporované služby prostřednictvím partnerského vztahu Microsoftu. Kroky v tomto článku vám pomůžou nakonfigurovat a spravovat filtry tras pro okruhy ExpressRoute.

Dynamics 365 služeb a služeb Office 365 například Exchange Online, SharePoint Online a Skype pro firmy, jsou přístupné prostřednictvím partnerského vztahu Microsoftu. Pokud partnerský vztah Microsoftu je nakonfigurován v okruhu ExpressRoute, jsou všechny předpony související s těmito službami inzerované prostřednictvím relace protokolu BGP, které jsou vytvořeny. Hodnota komunity protokolu BGP je připojený k každých předpona k identifikaci služby, které nabízí prostřednictvím předponu. Seznam hodnot komunity protokolu BGP a služeb, jsou mapovány najdete v tématu [komunit protokolu BGP](expressroute-routing.md#bgp).

Pokud budete potřebovat připojení ke všem službám, jsou velký počet předpon inzerovaných prostřednictvím protokolu BGP. Tím se výrazně zvyšuje velikost směrovací tabulky udržuje pomocí směrovačů v rámci vaší sítě. Pokud budete chtít využívat jenom podmnožinu službám nabízeným přes partnerský vztah Microsoftu, můžete snížit velikost vašeho směrovací tabulky dvěma způsoby. Můžete:

* Filtrovat nežádoucí předpony použitím filtry tras na komunit protokolu BGP. Tento postup standardní sítě a běžně používá v rámci mnoho sítí.

* Zadejte filtry tras a použít je pro váš okruh ExpressRoute. Filtr trasy je nový prostředek, který umožňuje vybrat seznam služeb, které budete chtít využívat prostřednictvím partnerského vztahu Microsoftu. ExpressRoute směrovače odeslat pouze seznam předpon, které patří na služby určené ve filtru trasy.

### <a name="about"></a>O filtrech směrování

Pokud partnerský vztah Microsoftu je nakonfigurován na váš okruh ExpressRoute, Microsoft hraniční směrovače vytvořte dvojici relací protokolu BGP s hraniční směrovače (váš nebo váš poskytovatel připojení). Žádné trasy inzerovány vaší sítě. Pokud chcete povolit inzerování trasy k síti, je nutné přidružit filtr trasy.

Filtr trasy umožňuje identifikoval služby, které chcete využívat prostřednictvím partnerského vztahu Microsoftu okruhu ExpressRoute. Je v podstatě prázdný seznam všech hodnot komunity protokolu BGP. Po filtru prostředek trasy definované a připojené k okruhu ExpressRoute, jsou všechny předpony, které jsou mapovány na hodnoty komunity protokolu BGP inzerovaný do vaší sítě.

Abyste mohli připojit filtry tras se službami Office 365 na ně, musí mít oprávnění pro využívání služeb Office 365 prostřednictvím ExpressRoute. Pokud nemáte oprávnění k využívání služeb Office 365 prostřednictvím ExpressRoute, operace připojit filtry tras se nezdaří. Další informace o procesu autorizačního najdete v tématu [Azure ExpressRoute pro Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Připojení ke službám Dynamics 365 nevyžaduje žádné předchozí autorizace.

> [!IMPORTANT]
> Okruhy ExpressRoute, které byly nakonfigurovány před 1. srpna 2017 partnerského vztahu Microsoftu bude mít všechny služby předpony inzerované prostřednictvím Microsoft partnerský vztah, i když nejsou definovány filtry tras. Okruhy ExpressRoute, které jsou nakonfigurované na nebo po 1 srpen 2017 partnerského vztahu Microsoftu nebude mít všechny předpony inzerované dokud trasy filtr je připojen k okruhu.
> 
> 

### <a name="workflow"></a>Pracovní postup

Abyste mohli úspěšně připojit ke službám prostřednictvím partnerského vztahu Microsoftu, musíte provést následující kroky konfigurace:

* Musí mít aktivní okruh ExpressRoute, který má Microsoft partnerský vztah zřízené. K těmto úkolům můžete použít následující pokyny:
  * [Vytvoření okruhu ExpressRoute](howto-circuit-cli.md) a mějte ho povolený vaším poskytovatelem připojení, než budete pokračovat. Okruh ExpressRoute musí být ve stavu zřízený a povolený.
  * [Vytvoření partnerského vztahu Microsoftu](howto-routing-cli.md) Pokud budete spravovat přímo relaci protokolu BGP. Nebo váš poskytovatel připojení zřídit partnerský vztah Microsoftu pro váš okruh.

* Musíte vytvořit a konfigurovat filtr trasy.
  * Identifikovat služby vám využívat prostřednictvím partnerského vztahu Microsoftu
  * Určení seznamu hodnot komunity protokolu BGP přidruženého ke službám
  * Vytvořit pravidlo, které umožňují seznamu předponu odpovídající hodnoty komunity protokolu BGP

* Filtr trasy musí připojit k okruhu ExpressRoute.

## <a name="before-you-begin"></a>Než začnete

Než začnete, nainstalujte si nejnovější verzi příkazů rozhraní příkazového řádku (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématech [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

* Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních](expressroute-workflows.md) před zahájením konfigurace.

* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](howto-circuit-cli.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve stavu zřízený a povolený.

* Musíte mít active partnerský vztah Microsoftu. Postupujte podle pokynů v [vytvoření a úprava konfigurace partnerského vztahu](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se k účtu Azure a vybrat své předplatné

Chcete-li zahájit konfiguraci, přihlaste se k účtu Azure. Pomůže vám pomůže s připojením v následujících příkladech:

```azurecli
az login
```

Zkontrolujte předplatná pro příslušný účet.

```azurecli
az account list
```

Vyberte předplatné, pro který chcete vytvořit okruh ExpressRoute.

```azurecli
az account set --subscription "<subscription ID>"
```

## <a name="prefixes"></a>Krok 1: Získání seznamu předpon a hodnotami komunity protokolu BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Získat seznam hodnot komunity protokolu BGP

Chcete-li získat seznam hodnot komunity protokolu BGP souvisejících se službou přístupné prostřednictvím partnerského vztahu Microsoftu a seznam předpon, které jsou s nimi spojených použijte následující rutinu:

```azurecli
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Zkontrolujte seznam hodnot, které chcete použít

Zkontrolujte seznam hodnotami komunity protokolu BGP, které chcete použít ve filtru trasy. Jako příklad je hodnota komunity protokolu BGP pro služby Dynamics 365 12076:5040.

## <a name="filter"></a>Krok 2: Vytvoření filtr trasy a pravidlo filtru

Filtr trasy může mít pouze jedno pravidlo a pravidlo musí být typu 'Povolit'. Toto pravidlo může mít seznam hodnot komunity protokolu BGP s ním spojená.

### <a name="1-create-a-route-filter"></a>1. Vytvoření filtru trasy

Nejprve vytvořte filtr trasy. Příkaz 'az vytvořit trasu filtr sítě, pouze vytvoří prostředek trasy filtru. Po vytvoření prostředku, musí pak vytvořit pravidlo a připojte ji k objektu filtru trasy. Spusťte následující příkaz pro vytvoření filtru prostředku trasy:

```azurecli
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Vytvořit pravidlo filtru

Spusťte následující příkaz pro vytvoření nového pravidla:
 
```azurecli
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>Krok 3: Připojení filtr trasy k okruhu ExpressRoute

Spusťte následující příkaz k okruhu ExpressRoute připojit filtr trasy:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>Běžné úlohy

### <a name="getproperties"></a>Chcete-li získat vlastnosti filtru trasy

Získat vlastnosti trasy filtru, použijte následující příkaz:

```azurecli
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>Aktualizovat vlastnosti filtr trasy

Pokud trasa filtru je již připojen k okruhu, aktualizace do seznamu komunity protokolu BGP automaticky rozšířit příslušné předpony oznámení o inzerovaném programu změny prostřednictvím zavedených relací protokolu BGP. Můžete aktualizovat seznam komunity protokolu BGP filtru trasu pomocí následujícího příkazu:

```azurecli
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>Odpojit filtr trasy z okruhu ExpressRoute

Jakmile trasy filtr je odpojená od okruhu ExpressRoute, jsou bez předpony inzerované prostřednictvím relace protokolu BGP. Můžete odpojit filtr trasy z okruhu ExpressRoute pomocí následujícího příkazu:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>Odstranění filtru trasy

Filtr trasy může odstranit pouze, pokud není připojený k žádné okruh. Zkontrolujte, že filtr trasy není připojen k žádné okruh před pokusem o jeho odstranění. Odstraněním trasy filtr, pomocí následujícího příkazu:

```azurecli
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
