---
title: Správa verzí rozhraní API pro rozhraní .NET a REST
titleSuffix: Azure Cognitive Search
description: Zásady verze pro rozhraní REST Azure Cognitive Search a klientskou knihovnu v sdk .NET.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 178f56354120bf7a65c51f1c9cf54e34bd011d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137284"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Verze rozhraní API v Azure Cognitive Search

Azure Cognitive Search pravidelně zavádí aktualizace funkcí. Někdy, ale ne vždy, tyto aktualizace vyžadují novou verzi rozhraní API zachovat zpětnou kompatibilitu. Publikování nové verze umožňuje řídit, kdy a jak integrovat aktualizace vyhledávací služby ve vašem kódu.

Tým Azure Cognitive Search zpravidla publikuje nové verze pouze v případě potřeby, protože může zahrnovat určité úsilí o upgrade kódu použít novou verzi rozhraní API. Nová verze je potřeba pouze v případě, že některé aspekty rozhraní API se změnilzpůsobem, který přeruší zpětnou kompatibilitu. K těmto změnám může dojít z důvodu oprav existujících funkcí nebo z důvodu nových funkcí, které mění existující plochu rozhraní API.

Stejné pravidlo platí pro aktualizace sady SDK. Sada Azure Cognitive Search SDK se řídí [sémantickými](https://semver.org/) pravidly správy verzí, což znamená, že jeho verze má tři části: hlavní, menší a číslo sestavení (například 1.1.0). Nová hlavní verze sady SDK je vydána pouze pro změny, které přeruší zpětnou kompatibilitu. Aktualizace nenarušujících funkcí zvýší dílčí verzi a opravy chyb pouze zvýší verzi sestavení.

> [!NOTE]
> Vaše instance služby Azure Cognitive Search podporuje několik verzí rozhraní REST API, včetně nejnovější. Verzi můžete nadále používat, pokud již není nejnovější, ale doporučujeme migrovat kód a používat nejnovější verzi. Při použití rozhraní REST API, musíte zadat verzi rozhraní API v každém požadavku prostřednictvím parametru api-version. Při použití sady .NET SDK určuje verze sady SDK, kterou používáte, odpovídající verzi rozhraní REST API. Pokud používáte starší sadu SDK, můžete pokračovat v spouštění tohoto kódu bez evidenčních změn, i když je služba upgradována na podporu novější verze rozhraní API.

## <a name="snapshot-of-current-versions"></a>Snímek aktuálních verzí
Níže je snímek aktuální verze všech programovacích rozhraní azure kognitivní vyhledávání.


| Rozhraní | Nejnovější hlavní verze | Status |
| --- | --- | --- |
| [Sada SDK rozhraní .NET](https://aka.ms/search-sdk) |9.0 |Obecně dostupné, vydáno v květnu 2019 |
| [Náhled sady .NET SDK](https://aka.ms/search-sdk-preview) |8.0-náhled |Náhled, vydáno v dubnu 2019 |
| [Rozhraní API služby REST](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Obecně dostupné |
| [Rozhraní REST API služby 2019-05-06-Preview](search-api-preview.md) |2019-05-06-Náhled |Preview |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Obecně dostupné |
| [Rozhraní REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/) |2020-03-13|Obecně dostupné |

Pro rest API, včetně `api-version` na každé volání je vyžadováno. Použití `api-version` usnadňuje cílit na konkrétní verzi, jako je například rozhraní API náhledu. Následující příklad ukazuje, `api-version` jak je parametr zadán:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Přestože každý `api-version`požadavek má , doporučujeme použít stejnou verzi pro všechny požadavky rozhraní API. To platí zejména při nové verze rozhraní API zavést atributy nebo operace, které nejsou rozpoznány předchozí verze. Míchání verzí rozhraní API může mít nezamýšlené důsledky a je třeba se jim vyhnout.
>
> Rozhraní REST API služby a rozhraní REST API služby jsou verzí nezávisle na sobě. Jakákoli podobnost v číslech verzí je náhodná.

Obecně dostupná (nebo GA) rozhraní API se můžou používat v produkčním prostředí a vztahují se na ně smlouvy o úrovni služeb Azure. Verze náhledu mají experimentální funkce, které nejsou vždy migrovány na verzi GA. **Důrazně doporučujeme, abyste se vyhnuli použití preview API v produkčních aplikacích.**

## <a name="update-to-the-latest-version-of-the-rest-api-by-october-15-2020"></a>Aktualizace na nejnovější verzi rozhraní REST API do 15.10.2020
Následující verze rozhraní REST pro Azure Cognitive Search budou vyřazené a už nebudou podporované od **2014-07-31-Preview** **15.** **2014-10-20-Preview** **2015-02-28-Preview** Kromě toho verze Azure Cognitive Search .NET SDK starší než **3.0.0-rc** bude také vyřazena, protože cílí na jednu z těchto verzí rozhraní REST API. Po tomto datu aplikace, které používají některou z zastaralé verze rozhraní REST API nebo Sady SDK již nebude fungovat a musí být inovovány. Stejně jako u každé změny tohoto typu, dáváme 12 měsíců předem, takže máte dostatek času na přizpůsobení.  Chcete-li pokračovat v používání Azure Cognitive Search, migrujte existující kód, který cílí [rozhraní REST API](search-api-migration.md) na rozhraní REST API verze [2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) nebo novější nebo .NET SDK na [verzi 3.0](search-dotnet-sdk-migration.md) nebo novější do října 15, 2020.  Máte-li jakékoli dotazy týkající se aktualizace na azuresearch_contact@microsoft.com nejnovější verzi, pošlete prosím poštu do května 15, 2020, abyste měli dostatek času na aktualizaci kódu.

## <a name="about-preview-and-generally-available-versions"></a>O verzi Preview a obecně dostupných verzích
Azure Cognitive Search vždy předběžné verze experimentální funkce prostřednictvím rozhraní REST API první, pak prostřednictvím předběžné verze .NET SDK.

Funkce náhledu jsou k dispozici pro testování a experimentování s cílem shromáždit zpětnou vazbu k návrhu a implementaci funkcí. Z tohoto důvodu se funkce náhledu mohou v průběhu času měnit, případně způsoby, které přeruší zpětnou kompatibilitu. To je na rozdíl od funkcí ve verzi GA, které jsou stabilní a nepravděpodobné, že se změní s výjimkou malých zpětně kompatibilních oprav a vylepšení. Také funkce náhledu se ne vždy převedou do verze GA.

Z těchto důvodů doporučujeme proti psaní produkčního kódu, který přebírá závislost na verzích preview. Pokud používáte starší verzi preview, doporučujeme migrovat na obecně dostupnou verzi (GA).

Pro .NET SDK: Pokyny pro migraci kódu lze nalézt na [upgrade .NET SDK](search-dotnet-sdk-migration-version-9.md).

Obecná dostupnost znamená, že Azure Cognitive Search je teď v rámci smlouvy o úrovni služeb (SLA). Smlouvu SLA najdete na adrese [Azure Cognitive Search Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
