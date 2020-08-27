---
title: Verze rozhraní API
titleSuffix: Azure Cognitive Search
description: Zásady verze pro rozhraní REST API služby Azure Kognitivní hledání a knihovnu klienta v sadě .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: bf3e4262f4342788f343ab287fd3db53d12736c7
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918107"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Verze rozhraní API v Azure Kognitivní hledání

Azure Kognitivní hledání pravidelně shrnuje aktualizace funkcí. V některých případech, ale ne vždy, vyžadují tyto aktualizace novou verzi rozhraní API, aby se zachovala zpětná kompatibilita. Publikování nové verze vám umožní řídit, kdy a jak integrovat aktualizace služby Search do kódu.

V rámci pravidla publikuje tým Azure Kognitivní hledání nové verze pouze v případě potřeby, protože může zahrnovat nějaké úsilí při upgradu kódu na použití nové verze rozhraní API. Nová verze je nutná pouze v případě, že se některé aspekty rozhraní API změnily způsobem, který zruší zpětnou kompatibilitu. Tyto změny mohou nastat kvůli opravám existujících funkcí nebo kvůli novým funkcím, které mění existující oblast rozhraní API.

Stejné pravidlo platí pro aktualizace sady SDK. Sada Azure Kognitivní hledání SDK dodržuje pravidla [sémantických verzí](https://semver.org/) , což znamená, že jeho verze má tři části: hlavní, vedlejší a číslo sestavení (například 1.1.0). Nová hlavní verze sady SDK se uvolní jenom pro změny, které přeruší zpětnou kompatibilitu. Nepodstatné aktualizace funkcí zvýší dílčí verzi a opravy chyb zvýší pouze verzi buildu.

> [!Important]
> Sady Azure SDK pro .NET, Java, Python a JavaScript slouží k zavádění nových klientských knihoven pro Azure Kognitivní hledání. V současné době žádná z knihoven Azure SDK plně nepodporuje nejaktuálnější rozhraní API pro vyhledávání (2020-06-30) nebo rozhraní REST API pro správu (2020-03-13), ale mění se v průběhu času. Tuto stránku můžete pravidelně kontrolovat nebo je to [nového](whats-new.md) u oznámení o funkčních vylepšeních. 

## <a name="rest-apis"></a>Rozhraní REST API

Instance služby Azure Kognitivní hledání podporuje několik verzí REST API, včetně nejnovějšího. Můžete i nadále používat verzi, pokud již není nejnovější, ale doporučujeme, abyste si [kód migrovali](search-api-migration.md) na používání nejnovější verze. Při použití REST API musíte v každé žádosti zadat verzi rozhraní API přes parametr verze API. Pokud používáte sadu .NET SDK, verze sady SDK, kterou používáte, určí odpovídající verzi REST API. Pokud používáte starší sadu SDK, můžete pokračovat ve spuštění tohoto kódu bez změny, i když je služba upgradována na podporu novější verze rozhraní API.

Následující tabulka uvádí historii verzí aktuálních a dříve vydaných verzí Search Service REST API. Dokumentace je publikována pouze pro nejnovější verzi stabilní verze a verzi Preview.

### <a name="search-service-apis"></a>Rozhraní API pro Search Service

Umožňuje vytvořit a spravovat obsah ve vyhledávací službě.

| Znění&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Problém zpětné kompatibility |
|-------------------------|--------|------------------------------|
| [Search 2020-06-30](/rest/api/searchservice/index)| Stable | Nejnovější stabilní verze rozhraní REST API pro vyhledávání s předstihy v bodování relevance a obecně dostupném pro znalostní bázi Knowledge Store.|
| [Search 2020-06-30-Preview](/rest/api/searchservice/index-preview)| Preview | Verze Preview přidružená k stabilní verzi Obsahuje více [funkcí verze Preview](search-api-preview.md). |
| Search 2019-05-06 | Stable | Přidá [komplexní typy](search-howto-complex-data-types.md). |
| Search 2019-05-06-Preview | Preview | Verze Preview přidružená k stabilní verzi |
| Hledání 2017-11-11 | Stable  | Přidá rozšíření dovednosti a [AI](cognitive-search-concept-intro.md). |
| Hledat 2017-11-11 – Preview | Preview | Verze Preview přidružená k stabilní verzi |
| Hledání 2016-09-01 |Stable | Přidá [indexery](search-indexer-overview.md). |
| Hledat 2016-09-01 – Preview | Preview | Verze Preview přidružená k stabilní verzi|
| Hledání 2015-02-28 | Stable  | První všeobecně dostupná verze  |
| Hledat 2015-02-28 – Preview | Preview | Verze Preview přidružená k stabilní verzi |
| Hledat 2014-10-20 – Preview | Preview | Druhá verze Public Preview. |
| Hledat 2014-07-31 – Preview | Preview | První verze Public Preview. |

### <a name="management-rest-apis"></a>Rozhraní REST API pro správu

Umožňuje vytvořit a nakonfigurovat vyhledávací službu a spravovat klíče rozhraní API.

| Znění&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Problém zpětné kompatibility |
|-------------------------|--------|------------------------------|
| [Management 2020-03-13](/rest/api/searchmanagement/) | Stable | Nejnovější stabilní verze rozhraní REST API pro správu s předem vydanými verzemi Endpoint Protection. Přidá [privátní koncový bod](service-create-private-endpoint.md) prostřednictvím privátního odkazu a [pravidla pro IP síť](service-configure-firewall.md) pro nové služby. |
| [Management 2019-10-01-Preview](/rest/api/searchmanagement/index-2019-10-01-preview) | Preview  | I přes číslo verze je to stále aktuální verze Preview rozhraní REST API pro správu. V tuto chvíli nejsou k dispozici žádné funkce verze Preview. Všechny funkce ve verzi Preview byly nedávno přepnuty na obecnou dostupnost. |
| Správa 2015-08-19  | Stable | První obecně dostupná verze rozhraní REST API pro správu. Poskytuje zřizování služeb, horizontální navýšení kapacity a správu klíčů rozhraní API. |
| Správa 2015-08-19 – Preview | Preview | První verze Preview rozhraní REST API pro správu. |

## <a name="azure-sdk-for-net"></a>Sada Azure SDK pro .NET

Historie verzí balíčku je dostupná na NuGet.org. Tato tabulka obsahuje odkazy na jednotlivé stránky balíčku.


| SDK version (Verze sady SDK) | Status | Popis |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11,0](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Stable | Nová knihovna klienta ze sady Azure .NET SDK vydaná v červenci 2020. července. Cílí na hledání REST API-Version = 2020-06-30 REST API, ale ještě nepodporují, geografické filtry nebo [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder?view=azure-dotnet). |
| [Microsoft. Azure. Search 10,0](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | Vydáno 2019. května. Cílí na rozhraní REST API pro hledání verze = 2019-05-06.|
| [Microsoft. Azure. Search 8,0 – Preview](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | Preview | vydáno v dubnu 2019. Cílí na hledání REST API-Version = 2019-05 -06-Preview.|
| [Microsoft. Azure. Management. Search 3.0.0](/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stable | Cílí na rozhraní REST API pro správu verze = 2015 – 08-19.  |

## <a name="azure-sdk-for-java"></a>Azure SDK pro Javu

| SDK version (Verze sady SDK) | Status | Popis  |
|-------------|--------|------------------------------|
| [Java Azure – hledání dokumentů 11](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-search-documents/11.0.0/index.html) | Stable | Nová knihovna klienta ze sady Azure .NET SDK vydaná v červenci 2020. července. Cílí na rozhraní REST API pro hledání verze = 2019-05-06. |
| [1.35.0 klienta pro správu Java](/java/api/overview/azure/search/management?view=azure-java-stable) | Stable | Cílí na rozhraní REST API pro správu verze = 2015 – 08-19. |

## <a name="azure-sdk-for-javascript"></a>Azure SDK for JavaScript

| SDK version (Verze sady SDK) | Status | Popis  |
|-------------|--------|------------------------------|
| [JavaScript Azure – hledání 11,0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | Stable | Nová knihovna klienta ze sady Azure .NET SDK vydaná v červenci 2020. července. Cílí na rozhraní REST API pro hledání verze = 2016-09-01. |
| [JavaScript – Azure – ARM – hledání](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | Stable | Cílí na rozhraní REST API pro správu verze = 2015 – 08-19. |

## <a name="azure-sdk-for-python"></a>Azure SDK pro Python

| SDK version (Verze sady SDK) | Status | Popis  |
|-------------|--------|------------------------------|
| [Python Azure – Search-Documents 11,0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | Stable | Nová knihovna klienta ze sady Azure .NET SDK vydaná v červenci 2020. července. Cílí na rozhraní REST API pro hledání verze = 2019-05-06. |
| [Python Azure – Správa – hledání 1,0](/python/api/overview/azure/search?view=azure-python) | Stable | Cílí na rozhraní REST API pro správu verze = 2015 – 08-19. |