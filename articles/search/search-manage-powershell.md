---
title: Skripty PowerShellu s použitím modulu AZ. Search
titleSuffix: Azure Cognitive Search
description: Vytvořte a nakonfigurujte službu Azure Kognitivní hledání pomocí prostředí PowerShell. Službu můžete škálovat směrem nahoru nebo dolů, spravovat správce a dotazovat klíče rozhraní API a dotazovat se na systémové informace.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: d7b672b7e2c3004eba4a38bd659965b7dee24db6
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422480"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Správa služby Azure Kognitivní hledání pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Rutiny a skripty prostředí PowerShell můžete spustit v systému Windows, Linux nebo v [Azure Cloud Shell](../cloud-shell/overview.md) a vytvořit a nakonfigurovat Azure kognitivní hledání. Modul **AZ. Search** rozšiřuje [Azure PowerShell](/powershell/) o úplnou paritu [rozhraní REST API pro správu vyhledávání](/rest/api/searchmanagement) a možnost provádět následující úlohy:

> [!div class="checklist"]
> * [Seznam služeb vyhledávání v předplatném](#list-search-services)
> * [Vrátit informace o službě](#get-search-service-information)
> * [Vytvoření nebo odstranění služby](#create-or-delete-a-service)
> * [Znovu vygenerovat klíče rozhraní API pro správu](#regenerate-admin-keys)
> * [Vytvoření nebo odstranění rozhraní API pro dotazování klíčů](#create-or-delete-query-keys)
> * [Horizontální navýšení nebo snížení kapacity díky replikám a oddílům](#scale-replicas-and-partitions)

V některých případech se otázky týkají úkolů, které *nejsou* uvedené na seznamu výše. V současné době nemůžete použít modul **AZ. Search** ani REST API správy ke změně názvu serveru, oblasti nebo vrstvy. Vyhrazené prostředky jsou přiděleny při vytvoření služby. Například změna základního hardwaru (umístění nebo typu uzlu) vyžaduje novou službu. Podobně nejsou k dispozici žádné nástroje ani rozhraní API pro přenos obsahu, jako je například index, z jedné služby do jiné.

V rámci služby je vytváření a Správa obsahu prostřednictvím [Search Service REST API](/rest/api/searchservice/) nebo [.NET SDK](/dotnet/api/overview/azure/search.documents-readme). I když pro obsah nejsou k dispozici žádné vyhrazené příkazy prostředí PowerShell, můžete napsat skript prostředí PowerShell, který volá rozhraní REST nebo rozhraní .NET API k vytváření a načítání indexů.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Kontrolovat verze a načíst moduly

Příklady v tomto článku jsou interaktivní a vyžadují zvýšená oprávnění. Azure PowerShell ( **AZ** Module) musí být nainstalované. Další informace najdete v tématu [instalace Azure PowerShell](/powershell/azure/).

### <a name="powershell-version-check-51-or-later"></a>Check verze PowerShellu (5,1 nebo novější)

V libovolném podporovaném operačním systému musí být místní PowerShell 5,1 nebo novější.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Načíst Azure PowerShell

Pokud si nejste jisti, jestli je nainstalovaný **AZ** , spusťte následující příkaz jako ověřovací krok. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Některé systémy nečtou moduly automaticky. Pokud se v předchozím příkazu zobrazí chyba, zkuste načíst modul a pokud se to nepovede, vraťte se k instalačním pokynům, abyste viděli, jestli jste krok nezmeškali.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Připojení k Azure pomocí přihlašovacího tokenu prohlížeče

K připojení k předplatnému v PowerShellu můžete použít přihlašovací údaje pro přihlášení k portálu. Případně můžete [bez interaktivně ověřit objekt služby](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Pokud máte více předplatných Azure, nastavte své předplatné Azure. Pokud chcete zobrazit seznam aktuálních předplatných, spusťte tento příkaz.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Chcete-li zadat odběr, spusťte následující příkaz. V následujícím příkladu je název předplatného `ContosoSubscription` .

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Seznam služeb v předplatném

Následující příkazy jsou z [**AZ. Resources**](/powershell/module/az.resources)a vracejí informace o stávajících prostředcích a službách, které jsou už ve vašem předplatném zřízené. Pokud si nejste jisti, kolik služeb vyhledávání již bylo vytvořeno, tyto příkazy tyto příkazy vrátí, a tím ušetříte cestu k portálu.

První příkaz vrátí všechny vyhledávací služby.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

V seznamu služeb vraťte informace o konkrétním prostředku.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Výsledky by měly vypadat podobně jako v následujícím výstupu.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Import AZ. Search

Příkazy z [**AZ. Search**](/powershell/module/az.search) nejsou k dispozici, dokud nenačtete modul.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Vypíše všechny příkazy AZ. Search.

V rámci ověřovacího kroku vrátíte seznam příkazů, které jsou k dispozici v modulu.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Výsledky by měly vypadat podobně jako v následujícím výstupu.

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

## <a name="get-search-service-information"></a>Získat informace o službě Search

Po naimportování **AZ. Search** , který znáte skupinu prostředků obsahující vaši vyhledávací službu, spusťte příkaz [Get-AzSearchService](/powershell/module/az.search/get-azsearchservice) , který vrátí definici služby, včetně názvu, oblasti, úrovně a počtu replik a oddílů.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Výsledky by měly vypadat podobně jako v následujícím výstupu.

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

[**New-AzSearchService**](/powershell/module/az.search/new-azsearchadminkey) se používá k [Vytvoření nové vyhledávací služby](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Výsledky by měly vypadat podobně jako v následujícím výstupu.

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

[**New-AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey) se používá k kumulativnímu navýšení [klíčů rozhraní API](search-security-api-keys.md)pro správu. Dva klíče správce se vytvoří s každou službou pro ověřený přístup. Klíče jsou požadovány při každém požadavku. Oba klíče správce jsou funkčně ekvivalentní, což poskytuje úplný přístup pro zápis do vyhledávací služby s možností načíst libovolné informace nebo vytvořit a odstranit libovolný objekt. Existují dva klíče, abyste je mohli použít při nahrazování druhé. 

V jednom okamžiku můžete znovu vygenerovat jenom jednu, zadanou buď jako `primary` klíč nebo `secondary` . U nepřerušované služby nezapomeňte aktualizovat veškerý kód klienta, aby používal sekundární klíč, a přitom přenášet primární klíč. Vyhněte se změnám klíčů, když jsou operace v letu.

V případě, že budete chtít znovu vygenerovat klíče bez aktualizace kódu klienta, požadavky, které používají starý klíč, nebudou úspěšné. Opětovné generování všech nových klíčů vám trvale nezamkne vaše služby a stále budete mít přístup ke službě prostřednictvím portálu. Po opětovném vygenerování primárního a sekundárního klíče můžete aktualizovat klientský kód tak, aby používal nové klíče a operace budou odpovídajícím způsobem pokračovat.

Služba generuje hodnoty pro klíče rozhraní API. Nemůžete zadat vlastní klíč pro použití Azure Kognitivní hledání. Podobně není k dispozici žádný uživatelsky definovaný název pro klíče rozhraní API pro správu. Odkazy na klíč jsou pevné řetězce, buď `primary` nebo `secondary` . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Výsledky by měly vypadat podobně jako v následujícím výstupu. Oba klíče jsou vráceny, i když pouze jednou změníte.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Vytvoření nebo odstranění klíčů dotazů

[**New-AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey) se používá k vytváření [klíčů rozhraní API](search-security-api-keys.md) pro dotazování pro přístup jen pro čtení z klientských aplikací do indexu služby Azure kognitivní hledání. Klíče dotazů se používají k ověření pro konkrétní index pro účely načítání výsledků hledání. Klíče dotazů neudělují přístup jen pro čtení k ostatním položkám ve službě, jako je index, zdroj dat nebo indexer.

Nemůžete poskytnout klíč pro použití Azure Kognitivní hledání. Služba vygeneruje klíče rozhraní API.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Škálování replik a oddílů

[**Set-AzSearchService**](/powershell/module/az.search/set-azsearchservice) se používá ke [zvýšení nebo snížení počtu replik a oddílů a](search-capacity-planning.md) k úpravě fakturovatelných prostředků v rámci služby. Zvýšením replik nebo oddílů se přidá do vašeho účtu, který má za pevnou i variabilní poplatky. Pokud máte dočasnou potřebu pro další výpočetní výkon, můžete zvýšit repliky a oddíly pro zpracování úloh. Oblast monitorování na stránce portálu přehledu obsahuje dlaždice pro latenci dotazů, dotazy za sekundu a omezování, které označují, jestli je aktuální kapacita adekvátní.

Přidání nebo odebrání rezdrojového může nějakou dobu trvat. Úpravy kapacity se vyskytují na pozadí, což umožňuje, aby existující úlohy pokračovaly. Další kapacita se používá pro příchozí požadavky, jakmile je připravená, a nevyžaduje žádnou další konfiguraci. 

Odebrání kapacity může být rušivé. Před snížením kapacity se doporučuje zastavit všechny úlohy indexování a indexerů, aby se předešlo vyřazeným žádostem. Pokud to není proveditelné, můžete zvážit snížení kapacity přírůstkově, jedné repliky a oddílu najednou, dokud nebudou dosaženy nové cílové úrovně.

Po odeslání příkazu neexistuje žádný způsob, jak ho ukončit. Než začnete s revizemi, budete muset počkat, než se příkaz dokončí.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Výsledky by měly vypadat podobně jako v následujícím výstupu.

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

## <a name="next-steps"></a>Další kroky

Sestavení [indexu](search-what-is-an-index.md), [dotazování indexu](search-query-overview.md) pomocí portálu, rozhraní REST API nebo sady .NET SDK.

* [Vytvoření indexu služby Azure Kognitivní hledání v Azure Portal](search-get-started-portal.md)
* [Nastavení indexeru pro načtení dat z jiných služeb](search-indexer-overview.md)
* [Dotazování indexu služby Azure Kognitivní hledání pomocí Průzkumníka služby Search v Azure Portal](search-explorer.md)
* [Jak používat Azure Kognitivní hledání v .NET](search-howto-dotnet-sdk.md)