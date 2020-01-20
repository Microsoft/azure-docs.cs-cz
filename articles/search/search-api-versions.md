---
title: Správa verzí rozhraní API pro .NET a REST
titleSuffix: Azure Cognitive Search
description: Zásady verze pro rozhraní REST API služby Azure Kognitivní hledání a knihovnu klienta v sadě .NET SDK.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c2f774401f32ff02ced690173c418dafc4c789b5
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278813"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Verze rozhraní API v Azure Kognitivní hledání

Azure Kognitivní hledání pravidelně shrnuje aktualizace funkcí. V některých případech, ale ne vždy, vyžadují tyto aktualizace novou verzi rozhraní API, aby se zachovala zpětná kompatibilita. Publikování nové verze vám umožní řídit, kdy a jak integrovat aktualizace služby Search do kódu.

V rámci pravidla publikuje tým Azure Kognitivní hledání nové verze pouze v případě potřeby, protože může zahrnovat nějaké úsilí při upgradu kódu na použití nové verze rozhraní API. Nová verze je nutná pouze v případě, že se některé aspekty rozhraní API změnily způsobem, který zruší zpětnou kompatibilitu. Tyto změny mohou nastat kvůli opravám existujících funkcí nebo kvůli novým funkcím, které mění existující oblast rozhraní API.

Stejné pravidlo platí pro aktualizace sady SDK. Sada Azure Kognitivní hledání SDK dodržuje pravidla [sémantických verzí](https://semver.org/) , což znamená, že jeho verze má tři části: hlavní, vedlejší a číslo sestavení (například 1.1.0). Nová hlavní verze sady SDK se uvolní jenom pro změny, které přeruší zpětnou kompatibilitu. Nepodstatné aktualizace funkcí zvýší dílčí verzi a opravy chyb zvýší pouze verzi buildu.

> [!NOTE]
> Vaše instance služby Azure Kognitivní hledání podporuje několik verzí REST API, včetně nejnovějšího. Můžete i nadále používat verzi, pokud již není nejnovější, ale doporučujeme, abyste si kód migrovali na používání nejnovější verze. Při použití REST API musíte v každé žádosti zadat verzi rozhraní API přes parametr verze API. Pokud používáte sadu .NET SDK, verze sady SDK, kterou používáte, určí odpovídající verzi REST API. Pokud používáte starší sadu SDK, můžete pokračovat ve spuštění tohoto kódu bez změny, i když je služba upgradována na podporu novější verze rozhraní API.

## <a name="snapshot-of-current-versions"></a>Snímek aktuálních verzí
Níže je snímek aktuální verze všech programovacích rozhraní do Azure Kognitivní hledání.


| Rozhraní | Nejnovější hlavní verze | Stav |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |Obecně dostupné, vydání květen 2019 |
| [.NET SDK Preview](https://aka.ms/search-sdk-preview) |8,0 – Preview |Preview, vydáno v dubnu 2019 |
| [Rozhraní API služby REST](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Obecná dostupnost |
| [Služba REST API 2019-05-06 – Preview](search-api-preview.md) |2019-05-06 – Preview |Preview |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Obecná dostupnost |
| [Rozhraní REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Obecná dostupnost |

Pro rozhraní REST API, včetně `api-version` pro každé volání se vyžaduje. Použití `api-version` usnadňuje cílení na konkrétní verzi, jako je rozhraní API pro verzi Preview. Následující příklad ukazuje, jak je zadán parametr `api-version`:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> I když každý požadavek má `api-version`, doporučujeme, abyste pro všechny požadavky rozhraní API používali stejnou verzi. To platí hlavně v případě, že nové verze rozhraní API zavádějí atributy nebo operace, které nejsou rozpoznávány předchozími verzemi. Kombinování verzí rozhraní API může mít nezamýšlené důsledky a je třeba se jim vyhnout.
>
> REST API REST API služby a správy jsou samostatně nezávislé na verzi. Jakákoli podobnost v číslech verzí je koincident.

Obecně dostupná rozhraní API (nebo GA) se dají používat v produkčním prostředí a podléhají smlouvám o úrovni služeb Azure. Verze Preview mají experimentální funkce, které nejsou vždycky migrovány na verzi GA. **Důrazně se doporučuje vyhnout se používání rozhraní API ve verzi Preview v produkčních aplikacích.**

## <a name="update-to-the-latest-version-of-the-rest-api-by-october-15-2020"></a>Aktualizace na nejnovější verzi REST API do 15. října 2020
Následující verze služby Azure Kognitivní hledání REST API budou vyřazeny a už se nepodporují od 15. října 2020: **2014-07-31-Preview**, **2014-10-20-** Preview, **2015-02-28-Preview**a **2015-02-28**. Verze sady Azure Kognitivní hledání .NET SDK starší než **3.0.0-RC** navíc budou také vyřazeny, protože cílí na jednu z těchto REST API verzí. Po tomto datu nebudou aplikace používající některou z zastaralých REST API nebo verzí SDK nadále fungovat a musí být upgradovány. Stejně jako u jakékoli změny tohoto typu se vám pošle 12 měsíců oznámení, takže budete mít adekvátní čas na jeho úpravu.  Pokud chcete dál používat Azure Kognitivní hledání, migrujte prosím stávající kód, který cílí na [REST API](search-api-migration.md) na [verzi REST API verze 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) nebo novější, nebo .net SDK na [verzi 3,0](search-dotnet-sdk-migration.md) nebo novější do 15. října 2020.  Pokud máte nějaké dotazy týkající se aktualizace na nejnovější verzi, pošlete e-mail na azuresearch_contact@microsoft.com od 15. května 2020 a ujistěte se, že máte dostatek času na aktualizaci kódu.

## <a name="about-preview-and-generally-available-versions"></a>O verzi Preview a obecně dostupné verze
Azure Kognitivní hledání vždy předem vydává experimentální funkce přes REST API a pak prostřednictvím předprodejní verze sady .NET SDK.

Funkce ve verzi Preview jsou k dispozici pro testování a experimentování s cílem shromažďovat zpětnou vazbu k návrhu a implementaci funkcí. Z tohoto důvodu se funkce verze Preview můžou v průběhu času měnit, možná způsobem, který přeruší zpětnou kompatibilitu. To je na rozdíl od funkcí ve verzi GA, které jsou stabilní a nepravděpodobné, že se nemění s výjimkou malých a nekompatibilních oprav a vylepšení. Funkce ve verzi Preview se také nedělají vždy do verze GA.

Z těchto důvodů doporučujeme před psaním produkčního kódu, který používá závislost ve verzi Preview. Pokud používáte starší verzi Preview, doporučujeme migrovat na všeobecně dostupnou verzi (GA).

Pro sadu .NET SDK: pokyny k migraci kódu najdete v [části upgrade sady .NET SDK](search-dotnet-sdk-migration-version-9.md).

Obecná dostupnost znamená, že Azure Kognitivní hledání je teď pod smlouvou o úrovni služeb (SLA). Smlouvu SLA najdete v části [smlouvy o úrovni služeb Azure kognitivní hledání](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
