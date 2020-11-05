---
title: Použití Azure.Search.Documents (V11) v .NET
titleSuffix: Azure Cognitive Search
description: Naučte se vytvářet a spravovat vyhledávací objekty v aplikaci .NET pomocí C# a klientské knihovny Azure.Search.Documents (V11). Fragmenty kódu ukazují připojení ke službě, vytváření indexů a dotazy.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4b3256591c0aa2536fd42bcdbb2ef339fc1d5c48
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356803"
---
# <a name="how-to-use-azuresearchdocuments-in-a-c-net-application"></a>Jak používat Azure.Search.Documents v aplikaci C# .NET

Tento článek vysvětluje, jak vytvořit a spravovat vyhledávací objekty pomocí jazyka C# a klientské knihovny [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) (verze 11).

## <a name="about-version-11"></a>Přibližně verze 11

Sada Azure SDK pro .NET přidá novou [**Azure.Search.Doc**](/dotnet/api/overview/azure/search) klientskou knihovnu uments z týmu Azure SDK, která je funkčně ekvivalentní s [Microsoft. Azure. hledejte](/dotnet/api/overview/azure/search/client10) klientské knihovny, ale využívá běžné přístupy a konvence tam, kde je to možné. Mezi příklady patří [`AzureKeyCredential`](/dotnet/api/azure.azurekeycredential) ověřování klíče a [System.Text.Js. Serializace](/dotnet/api/system.text.json.serialization) pro serializaci JSON

Stejně jako v předchozích verzích můžete tuto knihovnu použít k těmto akcím:

+ Vytváření a správa vyhledávacích indexů, zdrojů dat, indexerů, dovednosti a mapování synonym
+ Načtení a správa vyhledávacích dokumentů v indexu
+ Provádění dotazů, aniž byste museli řešit podrobnosti HTTP a JSON

Knihovna je distribuována jako jeden [ balíček NuGetAzure.Search.Document](https://www.nuget.org/packages/Azure.Search.Documents/), který zahrnuje všechna rozhraní API používaná pro programový přístup k vyhledávací službě.

Klientská knihovna definuje třídy jako `SearchIndex` , `SearchField` , a `SearchDocument` i operace, jako jsou `SearchIndexClient.CreateIndex` a `SearchClient.Search` na `SearchIndexClient` `SearchClient` třídy a. Tyto třídy jsou uspořádány do následujících oborů názvů:

+ [`Azure.Search.Documents`](/dotnet/api/azure.search.documents)
+ [`Azure.Search.Documents.Indexes`](/dotnet/api/azure.search.documents.indexes)
+ [`Azure.Search.Documents.Indexes.Models`](/dotnet/api/azure.search.documents.indexes.models)
+ [`Azure.Search.Documents.Models`](/dotnet/api/azure.search.documents.models)

Azure.Search.Documents (verze 11) cílí na verzi služby [ `2020-06-30` Azure kognitivní hledání REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Azure.Search/preview/2020-06-30). 

Klientská knihovna neposkytuje [operace správy služeb](/rest/api/searchmanagement/), jako je vytváření a škálování vyhledávacích služeb a Správa klíčů rozhraní API. Pokud potřebujete spravovat svoje prostředky hledání z aplikace .NET, použijte knihovnu [Microsoft. Azure. Management. Search](/dotnet/api/overview/azure/search/management) v sadě Azure SDK pro .NET.

## <a name="upgrade-to-v11"></a>Upgrade na v11

Pokud jste používali předchozí verzi sady .NET SDK a chcete upgradovat na aktuální všeobecně dostupnou verzi, přečtěte si téma [upgrade na Azure kognitivní hledání .NET SDK verze 11](search-dotnet-sdk-migration-version-11.md) .

## <a name="sdk-requirements"></a>Požadavky na sadu SDK

+ Visual Studio 2019 nebo novější.

+ Vaše vlastní služba Azure Kognitivní hledání. Aby bylo možné používat sadu SDK, budete potřebovat název služby a jeden nebo více klíčů rozhraní API. Pokud ho nemáte, [vytvořte na portálu službu](search-create-service-portal.md) .

+ Stažení [balíčkuAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents) pomocí **nástroje**  >  **Správce balíčků NuGet**  >  **Správa balíčků NuGet pro řešení** v aplikaci Visual Studio. Vyhledejte název balíčku `Azure.Search.Documents` .

Sada Azure SDK pro .NET odpovídá [.NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support), což znamená, že .NET Framework 4.6.1 a .net Core 2,0 jako minimální požadavky.

## <a name="example-application"></a>Ukázková aplikace

Tento článek "učí příklad", který se spoléhá na příklad [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) kódu na GitHubu, který ilustruje základní koncepty v Azure kognitivní hledání – konkrétně, jak vytvořit, načíst a dotazovat index vyhledávání.

Ve zbývající části tohoto článku Předpokládejme nový index s názvem "hotely", který je vyplněný několika dokumenty, s několika dotazy, které se shodují s výsledky.

Níže je hlavní program, který zobrazuje celkový tok:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchIndexClient indexClient = CreateSearchIndexClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, indexClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, indexClient);

    SearchClient searchClient = indexClient.GetSearchClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(searchClient);

    SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

    Console.WriteLine("{0}", "Run queries...\n");
    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

