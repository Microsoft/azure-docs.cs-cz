---
title: Skripty prostředí PowerShell pomocí modulu Az.Search
titleSuffix: Azure Cognitive Search
description: Vytvořte a nakonfigurujte službu Azure Cognitive Search pomocí PowerShellu. Službu můžete škálovat nahoru nebo dolů, spravovat klíče rozhraní api pro správce a dotaz a dotazovat se na systémové informace.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 711071e08a52a0075512bc8b3ffe14707238cdfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209292"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Správa služby Azure Cognitive Search pomocí PowerShellu
> [!div class="op_single_selector"]
> * [Portál](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [ROZHRANÍ API PRO ODPOČINEK](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [Sada SDK rozhraní .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Rutiny a skripty prostředí PowerShell můžete spouštět ve Windows, Linuxu nebo v [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) u vytváření a konfigurace Azure Cognitive Search. Modul **Az.Search** rozšiřuje [Azure PowerShell](https://docs.microsoft.com/powershell/) s plnou paritou na rozhraní REST pro [správu vyhledávání](https://docs.microsoft.com/rest/api/searchmanagement) a schopnost provádět následující úkoly:

> [!div class="checklist"]
> * [Seznam vyhledávacích služeb v předplatném](#list-search-services)
> * [Vrátit informace o službě](#get-search-service-information)
> * [Vytvoření nebo odstranění služby](#create-or-delete-a-service)
> * [Obnovit klíče rozhraní API správce](#regenerate-admin-keys)
> * [Vytvoření nebo odstranění klíčů api dotazu](#create-or-delete-query-keys)
> * [Škálování nahoru nebo dolů s replikami a oddíly](#scale-replicas-and-partitions)

V některých případě jsou kladeny otázky týkající se úkolů, které *nejsou* uvedeny ve výše uvedeném seznamu. V současné době nelze použít modul **Az.Search** nebo rozhraní REST API pro správu ke změně názvu serveru, oblasti nebo úrovně. Vyhrazené zdroje jsou přiděleny při vytvoření služby. Změna základního hardwaru (umístění nebo typu uzlu) tedy vyžaduje novou službu. Podobně neexistují žádné nástroje nebo api pro přenos obsahu, jako je například index, z jedné služby do druhé.

V rámci služby je vytváření a správa obsahu prostřednictvím [rozhraní REST API vyhledávací služby](https://docs.microsoft.com/rest/api/searchservice/) nebo sady [.NET SDK](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search). I když pro obsah neexistují žádné vyhrazené příkazy prostředí PowerShell, můžete zapisovat skript prostředí PowerShell, který volá rozhraní REST nebo rozhraní API .NET k vytvoření a načtení indexů.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Kontrola verzí a načítacích modulů

Příklady v tomto článku jsou interaktivní a vyžadují zvýšená oprávnění. Azure PowerShell (modul **Az)** musí být nainstalovaný. Další informace najdete [v tématu Instalace Azure PowerShellu](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>Kontrola verze prostředí PowerShell (5.1 nebo novější)

Místní prostředí PowerShell musí být 5.1 nebo novější v libovolném podporovaném operačním systému.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Načtení Azure PowerShellu

Pokud si nejste jisti, zda je **nainstalována aplikace Az,** spusťte jako krok ověření následující příkaz. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Některé systémy nenačítají moduly s automatickým načítáním. Pokud se u předchozího příkazu zobrazí chyba, zkuste modul načíst, a pokud se to nepodaří, vraťte se k pokynům k instalaci a zjistěte, zda jste nezmeškali krok.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Připojení k Azure pomocí přihlašovacího tokenu prohlížeče

Přihlašovací údaje k portálu můžete použít k připojení k předplatnému v PowerShellu. Případně můžete [ověřit neinteraktivně pomocí instančního objektu](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Pokud vlastníte více předplatných Azure, nastavte předplatné Azure. Chcete-li zobrazit seznam aktuálních předplatných, spusťte tento příkaz.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Chcete-li zadat odběr, spusťte následující příkaz. V následujícím příkladu je `ContosoSubscription`název předplatného .

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Seznam služeb v předplatném

Následující příkazy jsou od [**Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), vrácení informací o existujících prostředků a služeb, které již jsou zřízeny ve vašem předplatném. Pokud nevíte, kolik vyhledávacích služeb je již vytvořeno, tyto příkazy tyto informace vrátí a ušetří vám cestu na portál.

První příkaz vrátí všechny vyhledávací služby.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Ze seznamu služeb vraťte informace o konkrétním prostředku.

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

Příkazy z [**Az.Search**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) nejsou k dispozici, dokud nenačtete modul.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Vypsat všechny příkazy Az.Search

Jako ověřovací krok vraťte seznam příkazů uvedených v modulu.

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

## <a name="get-search-service-information"></a>Získat informace o vyhledávací službě

Po importu **az.Search** a znáte skupinu prostředků obsahující vyhledávací službu, spusťte [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) vrátit definici služby, včetně názvu, oblasti, vrstvy a repliky a oddílpočítá.

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

[**New-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) se používá k [vytvoření nové vyhledávací služby](search-create-service-portal.md).

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

## <a name="regenerate-admin-keys"></a>Obnovit klíče správce

[**New-AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) se používá k převrácení [klíče API](search-security-api-keys.md)správce . S každou službou pro ověřený přístup jsou vytvořeny dva klíče správce. Klíče jsou vyžadovány na každém požadavku. Oba klíče správce jsou funkčně ekvivalentní a poskytují úplný přístup pro zápis do vyhledávací služby s možností načíst jakékoli informace nebo vytvořit a odstranit libovolný objekt. Existují dva klíče, takže můžete použít jeden při výměně druhé. 

Můžete regenerovat pouze jeden po druhém, `primary` zadaný jako klíč nebo. `secondary` Pro nepřerušenou službu nezapomeňte aktualizovat veškerý kód klienta, aby používal sekundární klíč při přejíždění primárního klíče. Vyhněte se změně klíče, zatímco operace jsou v letu.

Jak můžete očekávat, pokud znovu vygenerujete klíče bez aktualizace kódu klienta, požadavky používající starý klíč se nezdaří. Obnovení všech nových klíčů není trvale zamknout mimo službu a stále můžete přistupovat ke službě prostřednictvím portálu. Po obnovení primárních a sekundárních klíčů můžete aktualizovat kód klienta tak, aby používal nové klíče a operace budou odpovídajícím způsobem pokračovat.

Hodnoty pro klíče rozhraní API jsou generovány službou. Nelze zadat vlastní klíč pro Azure Cognitive Search použít. Podobně neexistuje žádný uživatelem definovaný název pro klíče rozhraní API správce. Odkazy na klíč jsou pevné řetězce, `secondary`nebo `primary` . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Výsledky by měly vypadat podobně jako následující výstup. Oba klíče jsou vráceny, i když měníte pouze jeden po druhém.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Vytvoření nebo odstranění klíčů dotazu

[**New-AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) se používá k vytvoření [klíče rozhraní API](search-security-api-keys.md) dotazu pro přístup jen pro čtení z klientských aplikací do indexu Azure Cognitive Search. Klávesy dotazu se používají k ověření na konkrétní index za účelem načtení výsledků hledání. Klávesy dotazu neudělují přístup jen pro čtení k jiným položkám ve službě, jako je například index, zdroj dat nebo indexer.

Nelze zadat klíč pro Azure Cognitive Search použít. Klíče rozhraní API jsou generovány službou.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Škálování replik a oddílů

[**Set-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) se používá ke [zvýšení nebo snížení repliky a oddíly](search-capacity-planning.md) pro čitelné fakturovatelné prostředky v rámci služby. Zvýšení repliky nebo oddíly přidá k vaší vyúčtování, který má pevné i variabilní poplatky. Pokud máte dočasnou potřebu dalšího výpočetního výkonu, můžete zvýšit repliky a oddíly pro zpracování úlohy. Oblast monitorování na stránce portálu Přehled obsahuje dlaždice o latenci dotazů, dotazech za sekundu a omezení, které označují, zda je dostatečná aktuální kapacita.

Přidání nebo odebrání zdrojů může chvíli trvat. Úpravy kapacity dojít na pozadí, což umožňuje existující úlohy pokračovat. Další kapacita se používá pro příchozí požadavky, jakmile je připraven, bez nutnosti další konfigurace. 

Odstranění kapacity může být rušivé. Zastavení všech úloh indexování a indexeru před snížením kapacity se doporučuje, aby se zabránilo vynechání požadavků. Pokud to není možné, můžete zvážit snížení kapacity postupně, jednu repliku a oddíl najednou, dokud není dosaženo nové cílové úrovně.

Jakmile odešlete příkaz, neexistuje žádný způsob, jak jej ukončit v polovině. Budete muset počkat, až bude příkaz dokončen, než přepracujete počty.

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

## <a name="next-steps"></a>Další kroky

Vytvořte [index](search-what-is-an-index.md), [dotaz na index](search-query-overview.md) pomocí portálu, rozhraní REST API nebo sady .NET SDK.

* [Vytvoření indexu Azure Cognitive Search na webu Azure Portal](search-create-index-portal.md)
* [Nastavení indexeru pro načítání dat z jiných služeb](search-indexer-overview.md)
* [Dotaz na index Azure Cognitive Search pomocí průzkumníka vyhledávání na webu Azure Portal](search-explorer.md)
* [Jak používat Azure Cognitive Search v rozhraní .NET](search-howto-dotnet-sdk.md)