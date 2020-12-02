---
title: Ukázky .NET
titleSuffix: Azure Cognitive Search
description: Najděte ukázky kódu v jazyce C# pro Azure Kognitivní hledání, které používají klientské knihovny .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: ab6408621616a4be62631391456f73e90fced752
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498995"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Ukázky kódu .NET (C#) pro Azure Kognitivní hledání

Přečtěte si o ukázkách kódu v jazyce C#, které ukazují funkce a funkce Azure Kognitivní hledání. Primární úložiště jsou následující:

| Repository | Popis |
|------------|-------------|
| [Azure-SDK – for-NET/SDK/Search/Azure.Search.Documents/Samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | Ukázky vytvářené týmem Azure SDK, které se dodávají s klientskou knihovnou Azure.Search.Documents v sadě SDK Můžete si také projít [testy jednotek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) klientské knihovny, abyste viděli, jak jsou volána různá rozhraní API. |
| [Azure-Samples/Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) | Ukázky, které jsou přiložené k článkům v dokumentaci, včetně [toho, jak používat klientskou knihovnu .NET](search-howto-dotnet-sdk.md).|
| [Azure-Samples/Search-dotnet – Začínáme](https://github.com/Azure-Samples/search-dotnet-getting-started) | Ukázky, které doprovázejí rychlé starty a kurzy v dokumentaci.|

> [!Tip]
> Vyzkoušejte si [prohlížeč ukázek](/samples/browse/?languages=csharp&products=azure-cognitive-search) , kde najdete ukázky kódu Microsoftu v GitHubu, filtrované podle produktu, služby a jazyka.

## <a name="net-sdk-samples"></a>Ukázky .NET SDK

Sada Azure SDK pro .NET zahrnuje řadu ukázek a [ukázek Readme](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md) , které popisují každé z nich. Tento seznam je k dispozici pro vaše pohodlí.

| ukázky | Popis |
|---------|-------------|
| ["Hello World", synchronně](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Ukazuje, jak vytvořit klienta, ověřit a zpracovat chyby pomocí synchronních metod.|
| ["Hello World", asynchronně](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Ukazuje, jak vytvořit klienta, ověřit a zpracovat chyby pomocí asynchronních metod.  |
| [Operace na úrovni služby](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Ukazuje, jak vytvořit indexy, indexery, zdroje dat, dovednosti a mapování synonym. Tato ukázka také ukazuje, jak získat statistiku služby a postup dotazování indexu.  |
| [Operace indexu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Ukazuje, jak provést akci s existujícím indexem, v tomto případě pro získání počtu dokumentů uložených v indexu.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Ukazuje techniku pro práci s nepodporovanými datovými typy.  |
| [Indexování dokumentů (model nabízených oznámení)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Indexování modelů "push", kde odesíláte datovou část JSON do indexu ve službě.   |
| [Ukázka šifrovacího klíče](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Ukazuje použití šifrovacího klíče spravovaného zákazníkem k přidání další vrstvy ochrany nad citlivý obsah.  |

## <a name="documentation-samples"></a>Ukázky v dokumentaci

Následující ukázky mají přidružený článek v [dokumentaci k Azure kognitivní hledání](./index.yml).

| ukázky | Popis |
|---------|-------------|
| [rychlý Start](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Zdrojový kód pro [rychlý Start: vytvoření indexu vyhledávání ](search-get-started-dotnet.md).  |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Zdrojový kód pro [použití klientské knihovny .NET](search-howto-dotnet-sdk.md) |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Seznamy synonym se používají pro rozšíření dotazů a poskytují shodné výrazy, které jsou pro index externí. Tato ukázka je obsažena v [příkladu: Přidání synonym v jazyce C#](search-synonyms-tutorial-sdk.md). |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Zdrojový kód za fragmenty související s indexerem v různých článcích. Tento příklad ukazuje, jak nakonfigurovat indexer, který má plán, mapování polí a parametry.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Zdrojový kód pro [konfiguraci klíčů spravovaných zákazníkem pro šifrování dat](search-security-manage-encryption-keys.md) |
| [Vytvoření první aplikace v jazyce C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Zdrojový kód pro [kurz: Vytvoření první aplikace pro hledání](tutorial-csharp-create-first-app.md). I když je většina ukázek konzolové aplikace, Tato ukázka MVC používá webovou stránku k překrytí ukázkového indexu hotelů, který demonstruje základní vyhledávání, stránkování, automatické dokončování a navrhované dotazy, charakteristiky a filtry. |
| [zdroje s více zdroji dat](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Zdrojový kód pro [kurz: index z více zdrojů dat](tutorial-multiple-data-sources.md). |
|  [Optimalizace – indexování dat](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Zdrojový kód pro [kurz: Optimalizujte indexování pomocí rozhraní API push](tutorial-optimize-indexing-push-api.md).  |
| [Kurz – rozšíření AI](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Zdrojový kód pro [kurz: prohledávatelný obsah GENEROVANÝ AI z objektů blob Azure pomocí sady .NET SDK](cognitive-search-tutorial-blob-dotnet.md).  |

## <a name="standalone-samples-and-solutions"></a>Samostatné ukázky a řešení

| ukázky | Popis |
|---------|-------------|
| [Azure – vyhledávání – dovednosti](https://github.com/Azure-Samples/azure-search-power-skills)  | Zdrojový kód pro spotřební vlastní dovednosti, které můžete začlenit do získaných řešení.  |
| [Akcelerátor řešení pro dolování znalostí](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Obsahuje šablony, podpůrné soubory a analytické sestavy, které vám pomůžou s prototypem uceleného řešení dolování.  |
| [Úložiště vyhledávacích aplikací Covid-19](https://github.com/liamca/covid19search) | Úložiště zdrojového kódu pro [vyhledávací aplikaci Covidou](https://covid19search.azurewebsites.net/) založené na kognitivní hledání – 19 |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Přečtěte si další informace o [řešení JFK](https://www.microsoft.com/ai/ai-lab-jfk-files). |