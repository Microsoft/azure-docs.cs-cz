---
title: Kurz k synonymům ve verzi Preview ve službě Azure Search | Dokumentace Microsoftu
description: Přidání funkce synonym ve verzi Preview do indexu ve službě Azure Search.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 03/31/2017
ms.author: heidist
ms.openlocfilehash: 0f082397f832883b272a2ca38850a340b618adde
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="synonym-preview-c-tutorial-for-azure-search"></a>Kurz synonym (Preview) v jazyce C# pro Azure Search

Synonyma rozšiřují dotazy hledáním shody s termíny, které jsou považované za sémantické ekvivalenty vstupního výrazu. Chcete třeba, aby položce auto odpovídaly i dokumenty obsahující termíny automobil a vozidlo.

Ve službě Azure Search se synonyma definují v *mapě synonym*, pomocí *pravidel mapování*, která přidružují ekvivalentní termíny. Můžete vytvořit několik map synonym, zveřejnit je jako prostředky na úrovni služby dostupné pro všechny indexy a potom určit, který se má použít na úrovni pole. V době zpracování dotazu služba Azure Search nejenom prohledá index, ale pokud některé z polí využitých v tomto dotazu má mapu synonym, prohledá i tuto mapu.

> [!NOTE]
> Funkce synonym je aktuálně ve verzi Preview a je podporovaná jenom v nejnovějších verzích Preview rozhraní API a sady SDK(api-version=2016-09-01-Preview, SDK verze 4.x-preview). Podpora webu Azure Portal se v současnosti neposkytuje. Na rozhraní API ve verzi Preview se nevztahuje žádná smlouva SLA a funkce se také mohou změnit, proto je nedoporučujeme používat v produkčních aplikacích.

## <a name="prerequisites"></a>Požadavky

Požadavky kurzu zahrnují tyto položky:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Služba Azure Search](search-create-service-portal.md)
* [Verze Preview knihovny Microsoft.Azure.Search .NET](https://aka.ms/search-sdk-preview)
* [Jak používat Azure Search z aplikace .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Přehled

Dotazy před a po ukazují význam použití synonym. V tomto kurzu používáme ukázkovou aplikaci, která spustí dotazy a vrátí výsledky v ukázkovém indexu. Ukázková aplikace vytvoří malý index s názvem hotels naplněný dvěma dokumenty. Aplikace spustí vyhledávací dotazy s využitím termínů a frází, které nejsou v indexu uvedené, povolí funkci synonym a potom znovu provede stejné hledání. Uvedený kód ukazuje celkový tok.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
Postup vytvoření a naplnění ukázkového indexu jsou vysvětlené v tématu [Jak používat Azure Search z aplikace .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

## <a name="before-queries"></a>Dotazy „před“

V `RunQueriesWithNonExistentTermsInIndex` jsme vydali vyhledávací dotazy pro „five star“, „internet“ a „economy AND hotel“.
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Ani jeden z indexovaných dokumentů tyto termíny neobsahuje, proto z prvního volání `RunQueriesWithNonExistentTermsInIndex` dostáváme tento výstup.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Povolení synonym

Povolení synonyma je dvoustupňový proces. Nejdřív nadefinujeme a nahrajeme pravidla synonym a potom nakonfigurujeme pole pro jejich použití. Proces je popsaný v `UploadSynonyms` a `EnableSynonymsInHotelsIndex`.

1. Přidejte mapu synonym k vaší vyhledávací službě. V `UploadSynonyms` definujeme čtyři pravidla v mapě synonym desc-synonymmap a nahrajeme je do služby.
```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
```
Mapa synonym musí odpovídat opensourcovému standardnímu formátu `solr`. Tento formát je vysvětlený v tématu [Synonyma ve službě Azure Search](search-synonyms.md) v části `Apache Solr synonym format`.

2. Nakonfigurujte prohledávatelná pole tak, aby používala mapu synonym v definici indexu. V `EnableSynonymsInHotelsIndex` povolíme synonyma u dvou polí `category` a `tags` nastavením vlastnosti `synonymMaps` na název nově nahrané mapy synonym.
```csharp
  Index index = serviceClient.Indexes.Get("hotels");
  index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
  index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

  serviceClient.Indexes.CreateOrUpdate(index);
```
Po přidání mapy synonym není potřeba znovu sestavovat index. Mapu synonymum můžete přidat ke službě a potom změnit stávající definice pole v libovolném indexu tak, aby tuto novou mapu synonym používala. Přidání nových atributů nemá žádný vliv na dostupnost indexu. To samé platí i pro zákaz synonym u pole. Stačí nastavit vlastnost `synonymMaps` na prázdný seznam.
```csharp
  index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
```

## <a name="after-queries"></a>Dotazy „po“

Po nahrání mapy synonym a aktualizaci indexu druhé volání `RunQueriesWithNonExistentTermsInIndex` vrátí tento výstup:

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
První dotaz najde dokument z pravidla `five star=>luxury`. Druhý dotaz rozšíří vyhledávání pomocí `internet,wifi` a třetí použije k vyhledání shody v dokumentech `hotel, motel` i `economy,inexpensive=>budget`.

Přidání synonym úplně mění možnosti vyhledávání. Původním dotazům v tomto kurzu se nepodařilo vrátit smysluplné výsledky, i když dokumenty v indexu byly relevantní. Povolením synonym můžeme rozšířit index tak, aby zahrnoval běžně používané termíny, a nemusíme přitom nijak upravovat podkladová data v indexu.

## <a name="sample-application-source-code"></a>Zdrojový kód ukázkové aplikace
Úplný zdrojový kód ukázkové aplikace použité v tomto názorném postupu najdete na [Githubu](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="next-steps"></a>Další kroky

* Projděte si téma věnované [použití synonym ve službě Azure Search](search-synonyms.md).
* Projděte si [dokumentaci k rozhraní REST API pro synonyma](https://aka.ms/rgm6rq).
* Projděte si referenční materiály pro [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) a [REST API](https://docs.microsoft.com/rest/api/searchservice/).
