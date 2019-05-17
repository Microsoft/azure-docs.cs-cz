---
title: 'Rychlý start: Vytvoření indexu C# konzolové aplikace – Azure Search'
description: Zjistěte, jak vytvořit fulltextový index v prohledávatelných C# pomocí .NET SDK služby Azure Search.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/16/2019
ms.openlocfilehash: 8d186ae83e1016de9c4548d4b1c39303025a5270
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795823"
---
# <a name="quickstart-1---create-an-azure-search-index-in-c"></a>Rychlý start: 1 – Vytvoření indexu Azure Search vC#
> [!div class="op_single_selector"]
> * [C#](search-create-index-dotnet.md)
> * [Azure Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-fiddler.md)
>*

Tento článek vás provede procesem vytvoření [indexu Azure Search](search-what-is-an-index.md) pomocí C# a [sady .NET SDK](https://aka.ms/search-sdk). Toto je první lekcí cvičení 3 – součást pro vytváření, načítání a dotazování indexu. Vytvoření indexu se provádí pomocí provádí tyto úlohy:

> [!div class="checklist"]
> * Vytvoření [ `SearchServiceClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) objektu pro připojení k vyhledávací službě.
> * Vytvoření [ `Index` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) objektu předat jako parametr `Indexes.Create`.
> * Volání `Indexes.Create` metoda `SearchServiceClient` k odeslání `Index` ke službě.

## <a name="prerequisites"></a>Požadavky

Následující služby, nástroje a data se používají v tomto rychlém startu. 

+ [Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službou pro tento rychlý start.

+ [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), všechny edice. Ukázky kódu a instrukce byly testovány v bezplatná edice Community.

+ [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) poskytuje ukázkové řešení .NET Core konzolové aplikace napsané C#, který je umístěn v úložišti Githubu ukázky v Azure. Stažení a extrakci řešení. Ve výchozím nastavení řešení jsou jen pro čtení. Klikněte pravým tlačítkem na řešení a vymažte atribut jen pro čtení tak, že můžete upravovat soubory. Data je součástí řešení.

## <a name="get-a-key-and-url"></a>Získejte klíč a adresy URL

Volání služby vyžadují koncový bod adresy URL a přístupový klíč pro každý požadavek. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/)a ve vyhledávací službě **přehled** stránce, získat adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

2. V **nastavení** > **klíče**, získat klíč pro úplná práva správce na službu. Existují dva klíče zaměnitelné správce, v případě, že budete potřebovat k výměně jeden k dispozici zajišťuje nepřetržitý chod podniků. U požadavků můžete použít buď primární nebo sekundární klíč pro přidání, úpravy a odstraňování objektů.

![Získejte koncový bod a přístupový klíč rozhraní HTTP](media/search-fiddler/get-url-key.png "získat HTTP koncový bod a přístupový klíč")

Všechny požadavky vyžaduje klíč rozhraní api na každou požadavku odeslaného do vaší služby. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="1---configure-and-build"></a>1 – konfigurace a sestavení

1. Otevřít **DotNetHowTo.sln** souboru v sadě Visual Studio.

1. V souboru appsettings.json, nahraďte výchozí obsah níže uvedený příklad a potom zadejte název služby a správu rozhraní api-key pro vaši službu. 


   ```json
   {
       "SearchServiceName": "Put your search service name here (not the full URL)",
       "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
    }
   ```

  Pro název služby stačí samotný název. Například, pokud je vaše adresa URL https://mydemo.search.windows.net, přidejte `mydemo` do souboru JSON.

1. Stisknutím klávesy F5 sestavte řešení a spustíte aplikaci konzoly. Zbývající kroky v tomto cvičení a ty, které následují, jsou vysvětlení toho, jak tento kód funguje. 

Alternativně můžete se podívat do [použití Azure Search z aplikace .NET](search-howto-dotnet-sdk.md) podrobnější pokrytí chování sady SDK. 

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-a-client"></a>2 – Vytvoření klienta

Pokud chcete začít používat Azure Search .NET SDK, vytvořit instanci `SearchServiceClient` třídy. Tato třída obsahuje několik konstruktorů. Ten, který chcete, přijímá jako parametry název vaší vyhledávací služby a objekt `SearchCredentials`. `SearchCredentials` zabalí váš klíč api-key.

Následující kód můžete najít v souboru Program.cs. Vytvoří novou `SearchServiceClient` pomocí hodnot pro název vyhledávací služby a klíč api-key, které jsou uložené v konfiguračním souboru aplikace (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`Indexes` má vlastnost `SearchServiceClient`. Tato vlastnost poskytuje všechny metody, které potřebujete k vytváření, výpisu, aktualizaci nebo odstranění indexů Azure Search.

> [!NOTE]
> Třída `SearchServiceClient` spravuje připojení k vyhledávací službě. Aby se zabránilo otevírání příliš mnoha připojení, měli byste se pokusit sdílet jednu instanci třídy `SearchServiceClient` v rámci aplikace, pokud je to možné. Její metody jsou bezpečné pro přístup z více vláken a takové sdílení umožňují.
> 
> 

<a name="DefineIndex"></a>

## <a name="3---construct-index"></a>3 – vytvoření indexu
Jediné volání `Indexes.Create` metoda vytvoří index. Tato metoda přebírá jako parametr `Index` objekt, který definuje index Azure Search. Vytvoření `Index` objektu a inicializovat následujícím způsobem:

1. Nastavte vlastnost `Name` objektu `Index` na název indexu.

2. Nastavte vlastnost `Fields` objektu `Index` na pole objektů `Field`. Nejjednodušším způsobem vytvoření objektů `Field` je zavolání metody `FieldBuilder.BuildForType` a předání třídy modelu pro příslušný parametr typu. Třída modelu obsahuje vlastnosti, které se mapují na pole vašeho indexu. Díky tomu můžete vytvořit vazbu mezi dokumenty z indexu Search a instancemi třídy modelu.

> [!NOTE]
> Pokud nemáte v úmyslu používat třídu modelu, stále můžete definovat index přímým vytvořením objektů `Field`. Můžete zadat název pole do konstruktoru, společně s datovým typem (nebo analyzátor pro pole řetězce). Můžete také nastavit další vlastnosti, například `IsSearchable`, `IsFilterable`, pár.
>
>

Je důležité, že jste vyhledávání uživatelské prostředí a obchodní potřeby vzít v úvahu při navrhování indexu. Každému poli se musí přiřadit [atributy](https://docs.microsoft.com/rest/api/searchservice/Create-Index) , že určují, které vyhledávání (filtrování, používání faset, řazení a tak dále) se použijí u kterých polí. Pro vlastnost, kterou nenastavíte explicitně, se použije jako výchozí hodnota třídy `Field` zákaz odpovídající funkce hledání, pokud ji specificky nepovolíte.

V tomto příkladu je název indexu "hotels" a pole jsou definována pomocí třídy modelu. Každá vlastnost třídy modelu má atributy, které určují chování odpovídajícího pole indexu při vyhledávání. Třída modelu je definována takto:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
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
}
```

Atributy jsme pro každou vlastnost pečlivě zvolili podle toho, jak se pravděpodobně použijí v aplikaci. Například je pravděpodobné, že lidé hledající hotely se budou zajímat o výskyty klíčových slov v poli `description`, takže jsme pro toto pole povolili fulltextové vyhledávání přidáním atributu `IsSearchable` do vlastnosti `Description`.

Upozorňujeme, že právě jedno pole typu `string` v indexu musí být určené jako *klíčové* pole, a to přidáním atributu `Key` (viz `HotelId` v předchozím příkladu).

Výše uvedená definice indexu používá pro pole `description_fr` analyzátor jazyka, protože je určené k ukládání francouzského textu. Další informace najdete v tématu [jazykové analyzátory přidat do indexu Azure Search](index-add-language-analyzers.md).

> [!NOTE]
> Ve výchozím nastavení název každou vlastnost v třídě modelu odpovídá názvu pole v indexu. Pokud chcete namapovat všechny názvy vlastností na názvy polí ve stylu CamelCase, označte třídu atributem `SerializePropertyNamesAsCamelCase`. Pokud chcete názvy vlastností namapovat na jiné názvy, můžete k tomu použít atribut `JsonProperty`, jako je tomu u výše uvedené vlastnosti `DescriptionFr`. Atribut `JsonProperty` má přednost před atributem `SerializePropertyNamesAsCamelCase`.
> 
> 

Teď, když jsme nadefinovali třídu modelu, můžeme velmi snadno vytvořit definici indexu:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="4---call-indexescreate"></a>4 – Indexes.Create volání
Teď, když jste inicializovali `Index` objektu, vytvořte index voláním `Indexes.Create` na vaše `SearchServiceClient` objektu:

```csharp
serviceClient.Indexes.Create(definition);
```

V případě úspěšného požadavku dojde k normálnímu vrácení z metody. Pokud dojde k problému s požadavkem, jako je například neplatný parametr, vyvolá metoda výjimku `CloudException`.

Pokud jste s indexem hotovi a chcete ho odstranit, zavolejte `Indexes.Delete` metodu na vaše `SearchServiceClient`. Příklad:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> Příklad kódu v tomto článku používá pro jednoduchost synchronní metody sady Azure Search .NET SDK. Doporučujeme ve vlastních aplikacích použít asynchronní metody, aby aplikace byly škálovatelné a dobře reagovaly. Například ve výše uvedených příkladech můžete použít `CreateAsync` a `DeleteAsync` namísto `Create` a `Delete`.
> 
> 

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste vytvořili na základě schématu, který definuje datové typy polí a chování prázdný index Azure Search. Index je index "mnohastránkový", který se skládá z názvu a kolekci polí s atributy. Víc odpovídají realitě index bude zahrnovat další prvky, jako například [profily skórování](index-add-scoring-profiles.md), [moduly pro návrhy](index-add-suggesters.md) typeahead podporu [synonyma](search-synonyms.md)a případně [ vlastní analyzátory](index-add-custom-analyzers.md). Doporučujeme, abyste návštěvě těchto funkcí po pochopit základní pracovní postup.

Další rychlý start v tomto seriálu se zaměřuje na načtení indexu s prohledávatelný obsah.

> [!div class="nextstepaction"]
> [Načtení dat do indexu Azure Search pomocíC#](search-import-data-dotnet.md)