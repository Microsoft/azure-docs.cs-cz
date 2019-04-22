---
title: Dotazování dat do indexu Azure Search v C# (.NET SDK) – Azure Search
description: C#Příklad kódu pro vytváření vyhledávacího dotazu ve službě Azure Search. Přidání parametrů hledání k filtrování a řazení výsledků hledání.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: 1194407122123797c2564c96ac452b9582b017a4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264943"
---
# <a name="quickstart-3---query-an-azure-search-index-in-c"></a>Rychlý start: 3 – dotazování indexu Azure Search vC#

Tento článek popisuje, jak provádět dotazy [indexu Azure Search](search-what-is-an-index.md) pomocí C# a [sady .NET SDK](https://aka.ms/search-sdk). Hledání dokumentů v indexu se provádí pomocí provádí tyto úlohy:

> [!div class="checklist"]
> * Vytvoření [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) objektu pro připojení k indexu vyhledávání s oprávněními jen pro čtení.
> * Vytvoření [ `SearchParameters` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet) objekt, který obsahuje definici vyhledávání nebo filtru.
> * Volání `Documents.Search` metodu na `SearchIndexClient` odesílat dotazy na index.

## <a name="prerequisites"></a>Požadavky

[Načtení indexu Azure Search](search-import-data-dotnet.md) s ukázkovými daty hotels.

Získáte klíč dotazu používá pro přístup k dokumentům jen pro čtení. Až dosud jste použili klíče k rozhraní API pro správu tak, aby na služby search můžete vytvořit objektů a obsahu. Ale pro podporu dotazů v aplikacích, důrazně doporučujeme používat klíč dotazů. Pokyny najdete v tématu [vytvořte klíč dotazu](search-security-api-keys.md#create-query-keys).

## <a name="create-a-client"></a>Vytvoření klienta
Vytvoření instance `SearchIndexClient` třídy tak, aby mu můžete přiřadit klíč dotazu pro přístup jen pro čtení (na rozdíl od svěřené práva k zápisu `SearchServiceClient` použité v předchozí lekci).

Tato třída obsahuje několik konstruktorů. Ten, který chcete přebírá název vyhledávací služby, název indexu a [ `SearchCredentials` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials?view=azure-dotnet) objektu jako parametry. `SearchCredentials` zabalí váš klíč api-key.

Následující kód vytvoří novou `SearchIndexClient` pro index "hotels" pomocí hodnot pro název vyhledávací služby a klíč api-key, které jsou uložené v konfiguračním souboru aplikace (`appsettings.json` v případě třídy [ukázkovou aplikaci](https://aka.ms/search-dotnet-howto)):

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` má [ `Documents` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient.documents?view=azure-dotnet) vlastnost. Tato vlastnost poskytuje všechny metody, které potřebujete k dotazování indexů Azure Search.

## <a name="construct-searchparameters"></a>Vytvořit parametrech vyhledávání.
Vyhledávání pomocí .NET SDK je jednoduché, stačí na `SearchIndexClient` zavolat metodu `Documents.Search`. Tato metoda přijímá několik parametrů včetně textu vyhledávání, spolu s objektem `SearchParameters`, který lze použít pro další upřesnění dotazu.

### <a name="types-of-queries"></a>Typy dotazů
Dva hlavní [typy dotazů](search-query-overview.md#types-of-queries), které budete používat, jsou `search` a `filter`. Dotaz `search` vyhledává jeden nebo více výrazů ve všech *prohledávatelných* polích v indexu. Dotaz `filter` vyhodnocuje logický výraz na všech *filtrovatelných* polích v indexu. Vyhledávání a filtrování lze používat společně nebo samostatně.

Vyhledávání i filtrování se provádí pomocí metody `Documents.Search`. Vyhledávací dotaz lze předat v parametru `searchText`, zatímco výraz filtru lze předat ve vlastnosti `Filter` třídy `SearchParameters`. Chcete-li filtrovat bez vyhledávání, stačí předat `"*"` jako hodnotu parametru `searchText`. Chcete-li vyhledávat bez filtrování, ponechte vlastnost `Filter` nenastavenou nebo instanci `SearchParameters` vůbec nepředávejte.

### <a name="example-queries"></a>Ukázky dotazů
Následující ukázkový kód ukazuje několik různých způsobů dotazování indexu "hotels", které jsou definovány v [vytvoření indexu Azure Search v C# ](search-create-index-dotnet.md#DefineIndex). Všimněte si, že dokumenty vrácené ve výsledcích vyhledávání jsou instancemi `Hotel` třída, která byla definována v [Import dat do indexu Azure Search v C# ](search-import-data-dotnet.md#construct-indexbatch). Ukázkový kód využívá metody `WriteDocuments` k vypsání výsledků vyhledávání do konzoly. Tato metoda je popsaná v následujícím oddílu.

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

Zde je, co vypadají výsledky dotazů v předchozí části, za předpokladu, že je index "hotels" naplněný ukázková data:

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

Výše uvedený ukázkový kód používá k vypsání výsledků vyhledávání konzolu. Stejně tak budete potřebovat zobrazit výsledky vyhledávání ve své aplikaci. Příklad toho, jak vykreslování výsledků vyhledávání v aplikaci ASP.NET MVC, webových, najdete v článku [DotNetSample projektu](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) na Githubu.

## <a name="next-steps"></a>Další postup

Pokud jste to ještě neudělali, projděte si ukázkový kód v [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) na Githubu, spolu s [použití Azure Search z aplikace .NET](search-howto-dotnet-sdk.md) Podrobnější popisy ukázek kódu. 