Další je částečný snímek obrazovky výstupu. za předpokladu, že tuto aplikaci spustíte s platným názvem služby a klíči rozhraní API:

:::image type="content" source="media/search-howto-dotnet-sdk/console-output.png" alt-text="Výstup Console. WriteLine z ukázkového programu":::

### <a name="client-types"></a>Typy klientů

Klientská knihovna pro různé operace používá tři typy klientů: [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) k vytváření, aktualizaci nebo odstraňování indexů, [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) k načtení nebo dotazování indexu a [`SearchIndexerClient`](/dotnet/api/azure.search.documents.indexes.searchindexerclient) k práci s indexery a dovednosti. Tento článek se zaměřuje na první dva. 

Minimálně všechny klienty vyžadují název služby nebo koncový bod a klíč rozhraní API. Tyto informace je běžné zadat v konfiguračním souboru, podobně jako v `appsettings.json` souboru [ukázkové aplikace DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo). Chcete-li číst z konfiguračního souboru, přidejte `using Microsoft.Extensions.Configuration;` jej do programu.

Následující příkaz vytvoří klienta indexu, který slouží k vytváření, aktualizaci nebo odstranění indexů. Používá koncový bod pro hledání a klíč rozhraní API pro správu.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceEndPoint), new AzureKeyCredential(adminApiKey));
    return indexClient;
}
```

Následující příkaz vytvoří klienta vyhledávání, který se používá k načtení dokumentů nebo spuštění dotazů. `SearchClient` vyžaduje index. K načtení dokumentů budete potřebovat klíč rozhraní API pro správu, ale ke spouštění dotazů můžete použít klíč rozhraní API pro dotazy. 

```csharp
string indexName = configuration["SearchIndexName"];

private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

> [!NOTE]
> Pokud pro operaci importu zadáte neplatný klíč (například klíč dotazu, ve kterém byl vyžadován klíč správce), `SearchClient` vyvolá `CloudException` při prvním volání metody operace výjimku s chybovou zprávou "zakázáno". Pokud k tomu dojde, poklikejte znovu na klíč rozhraní API.
>

### <a name="deleting-the-index"></a>Odstraňuje se index.

V počátečních fázích vývoje můžete chtít zahrnout [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) příkaz pro odstranění indexu probíhajícího zpracování, abyste ho mohli znovu vytvořit s aktualizovanou definicí. Vzorový kód pro Azure Kognitivní hledání často zahrnuje krok odstranění, abyste mohli ukázku znovu spustit.

