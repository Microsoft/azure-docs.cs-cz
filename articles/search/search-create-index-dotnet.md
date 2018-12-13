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
ms.date: 05/22/2017
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 6d111b1be310a345e23c440f1af9da4183efff43
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312591"
---
# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Vytvoření indexu Azure Search pomocí sady .NET SDK
> [!div class="op_single_selector"]
> * [Přehled](search-what-is-an-index.md)
> * [Azure Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Tento článek vás provede procesem vytvoření [indexu](https://docs.microsoft.com/rest/api/searchservice/Create-Index) Azure Search pomocí sady [Azure Search .NET SDK](https://aka.ms/search-sdk).

Předtím, než podle těchto pokynů vytvoříte index, byste už měli mít [vytvořenou službu Azure Search](search-create-service-portal.md).

> [!NOTE]
> Ukázkový kód v tomto článku je napsán v jazyce C#. Úplný zdrojový kód najdete [na GitHubu](https://aka.ms/search-dotnet-howto). Můžete si také přečíst článek o sadě [Azure Search .NET SDK](search-howto-dotnet-sdk.md), který vás podrobněji provede ukázkovým kódem.


## <a name="identify-your-azure-search-services-admin-api-key"></a>Identifikace klíče rozhraní API správce služby Azure Search
Teď, když máte zřízenou službu Azure Search, jste skoro připraveni vydávat žádosti na koncový bod služby pomocí sady .NET SDK. Nejprve budete muset získat jeden z klíčů správce (api-key) vytvořených pro vyhledávací službu, kterou jste zřídili. .NET SDK bude tento klíč api-key odesílat v každém požadavku na vaši službu. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

1. Pokud chcete najít klíče api-key svojí služby, přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte do okna služby Azure Search.
3. Klikněte na ikonu klíčů.

Vaše služba bude mít *klíče správce* a *klíče dotazů*.

* Primární a sekundární *klíče správce* udělují úplná práva ke všem operacím, včetně možnosti spravovat službu, vytvářet a odstraňovat indexy, indexery a zdroje dat. Existují dva klíče, takže pokud se rozhodnete znovu vygenerovat primární klíč, můžete dál používat sekundární klíč, a naopak.
* Vaše *klíče dotazů* udělují přístup jen pro čtení k indexům a dokumentům a obvykle se distribuují klientským aplikacím, které vydávají požadavky hledání.

Pro účely vytvoření indexu můžete použít primární nebo sekundární klíč správce.

<a name="CreateSearchServiceClient"></a>

## <a name="create-an-instance-of-the-searchserviceclient-class"></a>Vytvoření instance třídy SearchServiceClient
Chcete-li začít používat sadu Azure Search .NET SDK, budete muset vytvořit instanci třídy `SearchServiceClient`. Tato třída obsahuje několik konstruktorů. Ten, který chcete, přijímá jako parametry název vaší vyhledávací služby a objekt `SearchCredentials`. `SearchCredentials` zabalí váš klíč api-key.

Následující kód vytvoří novou instanci `SearchServiceClient` pomocí hodnot pro název vyhledávací služby a klíč api-key, které jsou uložené v konfiguračním souboru aplikace (v případě [ukázkové aplikace](https://aka.ms/search-dotnet-howto) `appsettings.json`):

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

## <a name="define-your-azure-search-index"></a>Definování indexu Azure Search
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

## <a name="create-the-index"></a>Vytvoření indexu
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

