---
title: Příklad synonym jazyka C#
titleSuffix: Azure Cognitive Search
description: V tomto příkladu v jazyce C# se dozvíte, jak přidat funkci synonym do indexu v Azure Kognitivní hledání. Mapa synonym je seznam ekvivalentních termínů. Pole, která podporují synonymum, rozšiřují dotazy tak, aby zahrnovaly uživatelsky zadaný termín a všechna související synonyma.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 842d43c82875a1a8e5e45ba14f47ceb6eac26727
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91538802"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Příklad: Přidání synonym pro Azure Kognitivní hledání v jazyce C #

Synonyma rozšiřují dotazy hledáním shody s termíny, které jsou považované za sémantické ekvivalenty vstupního výrazu. Chcete třeba, aby položce auto odpovídaly i dokumenty obsahující termíny automobil a vozidlo. 

V Azure Kognitivní hledání jsou synonyma definována v *mapě synonym*prostřednictvím *pravidel mapování* , která přidružuje ekvivalentní podmínky. Tento příklad popisuje základní kroky pro přidání a používání synonym s existujícím indexem. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte mapu synonym pomocí třídy  [SynonymMap](/dotnet/api/microsoft.azure.search.models.synonymmap) . 
> * Nastavte vlastnost [SynonymMaps](/dotnet/api/microsoft.azure.search.models.field.synonymmaps) pro pole, která by měla podporovat rozbalování dotazů prostřednictvím synonym.

Můžete zadat dotaz na pole s povoleným synonymem, stejně jako normálně. Pro přístup k synonymům není nutná žádná další syntaxe dotazů.

Můžete vytvořit několik map synonym, zveřejnit je jako prostředky na úrovni služby dostupné pro všechny indexy a potom určit, který se má použít na úrovni pole. V době dotazu se kromě hledání indexu v Azure Kognitivní hledání provádí vyhledávání v mapě synonym, pokud je jedna zadaná u polí použitých v dotazu.

> [!NOTE]
> Synonyma lze vytvořit programově, ale ne na portálu. Pokud byste uvítali podporu synonym na portálu Azure Portal, sdělte nám svůj názor na webu [UserVoice](https://feedback.azure.com/forums/263029-azure-search)

## <a name="prerequisites"></a>Požadavky

Požadavky kurzu zahrnují tyto položky:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Služba Azure Kognitivní hledání](search-create-service-portal.md)
* [Knihovna Microsoft.Azure.Search .NET](/dotnet/api/overview/azure/search)
* [Jak používat Azure Kognitivní hledání z aplikace .NET](./search-howto-dotnet-sdk.md)

## <a name="overview"></a>Přehled

Dotazy před a po ukazují význam použití synonym. V tomto příkladu použijte ukázkovou aplikaci, která spustí dotazy a vrátí výsledky v ukázkovém indexu. Ukázková aplikace vytvoří malý index s názvem hotels naplněný dvěma dokumenty. Aplikace spustí vyhledávací dotazy s využitím termínů a frází, které nejsou v indexu uvedené, povolí funkci synonym a potom znovu provede stejné hledání. Uvedený kód ukazuje celkový tok.

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
Postup vytvoření a naplnění ukázkového indexu je vysvětlen v tématu [Jak používat Azure kognitivní hledání z aplikace .NET](./search-howto-dotnet-sdk.md).

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
```
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
```

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
   Mapa synonym musí odpovídat opensourcovému standardnímu formátu `solr`. Formát je vysvětlen v tématu [synonyma v Azure kognitivní hledání](search-synonyms.md) v části `Apache Solr synonym format` .

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

```
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```
První dotaz najde dokument z pravidla `five star=>luxury`. Druhý dotaz rozšíří vyhledávání pomocí `internet,wifi` a třetí použije k vyhledání shody v dokumentech `hotel, motel` i `economy,inexpensive=>budget`.

Přidání synonym úplně mění možnosti vyhledávání. V tomto příkladu se v původních dotazech nevrátily smysluplné výsledky, i když by dokumenty v našem rejstříku byly relevantní. Povolením synonym můžeme rozšířit index tak, aby zahrnoval běžně používané termíny, a nemusíme přitom nijak upravovat podkladová data v indexu.

## <a name="sample-application-source-code"></a>Zdrojový kód ukázkové aplikace
Úplný zdrojový kód ukázkové aplikace použité v tomto názorném postupu najdete na [Githubu](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejší způsob, jak vyčistit po příkladech, odstraněním skupiny prostředků obsahující službu Azure Kognitivní hledání. Odstraněním skupiny prostředků teď můžete trvale odstranit všechno, co se v ní nachází. Na portálu je název skupiny prostředků na stránce Přehled služby Azure Kognitivní hledání.

## <a name="next-steps"></a>Další kroky

Tento příklad ukázal funkci synonym v kódu jazyka C# k vytvoření a odeslání pravidel mapování a volání mapy synonym na dotaz. Další informace najdete v referenční dokumentaci [sady .NET SDK](/dotnet/api/microsoft.azure.search) a [rozhraní REST API](/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Jak používat synonyma v Azure Kognitivní hledání](search-synonyms.md)