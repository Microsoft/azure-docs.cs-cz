---
title: Synonyma C# příklad
titleSuffix: Azure Cognitive Search
description: V tomto příkladu Jazyka C# se dozvíte, jak přidat funkci synonym do indexu v Azure Cognitive Search. Mapa synonym je seznam ekvivalentních termínů. Pole se synonymem podporují rozšíření dotazů tak, aby zahrnovaly termín poskytnutý uživatelem a všechna související synonyma.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8cc085fd27004928babd7df305a4452d1b068f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794239"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Příklad: Přidání synonym pro Azure Cognitive Search v C #

Synonyma rozšiřují dotazy hledáním shody s termíny, které jsou považované za sémantické ekvivalenty vstupního výrazu. Chcete třeba, aby položce auto odpovídaly i dokumenty obsahující termíny automobil a vozidlo. 

V Azure Cognitive Search synonyma jsou definovány v *mapě synonyma*, prostřednictvím *mapování pravidel,* která přidruží ekvivalentní termíny. Tento příklad popisuje základní kroky pro přidávání a používání synonym s existující index. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte mapu synonym pomocí třídy [SynonymMap.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap?view=azure-dotnet) 
> * Nastavte [vlastnost SynonymMaps](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.synonymmaps?view=azure-dotnet) na pole, která by měla podporovat rozšíření dotazu prostřednictvím synonym.

Můžete zadat dotaz na pole s podporou synonym a za normálních okolností. Pro přístup ke synonymům není vyžadována žádná další syntaxe dotazu.

Můžete vytvořit několik map synonym, zveřejnit je jako prostředky na úrovni služby dostupné pro všechny indexy a potom určit, který se má použít na úrovni pole. V době dotazu, kromě vyhledávání indexu Azure Cognitive Search provádí vyhledávání v mapě synonym, pokud je zadán na pole použitá v dotazu.

> [!NOTE]
> Synonyma lze vytvořit programově, ale ne na portálu. Pokud byste uvítali podporu synonym na portálu Azure Portal, sdělte nám svůj názor na webu [UserVoice](https://feedback.azure.com/forums/263029-azure-search)

## <a name="prerequisites"></a>Požadavky

Požadavky kurzu zahrnují tyto položky:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Služba azure kognitivního vyhledávání](search-create-service-portal.md)
* [Knihovna Microsoft.Azure.Search .NET](https://aka.ms/search-sdk)
* [Jak používat Azure Cognitive Search z aplikace .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Přehled

Dotazy před a po ukazují význam použití synonym. V tomto příkladu použijte ukázkovou aplikaci, která provádí dotazy a vrací výsledky na ukázkový index. Ukázková aplikace vytvoří malý index s názvem hotels naplněný dvěma dokumenty. Aplikace spustí vyhledávací dotazy s využitím termínů a frází, které nejsou v indexu uvedené, povolí funkci synonym a potom znovu provede stejné hledání. Uvedený kód ukazuje celkový tok.

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
Kroky k vytvoření a naplnění ukázkového indexu jsou vysvětleny v [části Použití azure kognitivního vyhledávání z aplikace .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

## <a name="before-queries"></a>Dotazy „před“

V `RunQueriesWithNonExistentTermsInIndex` uveďte vyhledávací dotazy pro „five star“, „internet“ a „economy AND hotel“.
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
   Mapa synonym musí odpovídat opensourcovému standardnímu formátu `solr`. Formát je vysvětleno v [synonyma v](search-synonyms.md) `Apache Solr synonym format`Azure Cognitive Search v části .

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

Přidání synonym úplně mění možnosti vyhledávání. V tomto příkladu původní dotazy se nepodařilo vrátit smysluplné výsledky, i když dokumenty v našem indexu byly relevantní. Povolením synonym můžeme rozšířit index tak, aby zahrnoval běžně používané termíny, a nemusíme přitom nijak upravovat podkladová data v indexu.

## <a name="sample-application-source-code"></a>Zdrojový kód ukázkové aplikace
Úplný zdrojový kód ukázkové aplikace použité v tomto názorném postupu najdete na [Githubu](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejší způsob, jak vyčistit po příkladu je odstraněním skupiny prostředků obsahující službu Azure Cognitive Search. Odstraněním skupiny prostředků teď můžete trvale odstranit všechno, co se v ní nachází. Na portálu je název skupiny prostředků na stránce Přehled služby Azure Cognitive Search.

## <a name="next-steps"></a>Další kroky

Tento příklad demonstroval synonyma funkce v kódu Jazyka C# vytvořit a zaúčtovat pravidla mapování a potom volat mapu synonym a dotazu. Další informace najdete v referenční dokumentaci [sady .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) a [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Jak používat synonyma v Azure Cognitive Search](search-synonyms.md)
