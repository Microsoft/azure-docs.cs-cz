---
title: Import a přijímání dat ve vyhledávacích indexech
titleSuffix: Azure Cognitive Search
description: Naplňte a nahrajte data do indexu v Azure Kognitivní hledání z externích zdrojů dat.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: aa44a27fa5bf6b7b4ea649e1a9b9a69ef8cd78d3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049317"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Přehled importu dat – Azure Kognitivní hledání

V Azure Kognitivní hledání se dotazy spustí přes obsah načtený do [indexu vyhledávání](search-what-is-an-index.md)a uloží se do něj. Tento článek pojednává o dvou základních přístupech k naplnění *indexu:* nahrajte data do indexu prostřednictvím kódu programu nebo nasměrováním služby [Azure kognitivní hledání indexerem](search-indexer-overview.md) v podporovaném zdroji dat, aby se data mohla *načíst* .

V obou případech je cílem načíst data z externího zdroje dat do indexu služby Azure Kognitivní hledání. Azure Kognitivní hledání vám umožní vytvořit prázdný index, ale dokud do něj nevložíte nebo nenačtete data, nebude se Queryable.

> [!NOTE]
> Pokud je [rozšíření AI](cognitive-search-concept-intro.md) požadavkem na řešení, musíte k načtení indexu použít model pull (indexery). Externí zpracování se podporuje jenom prostřednictvím dovednosti připojených ke indexeru.

## <a name="pushing-data-to-an-index"></a>Nabídka dat do indexu

Model nabízených oznámení, který se používá k programovému odeslání vašich dat do Azure Kognitivní hledání, představuje nejpružnější přístup. Za prvé u něj neplatí žádná omezení týkající se typu zdroje dat. Do indexu služby Azure Kognitivní hledání lze vložit jakoukoli datovou sadu složenou z dokumentů JSON. za předpokladu, že každý dokument v datové sadě má pole mapování na pole definovaná ve schématu indexu. Za druhé u něj neplatí žádná omezení týkající se četnosti provádění. Do indexu můžete nabízet změny, jak často chcete. U aplikací vyžadujících velmi nízkou latenci (např. když potřebujete, aby operace hledání byly synchronizované s dynamickými databázemi zásob) je model Push vaší jedinou možností.

Tento přístup je flexibilnější než model Pull, protože můžete nahrávat dokumenty samostatně nebo v dávkách (jednotlivé dávky můžou obsahovat až 1000 dokumentů nebo 16 MB, podle toho, kterého omezení dosáhnete dříve). Model nabízených oznámení také umožňuje odeslat dokumenty do Azure Kognitivní hledání bez ohledu na to, kde jsou vaše data.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Jak nabízet data do indexu Azure Kognitivní hledání

Pomocí následujících rozhraní API můžete do indexu načíst jeden nebo několik dokumentů:

