---
title: Nahrání dat (.NET – Azure Search) | Dokumentace Microsoftu
description: Zjistěte, jak odesílat data do indexu Azure Search pomocí .NET SDK.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/13/2017
ms.openlocfilehash: fb8fb153c7393af0e084cb96ee2f5e82fba5b220
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798049"
---
# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Odesílání dat do služby Azure Search pomocí .NET SDK
> [!div class="op_single_selector"]
> * [Přehled](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Tento článek vám ukáže, jak používat [.NET SDK služby Azure Search](https://aka.ms/search-sdk) k importu dat do indexu Azure Search.

Před zahájením tohoto názorného průvodce byste již měli mít [vytvořený index Azure Search](search-what-is-an-index.md). Tento článek také předpokládá, že jste již vytvořili objekt `SearchServiceClient`, jak je ukázáno v tématu [Vytvoření indexu Azure Search pomocí .NET SDK](search-create-index-dotnet.md#CreateSearchServiceClient).

> [!NOTE]
> Ukázkový kód v tomto článku je napsán v jazyce C#. Úplný zdrojový kód najdete [na GitHubu](http://aka.ms/search-dotnet-howto). Můžete si také přečíst článek o sadě [Azure Search .NET SDK](search-howto-dotnet-sdk.md), který vás podrobněji provede ukázkovým kódem.

Pro vkládání dokumentů do indexu pomocí .NET SDK budete potřebovat:

1. Vytvořit objekt `SearchIndexClient` pro připojení k indexu vyhledávání.
2. Vytvořit metodu `IndexBatch`, která bude obsahovat dokumenty, které se mají přidat, upravit nebo odstranit.
3. Odeslat do indexu vyhledávání `IndexBatch` zavoláním metody `Documents.Index` vašeho `SearchIndexClient`.

## <a name="create-an-instance-of-the-searchindexclient-class"></a>Vytvoření instance třídy SearchIndexClient
Chcete-li do svého indexu importovat data pomocí .NET SDK služby Azure Search, budete muset vytvořit instanci třídy `SearchIndexClient`. Tuto instanci můžete vytvořit sami, ale snadnější je zavolání metody `Indexes.GetClient` instance `SearchServiceClient`, pokud ji už máte. Zde je ukázka, jak lze získat `SearchIndexClient` pro index s názvem „hotels“ z `SearchServiceClient` s názvem `serviceClient`:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> V typické vyhledávací aplikaci se o správu a naplňování indexu stará samostatná komponenta volaná dotazy vyhledávání. `Indexes.GetClient` je vhodné pro naplňování indexu, protože díky tomu není nutné poskytovat další `SearchCredentials`. Dělá to pomocí předání klíče správce, který jste použili pro vytvoření `SearchServiceClient`, službě `SearchIndexClient`. V části aplikace, který provádí dotazy, je nicméně lepší vytvořit `SearchIndexClient` přímo, abyste mohli předávat klíč dotazů místo klíče správce. To je konzistentní s [principem minimálního oprávnění](https://en.wikipedia.org/wiki/Principle_of_least_privilege) a pomůže vám to lépe zabezpečit vaši aplikaci. Další informace o klíčích dotazů a správce najdete v tématu [Reference k rozhraní REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/).
> 
> 

`SearchIndexClient` obsahuje vlastnost `Documents`. Tato vlastnost poskytuje všechny metody, potřebné pro přidáním, upravení, odstranění nebo dotazování dokumentů v indexu.

## <a name="decide-which-indexing-action-to-use"></a>Rozhodněte, jakou akci indexování použít
Pro import dat pomocí .NET SDK budete muset data zabalit do objektu `IndexBatch`. `IndexBatch` zapouzdřuje kolekci objektů `IndexAction`, z nichž každý obsahuje dokument a vlastnost, která říká službě Azure Search, kterou akci má na tomto dokumentu provést (odeslání, sloučení, odstranění atd.). V závislosti na zvolené akci musí objekt pro každý dokument obsahovat pouze určitá pole.

| Akce | Popis | Potřebná pole pro každý dokument | Poznámky |
| --- | --- | --- | --- |
| `Upload` |Akce `Upload` je podobná akci „upsert“, kdy je dokument vložený, pokud je nový a aktualizovaný nebo nahrazený, pokud již existuje. |klíč a další pole, která si přejete definovat |Pokud aktualizujete nebo nahrazujete stávající dokument, bude každé pole, které není zadané v žádosti, nastavené na `null`. K tomu dojde i v případě, že bylo pole dříve nastavené na nenulovou hodnotu. |
| `Merge` |Aktualizuje stávající dokument se zadanými poli. Pokud dokument v indexu neexistuje, sloučení selže. |klíč a další pole, která si přejete definovat |Každé pole zadané ve sloučení nahradí stávající pole v dokumentu. To zahrnuje i pole typu `DataType.Collection(DataType.String)`. Například pokud dokument obsahuje pole `tags` s hodnotou `["budget"]` a vy spustíte sloučení s polem `tags` s hodnotou `["economy", "pool"]`, konečná hodnota pole `tags` bude `["economy", "pool"]`. Hodnota nebude `["budget", "economy", "pool"]`. |
| `MergeOrUpload` |Pokud již dokument s daným klíčem v indexu existuje, chová se tato akce jako `Merge`. Pokud dokument neexistuje, chová se s novým dokumentem jako `Upload`. |klíč a další pole, která si přejete definovat |- |
| `Delete` |Odebere z indexu zadaný dokument. |pouze klíč |Všechna zadaná pole kromě pole klíče budou ignorována. Chcete-li odebrat z dokumentu jednotlivá pole, použijte místo toho `Merge` a jednoduše nastavte hodnotu pole na „null“. |

Určit, jakou akci chcete použít, můžete pomocí různých statických metod tříd `IndexBatch` a `IndexAction`, jak je ukázáno v následujícím oddílu.

## <a name="construct-your-indexbatch"></a>Vytvoření třídy IndexBatch
Nyní již víte, jaké akce lze na dokumentech provádět a jste připraveni vytvořit `IndexBatch`. Následující příklad ukazuje postup vytvoření dávky s různými akcemi. Všimněte si, že naše ukázka používá vlastní třídu s názvem `Hotel`, která se mapuje na dokument v indexu „hotels“.

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

## <a name="import-data-to-the-index"></a>Import dat do indexu
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

<a name="HotelClass"></a>

### <a name="how-the-net-sdk-handles-documents"></a>Jak .NET SDK zpracovává dokumenty
Možná vás zajímá, jak může .NET SDK služby Azure Search odesílat do indexu instance uživatelsky definované třídy, jako třeba `Hotel`. Pro odpověď na tuto otázku se podívejme na třídu `Hotel`, která se mapuje na schéma indexu definované v tématu [Vytvoření indexu Azure Search pomocí .NET SDK](search-create-index-dotnet.md#DefineIndex).

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

Nejprve si všimněte, že každá veřejná vlastnost třídy `Hotel` odpovídá poli v definici indexu s jedním zásadním rozdílem: Název každého pole začíná malým písmenem („CamelCase“), zatímco název každé veřejné vlastnosti třídy `Hotel` začíná velkým písmenem („PascalCase“). To je běžný scénář v .NET aplikacích provádějících datové vazby, kde je cílové schéma mimo kontrolu vývojáře aplikace. Místo porušování směrnic pojmenování .NET psaním názvů vlastností ve stylu CamelCase můžete pomocí atributu `[SerializePropertyNamesAsCamelCase]` říct sadě SDK, aby mapovala názvy vlastností na CamelCase automaticky.

> [!NOTE]
> .NET SDK služby Azure Search používá k serializaci a deserializaci vlastních objektů modelu do a z JSON knihovnu [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm). V případě potřeby lze serializaci přizpůsobit. Další podrobnosti najdete v části [Vlastní serializace pomocí technologie JSON.NET](search-howto-dotnet-sdk.md#JsonDotNet). Příkladem toho je použití atributu `[JsonProperty]` na vlastnost `DescriptionFr` ve výše uvedeném ukázkovém kódu.
> 
> 

Dále jsou ve třídě `Hotel` důležité datové typy veřejných vlastností. .NET typy těchto vlastností se mapují na odpovídající typy polí v definici indexu. Například řetězcová vlastnost `Category` se mapuje na pole `category`, které je typu `DataType.String`. Podobná mapování typu probíhají mezi `bool?` a `DataType.Boolean`, `DateTimeOffset?` a `DataType.DateTimeOffset` atd. Konkrétní pravidla pro mapování typu jsou popsaná u metody `Documents.Get` v tématu [Reference k sadě .NET SDK služby Azure Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_).

Tato možnost používat vlastní třídy jako dokumenty funguje v obou směrech – Můžete také načíst výsledky vyhledávání a nechat sadu SDK, aby je automaticky deserializovala do požadovaného typu, jak ukazuje [následující článek](search-query-dotnet.md).

> [!NOTE]
> .NET SDK služby Azure Search také podporuje dynamicky typované dokumenty pomocí třídy `Document`, která zajišťuje mapování klíč-hodnota názvů polí na hodnoty polí. To je užitečné v situacích, kdy v době navrhování neznáte schéma indexu nebo kde by vázání na konkrétní třídy modelu bylo nepraktické. Všechny metody v sadě SDK, které pracují s dokumenty, mají přetížení, které pracují se třídou `Document`, ale i přetížení silně závislá na typu, která přebírají parametr obecného typu. V ukázkovém kódu v tomto článku používáme pouze ty druhé.
> 
> 

**Proč byste měli používat datové typy s možnou hodnotou null**

Při navrhování vlastních tříd modelu pro mapování na index Azure Search doporučujeme deklarovat vlastnosti typů hodnot, jako jsou `bool` nebo `int`, s možnou hodnotou null (například `bool?` místo `bool`). Pokud použijete vlastnost se zakázanou hodnotou null, musíte **zajistit**, aby žádné dokumenty v indexu neobsahovaly pro odpovídající pole hodnotu null. Sada SDK, ani služba Azure Search vám s tím nepomůže.

Nejedná se pouze o hypotetický problém: představte si situaci, kdy přidáte nové pole do stávajícího indexu typu `DataType.Int32`. Po aktualizaci definice indexu budou mít všechny dokumenty pro toto nové pole hodnotu null (protože všechny typy jsou ve službě Azure Search s možností null). Pokud pak použijete třídu modelu s vlastností `int` se zakázanou hodnotou null, při pokusu o načtení dokumentů dojde k vyvolání podobné výjimky `JsonSerializationException`:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tohoto důvodu doporučujeme jako osvědčený postup používat ve třídách modelu typy s možnou hodnotou null.

## <a name="next-steps"></a>Další kroky
Po naplnění indexu Azure Search budete připraveni začít vydávat dotazy pro vyhledávání dokumentů.  Podrobnosti naleznete v tématu [Dotazování indexu Azure Search](search-query-overview.md).

