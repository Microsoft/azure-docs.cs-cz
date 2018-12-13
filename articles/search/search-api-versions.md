---
title: Správa verzí rozhraní API pro .NET SDK a rozhraní REST API – Azure Search
description: Zásada verze rozhraní REST API Azure Search a klientské knihovny v sadě .NET SDK.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 0cf5cac341cb36029c09ee2da5477323fac79cf5
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53311639"
---
# <a name="api-versions-in-azure-search"></a>Verze rozhraní API ve službě Azure Search
Služba Azure Search zavádění aktualizací funkcí pravidelně. Někdy, ale ne vždy tyto aktualizace vyžadují novou verzi rozhraní API pro zachování zpětné kompatibility. Publikování nové verze umožňuje řídit, kdy a jak integrovat aktualizace služby vyhledávání v kódu.

Zpravidla publikuje týmu Azure Search nové verze pouze v případě potřeby, protože to může zahrnovat některé úsilí k upgradu vašeho kódu na použití nové verze rozhraní API. Nová verze je nutný jenom v případě, že došlo ke změně některých aspektů rozhraní API tak, aby přeruší zpětné kompatibility. Tyto změny může dojít z důvodu opravy stávajících funkcí nebo z důvodu nových funkcí, které se mění stávající svrchní oblasti rozhraní API.

Stejné pravidlo platí i pro aktualizace sady SDK. Následuje SDK služby Azure Search [sémantické správy verzí](http://semver.org/) pravidla, která znamená, že jeho verze má tři části: hlavní verze, podverze a sestavení číslo (například 1.1.0). Pouze pro změny poškozující zpětné kompatibility vydání nové hlavní verze sady SDK. Non nejnovější aktualizace funkcí se zvýší podverze a opravy chyb pouze zvýší verzi sestavení.

> [!NOTE]
> Instance služby Azure Search podporuje několik verzí rozhraní REST API, včetně nejnovější. Můžete nadále používat verzi, pokud již není nejnovější, ale doporučujeme migrovat kód Refaktorovat pro použití na nejnovější verzi. Při použití rozhraní REST API, je nutné zadat verzi rozhraní API v každé žádosti prostřednictvím parametru verze api-version. Při použití sady .NET SDK, určuje verzi sady SDK, které používáte odpovídající verzi rozhraní REST API. Pokud používáte starší sada SDK, můžete nadále spouštět tento kód beze změny i v případě, že služba se upgraduje na novější verzi rozhraní API podporují.

## <a name="snapshot-of-current-versions"></a>Snímek aktuální verze
Níže je snímek aktuální verze všech programování rozhraní do služby Azure Search.

| Rozhraní | Nejnovější hlavní verzi | Status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |5.0 |Obecně dostupné, vydáno v dubnu 2018 |
| [.NET SDK ve verzi Preview](https://aka.ms/search-sdk-preview) |4.0.1-Preview |Ve verzi Preview, všeobecně dostupné. května 2017 |
| [Rozhraní API služby REST](https://docs.microsoft.com/rest/api/searchservice/) |2017-11-11 |Obecná dostupnost |
| [Služba REST API 2017-11-11-Preview](search-api-2017-11-11-preview.md) |2017-11-11-Preview |Preview |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |2.0 |Obecná dostupnost |
| [Rozhraní REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Obecná dostupnost |

Pro rozhraní REST API, včetně `api-version` při každém volání je povinný. Pomocí `api-version` umožňuje snadno cílit na konkrétní verzi, jako je ve verzi preview rozhraní API. Následující příklad ukazuje, jak `api-version` je zadán parametr:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> I když má každý požadavek `api-version`, doporučujeme používat stejnou verzi pro všechny požadavky rozhraní API. To platí zejména při zavedení nových verzích rozhraní API atributů a operací, které nejsou rozpoznány v předchozích verzích. Kombinace verze rozhraní API může mít nežádoucí důsledky a mělo by se vyhnout.
>
> Rozhraní REST API služby a rozhraní REST API pro správu se systémovou správou verzí nezávisle na sobě. Každá podobnost v číslech verzí je náhodný.

Obecná dostupnost (nebo GA) rozhraní API můžete použít v produkčním prostředí a jsou v souladu s smluv o úrovni služeb Azure. Verze Preview mají seznámit s experimentálními funkcemi, které nejsou vždy migrovat na verzi všeobecné dostupnosti. **Se důrazně doporučuje, abyste se vyhnuli použití ve verzi preview rozhraní API v aplikacích v produkčním prostředí.**

## <a name="about-preview-and-generally-available-versions"></a>O verzích Preview a obecně dostupná
Služba Azure Search vždy předem uvolní seznámit s experimentálními funkcemi přes rozhraní REST API nejprve, pak prostřednictvím předběžné verze sady .NET SDK.

Funkce ve verzi Preview jsou k dispozici pro testování a experimentování, s cílem shromažďování zpětné vazby na funkce návrhu a implementaci. Z tohoto důvodu se můžou časem, případně takovým způsobem, který zpětně porušit kompatibilitu změnit funkce ve verzi preview. To se liší od funkce ve verzi GA, které jsou stabilní a pravděpodobně nebudou měnit s výjimkou malé zpětně kompatibilní s opravami a vylepšeními. Kromě toho funkce ve verzi preview vždy neukládejte ji do verze GA.

Z těchto důvodů nedoporučujeme psaní kódu produkčního prostředí, který je závislá na verze preview. Pokud používáte starší verzi preview, doporučujeme migrovat na verzi všeobecně dostupná (GA).

Pro sadu .NET SDK: Pokyny k migraci kódu lze nalézt v [Upgrade sady .NET SDK](search-dotnet-sdk-migration.md).

Obecná dostupnost znamená, že Azure Search je teď v části smlouva o úrovni služeb (SLA). Smlouvy SLA najdete na [smlouvy o úrovni služeb Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
