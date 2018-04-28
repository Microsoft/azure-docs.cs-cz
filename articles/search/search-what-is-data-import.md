---
title: Import dat ve službě Azure Search | Dokumentace Microsoftu
description: Naučte se nahrát data do indexu ve službě Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: heidist
ms.openlocfilehash: e54b89d31042d43a14379dea6daffd3e7763a64a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="indexing-in-azure-search"></a>Indexování ve službě Azure Search
> [!div class="op_single_selector"]
> * [Přehled](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Ve službě Azure Search se dotazy provádějí nad obsahem načteným do [indexu vyhledávání](search-what-is-an-index.md). Tento článek zkoumá dva základní přístupy k načítání obsahu do indexu: *nabídka* (Push) dat do indexu prostřednictvím kódu programu nebo nasměrování [indexeru Azure Search](search-indexer-overview.md) na podporovaný zdroj dat a *přetáhnutí* (Pull) dat.

## <a name="pushing-data-to-an-index"></a>Nabídka dat do indexu
Model Push, který slouží k odesílání dat do služby Azure Search prostřednictvím kódu programu, je nejflexibilnějším přístupem. Za prvé u něj neplatí žádná omezení týkající se typu zdroje dat. Do indexu Azure Search je možné nabídnout jakoukoli datovou sadu skládající se z dokumentů JSON za předpokladu, že každý dokumente v datové sadě obsahuje mapování polí na pole definovaná ve schématu vašeho indexu. Za druhé u něj neplatí žádná omezení týkající se četnosti provádění. Do indexu můžete nabízet změny, jak často chcete. U aplikací vyžadujících velmi nízkou latenci (např. když potřebujete, aby operace hledání byly synchronizované s dynamickými databázemi zásob) je model Push vaší jedinou možností.

Tento přístup je flexibilnější než model Pull, protože můžete nahrávat dokumenty samostatně nebo v dávkách (jednotlivé dávky můžou obsahovat až 1000 dokumentů nebo 16 MB, podle toho, kterého omezení dosáhnete dříve). Model Push vám taky umožňuje nahrát dokumenty do služby Azure Search bez ohledu na to, kde se data nacházejí.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Nabídka dat do indexu Azure Search

Pomocí následujících rozhraní API můžete do indexu načíst jeden nebo několik dokumentů:

+ [Přidávání, aktualizace a odstraňování dokumentů (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Třída indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) nebo [třída indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Vkládání dat prostřednictvím portálu není aktuálně podporováno.

Úvod k jednotlivým metodologiím najdete v tématu [Import dat pomocí REST](search-import-data-rest-api.md) nebo [Import dat pomocí .NET](search-import-data-dotnet.md).


## <a name="pulling-data-into-an-index"></a>Přetáhnutí dat do indexu
Model Pull prochází podporovaný zdroj dat a automaticky nahrává data do vašeho indexu. Ve službě Azure Search je tato schopnost implementovaná prostřednictvím *indexerů*, které jsou aktuálně dostupné pro tyto platformy:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer)
+ [Azure SQL Database a SQL Server na virtuálních počítačích Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indexery propojují index se zdrojem dat (obvykle tabulka, zobrazení nebo ekvivalentní struktura) a mapují pole zdroje na odpovídající pole v indexu. Během provádění je sada řádků automaticky převedena na formát JSON a načtena do určeného indexu. Všechny indexery podporují plánování, takže můžete určit, jak často se data budou aktualizovat. Většina indexerů umožňuje sledování změn dat, pokud ho zdroj dat podporuje. Indexery sledují změny a odstranění ve stávajících dokumentech a rozpoznávají nové dokumenty, a díky tomu není potřeba aktivně spravovat data v indexu. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Přetáhnutí dat do indexu Azure Search

Funkce indexeru jsou přístupné pomocí webu [Azure Portal](search-import-data-portal.md), rozhraní [REST API](/rest/api/searchservice/Indexer-operations) a sady [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperations). 

Výhodou použití portálu je, že Azure Search většinou za vás dokáže vygenerovat výchozí schéma indexu podle metadat zdrojové datové sady. Vygenerovaný index můžete upravit až do zpracování indexu. Poté jsou povoleny jen takové změny schématu, které nevyžadují přeindexování. Pokud provedené změny přímo ovlivní schéma indexu, bude nutné index znovu sestavit. 

## <a name="verify-data-import-with-search-explorer"></a>Ověření importu dat pomocí Průzkumníka služby Search

Rychlý způsob, jak provést předběžnou kontrolu při odeslání dokumentu, představuje **Průzkumník služby Search** na portálu. Průzkumníka můžete použít k zadávání dotazů na index, aniž byste museli programovat. Funkce vyhledávání je založena na výchozím nastavení, jako je [jednoduchá syntaxe](/rest/api/searchservice/simple-query-syntax-in-azure-search) a výchozí [parametr dotazu searchMode](/rest/api/searchservice/search-documents). Výsledky jsou vráceny ve formátu JSON, abyste si mohli prohlédnout celý dokument.

> [!TIP]
> Jako výchozí bod můžete využít celou řadu [ukázek kódu pro Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) zahrnujících vložené nebo snadno dostupné datové sady. Na portálu také najdete ukázkový indexer a zdroj dat, obsahující datovou sadu malé realitní kanceláře (s názvem realestate-us-sample). Když spustíte předkonfigurovaný indexer na ukázkový zdroj dat, vytvoří se index a načtou se do něj dokumenty, na které se můžete dotazovat pomocí Průzkumníka služby Search nebo pomocí vlastního kódu.

## <a name="see-also"></a>Další informace najdete v tématech

+ [Přehled indexeru](search-indexer-overview.md)
+ [Průvodce portálem: vytvoření, načtení a dotazování indexu](search-get-started-portal.md)