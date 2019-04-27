---
title: Načtení dat do indexu Azure Search v C# (.NET SDK) – Azure Search
description: Zjistěte, jak nahrát data do prohledávatelná fulltextový index Azure Search pomocí C# ukázkový kód a sady .NET SDK.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: d2d54d1425bbb67a3f5ba1b6081a9f74ff87f4d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871042"
---
# <a name="quickstart-2---load-data-to-an-azure-search-index-using-c"></a>Rychlý start: 2. načtení dat do indexu Azure Search pomocíC#

V tomto článku se dozvíte, jak importovat data do [indexu Azure Search](search-what-is-an-index.md) pomocí C# a [sady .NET SDK](https://aka.ms/search-sdk). Vkládání dokumentů do indexu se provádí pomocí provádí tyto úlohy:

> [!div class="checklist"]
> * Vytvoření [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) objektu pro připojení k indexu vyhledávání.
> * Vytvoření [ `IndexBatch` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) objekt obsahující dokumenty, které chcete přidat, upravit nebo odstranit.
> * Volání `Documents.Index` metodu na `SearchIndexClient` k odeslání dokumentů do indexu.

## <a name="prerequisites"></a>Požadavky

[Vytvoření indexu Azure Search](search-create-index-dotnet.md) a `SearchServiceClient` objektu, jak je znázorněno v ["Vytvoření klienta"](search-create-index-dotnet.md#CreateSearchServiceClient).


## <a name="create-a-client"></a>Vytvoření klienta
K importu dat, budete potřebovat instanci `SearchIndexClient` třídy. Existuje několik přístupů pro vytvoření této třídy, včetně použití `SearchServiceClient` instanci, která už je vytvořená. 

Následující příklad ukazuje, můžete použít `SearchServiceClient` instance a volání jeho `Indexes.GetClient` metoda. Získá tento fragment kódu `SearchIndexClient` pro index s názvem "hotels" z `SearchServiceClient` s názvem `serviceClient`.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

`SearchIndexClient` obsahuje vlastnost `Documents`. Tato vlastnost poskytuje všechny metody, potřebné pro přidáním, upravení, odstranění nebo dotazování dokumentů v indexu.

> [!NOTE]
> V typické vyhledávací aplikaci dotazů a indexování zpracovávat odděleně. Zatímco `Indexes.GetClient` je pohodlné, protože můžete znovu použít objekty, jako jsou `SearchCredentials`, robustnějším přístupem zahrnuje vytvoření `SearchIndexClient` přímo tak, aby můžete předávat klíč dotazů místo klíče správce. Tento postup je v souladu s [principu nejnižších možných oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) a pomáhá zabezpečit vaše aplikace. Budete vytvoříte `SearchIndexClient` v dalším cvičení. Další informace o klíčích najdete v tématu [vytvořit a spravovat klíče rozhraní api pro službu Azure Search](search-security-api-keys.md).
> 
> 

<a name="construct-indexbatch"></a>

## <a name="construct-indexbatch"></a>Konstrukce IndexBatch

Import dat pomocí sady .NET SDK, zabalit vaše data do `IndexBatch` objektu. `IndexBatch` Zapouzdřuje kolekci `IndexAction` objektů, z nichž každý obsahuje dokument a vlastnost, která říká službě Azure Search jakou akci chcete provést pro daný dokument (odeslání, sloučení, odstranění a mergeOrUpload). Další informace o indexování akcích najdete v tématu [indexování akce: odeslání, sloučení, mergeOrUpload, odstraňte](search-what-is-data-import.md#indexing-actions).

Za předpokladu, že víte, jaké akce lze na dokumentech provádět, jste připraveni k sestavení kompletních `IndexBatch`. Následující příklad ukazuje postup vytvoření dávky s různými akcemi. V příkladu se používá vlastní třídu s názvem `Hotel` , která se mapuje na dokument v indexu "hotels".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

V tomto případě používáme jako naše vyhledávací metody `Upload`, `MergeOrUpload`, a `Delete` podle toho, jak jsou určené metodami volanými na třídu `IndexAction`.

Předpokládejme, že je ukázkový index „hotels“ již naplněný řadou dokumentů. Všimněte si, že při použití `MergeOrUpload` nebylo nutné zadat všechna možná pole dokumentu, a při použití `Delete` jsme zadali pouze klíč dokumentu (`HotelId`).

Mějte také na paměti, že můžete v jedné žádosti indexování zahrnout maximálně 1000 dokumentů.

> [!NOTE]
> V této ukázce používáme různé akce na různé dokumenty. Místo volání `IndexBatch.New` můžete použít jiné statické metody třídy `IndexBatch`, pokud chcete provádět stejné akce na všech dokumentech v dávce. Dávky můžete vytvořit například voláním `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` nebo `IndexBatch.Delete`. Tyto metody přijímají místo objektů `IndexAction` kolekci dokumentů (v této ukázce objekty typu `Hotel`).
> 
> 

## <a name="call-documentsindex"></a>Volání Documents.Index
Nyní, když jste inicializovali objekt `IndexBatch`, můžete ho odeslat do indexu zavoláním `Documents.Index` na objekt `SearchIndexClient`. Následující příklad ukazuje způsob volání `Index`, spolu s některými dalšími kroky, které budete muset provést.

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Všimněte si `try`/`catch`, které obaluje volání metody `Index`. Blok catch se stará o zachytávání důležitých chyb pro indexování. Pokud služba Azure Search při indexování některých dokumentů v dávce selže, `Documents.Index` vyvolá výjimku `IndexBatchException`. To může nastat v případě indexování dokumentů, zatímco je služba velmi zatížená. **Důrazně doporučujeme v kódu explicitně zpracovávat tento případ.** Indexování dokumentů, které selhaly, můžete odložit a poté zkusit znovu, nebo v závislosti na požadavcích vaší aplikace na konzistenci dat provést něco jiného.

Nakonec se kód v ukázce výše na 2 sekundy odloží. Indexování ve službě Azure Search probíhá asynchronně, takže ukázková aplikace musí chvíli počkat a ujistit se, že jsou dokumenty dostupné pro vyhledávání. Tato odložení se obvykle používají pouze v ukázkových aplikacích a při testech.

Další informace o zpracování dokumentů najdete v tématu ["Jak .NET SDK zpracovává dokumenty"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).


## <a name="next-steps"></a>Další postup
Po naplnění indexu Azure Search, je dalším krokem vydávat dotazy pro vyhledávání dokumentů. 

> [!div class="nextstepaction"]
> [Dotazování indexu Azure Search vC#](search-query-dotnet.md)
