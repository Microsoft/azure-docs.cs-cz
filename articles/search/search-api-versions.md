---
title: Verze rozhraní API Azure Search | Microsoft Docs
description: Zásady verze rozhraní API REST služby Azure Search a klientské knihovny sady .NET SDK.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 3235813708b3c097e7f66610796f75eafd06aa05
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="api-versions-in-azure-search"></a>Verze rozhraní API ve službě Azure Search
Služba Azure Search zavede funkce aktualizace pravidelně. Někdy, ale ne vždy vyžadují tyto aktualizace nám publikovat novou verzi našem rozhraní API pro zachování zpětné kompatibility. Publikování nové verze, můžete řídit, kdy a jak integrovat aktualizace služby search v kódu.

Platí pravidlo pokusíme se publikování nové verze pouze v případě potřeby, protože se může týkat některé úsilí k upgradu kódu pro použití nové verze rozhraní API. Nová verze jsme bude publikovat, pouze pokud je potřeba změnit některých aspektů rozhraní API tak, aby dělí zpětné kompatibility. Tomu může dojít z důvodu opravy stávajících funkcí, nebo z důvodu nové funkce, které změnit stávající útoku na rozhraní API.

Jsme podle stejného pravidla pro aktualizace sady SDK. Následuje SDK služby Azure Search [sémantické verze](http://semver.org/) pravidla, která znamená, že jeho verze má tři části: hlavní, malé a číslo (například 1.1.0) sestavení. Vydáváme nové hlavní verzi sady SDK jenom v případě změny, které rozdělit zpětné kompatibility. Pro pevné funkce aktualizace jsme se zvýší na podverzi a pro oprav chyb jsme zvýší pouze verzi sestavení.

> [!NOTE]
> Instanci služby Azure Search podporuje několik verzí rozhraní REST API, včetně nejnovější. Můžete použít verzi, když je už nejnovějšího, ale doporučujeme migraci kód a použít nejnovější verzi. Při použití rozhraní REST API, musíte zadat verze rozhraní API v každé žádosti o prostřednictvím parametr api-version. Když pomocí sady .NET SDK, určuje verzi sady SDK používáte odpovídající verzi rozhraní REST API. Pokud používáte starší SDK, můžete spustit tento kód se žádné změny, i v případě, že je služba upgradovat pro podporu na novější verzi rozhraní API.

## <a name="snapshot-of-current-versions"></a>Snímek aktuální verze
Níže je snímek aktuální verze všech programovací rozhraní do služby Azure Search.

| Rozhraní | Nejnovější hlavní verzi | Status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |5.0 |Obecně k dispozici, vydání duben 2018 |
| [.NET SDK Preview](https://aka.ms/search-sdk-preview) |4.0.1-Preview |Ve verzi Preview vydané může 2017 |
| [Rozhraní API služby REST](https://docs.microsoft.com/rest/api/searchservice/) |2017. 11 11 |Obecně k dispozici |
| [Verze Preview rozhraní API služby REST](search-api-2016-09-01-preview.md) |2016-09-01-preview |Preview |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |2.0 |Obecně k dispozici |
| [Rozhraní REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Obecně k dispozici |

Pro rozhraní REST API, včetně `api-version` každé volání je vyžadována. To usnadňuje zacílit na konkrétní verzi, jako je například preview rozhraní API. Následující příklad ukazuje, jak `api-version` je zadán parametr:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> I když má každý požadavek `api-version`, doporučujeme použít stejnou verzi pro všechny požadavky rozhraní API. To platí hlavně při zavádění nové verze rozhraní API atributů a operací, které nejsou rozpoznány v předchozích verzích. Kombinování verze rozhraní API může mít nežádoucích důsledků a je nutno.
>
> Rozhraní API služby REST a REST API pro správu jsou verzí nezávisle na sobě navzájem. Každá podobnost v číslech verzí je náhodný.

Obecně dostupné (nebo GA) rozhraní API lze použít v produkčním prostředí a podléhají smlouvy o úrovni služeb Azure. Verze Preview mají povolenými experimentálními funkcemi, které nejsou vždy migrovat do verze GA. **Důrazně nedoporučujeme používání preview rozhraní API v produkční aplikace.**

## <a name="about-preview-and-generally-available-versions"></a>O verze Preview a obecně k dispozici
Služba Azure Search vždy předem uvolní povolenými experimentálními funkcemi přes rozhraní REST API nejprve pak prostřednictvím předprodejní verze sady SDK pro .NET.

Funkce Preview se nezaručuje, že chcete migrovat do verze GA. Zatímco funkce verze GA jsou považovány za stabilní a pravděpodobně změnit s výjimkou malé zpětně kompatibilní opravy a vylepšení, jsou k dispozici pro testování a experimentování, s cílem shromažďování zpětné vazby na funkce návrhu a implementace funkce verze preview.

Nicméně protože funkce preview se mohou změnit, doporučujeme proti zápisu produkčním kódu, který přebírá závislost na verze preview. Pokud používáte starší verzi preview, doporučujeme migraci do všeobecně dostupná verze (GA).

Pro .NET SDK: pokyny pro migraci kódu najdete na [Upgrade .NET SDK](search-dotnet-sdk-migration.md).

Obecné dostupnosti znamená, že Azure Search je nyní v části smlouvu o úrovni služeb (SLA). Smlouva SLA naleznete na adrese [smlouvy o úrovni služeb Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
