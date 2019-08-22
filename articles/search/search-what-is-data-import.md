---
title: Import dat pro přijímání dat do indexu vyhledávání – Azure Search
description: Naplňte a nahrajte data do indexu v Azure Search z externích zdrojů dat.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 71ee63dfbe880cbf6018f3dd13d360850ed994f9
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647339"
---
# <a name="data-import-overview---azure-search"></a>Přehled importu dat – Azure Search

V Azure Search se dotazy spustí přes obsah načtený do [indexu vyhledávání](search-what-is-an-index.md)a uloží se do něj. Tento článek prověřuje dva základní přístupy k naplnění indexu: nahrajte data do indexu prostřednictvím kódu programu nebo najeďte [Azure Search indexerem](search-indexer-overview.md) na podporovaný zdroj dat, který se načte v datech.

V obou případech je cílem *načíst data* z externího zdroje dat do indexu Azure Search. Azure Search vám umožní vytvořit prázdný index, ale dokud do něj nevložíte nebo nenačtete data, není Queryable.

## <a name="pushing-data-to-an-index"></a>Nabídka dat do indexu
Model Push, který slouží k odesílání dat do služby Azure Search prostřednictvím kódu programu, je nejflexibilnějším přístupem. Za prvé u něj neplatí žádná omezení týkající se typu zdroje dat. Do indexu Azure Search je možné nabídnout jakoukoli datovou sadu skládající se z dokumentů JSON za předpokladu, že každý dokumente v datové sadě obsahuje mapování polí na pole definovaná ve schématu vašeho indexu. Za druhé u něj neplatí žádná omezení týkající se četnosti provádění. Do indexu můžete nabízet změny, jak často chcete. U aplikací vyžadujících velmi nízkou latenci (např. když potřebujete, aby operace hledání byly synchronizované s dynamickými databázemi zásob) je model Push vaší jedinou možností.

Tento přístup je flexibilnější než model Pull, protože můžete nahrávat dokumenty samostatně nebo v dávkách (jednotlivé dávky můžou obsahovat až 1000 dokumentů nebo 16 MB, podle toho, kterého omezení dosáhnete dříve). Model Push vám taky umožňuje nahrát dokumenty do služby Azure Search bez ohledu na to, kde se data nacházejí.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Nabídka dat do indexu Azure Search

Pomocí následujících rozhraní API můžete do indexu načíst jeden nebo několik dokumentů:

+ [Přidávání, aktualizace a odstraňování dokumentů (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Třída indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) nebo [třída indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Vkládání dat prostřednictvím portálu není aktuálně podporováno.

Úvod ke každé metodologii najdete v tématu [rychlý Start: Vytvoření indexu Azure Search pomocí PowerShellu](search-create-index-rest-api.md) nebo [ C# rychlého startu: Vytvoření indexu Azure Search pomocí sady .NET SDK](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Akce indexování: nahrání, sloučení, mergeOrUpload, odstranění

Můžete řídit typ akce indexování pro jednotlivé dokumenty a určit, jestli se má dokument nahrát úplně, sloučit s existujícím obsahem dokumentu nebo odstranit.

V REST API vydejte žádosti HTTP POST s texty požadavku JSON na adresu URL koncového bodu indexu Azure Search. Každý objekt JSON v poli "value" obsahuje klíč dokumentu a určuje, zda má akce indexování přidat, aktualizovat nebo odstranit obsah dokumentu. Příklad kódu naleznete v tématu [Load Documents](search-get-started-dotnet.md#load-documents).

V sadě .NET SDK vytvořte balíček dat do `IndexBatch` objektu. Zapouzdřuje kolekci objektů, z nichž každý obsahuje dokument a vlastnost, která oznamuje Azure Search, jakou akci má tento dokument provádět. `IndexAction` `IndexBatch` Příklad kódu naleznete v tématu [ C# rychlý Start](search-get-started-dotnet.md).


| @search.action | Popis | Potřebná pole pro každý dokument | Poznámky |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Akce `upload` je podobná akci „upsert“, kdy je dokument vložený, pokud je nový a aktualizovaný nebo nahrazený, pokud již existuje. |klíč a další pole, která si přejete definovat |Pokud aktualizujete nebo nahrazujete stávající dokument, bude každé pole, které není zadané v žádosti, nastavené na `null`. K tomu dojde i v případě, že bylo pole dříve nastavené na nenulovou hodnotu. |
| `merge` |Aktualizuje stávající dokument se zadanými poli. Pokud dokument v indexu neexistuje, sloučení selže. |klíč a další pole, která si přejete definovat |Každé pole zadané ve sloučení nahradí stávající pole v dokumentu. V sadě .NET SDK obsahuje pole typu `DataType.Collection(DataType.String)`. V REST API obsahuje pole typu `Collection(Edm.String)`. Například pokud dokument obsahuje pole `tags` s hodnotou `["budget"]` a vy spustíte sloučení s polem `tags` s hodnotou `["economy", "pool"]`, konečná hodnota pole `tags` bude `["economy", "pool"]`. Hodnota nebude `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Pokud již dokument s daným klíčem v indexu existuje, chová se tato akce jako `merge`. Pokud dokument neexistuje, chová se s novým dokumentem jako `upload`. |klíč a další pole, která si přejete definovat |- |
| `delete` |Odebere z indexu zadaný dokument. |pouze klíč |Všechna zadaná pole kromě pole klíče budou ignorována. Chcete-li odebrat z dokumentu jednotlivá pole, použijte místo toho `merge` a jednoduše nastavte hodnotu pole na „null“. |

## <a name="decide-which-indexing-action-to-use"></a>Rozhodněte, jakou akci indexování použít
Import dat pomocí sady .NET SDK (nahrávání, sloučení, odstranění a mergeOrUpload). V závislosti na zvolené akci musí objekt pro každý dokument obsahovat pouze určitá pole.


### <a name="formulate-your-query"></a>Formulování dotazu
Existují dva způsoby [vyhledávání v indexu pomocí REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). První způsob je vydání požadavku HTTP POST, kde parametry dotazu jsou určené v objektu JSON v textu požadavku. Druhý způsob je vydání požadavku HTTP GET, kde parametry dotazu jsou určené v rámci URL požadavku. Metoda POST má [mírnější omezení](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) velikosti parametrů dotazu než metoda GET. Z tohoto důvodu doporučujeme používat metodu POST, pokud pro vás neplatí zvláštní podmínky, kdy by bylo pohodlnější použití metody GET.

U metody POST i GET budete muset v URL požadavku poskytnout *název služby*, *název indexu* a správnou *verzi rozhraní API* (v době publikování tohoto dokumentu je aktuální verze rozhraní API `2019-05-06`). U metody GET zadáte parametry dotazu v rámci *řetězce dotazu* na konci adresy URL. Formát URL vidíte níže:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

Formát pro metodu POST je stejný, ale jako parametr řetězce dotazu obsahuje pouze api-version.


## <a name="pulling-data-into-an-index"></a>Přetáhnutí dat do indexu
Model Pull prochází podporovaný zdroj dat a automaticky nahrává data do vašeho indexu. Ve službě Azure Search je tato schopnost implementovaná prostřednictvím *indexerů*, které jsou aktuálně dostupné pro tyto platformy:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Databáze Azure SQL a SQL Server na virtuálních počítačích Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indexery propojují index se zdrojem dat (obvykle tabulka, zobrazení nebo ekvivalentní struktura) a mapují pole zdroje na odpovídající pole v indexu. Během provádění je sada řádků automaticky převedena na formát JSON a načtena do určeného indexu. Všechny indexery podporují plánování, takže můžete určit, jak často se data budou aktualizovat. Většina indexerů umožňuje sledování změn dat, pokud ho zdroj dat podporuje. Indexery sledují změny a odstranění ve stávajících dokumentech a rozpoznávají nové dokumenty, a díky tomu není potřeba aktivně spravovat data v indexu. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Přetáhnutí dat do indexu Azure Search

Funkce indexeru jsou přístupné pomocí webu [Azure Portal](search-import-data-portal.md), rozhraní [REST API](/rest/api/searchservice/Indexer-operations) a sady [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

Výhodou použití portálu je, že Azure Search většinou za vás dokáže vygenerovat výchozí schéma indexu podle metadat zdrojové datové sady. Vygenerovaný index můžete upravit až do zpracování indexu. Poté jsou povoleny jen takové změny schématu, které nevyžadují přeindexování. Pokud provedené změny přímo ovlivní schéma indexu, bude nutné index znovu sestavit. 

## <a name="verify-data-import-with-search-explorer"></a>Ověření importu dat pomocí Průzkumníka vyhledávání

Rychlý způsob, jak provést předběžnou kontrolu při nahrávání dokumentu, je použití **Průzkumníka vyhledávání** na portálu. Průzkumníka můžete použít k zadávání dotazů na index, aniž byste museli programovat. Funkce vyhledávání je založena na výchozím nastavení, jako je [jednoduchá syntaxe](/rest/api/searchservice/simple-query-syntax-in-azure-search) a výchozí [parametr dotazu searchMode](/rest/api/searchservice/search-documents). Výsledky jsou vráceny ve formátu JSON, abyste si mohli prohlédnout celý dokument.

> [!TIP]
> Jako výchozí bod můžete využít celou řadu [ukázek kódu pro Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) zahrnujících vložené nebo snadno dostupné datové sady. Na portálu také najdete ukázkový indexer a zdroj dat, obsahující datovou sadu malé realitní kanceláře (s názvem realestate-us-sample). Když spustíte předem nakonfigurovaný indexer na vzorovém zdroji dat, vytvoří se index a načte se do dokumentů, na které se pak můžete dotazovat v Průzkumníku služby Search nebo pomocí kódu, který zapisujete.

## <a name="see-also"></a>Viz také:

+ [Přehled indexeru](search-indexer-overview.md)
+ [Průvodce portálem: vytvoření, načtení a dotazování indexu](search-get-started-portal.md)