Následující řádek volá `DeleteIndexIfExists` :

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, indexClient);
```

Tato metoda používá daný `SearchIndexClient` pro kontrolu, zda index existuje, a pokud ano, odstraní ho:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchIndexClient indexClient)
{
    try
    {
        if (indexClient.GetIndex(indexName) != null)
        {
            indexClient.DeleteIndex(indexName);
        }
    }
    catch (RequestFailedException e) when (e.Status == 404)
    {
        // Throw an exception if the index name isn't found
        Console.WriteLine("The index doesn't exist. No deletion occurred.");
```

> [!NOTE]
> Vzorový kód v tomto článku používá synchronní metody pro jednoduchost, ale měli byste použít asynchronní metody ve vlastních aplikacích, abyste zachovali jejich škálovatelnost a rychlost reakce. Například v metodě výše můžete použít [`DeleteIndexAsync`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindexasync) místo [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) .
>

## <a name="create-an-index"></a>Vytvoření indexu

Můžete použít [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) k vytvoření indexu. 

Níže uvedená metoda vytvoří nový [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) objekt se seznamem [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) objektů, které definují schéma nového indexu. Každé pole má název, datový typ a několik atributů, které definují jeho chování hledání. 

Pole lze definovat z třídy modelu pomocí [`FieldBuilder`](/dotnet/api/azure.search.documents.indexes.fieldbuilder) . `FieldBuilder`Třída používá reflexi k vytvoření seznamu `SearchField` objektů pro index prozkoumáním veřejných vlastností a atributů dané `Hotel` třídy modelu. Později se podrobněji podíváme na `Hotel` třídu.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    indexClient.CreateOrUpdateIndex(definition);
}
```

Kromě polí můžete také do indexu přidat profily vyhodnocování, moduly pro návrhy a možnosti CORS (tyto parametry jsou vynechány v ukázce pro zkrácení). Můžete najít další informace o objektu SearchIndex a jeho částech v [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) seznamu vlastností a také v [odkazu na REST API](/rest/api/searchservice/).

> [!NOTE]
> Seznam objektů můžete kdykoli vytvořit `Field` přímo místo použití v `FieldBuilder` případě potřeby. Například nebudete chtít použít třídu modelu nebo může být nutné použít existující třídu modelu, kterou nechcete upravovat, přidáním atributů.
>

### <a name="call-createindex-in-main"></a>Volání CreateIndex v Main ()

`Main` Vytvoří nový index hotelů voláním výše uvedené metody:

```csharp
Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, indexClient);
```

## <a name="use-a-model-class-for-data-representation"></a>Použití třídy modelu pro reprezentaci dat

Ukázka DotNetHowTo používá třídy modelu pro datové struktury [hotelů](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Hotel.cs), [adres](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Address.cs)a [místností](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Room.cs) . `Hotel` odkazuje na `Address` jeden komplexní typ (pole s více částmi) a `Room` (kolekce polí s více částmi).

Tyto typy můžete použít k vytvoření a načtení indexu a ke strukturování odpovědi z dotazu:

```csharp
// Use-case: <Hotel> in a field definition
FieldBuilder fieldBuilder = new FieldBuilder();
var searchFields = fieldBuilder.Build(typeof(Hotel));

// Use-case: <Hotel> in a response
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="field-definitions"></a>Definice polí

Váš datový model v .NET a odpovídající schéma indexu by měly podporovat vyhledávací prostředí, které chcete dát koncovému uživateli. Každý objekt nejvyšší úrovně v rozhraní .NET, jako je například vyhledávací dokument v indexu vyhledávání, odpovídá výsledku hledání, který jste nacházeli v uživatelském rozhraní. Například v aplikaci pro vyhledávání hotelů můžou koncoví uživatelé hledat podle názvu hotelu, funkce hotelu nebo charakteristiky konkrétní místnosti. 

V rámci každé třídy je pole definováno s datovým typem a atributy, které určují, jak se používají. Název každé veřejné vlastnosti v každé třídě se mapuje na pole se stejným názvem v definici indexu. 

