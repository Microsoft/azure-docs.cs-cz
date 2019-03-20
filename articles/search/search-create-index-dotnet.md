---
title: Vytvořte index v kódu s využitím rozhraní API pro .NET – Azure Search
description: Zjistěte, jak vytvořit fulltextový index s možností vyhledávání pomocí .NET SDK služby Azure Search a C# ukázkový kód.
author: brjohnstmsft
manager: jlembicz
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/14/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: e0aa7dce0b3cc4609a995097d8e70f8ec4336809
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226364"
---
# <a name="quickstart-create-load-and-query-an-azure-search-index-using-the-net-sdk"></a>Rychlý start: Vytvoření, načtení a dotazování indexu Azure Search pomocí .NET SDK
> [!div class="op_single_selector"]
> * [C#](search-create-index-dotnet.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [Postman (REST)](search-fiddler.md)
> * [Azure Portal](search-create-index-portal.md)
> 

Tento článek vás provede procesem vytvoření, načtení a dotazování Azure Search [index](search-what-is-an-index.md) pomocí [Azure Search .NET SDK](https://aka.ms/search-sdk).

> [!NOTE]
> Ukázkový kód v tomto článku je napsán v jazyce C#. Úplný zdrojový kód najdete [na GitHubu](https://aka.ms/search-dotnet-howto). Můžete si také přečíst článek o sadě [Azure Search .NET SDK](search-howto-dotnet-sdk.md), který vás podrobněji provede ukázkovým kódem.

## <a name="prerequisites"></a>Požadavky

+ [Vytvoření služby Azure Search](search-create-service-portal.md). Můžete použít bezplatnou službou pro tento rychlý start.

+ [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), všechny edice. Ukázky kódu a instrukce byly testovány v bezplatná edice Community.

+ Koncový bod adresy URL a správce klíč rozhraní api služby Search. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

  1. Na webu Azure Portal [svou službu vyhledejte](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v seznamu služeb.

  2. V **přehled**, získat adresu URL. Příkladem koncového bodu může být `https://my-service-name.search.windows.net`.

  3. V **nastavení** > **klíče**, získat klíč pro úplná práva správce na službu. Existují dva klíče zaměnitelné správce, v případě, že budete potřebovat k výměně jeden k dispozici zajišťuje nepřetržitý chod podniků. Na vaši žádost můžete použít buď primárním nebo sekundárním klíčem.

     ![Získejte koncový bod a přístupový klíč rozhraní HTTP](media/search-fiddler/get-url-key.png "získat HTTP koncový bod a přístupový klíč")

     Všechny požadavky vyžaduje klíč rozhraní api na každou požadavku odeslaného do vaší služby. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="1---create-a-new-project"></a>1 – Vytvoření nového projektu

V sadě Visual Studio vytvořte nový vizuál C# projektu. Správné šablony pro tento rychlý start je vizuál C# > Začínáme > webové aplikace. Tato šablona vám soubor appsettings.json.  

V souboru appsettings.json, nahraďte výchozí obsah níže uvedený příklad a potom zadejte název služby a správu rozhraní api-key pro vaši službu. Pro název služby stačí samotný název. Například, pokud je vaše adresa URL https://mydemo.search.windows.net, přidejte `mydemo` do souboru JSON.


```json
{
    "SearchServiceName": "Put your search service name here",
    "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
}
```

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-an-instance-of-the-searchserviceclient-class"></a>2. vytvoření instance třídy SearchServiceClient
Chcete-li začít používat sadu Azure Search .NET SDK, budete muset vytvořit instanci třídy `SearchServiceClient`. Tato třída obsahuje několik konstruktorů. Ten, který chcete, přijímá jako parametry název vaší vyhledávací služby a objekt `SearchCredentials`. `SearchCredentials` zabalí váš klíč api-key.

Do souboru Program.js zkopírujte následující kód. Následující kód vytvoří novou `SearchServiceClient` pomocí hodnot pro název vyhledávací služby a klíč api-key, které jsou uložené v konfiguračním souboru aplikace (appsettings.json).

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

## <a name="3---define-an-index-schema"></a>3 - definování schématu indexu
Jediné volání metody `Indexes.Create` vytvoří váš index. Tato metoda přebírá jako parametr objekt `Index`, který definuje index Azure Search. Je nutné vytvořit objekt `Index` a provést jeho inicializaci následujícím způsobem:

1. Nastavte vlastnost `Name` objektu `Index` na název indexu.

2. Nastavte vlastnost `Fields` objektu `Index` na pole objektů `Field`. Nejjednodušším způsobem vytvoření objektů `Field` je zavolání metody `FieldBuilder.BuildForType` a předání třídy modelu pro příslušný parametr typu. Třída modelu obsahuje vlastnosti, které se mapují na pole vašeho indexu. Díky tomu můžete vytvořit vazbu mezi dokumenty z indexu Search a instancemi třídy modelu.

> [!NOTE]
> Pokud nemáte v úmyslu používat třídu modelu, stále můžete definovat index přímým vytvořením objektů `Field`. Můžete zadat název pole do konstruktoru, společně s datovým typem (nebo analyzátor pro pole řetězce). Můžete také nastavit další vlastnosti, například `IsSearchable`, `IsFilterable` atd.
>
>

Při navrhování indexu je důležité zohlednit činnost koncového uživatele při vyhledávání a potřeby podniku, protože každému poli se musí přiřadit [vhodné vlastnosti](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Tyto vlastnosti určují, které funkce vyhledávání (filtrování, používání omezujících vlastností, řazení fulltextového vyhledávání atd.) se použijí u kterých polí. Pro vlastnost, kterou nenastavíte explicitně, se použije jako výchozí hodnota třídy `Field` zákaz odpovídající funkce hledání, pokud ji specificky nepovolíte.

V našem příkladu jsme nazvali index „hotels“ a pole jsme definovali pomocí třídy modelu. Každá vlastnost třídy modelu má atributy, které určují chování odpovídajícího pole indexu při vyhledávání. Třída modelu je definována takto:

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

Výše uvedená definice indexu používá pro pole `description_fr` analyzátor jazyka, protože je určené k ukládání francouzského textu. Další informace o analyzátorech jazyka najdete v [tématu jazykové podpory](https://docs.microsoft.com/rest/api/searchservice/Language-support) a příslušném [příspěvku na blogu](https://azure.microsoft.com/blog/language-support-in-azure-search/).

> [!NOTE]
> Ve výchozím nastavení se název názvy jednotlivých vlastností v třídě modelu použijí jako názvy odpovídajících polí v indexu. Pokud chcete namapovat všechny názvy vlastností na názvy polí ve stylu CamelCase, označte třídu atributem `SerializePropertyNamesAsCamelCase`. Pokud chcete názvy vlastností namapovat na jiné názvy, můžete k tomu použít atribut `JsonProperty`, jako je tomu u výše uvedené vlastnosti `DescriptionFr`. Atribut `JsonProperty` má přednost před atributem `SerializePropertyNamesAsCamelCase`.
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

## <a name="4---create-the-index-on-the-service"></a>4 – vytvoření indexu ve službě
Nyní, když jste inicializovali objekt `Index`, můžete vytvořit index jednoduchým voláním metody `Indexes.Create` pro objekt `SearchServiceClient`:

```csharp
serviceClient.Indexes.Create(definition);
```

V případě úspěšného požadavku dojde k normálnímu vrácení z metody. Pokud dojde k problému s požadavkem, jako je například neplatný parametr, vyvolá metoda výjimku `CloudException`.

Pokud jste s indexem hotovi a chcete ho odstranit, stačí zavolat metodu `Indexes.Delete` pro objekt `SearchServiceClient`. Takto bychom například odstranili index „hotels“:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> Příklad kódu v tomto článku používá pro jednoduchost synchronní metody sady Azure Search .NET SDK. Doporučujeme ve vlastních aplikacích použít asynchronní metody, aby aplikace byly škálovatelné a dobře reagovaly. Například ve výše uvedených příkladech můžete použít `CreateAsync` a `DeleteAsync` namísto `Create` a `Delete`.
> 
> 


## <a name="next-steps"></a>Další postup
Po vytvoření indexu Azure Search budete připravení [nahrát do indexu obsah](search-what-is-data-import.md), abyste mohli začít prohledávat data.

