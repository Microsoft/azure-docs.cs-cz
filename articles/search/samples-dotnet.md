---
title: Ukázky .NET
titleSuffix: Azure Cognitive Search
description: Najděte ukázky kódu v jazyce C# pro Azure Kognitivní hledání, které používají klientské knihovny .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 5567cf3bf606b08ce430f9189467d796498ae691
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953899"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Ukázky kódu .NET (C#) pro Azure Kognitivní hledání

Přečtěte si o ukázkách kódu v jazyce C#, které ukazují funkčnost a pracovní postup řešení Azure Kognitivní hledání. Tyto ukázky používají [**klientskou knihovnu azure kognitivní hledání**](/dotnet/api/overview/azure/search) pro [**sadu Azure SDK pro .NET**](/dotnet/azure/), kterou můžete prozkoumat pomocí následujících odkazů.

| Cíl | Odkaz |
|--------|------|
| Stažení balíčku | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| API – referenční informace | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| Testovací případy rozhraní API | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/testy](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| Zdrojový kód | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>Ukázky sady SDK

Ukázky kódu z vývojového týmu Azure SDK ukazují použití rozhraní API. Tyto ukázky najdete v [**Azure/Azure-SDK-for-NET/Tree/Master/SDK/Search/Azure.Search.Documents/Samples**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) na GitHubu.

| ukázky | Description |
|---------|-------------|
| ["Hello World", synchronně](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Ukazuje, jak vytvořit klienta, ověřit a zpracovat chyby pomocí synchronních metod.|
| ["Hello World", asynchronně](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Ukazuje, jak vytvořit klienta, ověřit a zpracovat chyby pomocí asynchronních metod.  |
| [Operace na úrovni služby](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Ukazuje, jak vytvořit indexy, indexery, zdroje dat, dovednosti a mapování synonym. Tato ukázka také ukazuje, jak získat statistiku služby a postup dotazování indexu.  |
| [Operace indexu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Ukazuje, jak provést akci s existujícím indexem, v tomto případě pro získání počtu dokumentů uložených v indexu.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Ukazuje techniku pro práci s nepodporovanými datovými typy.  |
| [Indexování dokumentů (model nabízených oznámení)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Indexování modelů "push", kde odesíláte datovou část JSON do indexu ve službě.   |
| [Ukázka šifrovacího klíče](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Ukazuje použití šifrovacího klíče spravovaného zákazníkem k přidání další vrstvy ochrany nad citlivý obsah.  |

## <a name="doc-samples"></a>Ukázky dokumentů

Ukázky kódu z týmu Kognitivní hledání ukazují funkce a pracovní postupy. Mnohé z těchto ukázek jsou odkazovány v kurzech, rychlých startech a článcích s postupy. Tyto ukázky najdete v článku [**Azure-Samples/Azure-Search-dotnet-Samples**](https://github.com/Azure-Samples/azure-search-dotnet-samples) a v [**Azure-Samples/Search-dotnet-Začínáme**](https://github.com/Azure-Samples/search-dotnet-getting-started/) na GitHubu.

| ukázky | Článek  |
|---------|-------------|
| [rychlý Start](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Zdrojový kód pro [rychlý Start: vytvoření indexu vyhledávání ](search-get-started-dotnet.md). Tento článek popisuje základní pracovní postup pro vytvoření, načtení a dotazování indexu vyhledávání pomocí ukázkových dat. |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Zdrojový kód pro [použití klientské knihovny .NET](search-howto-dotnet-sdk.md). Tento článek popisuje základní pracovní postup, ale podrobněji a diskuzi o využití rozhraní API.  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Zdrojový kód [například: přidejte synonyma v jazyce C#](search-synonyms-tutorial-sdk.md). Seznamy synonym se používají pro rozšíření dotazů a poskytují shodné výrazy, které jsou pro index externí. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Zdrojový kód pro [kurz: indexování dat Azure SQL pomocí sady .NET SDK](search-indexer-tutorial.md). Tento článek ukazuje, jak nakonfigurovat indexer Azure SQL, který má plán, mapování polí a parametry.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Zdrojový kód pro [konfiguraci klíčů spravovaných zákazníkem pro šifrování dat](search-security-manage-encryption-keys.md). |
| [Vytvoření první aplikace v jazyce C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Zdrojový kód pro [kurz: Vytvoření první aplikace pro hledání](tutorial-csharp-create-first-app.md). I když je většina ukázek konzolové aplikace, Tato ukázka MVC používá webovou stránku k překrytí ukázkového indexu hotelů, který demonstruje základní vyhledávání, stránkování, automatické dokončování a navrhované dotazy, charakteristiky a filtry. |
| [zdroje s více zdroji dat](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Zdrojový kód pro [kurz: index z více zdrojů dat](tutorial-multiple-data-sources.md). |
|  [Optimalizace – indexování dat](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Zdrojový kód pro [kurz: Optimalizujte indexování pomocí rozhraní API push](tutorial-optimize-indexing-push-api.md).  |
| [Kurz – rozšíření AI](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Zdrojový kód pro [kurz: prohledávatelný obsah GENEROVANÝ AI z objektů blob Azure pomocí sady .NET SDK](cognitive-search-tutorial-blob-dotnet.md).  |

> [!Tip]
> Vyzkoušejte si [prohlížeč ukázek](/samples/browse/?languages=csharp&products=azure-cognitive-search) , kde najdete ukázky kódu Microsoftu v GitHubu, filtrované podle produktu, služby a jazyka.

## <a name="other-samples"></a>Další ukázky

Následující ukázky jsou publikovány také týmem Kognitivní hledání, ale nejsou odkazovány v dokumentaci. Přidružené soubory Readme poskytují pokyny k používání.

| ukázky | Description |
|---------|-------------|
| [Azure – vyhledávání – dovednosti](https://github.com/Azure-Samples/azure-search-power-skills)  | Zdrojový kód pro spotřební vlastní dovednosti, které můžete začlenit do získaných řešení.  |
| [Akcelerátor řešení pro dolování znalostí](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Obsahuje šablony, podpůrné soubory a analytické sestavy, které vám pomůžou s prototypem uceleného řešení dolování.  |
| [Úložiště vyhledávacích aplikací Covid-19](https://github.com/liamca/covid19search) | Úložiště zdrojového kódu pro [vyhledávací aplikaci Covidou](https://covid19search.azurewebsites.net/) založené na kognitivní hledání – 19 |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Přečtěte si další informace o [řešení JFK](https://www.microsoft.com/ai/ai-lab-jfk-files). |