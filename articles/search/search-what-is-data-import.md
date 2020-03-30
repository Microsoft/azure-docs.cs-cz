---
title: Import a přijím dat v indexech vyhledávání
titleSuffix: Azure Cognitive Search
description: Naplňte a nahrajte data do indexu v Azure Cognitive Search z externích zdrojů dat.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3f38e9bb96ce76263a3124f8bfdc49dc638bfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282754"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Přehled importu dat – Azure Cognitive Search

V Azure Cognitive Search se dotazy spouštějí přes obsah načtený do indexu vyhledávání a uložený v [indexu vyhledávání](search-what-is-an-index.md). Tento článek zkoumá dva základní přístupy pro vyplnění indexu: *nabízení* dat do indexu programově nebo bod [indexeru Azure Cognitive Search](search-indexer-overview.md) na podporovaný zdroj dat *pro vyžádat* data.

S oběma přístupy je cílem *načíst data* z externího zdroje dat do indexu Azure Cognitive Search. Azure Cognitive Search vám umožní vytvořit prázdný index, ale dokud do něj nestlačíte nebo nevytácíte data, není to dotazovatelné.

## <a name="pushing-data-to-an-index"></a>Nabídka dat do indexu
Model nabízených síly, který se používá k programovému odesílání dat do Azure Cognitive Search, je nejflexibilnější přístup. Za prvé u něj neplatí žádná omezení týkající se typu zdroje dat. Libovolná datová sada složená z dokumentů JSON může být posunuta do indexu Azure Cognitive Search za předpokladu, že každý dokument v datové sadě obsahuje mapování polí na pole definovaná ve schématu indexu. Za druhé u něj neplatí žádná omezení týkající se četnosti provádění. Do indexu můžete nabízet změny, jak často chcete. U aplikací vyžadujících velmi nízkou latenci (např. když potřebujete, aby operace hledání byly synchronizované s dynamickými databázemi zásob) je model Push vaší jedinou možností.

Tento přístup je flexibilnější než model Pull, protože můžete nahrávat dokumenty samostatně nebo v dávkách (jednotlivé dávky můžou obsahovat až 1000 dokumentů nebo 16 MB, podle toho, kterého omezení dosáhnete dříve). Model nabízených zpráv také umožňuje nahrát dokumenty do Azure Cognitive Search bez ohledu na to, kde jsou vaše data.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Jak nabízená data do indexu Azure Cognitive Search

Pomocí následujících rozhraní API můžete do indexu načíst jeden nebo několik dokumentů:

+ [Přidávání, aktualizace a odstraňování dokumentů (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Třída indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) nebo [třída indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Vkládání dat prostřednictvím portálu není aktuálně podporováno.

Úvod ke každé metodice najdete v [tématu Úvodní příručka: Vytvoření indexu Azure Cognitive Search pomocí PowerShellu](search-create-index-rest-api.md) nebo [C# Úvodní příručka: Vytvoření indexu Azure Cognitive Search pomocí .NET SDK](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Akce indexování: nahrát, sloučit, sloučitOrUpload, odstranit

Můžete určit typ akce indexování pro daný dokument a určit, zda má být dokument odeslán v plném rozsahu, sloučen s existujícím obsahem dokumentu nebo odstraněn.

V rozhraní REST API, problém HTTP POST požadavky s těly požadavků JSON na adresu URL koncového bodu indexu Azure Cognitive Search. Každý objekt JSON v poli "hodnota" obsahuje klíč dokumentu a určuje, zda akce indexování přidá, aktualizuje nebo odstraní obsah dokumentu. Příklad kódu naleznete v tématu [Načtení dokumentů](search-get-started-dotnet.md#load-documents).

V sdk .NET sbalení data `IndexBatch` do objektu. Zapouzdřuje `IndexBatch` kolekci `IndexAction` objektů, z nichž každý obsahuje dokument a vlastnost, která říká Azure Cognitive Search, jaké akce provést v tomto dokumentu. Příklad kódu naleznete v tématu [C# Quickstart](search-get-started-dotnet.md).


| @search.action | Popis | Potřebná pole pro každý dokument | Poznámky |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Akce `upload` je podobná akci „upsert“, kdy je dokument vložený, pokud je nový a aktualizovaný nebo nahrazený, pokud již existuje. |klíč a další pole, která si přejete definovat |Pokud aktualizujete nebo nahrazujete stávající dokument, bude každé pole, které není zadané v žádosti, nastavené na `null`. K tomu dojde i v případě, že bylo pole dříve nastavené na nenulovou hodnotu. |
| `merge` |Aktualizuje stávající dokument se zadanými poli. Pokud dokument v indexu neexistuje, sloučení selže. |klíč a další pole, která si přejete definovat |Každé pole zadané ve sloučení nahradí stávající pole v dokumentu. V sdk.net s. to `DataType.Collection(DataType.String)`zahrnuje pole typu . V rozhraní REST API to `Collection(Edm.String)`zahrnuje pole typu . Například pokud dokument obsahuje pole `tags` s hodnotou `["budget"]` a vy spustíte sloučení s polem `tags` s hodnotou `["economy", "pool"]`, konečná hodnota pole `tags` bude `["economy", "pool"]`. Hodnota nebude `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Pokud již dokument s daným klíčem v indexu existuje, chová se tato akce jako `merge`. Pokud dokument neexistuje, chová se s novým dokumentem jako `upload`. |klíč a další pole, která si přejete definovat |- |
| `delete` |Odebere z indexu zadaný dokument. |pouze klíč |Všechna zadaná pole kromě pole klíče budou ignorována. Chcete-li odebrat z dokumentu jednotlivá pole, použijte místo toho `merge` a jednoduše nastavte hodnotu pole na „null“. |

## <a name="decide-which-indexing-action-to-use"></a>Rozhodněte, jakou akci indexování použít
Chcete-li importovat data pomocí sady .NET SDK(nahrát, sloučit, odstranit a sloučitOrUpload). V závislosti na zvolené akci musí objekt pro každý dokument obsahovat pouze určitá pole.


### <a name="formulate-your-query"></a>Formulování dotazu
Existují dva způsoby [vyhledávání v indexu pomocí REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). První způsob je vydání požadavku HTTP POST, kde parametry dotazu jsou určené v objektu JSON v textu požadavku. Druhý způsob je vydání požadavku HTTP GET, kde parametry dotazu jsou určené v rámci URL požadavku. Metoda POST má [mírnější omezení](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) velikosti parametrů dotazu než metoda GET. Z tohoto důvodu doporučujeme používat metodu POST, pokud pro vás neplatí zvláštní podmínky, kdy by bylo pohodlnější použití metody GET.

U metody POST i GET budete muset v URL požadavku poskytnout *název služby*, *název indexu* a správnou *verzi rozhraní API* (v době publikování tohoto dokumentu je aktuální verze rozhraní API `2019-05-06`). U metody GET zadáte parametry dotazu v rámci *řetězce dotazu* na konci adresy URL. Formát URL vidíte níže:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

Formát pro metodu POST je stejný, ale jako parametr řetězce dotazu obsahuje pouze api-version.


## <a name="pulling-data-into-an-index"></a>Přetáhnutí dat do indexu
Model Pull prochází podporovaný zdroj dat a automaticky nahrává data do vašeho indexu. V Azure Cognitive Search je tato funkce implementována prostřednictvím *indexerů*, které jsou momentálně dostupné pro tyto platformy:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Databáze Azure SQL a SQL Server na virtuálních počítačích Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indexery propojují index se zdrojem dat (obvykle tabulka, zobrazení nebo ekvivalentní struktura) a mapují pole zdroje na odpovídající pole v indexu. Během provádění je sada řádků automaticky převedena na formát JSON a načtena do určeného indexu. Všechny indexery podporují plánování, takže můžete určit, jak často se data budou aktualizovat. Většina indexerů umožňuje sledování změn dat, pokud ho zdroj dat podporuje. Indexery sledují změny a odstranění ve stávajících dokumentech a rozpoznávají nové dokumenty, a díky tomu není potřeba aktivně spravovat data v indexu. 


### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Jak získat data do indexu Azure Cognitive Search

Funkce indexeru jsou přístupné pomocí webu [Azure Portal](search-import-data-portal.md), rozhraní [REST API](/rest/api/searchservice/Indexer-operations) a sady [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

Výhodou použití portálu je, že Azure Cognitive Search můžete obvykle generovat výchozí schéma indexu pro vás čtením metadat zdrojové datové sady. Vygenerovaný index můžete upravit až do zpracování indexu. Poté jsou povoleny jen takové změny schématu, které nevyžadují přeindexování. Pokud provedené změny přímo ovlivní schéma indexu, bude nutné index znovu sestavit. 

## <a name="verify-data-import-with-search-explorer"></a>Ověření importu dat pomocí Průzkumníka hledání

Rychlý způsob, jak provést předběžnou kontrolu při nahrávání dokumentu, je použít **průzkumníka vyhledávání** na portálu. Průzkumníka můžete použít k zadávání dotazů na index, aniž byste museli programovat. Vyhledávání je založeno na výchozím nastavení, jako je například [jednoduchá syntaxe](/rest/api/searchservice/simple-query-syntax-in-azure-search) a výchozí [parametr dotazu searchMode](/rest/api/searchservice/search-documents). Výsledky jsou vráceny ve formátu JSON, abyste si mohli prohlédnout celý dokument.

> [!TIP]
> Mezi četné [ukázky kódu Azure Cognitive Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) patří vložené nebo snadno dostupné datové sady, které nabízejí snadný způsob, jak začít. Na portálu také najdete ukázkový indexer a zdroj dat, obsahující datovou sadu malé realitní kanceláře (s názvem realestate-us-sample). Při spuštění předkonfigurované indexeru na ukázkový zdroj dat je vytvořen index a načten s dokumenty, které pak mohou být dotazovány v průzkumníku vyhledávání nebo podle kódu, který píšete.

## <a name="see-also"></a>Viz také

+ [Přehled indexeru](search-indexer-overview.md)
+ [Průvodce portálem: vytvoření, načtení a dotazování indexu](search-get-started-portal.md)