Podívejte se na následující fragment kódu, který načte několik definic polí z třídy hotelu. Všimněte si, že adresa a místnosti jsou typy C# s vlastními definicemi třídy (Pokud je chcete zobrazit, přečtěte si ukázku kódu). Oba jsou komplexní typy. Další informace najdete v tématu [modelování komplexních typů](search-howto-complex-data-types.md).

```csharp
public partial class Hotel
{
    [SimpleField(IsKey = true, IsFilterable = true)]
    public string HotelId { get; set; }

    [SearchableField(IsSortable = true)]
    public string HotelName { get; set; }

    [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
    public string Description { get; set; }

    [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
    public string Category { get; set; }

    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [SearchableField]
    public Address Address { get; set; }

    public Room[] Rooms { get; set; }
```

#### <a name="choosing-a-field-class"></a>Výběr třídy pole

Při definování polí můžete použít základní [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) třídu, nebo můžete použít odvozené pomocné modely, které slouží jako "šablony", s předem nakonfigurovanými vlastnostmi.

Právě jedno pole v indexu musí sloužit jako klíč dokumentu ( `IsKey = true` ). Musí to být řetězec a musí jednoznačně identifikovat každý dokument. Je také nutné mít `IsHidden = true` , což znamená, že nelze zobrazit ve výsledcích hledání.

| Typ pole | Popis a použití |
|------------|-----------------------|
| [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) | Základní třída s největší vlastností nastavenou na hodnotu null, s výjimkou toho `Name` , která je vyžadována a `AnalyzerName` která má výchozí nastavení standardní Lucene. |
| [`SimpleField`](/dotnet/api/azure.search.documents.indexes.models.simplefield) | Pomocný model. Může se jednat o libovolný datový typ, je vždycky neprohledávatelné (pro fulltextové vyhledávací dotazy se ignoruje) a dá se získat (není skrytý). Ostatní atributy jsou ve výchozím nastavení vypnuté, ale dají se povolit. Můžete použít `SimpleField` pro ID dokumentů nebo pole, která se používají jenom v filtrech, omezujících objektech nebo profilech vyhodnocování. Pokud ano, nezapomeňte použít všechny atributy, které jsou nezbytné pro daný scénář, například `IsKey = true` pro ID dokumentu. Další informace naleznete v tématu [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) ve zdrojovém kódu. |
| [`SearchableField`](/dotnet/api/azure.search.documents.indexes.models.searchablefield) | Pomocný model. Musí to být řetězec, který je vždycky prohledávatelný a dá se získat. Ostatní atributy jsou ve výchozím nastavení vypnuté, ale dají se povolit. Vzhledem k tomu, že tento typ pole lze prohledávat, podporuje synonyma a úplný doplněk vlastností analyzátoru. Další informace naleznete v tématu [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) ve zdrojovém kódu. |

