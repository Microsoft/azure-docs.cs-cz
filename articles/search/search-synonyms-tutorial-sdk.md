---
title: Příklad synonym v C#
titleSuffix: Azure Cognitive Search
description: V tomto příkladu v jazyce C# se dozvíte, jak přidat funkci synonym do indexu v Azure Kognitivní hledání. Mapa synonym je seznam ekvivalentních termínů. Pole, která podporují synonymum, rozšiřují dotazy tak, aby zahrnovaly uživatelsky zadaný termín a všechna související synonyma.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 50d5d73e71b8129f061ec49b363a0ebb13d22bdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97704652"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Příklad: Přidání synonym pro Azure Kognitivní hledání v jazyce C #

Synonyma rozšiřují dotazy hledáním shody s termíny, které jsou považované za sémantické ekvivalenty vstupního výrazu. Chcete třeba, aby položce auto odpovídaly i dokumenty obsahující termíny automobil a vozidlo. 

V Azure Kognitivní hledání jsou synonyma definována v *mapě synonym* prostřednictvím *pravidel mapování* , která přidružuje ekvivalentní podmínky. Tento příklad popisuje základní kroky pro přidání a používání synonym s existujícím indexem.

V tomto příkladu se naučíte:

> [!div class="checklist"]
> * Vytvořte mapu synonym pomocí [třídy SynonymMap](/dotnet/api/azure.search.documents.indexes.models.synonymmap). 
> * Nastavte [vlastnost SynonymMapsName](/dotnet/api/azure.search.documents.indexes.models.searchfield.synonymmapnames) pro pole, která by měla podporovat rozbalování dotazů prostřednictvím synonym.

Můžete zadat dotaz na pole s povoleným synonymem, stejně jako normálně. Pro přístup k synonymům není nutná žádná další syntaxe dotazů.

Můžete vytvořit několik map synonym, zveřejnit je jako prostředky na úrovni služby dostupné pro všechny indexy a potom určit, který se má použít na úrovni pole. V době dotazu se kromě hledání indexu v Azure Kognitivní hledání provádí vyhledávání v mapě synonym, pokud je jedna zadaná u polí použitých v dotazu.

> [!NOTE]
> Synonyma lze vytvořit programově, ale ne na portálu.

## <a name="prerequisites"></a>Požadavky

