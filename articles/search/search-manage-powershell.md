---
title: Skripty prostředí PowerShell použitím modulu Az.Search – Azure Search
description: Vytvoření a konfigurace služby Azure Search pomocí Powershellu. Můžete škálování služby směrem nahoru nebo dolů, spravovat správce a klíče dotazu api Key a dotaz na systémové informace.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: heidist
ms.openlocfilehash: 8f07468ccff4431e1afdf66aedc72599ddc0c25b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620593"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Správa služby Azure Search pomocí Powershellu
> [!div class="op_single_selector"]
> * [Azure Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Spuštěním rutin a skriptů Powershellu na Windows, Linux, nebo v [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) vytvoření a konfigurace Azure Search. **Az.Search** rozšiřuje modul Azure PowerShell] s úplnou paritu [Management REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchmanagement). Pomocí Azure Powershellu a **Az.Search**, můžete provádět následující úlohy:

> [!div class="checklist"]
> * [Seznam všech vyhledávací služby v rámci vašeho předplatného](#list-search-services)
> * [Získejte informace o konkrétní vyhledávací služba](#get-search-service-information)
> * [Vytvoření nebo odstranění služby](#create-or-delete-a-service)
> * [Znovu vygenerovat klíče API-Key správce](#regenerate-admin-keys)
> * [Vytvoření nebo odstranění klíče dotazu api Key](#create-or-delete-query-keys)
> * [Škálování služby zvýšením nebo snížením repliky a oddíly](#scale-replicas-and-partitions)

Chcete-li změnit název, oblast nebo vrstva služby nelze použít PowerShell. Vyhrazené prostředky se přidělují při vytváření služby. Změna základního hardwaru (typ umístění nebo uzel) vyžaduje novou službu. Neexistují žádné nástroje nebo rozhraní API pro přenos obsahu z jedné služby do jiného. Všechny správy obsahu, je prostřednictvím [REST](https://docs.microsoft.com/rest/api/searchservice/) nebo [.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) rozhraní API, a pokud chcete přesunout indexy, budete muset znovu vytvořit a načítat je znovu na novou službu. 

Když neexistují žádné vyhrazené příkazy prostředí PowerShell pro správu obsahu, můžete napsat skript Powershellu, který volá REST nebo .NET k vytváření a načítání indexů. **Az.Search** modulu sám o sobě neposkytuje tyto operace.

Další úlohy nejsou podporovány prostřednictvím Powershellu nebo jakékoli jiné rozhraní API (pouze portálu) patří:
+ [Připojit prostředek služeb cognitive services](cognitive-search-attach-cognitive-services.md) pro [AI obohacené indexování](cognitive-search-concept-intro.md). Služby cognitive Services je připojen k dovedností, nikoli předplatné nebo služby.
+ [Monitorování řešení doplňků](search-monitor-usage.md#add-on-monitoring-solutions) nebo [Analýza provozu vyhledávání](search-traffic-analytics.md) používá pro monitorování Azure Search.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Kontrola verze a moduly zatížení

Příklady v tomto článku jsou interaktivní a vyžaduje zvýšenou úroveň oprávnění. Prostředí Azure PowerShell ( **Az** modul) musí být nainstalována. Další informace najdete v tématu [instalace Azure Powershellu](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>Kontrola verze Powershellu (5.1 nebo novější)

Místní PowerShell musí být 5.1 nebo novější, pro všechny podporované operační systémy.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Načíst Azure PowerShell

Pokud si nejste jistí, jestli **Az** je nainstalovali, spusťte následující příkaz jako krok ověření. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Některé systémy to není automaticky načíst moduly. Pokud dojde k chybě v předchozí příkaz, zkuste načíst modul a pokud selže, vraťte se na pokyny k instalaci zobrazíte, pokud nějaký krok vynechali.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Připojení k Azure pomocí prohlížeče přihlašovací token

Pro připojení k předplatnému v prostředí PowerShell můžete použít portál přihlašovacími údaji. Případně můžete [neinteraktivnímu ověřování pomocí instančního objektu](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Pokud podržíte více předplatných Azure, nastavte své předplatné Azure. Chcete-li zobrazit seznam aktuálních předplatných. Spusťte tento příkaz.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Pokud chcete zadat předplatné, spusťte následující příkaz. V následujícím příkladu je název předplatného `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-all-azure-search-services-in-your-subscription"></a>Výpis všech služeb Azure Search ve vašem předplatném

Následující příkazy jsou z [ **Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), vrací informace o existujících prostředků a služeb, které jsou už zřízené v rámci vašeho předplatného. Pokud si nejste jisti, kolik vyhledávací služby budou vytvořeny již, tyto příkazy vrátí tyto informace ukládá výlet na portál.

První příkaz vrátí všechny vyhledávací služby.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Ze seznamu služeb vrací informace o konkrétní prostředek.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Výsledky by měly vypadat podobně jako následující výstup.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importovat Az.Search

Příkazy z [ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) nejsou k dispozici, dokud se načtení modulu.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Seznam všech příkazů Az.Search

Jako ověřovací krok vrátí seznam příkazů v modulu je k dispozici.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Výsledky by měly vypadat podobně jako následující výstup.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>Získat informace o službě search

Po **Az.Search** importu a skupinu prostředků obsahující vaše vyhledávací služba, spuštění, které znáte [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) vrátit definice služby, včetně názvu, oblast, vrstvy a repliky a počet oddílů.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Výsledky by měly vypadat podobně jako následující výstup.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Vytvoření nebo odstranění služby

[**Nové AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) se používá k [vytvořit novou vyhledávací službu](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Výsledky by měly vypadat podobně jako následující výstup.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>Znovu vygenerovat klíče správce

[**Nové AzSearchAdminKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) se používá k výměně správce [klíče rozhraní API](search-security-api-keys.md). Dva klíče správce se vytvoří s každou službu pro ověřený přístup. Klíče jsou povinné u každého požadavku. Oba klíče správce jsou funkčně ekvivalentní, poskytování úplný přístup pro zápis vyhledávací službě umožňuje načíst všechny informace, nebo vytvářet a odstraňovat libovolný objekt. Dva klíče existují, takže můžete použít jednu při nahrazování druhé. 

Můžete pouze vygenerovat znovu postupně, zadaný jako buď `primary` nebo `secondary` klíč. Bez přerušení služby nezapomeňte aktualizovat všechny kódu klienta používat sekundární klíč při vrácení přes primární klíč. Vyhněte se změna klíčů, zatímco probíhají operace.

Jak byste asi očekávali, je-li znovu vygenerovat klíče bez aktualizace kódu klienta, se nezdaří požadavků používá starý klíč. Obnovení všech nových klíčů není k zablokování trvale vaši službu a můžete i nadále přístup ke službě prostřednictvím portálu. Po opětovném vygenerování primárního a sekundárního klíče, můžete aktualizovat klientským kódem, aby používaly tyto nové klíče a operace bude pokračovat, odpovídajícím způsobem.

Hodnoty klíče rozhraní API jsou vygenerované službou. Nelze zadat vlastní klíč pro službu Azure Search k použití. Podobně není žádný uživatelem definovaný název pro správu klíčů rozhraní API. Vyřešené odkazy na klíči řetězce, buď `primary` nebo `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Výsledky by měly vypadat podobně jako následující výstup. Oba klíče jsou vráceny, i když můžete změnit pouze jeden po druhém.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Vytvoření nebo odstranění klíče dotazu

[**Nové AzSearchQueryKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) slouží k vytvoření dotazu [klíče rozhraní API](search-security-api-keys.md) pro přístup jen pro čtení z klientských aplikací do indexu Azure Search. Klíče dotazu se používají k ověření do konkrétního indexu pro účely načítání výsledků hledání. Klíče dotazu bez možnosti udělovat přístup k ostatním položkám na službě, například index, indexer nebo zdroj dat jen pro čtení.

Nelze zadat klíč pro použití Azure Search. Klíče rozhraní API jsou vygenerované službou.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Škálování repliky a oddíly

[**Set-AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) se používá k [zvětšit nebo zmenšit repliky a oddíly](search-capacity-planning.md) chcete přizpůsobit fakturovatelné prostředky v rámci vaší služby. Zvýšení repliky nebo oddíly přidá na váš účet, který má obě pevné a proměnlivé poplatky. Pokud máte dočasné potřebu další výpočetní výkon, můžete zvýšit repliky a oddíly, které ke zpracování úlohy. Monitorování oblasti na stránce Přehled portálu má dlaždice na latenci dotazů, dotazy za sekundu a omezení šířky pásma, určující, zda je aktuální kapacita odpovídající.

Může trvat nějakou přidat nebo odebrat prostředky. Úpravy kapacity probíhá na pozadí, takže stávající úlohy, abyste mohli pokračovat. Zvýšení kapacity se používá pro příchozí požadavky, jakmile to bude připravené, bez další nezbytné konfigurace. 

Odebrání kapacity může působit rušivě. Zamítnuté požadavky, aby se doporučuje zastavit všechny úlohy indexování a indexeru před zmenší kapacita. Pokud to není proveditelné, měli byste postupně zmenší kapacita jednu repliku a oddílu v době, dokud se dosáhne nové cílové úrovně.

Jakmile odešlete příkaz neexistuje žádný způsob, jak ji ukončete uprostřed prostřednictvím. Budete muset počkat dokud nebude dokončeno před revize počtů.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Výsledky by měly vypadat podobně jako následující výstup.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```


## <a name="next-steps"></a>Další postup

Sestavení [index](search-what-is-an-index.md), [dotazování indexu](search-query-overview.md) pomocí portálu, rozhraní REST API nebo .NET SDK.

* [Vytvoření indexu Azure Search na webu Azure Portal](search-create-index-portal.md)
* [Nastavte si indexer načítat data z jiných služeb](search-indexer-overview.md)
* [Dotazování indexu Azure Search pomocí Průzkumníka služby Search na webu Azure Portal](search-explorer.md)
* [Použití služby Azure Search v rozhraní .NET](search-howto-dotnet-sdk.md)