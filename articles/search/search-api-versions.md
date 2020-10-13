---
title: Verze rozhraní API
titleSuffix: Azure Cognitive Search
description: Zásady verze pro rozhraní REST API služby Azure Kognitivní hledání a knihovnu klienta v sadě .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: fab49c4242afc2e4f2d0595e2aa95f19f3d976f8
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951575"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Verze rozhraní API v Azure Kognitivní hledání

Azure Kognitivní hledání pravidelně shrnuje aktualizace funkcí. V některých případech, ale ne vždy, vyžadují tyto aktualizace novou verzi rozhraní API, aby se zachovala zpětná kompatibilita. Publikování nové verze vám umožní řídit, kdy a jak integrovat aktualizace služby Search do kódu.

V rámci pravidla publikuje tým Azure Kognitivní hledání nové verze pouze v případě potřeby, protože může zahrnovat nějaké úsilí při upgradu kódu na použití nové verze rozhraní API. Nová verze je nutná pouze v případě, že se některé aspekty rozhraní API změnily způsobem, který zruší zpětnou kompatibilitu. Tyto změny mohou nastat kvůli opravám existujících funkcí nebo kvůli novým funkcím, které mění existující oblast rozhraní API.

Stejné pravidlo platí pro aktualizace sady SDK. Sada Azure Kognitivní hledání SDK dodržuje pravidla [sémantických verzí](https://semver.org/) , což znamená, že jeho verze má tři části: hlavní, vedlejší a číslo sestavení (například 1.1.0). Nová hlavní verze sady SDK se uvolní jenom pro změny, které přeruší zpětnou kompatibilitu. Nepodstatné aktualizace funkcí zvýší dílčí verzi a opravy chyb zvýší pouze verzi buildu.

> [!Important]
> Sady Azure SDK pro .NET, Java, Python a JavaScript slouží k zavádění nových klientských knihoven pro Azure Kognitivní hledání. V současné době žádná z knihoven Azure SDK plně nepodporuje nejaktuálnější rozhraní API pro vyhledávání (2020-06-30) nebo rozhraní REST API pro správu (2020-03-13), ale mění se v průběhu času. Tuto stránku můžete pravidelně kontrolovat nebo je to [nového](whats-new.md) u oznámení o funkčních vylepšeních.

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>Nepodporované verze

Upgradujte stávající řešení hledání na nejnovější verzi REST API do 15. října 2020. V tuto chvíli budou následující verze služby Azure Kognitivní hledání REST API vyřazené a už se nepodporují:

+ **2015-02-28**
+ **2015-02-28 – Preview**
+ **2014-07-31 – Preview**
+ **2014-10-20 – Preview**

Verze sady Azure Kognitivní hledání .NET SDK starší než [**3.0.0-RC**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc) navíc budou také vyřazeny, protože cílí na jednu z těchto REST API verzí. 

Po tomto datu nebudou aplikace používající některou z zastaralých REST API nebo verzí SDK nadále fungovat a musí být upgradovány. Stejně jako u jakékoli změny tohoto typu se vám pošle 12 měsíců oznámení, takže budete mít adekvátní čas na jeho úpravu.

Pokud chcete dál používat Azure Kognitivní hledání, migrujte prosím stávající kód, který cílí na [REST API](search-api-migration.md) na [REST API verze 2020-06-30](/rest/api/searchservice/) nebo novější SDK do 15. října 2020.  Pokud máte nějaké dotazy týkající se aktualizace na nejnovější verzi, odešlete e-mail na adresu azuresearch_contact@microsoft.com 15. května 2020, abyste měli dostatek času na aktualizaci kódu.

## <a name="rest-apis"></a>Rozhraní REST API

Instance služby Azure Kognitivní hledání podporuje několik verzí REST API, včetně nejnovějšího. Můžete i nadále používat verzi, pokud již není nejnovější, ale doporučujeme, abyste si [kód migrovali](search-api-migration.md) na používání nejnovější verze. Při použití REST API musíte v každé žádosti zadat verzi rozhraní API přes parametr verze API. Pokud používáte sadu .NET SDK, verze sady SDK, kterou používáte, určí odpovídající verzi REST API. Pokud používáte starší sadu SDK, můžete pokračovat ve spuštění tohoto kódu bez změny, i když je služba upgradována na podporu novější verze rozhraní API.

Následující tabulka uvádí historii verzí aktuálních a dříve vydaných verzí Search Service REST API. Dokumentace je publikována pouze pro nejnovější verzi stabilní verze a verzi Preview.

### <a name="search-service-apis"></a>Rozhraní API pro Search Service

Umožňuje vytvořit a spravovat obsah ve vyhledávací službě.

| Znění&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Popis |
|-------------------------|--------|------------------------------|
| [Search 2020-06-30](/rest/api/searchservice/index)| Stable | Nejnovější stabilní verze rozhraní REST API pro vyhledávání s předstihy v bodování relevance a obecně dostupném pro znalostní bázi Knowledge Store.|
| [Search 2020-06-30-Preview](/rest/api/searchservice/index-preview)| Preview | Verze Preview přidružená k stabilní verzi Obsahuje více [funkcí verze Preview](search-api-preview.md). |
| Search 2019-05-06 | Stable  | Přidá [komplexní typy](search-howto-complex-data-types.md). |
| Search 2019-05-06-Preview | Preview | Verze Preview přidružená k stabilní verzi |
| Hledání 2017-11-11 | Stable | Přidá rozšíření dovednosti a [AI](cognitive-search-concept-intro.md). |
| Hledat 2017-11-11 – Preview | Preview | Verze Preview přidružená k stabilní verzi |
| Hledání 2016-09-01 |Stable | Přidá [indexery](search-indexer-overview.md). |
| Hledat 2016-09-01 – Preview | Preview | Verze Preview přidružená k stabilní verzi|
| Hledání 2015-02-28 | Nepodporované po 10-10-2020   | První všeobecně dostupná verze  |
| Hledat 2015-02-28 – Preview | Nepodporované po 10-10-2020  | Verze Preview přidružená k stabilní verzi |
| Hledat 2014-10-20 – Preview | Nepodporované po 10-10-2020 | Druhá verze Public Preview. |
| Hledat 2014-07-31 – Preview | Nepodporované po 10-10-2020  | První verze Public Preview. |

### <a name="management-rest-apis"></a>Rozhraní REST API pro správu

Umožňuje vytvořit a nakonfigurovat vyhledávací službu a spravovat klíče rozhraní API.

| Znění&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Popis |
|-------------------------|--------|------------------------------|
| [Správa 2020-08-01](/rest/api/searchmanagement/) | Stable | Nejnovější stabilní verze rozhraní REST API pro správu Přidá všeobecně dostupnou podporu sdíleného prostředku privátního propojení pro všechny prostředky s odchozím přístupem, kromě těch, které jsou uvedené ve verzi Preview. |
| [Správa 2020-08-01 – Preview](/rest/api/searchmanagement/index-preview) | Preview  | Aktuálně ve verzi Preview: Sdílená podpora prostředků privátního propojení pro Azure Functions a Azure Database for MySQL. |
| Management 2020-03-13  | Stable | Přidá [privátní koncový bod](service-create-private-endpoint.md) prostřednictvím privátního odkazu a [pravidla pro IP síť](service-configure-firewall.md) pro nové služby. Další informace najdete v této [specifikaci Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2020-08-01). |
| Management 2019-10-01-Preview | Preview  | V tomto seznamu nebyly zavedeny žádné funkce verze Preview. Tato verze Preview je funkčně ekvivalentní 2020-03-13. Další informace najdete v této [specifikaci Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/preview/2019-10-01-preview). |
| Správa 2015-08-19  | Stable | První obecně dostupná verze rozhraní REST API pro správu. Poskytuje zřizování služeb, horizontální navýšení kapacity a správu klíčů rozhraní API. Další informace najdete v této [specifikaci Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19). |
| Správa 2015-08-19 – Preview  | Preview | První verze Preview rozhraní REST API pro správu. Další informace najdete v této [specifikaci Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19). |

## <a name="azure-sdk-for-net"></a>Sada Azure SDK pro .NET

Následující tabulka obsahuje odkazy na novější verze sady SDK. 

| SDK version (Verze sady SDK) | Status | Popis |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11,0](/dotnet/api/overview/azure/search.documents-readme) | Stable | Nová knihovna klienta ze sady Azure .NET SDK vydaná v červenci 2020. července. Cílí na hledání REST API-Version = 2020-06-30 REST API, ale ještě nepodporují, geografické filtry nebo [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder). |
| [Microsoft. Azure. Search 10,0](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | Vydáno 2019. května. Cílí na rozhraní REST API pro hledání verze = 2019-05-06.|
| [Microsoft. Azure. Management. Search 4.0.0](/dotnet/api/overview/azure/search/management) | Stable | Cílí na rozhraní REST API pro správu verze = 2020-08-01.  |
| Microsoft. Azure. Management. Search 3.0.0 | Stable | Cílí na rozhraní REST API pro správu verze = 2015 – 08-19.  |

## <a name="azure-sdk-for-java"></a>Azure SDK pro Javu

| SDK version (Verze sady SDK) | Status | Popis  |
|-------------|--------|------------------------------|
| [Java Azure – hledání dokumentů 11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Stable | Nová knihovna klienta ze sady Azure .NET SDK vydaná v červenci 2020. července. Cílí na rozhraní REST API pro hledání verze = 2019-05-06. |
| [1.35.0 klienta pro správu Java](/java/api/overview/azure/search/management) | Stable | Cílí na rozhraní REST API pro správu verze = 2015 – 08-19. |

## <a name="azure-sdk-for-javascript"></a>Azure SDK for JavaScript

| SDK version (Verze sady SDK) | Status | Popis  |
|-------------|--------|------------------------------|
| [JavaScript Azure – hledání 11,0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | Stable | Nová knihovna klienta ze sady Azure .NET SDK vydaná v červenci 2020. července. Cílí na rozhraní REST API pro hledání verze = 2016-09-01. |
| [JavaScript – Azure – ARM – hledání](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | Stable | Cílí na rozhraní REST API pro správu verze = 2015 – 08-19. |

## <a name="azure-sdk-for-python"></a>Azure SDK pro Python

| SDK version (Verze sady SDK) | Status | Popis  |
|-------------|--------|------------------------------|
| [Python Azure – Search-Documents 11,0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | Stable | Nová knihovna klienta ze sady Azure .NET SDK vydaná v červenci 2020. července. Cílí na rozhraní REST API pro hledání verze = 2019-05-06. |
| [Python Azure – Správa – hledání 1,0](/python/api/overview/azure/search) | Stable | Cílí na rozhraní REST API pro správu verze = 2015 – 08-19. |