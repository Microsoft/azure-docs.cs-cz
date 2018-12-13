---
title: Dotazování indexu v kódu pomocí sady .NET SDK – Azure Search
description: C#Příklad kódu pro vytváření vyhledávacího dotazu ve službě Azure Search. Přidání parametrů hledání k filtrování a řazení výsledků hledání.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/19/2017
ms.custom: seodec2018
ms.openlocfilehash: 5c89902da5e773c60c8e2694159ddeed874ecab2
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316994"
---
# <a name="query-your-azure-search-index-using-the-net-sdk"></a>Dotazování indexu Azure Search pomocí .NET SDK
> [!div class="op_single_selector"]
> * [Přehled](search-query-overview.md)
> * [Azure Portal](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Tento článek vám ukáže postup dotazování indexu pomocí [.NET SDK služby Azure Search](https://aka.ms/search-sdk).

Před zahájením tohoto názorného průvodce byste již měli mít [vytvořený index Azure Search](search-what-is-an-index.md) a ten by měl být [naplněný daty](search-what-is-data-import.md).

> [!NOTE]
> Ukázkový kód v tomto článku je napsán v jazyce C#. Úplný zdrojový kód najdete [na GitHubu](https://aka.ms/search-dotnet-howto). Můžete si také přečíst článek o sadě [Azure Search .NET SDK](search-howto-dotnet-sdk.md), který vás podrobněji provede ukázkovým kódem.

## <a name="identify-your-azure-search-services-query-api-key"></a>Zjistěte klíč api-key správce služby Azure Search
Po vytvoření indexu Azure Search jste již téměř připraveni vydávat dotazy pomocí .NET SDK. Nejprve budete muset získat jeden z klíčů dotazů (api-key) vytvořených pro vyhledávací službu, kterou jste zřídili. .NET SDK bude tento klíč api-key odesílat v každém požadavku na vaši službu. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

1. Pokud chcete najít klíče api-key svojí služby, přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte do okna služby Azure Search.
3. Klikněte na ikonu klíčů.

Vaše služba bude mít *klíče správce* a *klíče dotazů*.

* Primární a sekundární *klíče správce* udělují úplná práva ke všem operacím, včetně možnosti spravovat službu, vytvářet a odstraňovat indexy, indexery a zdroje dat. Existují dva klíče, takže pokud se rozhodnete znovu vygenerovat primární klíč, můžete dál používat sekundární klíč, a naopak.
* Vaše *klíče dotazů* udělují přístup jen pro čtení k indexům a dokumentům a obvykle se distribuují klientským aplikacím, které vydávají požadavky hledání.

Pro účely dotazování indexu můžete použít jeden z klíčů dotazů. Pro dotazy lze použít i klíče správce, ale ve svých aplikacích byste měli používat klíče dotazů, což lépe odpovídá [Principu minimálního oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="create-an-instance-of-the-searchindexclient-class"></a>Vytvoření instance třídy SearchIndexClient
Chcete-li vydávat dotazy pomocí .NET SDK služby Azure Search, budete muset vytvořit instanci třídy `SearchIndexClient`. Tato třída obsahuje několik konstruktorů. Ten, který chcete, přijímá jako parametry název vaší vyhledávací služby, název indexu a objekt `SearchCredentials`. `SearchCredentials` zabalí váš klíč api-key.

Následující kód vytvoří novou třídu `SearchIndexClient` pro index „hotels“ (vytvořený v tématu [Vytvoření indexu Azure Search pomocí .NET SDK](search-create-index-dotnet.md)) pomocí hodnot pro název vyhledávací služby a klíče api-key, které jsou uložené v konfiguračním souboru aplikace (v případě [ukázkové aplikace](https://aka.ms/search-dotnet-howto) `appsettings.json`):

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` obsahuje vlastnost `Documents`. Tato vlastnost poskytuje všechny metody, které potřebujete k dotazování indexů Azure Search.

## <a name="query-your-index"></a>Dotázání indexu
Vyhledávání pomocí .NET SDK je jednoduché, stačí na `SearchIndexClient` zavolat metodu `Documents.Search`. Tato metoda přijímá několik parametrů včetně textu vyhledávání, spolu s objektem `SearchParameters`, který lze použít pro další upřesnění dotazu.

#### <a name="types-of-queries"></a>Typy dotazů
Dva hlavní [typy dotazů](search-query-overview.md#types-of-queries), které budete používat, jsou `search` a `filter`. Dotaz `search` vyhledává jeden nebo více výrazů ve všech *prohledávatelných* polích v indexu. Dotaz `filter` vyhodnocuje logický výraz na všech *filtrovatelných* polích v indexu. Vyhledávání a filtrování lze používat společně nebo samostatně.

Vyhledávání i filtrování se provádí pomocí metody `Documents.Search`. Vyhledávací dotaz lze předat v parametru `searchText`, zatímco výraz filtru lze předat ve vlastnosti `Filter` třídy `SearchParameters`. Chcete-li filtrovat bez vyhledávání, stačí předat `"*"` jako hodnotu parametru `searchText`. Chcete-li vyhledávat bez filtrování, ponechte vlastnost `Filter` nenastavenou nebo instanci `SearchParameters` vůbec nepředávejte.

#### <a name="example-queries"></a>Ukázky dotazů
Následující vzorový kód ukazuje několik různých způsobů dotazování indexu „hotels“, definovaného v tématu [Vytvoření indexu Azure Search pomocí .NET SDK](search-create-index-dotnet.md#DefineIndex). Všimněte si, že dokumenty vrácené ve výsledcích vyhledávání jsou instancemi třídy `Hotel`, která byla definovaná v tématu [Import dat do služby Azure Search pomocí .NET SDK](search-import-data-dotnet.md#HotelClass). Ukázkový kód využívá metody `WriteDocuments` k vypsání výsledků vyhledávání do konzoly. Tato metoda je popsaná v následujícím oddílu.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="handle-search-results"></a>Zpracování výsledků vyhledávání
Metoda `Documents.Search` vrací objekt `DocumentSearchResult`, který obsahuje výsledky dotazu. Ukázka v předchozím oddílu používala pro vypsání výsledků vyhledávání do konzoly metodu s názvem `WriteDocuments`:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Zde vidíte, jak vypadají výsledky dotazů v předchozím oddílu za předpokladu, že je index „hotels“ naplněný vzorovými daty v tématu [Import Dat do služby Azure Search pomocí .NET SDK](search-import-data-dotnet.md):

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
```

Výše uvedený ukázkový kód používá k vypsání výsledků vyhledávání konzolu. Stejně tak budete potřebovat zobrazit výsledky vyhledávání ve své aplikaci. Ukázku vykreslování výsledků vyhledávání ve webové aplikaci založené na ASP.NET MVC naleznete v [této ukázce na GitHubu](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample).