Bez ohledu na to, jestli používáte základní `SearchField` rozhraní API nebo některý z pomocných modelů, musíte explicitně povolit atributy Filter, Face a Sort. Například může být [nefiltrovaná](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [nesetříditelné](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)a vlastnost- [Face](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) , jak je znázorněno v ukázce výše.

#### <a name="adding-field-attributes"></a>Přidání atributů pole

Všimněte si, jak je každé pole upraveno pomocí atributů, jako jsou `IsFilterable` ,, `IsSortable` `IsKey` a `AnalyzerName` . Tyto atributy jsou mapovány přímo k [odpovídajícím atributům polí v indexu služby Azure kognitivní hledání](/rest/api/searchservice/create-index). `FieldBuilder`Třída pomocí těchto vlastností vytvoří definice polí pro index.

#### <a name="field-type-mapping"></a>Mapování typu pole

Typy .NET vlastností jsou mapovány na jejich ekvivalentní typy polí v definici indexu. Například řetězcová vlastnost `Category` se mapuje na pole `category`, které je typu `Edm.String`. Existují podobné mapování typů mezi `bool?` , `Edm.Boolean` , a a `DateTimeOffset?` `Edm.DateTimeOffset` tak dále. 

Nedošlo k tomu, abyste si poznamenali `SmokingAllowed` vlastnost?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

`JsonIgnore`Atribut v této vlastnosti říká, že `FieldBuilder` ho neserializovat do indexu jako pole.  Toto je skvělý způsob, jak vytvořit počítané vlastnosti na straně klienta, které můžete použít jako pomocníky ve vaší aplikaci.  V tomto případě `SmokingAllowed` vlastnost odráží, zda některý `Room` z `Rooms` kolekcí umožňuje kouření. Pokud jsou všechny hodnoty false, znamená to, že celý Hotel nedovoluje kouření.

## <a name="load-an-index"></a>Načtení indexu

V dalším kroku `Main` naplníte nově vytvořený index "hotely". Tento index naplnění se provádí v následující metodě: (kód se nahradil znakem "...". pro ilustrační účely. Kompletní kód pro naplnění dat najdete v úplném ukázkovém řešení.)

```csharp
private static void UploadDocuments(SearchClient searchClient)
{
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                HotelName = "Secret Point Motel",
                ...
                Address = new Address()
                {
                    StreetAddress = "677 5th Ave",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Budget Room, 1 Queen Bed (Cityside)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (City View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                HotelName = "Twin Dome Motel",
                ...
                {
                    StreetAddress = "140 University Town Center Dr",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Suite, 2 Double Beds (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Standard Room, 1 Queen Bed (City View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Waterfront View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "3",
                HotelName = "Triple Landscape Hotel",
                ...
                Address = new Address()
                {
                    StreetAddress = "3393 Peachtree Rd",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Standard Room, 2 Queen Beds (Amenities)",
                        ...
                    },
                    new Room ()
                    {
                        Description = "Standard Room, 2 Double Beds (Waterfront View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (Cityside)",
                        ...
                    }
                }
            }
        };

    try
    {
        IndexDocumentsResult result = searchClient.IndexDocuments(batch);
    }
    catch (Exception)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine("Failed to index some of the documents: {0}");
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
```

Tato metoda má čtyři části. První vytvoří pole 3 `Hotel` objekty každý se 3 `Room` objekty, které budou sloužit jako naše vstupní data pro nahrání do indexu. Tato data jsou pevně kódovaná pro zjednodušení. Ve skutečné aplikaci budou data pravděpodobná z externího zdroje dat, jako je třeba databáze SQL.

Druhá část vytvoří [`IndexDocumentsBatch`](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) dokumenty obsahující. Určete operaci, kterou chcete použít pro dávku v okamžiku jejího vytvoření, v tomto případě voláním [`IndexDocumentsAction.Upload`](/dotnet/api/azure.search.documents.models.indexdocumentsaction.upload) . Dávka se pak nahraje do indexu Azure Kognitivní hledání pomocí [`IndexDocuments`](/dotnet/api/azure.search.documents.searchclient.indexdocuments) metody.

> [!NOTE]
> V tomto příkladu budeme nahrávat jenom dokumenty. Pokud jste chtěli sloučit změny do existujících dokumentů nebo odstraňovat dokumenty, mohli byste vytvořit dávky voláním `IndexDocumentsAction.Merge` , `IndexDocumentsAction.MergeOrUpload` nebo `IndexDocumentsAction.Delete` místo toho. Různé operace můžete v jedné dávce kombinovat také voláním metody `IndexBatch.New` , která přebírá kolekci `IndexDocumentsAction` objektů, z nichž každý z nich oznamuje, že Azure kognitivní hledání provádět konkrétní operace s dokumentem. Můžete vytvořit každý `IndexDocumentsAction` s vlastní operací voláním odpovídající metody, jako například `IndexDocumentsAction.Merge` , `IndexAction.Upload` , a tak dále.
> 

Třetí část této metody je blok catch, který zpracovává důležitý chybový případ pro indexování. Pokud vaše vyhledávací služba při indexování některých dokumentů v dávce neprojde, `IndexBatchException` vyvolá se `IndexDocuments` . Tato výjimka se může vyskytnout, pokud indexování dokumentů probíhá v případě velkého zatížení služby. **Důrazně doporučujeme v kódu explicitně zpracovávat tento případ.** Indexování dokumentů, které selhaly, můžete odložit a poté zkusit znovu, nebo v závislosti na požadavcích vaší aplikace na konzistenci dat provést něco jiného.

Nakonec je `UploadDocuments` Metoda zpožděna po dobu dvou sekund. Indexování probíhá asynchronně ve vaší vyhledávací službě, takže ukázková aplikace potřebuje počkat krátkou dobu, aby se zajistila dostupnost dokumentů pro hledání. Tato odložení se obvykle používají pouze v ukázkových aplikacích a při testech.

### <a name="call-uploaddocuments-in-main"></a>Volání UploadDocuments v Main ()

Následující fragment kódu nastaví instanci [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) pomocí [`GetSearchClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient.getsearchclient) metody indexClient. IndexClient používá pro své požadavky klíč rozhraní API pro správu, který je vyžadován pro načítání nebo obnovení dokumentů.

Alternativním přístupem je zavolat `SearchClient` přímo a předat do něj klíč rozhraní API pro správu `AzureKeyCredential` .

```csharp
SearchClient searchClient = indexClient.GetSearchClient(indexName);

Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(searchClient);
```

## <a name="run-queries"></a>Spouštění dotazů

Nejdřív nastavte a `SearchClient` přečte koncový bod vyhledávání a klíč rozhraní API pro dotaz z **appsettings.jsna** :

```csharp
private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

Potom definujte metodu, která odešle požadavek na dotaz. 

Pokaždé, když metoda spustí dotaz, vytvoří nový [`SearchOptions`](/dotnet/api/azure.search.documents.searchoptions) objekt. Tento objekt se používá k určení dalších možností pro dotaz, jako je řazení, filtrování, stránkování a omezující vlastnosti. V této metodě nastavujeme `Filter` `Select` vlastnost, a `OrderBy` pro různé dotazy. Další informace o syntaxi výrazu vyhledávacího dotazu najdete v [jednoduché syntaxi dotazů](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).

Dalším krokem je ve skutečnosti spustit vyhledávací dotaz. Spuštění vyhledávání je provedeno pomocí `SearchClient.Search` metody. Pro každý dotaz předejte hledaný text, který se má použít jako řetězec (nebo `"*"` Pokud neexistuje hledaný text), a také možnosti hledání, které jste vytvořili dříve. Také určujeme `Hotel` jako parametr typu pro `SearchClient.Search` , který dává sadě SDK pokyn k deserializaci dokumentů ve výsledcích hledání do objektů typu `Hotel` .

```csharp
private static void RunQueries(SearchClient searchClient)
{
    SearchOptions options;
    SearchResults<Hotel> results;

    Console.WriteLine("Query 1: Search for 'motel'. Return only the HotelName in results:\n");

    options = new SearchOptions();
    options.Select.Add("HotelName");

    results = searchClient.Search<Hotel>("motel", options);

    WriteDocuments(results);

    Console.Write("Query 2: Apply a filter to find hotels with rooms cheaper than $100 per night, ");
    Console.WriteLine("returning the HotelId and Description:\n");

    options = new SearchOptions()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)"
    };
    options.Select.Add("HotelId");
    options.Select.Add("Description");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.Write("Query 3: Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    options =
        new SearchOptions()
        {
            Size = 2
        };
    options.OrderBy.Add("LastRenovationDate desc");
    options.Select.Add("HotelName");
    options.Select.Add("LastRenovationDate");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.WriteLine("Query 4: Search the HotelName field for the term 'hotel':\n");

    options = new SearchOptions();
    options.SearchFields.Add("HotelName");

    //Adding details to select, because "Location" is not supported yet when deserialize search result to "Hotel"
    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Category");
    options.Select.Add("Tags");
    options.Select.Add("ParkingIncluded");
    options.Select.Add("LastRenovationDate");
    options.Select.Add("Rating");
    options.Select.Add("Address");
    options.Select.Add("Rooms");

    results = searchClient.Search<Hotel>("hotel", options);

    WriteDocuments(results);
}
```

Třetí definuje metodu, která zapisuje odpověď, tisk každého dokumentu do konzoly:

```csharp
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="call-runqueries-in-main"></a>Volání RunQueries v Main ()

```csharp
SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

Console.WriteLine("{0}", "Running queries...\n");
RunQueries(indexClientForQueries);
```

### <a name="explore-query-constructs"></a>Prozkoumat konstrukce dotazů

Pojďme se podrobněji podívat na každý z dotazů. Zde je kód pro provedení prvního dotazu:

```csharp
options = new SearchOptions();
options.Select.Add("HotelName");

results = searchClient.Search<Hotel>("motel", options);

WriteDocuments(results);
```

V tomto případě prohledáváme celý index pro slovo "Motel" v jakémkoli vyhledávacím poli a chceme pouze načíst názvy hotelů, jak je určeno `Select` možností. Tady jsou výsledky:

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

Ve druhém dotazu použijte filtr k výběru místnosti s noční frekvencí menší než $100. Vrátí do výsledků jenom ID a popis hotelu:

```csharp
options = new SearchOptions()
{
    Filter = "Rooms/any(r: r/BaseRate lt 100)"
};
options.Select.Add("HotelId");
options.Select.Add("Description");

results = searchClient.Search<Hotel>("*", options);
```

Výše uvedený dotaz používá výraz OData `$filter` `Rooms/any(r: r/BaseRate lt 100)` pro filtrování dokumentů v indexu. Pomocí [operátoru any](./search-query-odata-collection-operators.md) se v kolekci místností aplikuje ' BaseRate lt 100 ' na všechny položky. Další informace najdete v tématu [syntaxe filtru OData](./query-odata-filter-orderby-syntax.md).

V třetím dotazu vyhledejte horní dva hotely, které byly naposledy renovated, a zobrazte název hotelu a datum poslední renovace. Zde je kód: 

```csharp
options =
    new SearchOptions()
    {
        Size = 2
    };
options.OrderBy.Add("LastRenovationDate desc");
options.Select.Add("HotelName");
options.Select.Add("LastRenovationDate");

results = searchClient.Search<Hotel>("*", options);

WriteDocuments(results);
```

V posledním dotazu vyhledejte názvy všech hotelů, které odpovídají slovu "Hotel":

```csharp
options.Select.Add("HotelId");
options.Select.Add("HotelName");
options.Select.Add("Description");
options.Select.Add("Category");
options.Select.Add("Tags");
options.Select.Add("ParkingIncluded");
options.Select.Add("LastRenovationDate");
options.Select.Add("Rating");
options.Select.Add("Address");
options.Select.Add("Rooms");

results = searchClient.Search<Hotel>("hotel", options);

WriteDocuments(results);
```

V této části se uzavírá tento Úvod do sady .NET SDK, ale nezastaví se zde. V další části se dozvíte další zdroje informací o programování s využitím Azure Kognitivní hledání.

## <a name="next-steps"></a>Další kroky

+ Projděte si referenční dokumentaci k rozhraní API [Azure.Search.Documents](/dotnet/api/azure.search.documents) a [REST API](/rest/api/searchservice/)

+ Procházení dalších ukázek kódu na základě Azure.Search.Documents ve [službě Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) and [Search-Začínáme-dotnet](https://github.com/Azure-Samples/search-dotnet-getting-started)

+ Přečtěte si [zásady vytváření názvů](/rest/api/searchservice/Naming-rules) a Naučte se pravidla pro pojmenovávání různých objektů.

+ Kontrola [podporovaných datových typů](/rest/api/searchservice/Supported-data-types)