Požadavky kurzu zahrnují tyto položky:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Služba Azure Kognitivní hledání](search-create-service-portal.md)
* [ Balíček umentsAzure.Search.Doc](https://www.nuget.org/packages/Azure.Search.Documents/)

Pokud nejste obeznámeni s klientskou knihovnou .NET, přečtěte si téma [Jak používat Azure kognitivní hledání v .NET](search-howto-dotnet-sdk.md).

## <a name="sample-code"></a>Ukázka kódu

Úplný zdrojový kód ukázkové aplikace použité v tomto příkladu najdete na [GitHubu](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="overview"></a>Přehled

Dotazy před a po slouží k předvedení hodnoty synonym. V tomto příkladu ukázková aplikace spouští dotazy a vrací výsledky pro ukázkový index "hotely" naplněný dvěma dokumenty. Nejprve aplikace provede vyhledávací dotazy pomocí výrazů a frází, které nejsou uvedeny v indexu. Za druhé kód umožňuje funkci synonym a pak znovu vystavuje stejné dotazy a tentokrát vrací výsledky na základě shody v mapě synonym. 

Uvedený kód ukazuje celkový tok.

```csharp
static void Main(string[] args)
{
   SearchIndexClient indexClient = CreateSearchIndexClient();

   Console.WriteLine("Cleaning up resources...\n");
   CleanupResources(indexClient);

   Console.WriteLine("Creating index...\n");
   CreateHotelsIndex(indexClient);

   SearchClient searchClient = indexClient.GetSearchClient("hotels");

   Console.WriteLine("Uploading documents...\n");
   UploadDocuments(searchClient);

   SearchClient searchClientForQueries = CreateSearchClientForQueries();

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Adding synonyms...\n");
   UploadSynonyms(indexClient);

   Console.WriteLine("Enabling synonyms in the test index...\n");
   EnableSynonymsInHotelsIndexSafely(indexClient);
   Thread.Sleep(10000); // Wait for the changes to propagate

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Complete.  Press any key to end application...\n");

   Console.ReadKey();
}
```

## <a name="before-queries"></a>Dotazy „před“

V `RunQueriesWithNonExistentTermsInIndex` uveďte vyhledávací dotazy pro „five star“, „internet“ a „economy AND hotel“.

Fráze dotazování, jako je "pět hvězdiček", musí být uzavřeny v uvozovkách a mohou také vyžadovat řídicí znaky v závislosti na vašem klientovi.

```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = searchClient.Search<Hotel>("\"five star\"", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = searchClient.Search<Hotel>("internet", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = searchClient.Search<Hotel>("economy AND hotel", searchOptions);
WriteDocuments(results);
```

Ani jeden z těchto dvou indexovaných dokumentů tyto výrazy neobsahuje, takže od prvního získáme následující výstup `RunQueriesWithNonExistentTermsInIndex` :  **žádný dokument se neshoduje**.

## <a name="enable-synonyms"></a>Povolení synonym

Po spuštění dotazů "před" se v ukázkovém kódu povolí synonyma. Povolení synonyma je dvoustupňový proces. Nejdřív definujte a nahrajte pravidla synonym. Za druhé nakonfigurujte pole, která se mají použít. Proces je popsaný v `UploadSynonyms` a `EnableSynonymsInHotelsIndex`.

1. Přidejte mapu synonym k vaší vyhledávací službě. V `UploadSynonyms` definujeme čtyři pravidla v mapě synonym desc-synonymmap a nahrajeme je do služby.

   ```csharp
   private static void UploadSynonyms(SearchIndexClient indexClient)
   {
      var synonymMap = new SynonymMap("desc-synonymmap", "hotel, motel\ninternet,wifi\nfive star=>luxury\neconomy,inexpensive=>budget");

      indexClient.CreateOrUpdateSynonymMap(synonymMap);
   }
   ```

1. Nakonfigurujte prohledávatelná pole tak, aby používala mapu synonym v definici indexu. V `AddSynonymMapsToFields` povolíme synonyma u dvou polí `category` a `tags` nastavením vlastnosti `SynonymMapNames` na název nově nahrané mapy synonym.

   ```csharp
   private static SearchIndex AddSynonymMapsToFields(SearchIndex index)
   {
      index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
      index.Fields.First(f => f.Name == "tags").SynonymMapNames.Add("desc-synonymmap");
      return index;
   }
   ```

   Po přidání mapy synonym není potřeba znovu sestavovat index. Mapu synonymum můžete přidat ke službě a potom změnit stávající definice pole v libovolném indexu tak, aby tuto novou mapu synonym používala. Přidání nových atributů nemá žádný vliv na dostupnost indexu. To samé platí i pro zákaz synonym u pole. Stačí nastavit vlastnost `SynonymMapNames` na prázdný seznam.

   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
   ```

## <a name="after-queries"></a>Dotazy „po“

Po nahrání mapy synonym a aktualizaci indexu druhé volání `RunQueriesWithNonExistentTermsInIndex` vrátí tento výstup:

```dos
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```

První dotaz najde dokument z pravidla `five star=>luxury`. Druhý dotaz rozšíří vyhledávání pomocí `internet,wifi` a třetí použije k vyhledání shody v dokumentech `hotel, motel` i `economy,inexpensive=>budget`.

Přidání synonym úplně mění možnosti vyhledávání. V tomto příkladu se v původních dotazech nevrátily smysluplné výsledky, i když by dokumenty v našem rejstříku byly relevantní. Povolením synonym můžeme rozšířit index tak, aby zahrnoval běžně používané termíny, a nemusíme přitom nijak upravovat podkladová data v indexu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejší způsob, jak vyčistit po příkladech, odstraněním skupiny prostředků obsahující službu Azure Kognitivní hledání. Odstraněním skupiny prostředků teď můžete trvale odstranit všechno, co se v ní nachází. Na portálu je název skupiny prostředků na stránce Přehled služby Azure Kognitivní hledání.

## <a name="next-steps"></a>Další kroky

Tento příklad ukázal funkci synonym v kódu jazyka C# k vytvoření a odeslání pravidel mapování a volání mapy synonym na dotaz. Další informace najdete v referenční dokumentaci [sady .NET SDK](/dotnet/api/overview/azure/search.documents-readme) a [rozhraní REST API](/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Jak používat synonyma v Azure Kognitivní hledání](search-synonyms.md)