+ [Přidávání, aktualizace a odstraňování dokumentů (REST API)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ Třída [IndexDocumentsAction](/dotnet/api/azure.search.documents.models.indexdocumentsaction) nebo [Třída IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) 

Vkládání dat prostřednictvím portálu není aktuálně podporováno.

Úvod ke každé metodologii najdete v tématu [rychlý Start: vytvoření indexu služby azure kognitivní hledání pomocí prostředí PowerShell](./search-get-started-powershell.md) nebo [jazyka C# Start: vytvoření indexu Azure kognitivní hledání pomocí sady .NET SDK](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Akce indexování: nahrání, sloučení, mergeOrUpload, odstranění

Můžete řídit typ akce indexování pro jednotlivé dokumenty a určit, jestli se má dokument nahrát úplně, sloučit s existujícím obsahem dokumentu nebo odstranit.

V REST API vystavte žádosti HTTP POST s texty požadavku JSON na adresu URL koncového bodu indexu služby Azure Kognitivní hledání. Každý objekt JSON v poli "value" obsahuje klíč dokumentu a určuje, zda má akce indexování přidat, aktualizovat nebo odstranit obsah dokumentu. Příklad kódu naleznete v tématu [Load Documents](search-get-started-dotnet.md#load-documents).

V sadě .NET SDK vytvořte balíček dat do `IndexBatch` objektu. `IndexBatch`Zapouzdřuje kolekci `IndexAction` objektů, z nichž každý obsahuje dokument a vlastnost, která oznamuje službě Azure kognitivní hledání, jakou akci má tento dokument dělat. Příklad kódu naleznete v tématu [rychlý Start pro C#](search-get-started-dotnet.md).


| @search.action | Popis | Potřebná pole pro každý dokument | Poznámky |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Akce `upload` je podobná akci „upsert“, kdy je dokument vložený, pokud je nový a aktualizovaný nebo nahrazený, pokud již existuje. |klíč a další pole, která si přejete definovat |Pokud aktualizujete nebo nahrazujete stávající dokument, bude každé pole, které není zadané v žádosti, nastavené na `null`. K tomu dojde i v případě, že bylo pole dříve nastavené na nenulovou hodnotu. |
| `merge` |Aktualizuje stávající dokument se zadanými poli. Pokud dokument v indexu neexistuje, sloučení selže. |klíč a další pole, která si přejete definovat |Každé pole zadané ve sloučení nahradí stávající pole v dokumentu. V sadě .NET SDK obsahuje pole typu `DataType.Collection(DataType.String)` . V REST API obsahuje pole typu `Collection(Edm.String)` . Například pokud dokument obsahuje pole `tags` s hodnotou `["budget"]` a vy spustíte sloučení s polem `tags` s hodnotou `["economy", "pool"]`, konečná hodnota pole `tags` bude `["economy", "pool"]`. Hodnota nebude `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Pokud již dokument s daným klíčem v indexu existuje, chová se tato akce jako `merge`. Pokud dokument neexistuje, chová se s novým dokumentem jako `upload`. |klíč a další pole, která si přejete definovat |- |
| `delete` |Odebere z indexu zadaný dokument. |pouze klíč |Všechna zadaná pole kromě pole klíče budou ignorována. Chcete-li odebrat z dokumentu jednotlivá pole, použijte místo toho `merge` a jednoduše nastavte hodnotu pole na „null“. |

### <a name="formulate-your-query"></a>Formulování dotazu

Existují dva způsoby [vyhledávání v indexu pomocí REST API](/rest/api/searchservice/Search-Documents). První způsob je vydání požadavku HTTP POST, kde parametry dotazu jsou určené v objektu JSON v textu požadavku. Druhý způsob je vydání požadavku HTTP GET, kde parametry dotazu jsou určené v rámci URL požadavku. Metoda POST má [mírnější omezení](/rest/api/searchservice/Search-Documents) velikosti parametrů dotazu než metoda GET. Z tohoto důvodu doporučujeme používat metodu POST, pokud pro vás neplatí zvláštní podmínky, kdy by bylo pohodlnější použití metody GET.

Pro POST i GET musíte zadat *název služby*, *název indexu* a *verzi rozhraní API* v adrese URL požadavku. 

U metody GET zadáte parametry dotazu v rámci *řetězce dotazu* na konci adresy URL. Formát URL vidíte níže:

```http
    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06
```

Formát pro POST je stejný, ale `api-version` v parametrech řetězce dotazu.

## <a name="pulling-data-into-an-index"></a>Přetáhnutí dat do indexu

Model Pull prochází podporovaný zdroj dat a automaticky nahrává data do vašeho indexu. V Azure Kognitivní hledání je tato schopnost implementovaná prostřednictvím *indexerů*, které jsou aktuálně dostupné pro tyto platformy:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database, spravovaná instance SQL a SQL Server na virtuálních počítačích Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SharePoint Online (Preview)](search-howto-index-sharepoint-online.md)
+ [Azure Data Lake Storage Gen2 (Preview)](search-howto-index-azure-data-lake-storage.md)

Indexery propojují index se zdrojem dat (obvykle tabulka, zobrazení nebo ekvivalentní struktura) a mapují pole zdroje na odpovídající pole v indexu. Během provádění je sada řádků automaticky převedena na formát JSON a načtena do určeného indexu. Všechny indexery podporují plánování, takže můžete určit, jak často se data budou aktualizovat. Většina indexerů umožňuje sledování změn dat, pokud ho zdroj dat podporuje. Indexery sledují změny a odstranění ve stávajících dokumentech a rozpoznávají nové dokumenty, a díky tomu není potřeba aktivně spravovat data v indexu.

### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Jak načíst data do indexu služby Azure Kognitivní hledání

Funkce indexeru jsou přístupné pomocí webu [Azure Portal](search-import-data-portal.md), rozhraní [REST API](/rest/api/searchservice/Indexer-operations) a sady [.NET SDK](/dotnet/api/azure.search.documents.indexes.searchindexerclient).

Výhodou používání portálu je, že Azure Kognitivní hledání může obvykle vygenerovat výchozí schéma indexu pro vás tím, že si přečte metadata zdrojové datové sady. Vygenerovaný index můžete upravit až do zpracování indexu. Poté jsou povoleny jen takové změny schématu, které nevyžadují přeindexování. Pokud provedené změny přímo ovlivní schéma indexu, bude nutné index znovu sestavit. 

## <a name="verify-data-import-with-search-explorer"></a>Ověření importu dat pomocí Průzkumníka vyhledávání

Rychlý způsob, jak provést předběžnou kontrolu při nahrávání dokumentu, je použití **Průzkumníka vyhledávání** na portálu. Průzkumníka můžete použít k zadávání dotazů na index, aniž byste museli programovat. Možnosti vyhledávání jsou založené na výchozích nastaveních, jako je [Jednoduchá syntaxe](/rest/api/searchservice/simple-query-syntax-in-azure-search) a výchozí [parametr dotazu searchMode](/rest/api/searchservice/search-documents). Výsledky jsou vráceny ve formátu JSON, abyste si mohli prohlédnout celý dokument.

> [!TIP]
> Řada [ukázek kódu Azure kognitivní hledání](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) zahrnuje vložené nebo snadno dostupné datové sady, které nabízejí snadný způsob, jak začít. Na portálu také najdete ukázkový indexer a zdroj dat, obsahující datovou sadu malé realitní kanceláře (s názvem realestate-us-sample). Když spustíte předem nakonfigurovaný indexer na vzorovém zdroji dat, vytvoří se index a načte se do dokumentů, na které se pak můžete dotazovat v Průzkumníku služby Search nebo pomocí kódu, který zapisujete.

## <a name="see-also"></a>Viz také

+ [Přehled indexeru](search-indexer-overview.md)
+ [Průvodce portálem: vytvoření, načtení a dotazování indexu](search-get-started-portal.md)