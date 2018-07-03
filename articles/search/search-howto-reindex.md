---
title: Znovu sestavit Azure Search index nebo aktualizace prohledávatelného obsahu | Dokumentace Microsoftu
description: Přidat nové prvky, aktualizovat stávající prvky nebo dokumenty nebo odstranit zastaralé dokumentů v úplné opětovné sestavení nebo částečné přírůstkové indexování na aktualizace indexu Azure Search.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: 374e7601169647f0eb7d3a214cf15567b7b11090
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "34641420"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Postup opětovné sestavení indexu Azure Search

Znovu sestavit na index změny jeho strukturu, změna fyzické výrazu indexu ve službě Azure Search. Naopak aktualizace indexu je jen pro obsah aktualizace, aby se získaly nejnovější změny ze zdroje externích dat přispívající. Tento článek obsahuje směr o tom, jak aktualizovat indexy strukturálně a zákazníků.

Oprávnění ke čtení a zápis na úrovni služby jsou požadovány pro aktualizace indexu. Znovu sestavit nebo přírůstkové indexování obsahu, s parametry zadání možnosti aktualizace prostřednictvím kódu programu, můžete volat rozhraní API .NET nebo REST. 

Obecně platí jsou aktualizace indexu na vyžádání. Ale pro indexy vyplnit hodnotami pomocí konkrétní zdroj [indexery](search-indexer-overview.md), můžete použít integrované plánovače. Plánovač podporuje aktualizaci dokumentů tak často, jak každých 15 minut až jakýkoli interval a vzor, budete potřebovat. Rychlejší obnovovací frekvence vyžaduje doručením (push) aktualizace indexu ručně, případně pomocí double zápis transakce současně aktualizuje externí zdroj dat a index Azure Search.

## <a name="full-rebuilds"></a>Úplné znovu sestaví

Pro mnoho typů aktualizací se vyžaduje úplné opětovné sestavení. Úplné opětovné sestavení odkazuje na odstranění indexu, data a metadata, za nímž následuje opětovného vyplnění z externích zdrojů dat do indexu. Prostřednictvím kódu programu [odstranit](https://docs.microsoft.com/rest/api/searchservice/delete-index), [vytvořit](https://docs.microsoft.com/rest/api/searchservice/create-index), a [znovu načíst](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) index znovu sestavit. 

Po opětovné sestavení, nezapomeňte, že pokud máte byla testování vzory dotazů a vyhodnocování profilů, můžete očekávat, že změny ve výsledcích dotazu Pokud došlo ke změně podkladového obsahu.

## <a name="when-to-rebuild"></a>Kdy k opětovnému sestavení

Plán na časté, úplné znovu sestaví při aktivním vývoji, když index schémata jsou ve stavu toku.

| Úpravy | Znovu sestavit stav|
|--------------|---------------|
| Název pole, změnit datový typ, nebo jeho [atributy indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) | Změna definice pole obvykle je zpoplatněna penalizace opětovného sestavení, s výjimkou těchto [atributy indexu](https://docs.microsoft.com/rest/api/searchservice/create-index): Retrievable, SearchAnalyzer, SynonymMaps. Atributy Retrievable, SearchAnalyzer a SynonymMaps můžete přidat do existujícího pole bez nutnosti znovu sestavovat jejich indexu.|
| Přidání pole | Žádný striktní požadavek na opětovné sestavení. Existující indexovaných dokumentů mít hodnotu null pro nové pole. V budoucích reindex nahraďte hodnoty ze zdrojových dat hodnoty Null přidal Azure Search. |
| Odstranit pole | Pole nelze odstranit přímo z indexu Azure Search. Místo toho byste měli mít vaše aplikace "odstraněné" pole, abyste se vyhnuli použití ji ignorovat. Fyzicky definici pole a obsah zůstane v indexu až do příštího opětovné sestavení indexu pomocí schématu, která vynechává dané oblasti.|

> [!Note]
> Opětovné sestavení je také nutný, pokud přejdete úrovně. Pokud v určitém okamžiku rozhodnete větší kapacitu, neexistuje žádný místní upgrade. Nová služba musí být vytvořeny na nový bod kapacity a musí být sestaveny indexy od nuly v nové službě. 

## <a name="partial-or-incremental-indexing"></a>Částečné nebo přírůstkové indexování

Po indexu v produkčním prostředí se pozornost zaměří na přírůstkové indexování, obvykle pomocí bez přerušení služeb viditelné. Částečné nebo přírůstkové indexování je úloha pouze obsah, který synchronizuje obsah vyhledávací index tak, aby odrážel stav obsahu ve zdroji dat přispívající. Dokument přidány nebo odstraněny ve zdroji přidána nebo odstraněna do indexu. V kódu, zavolejte [přidání, aktualizace nebo odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) operace nebo jeho ekvivalent .NET.

> [!Note]
> Při použití indexerů, které procházejí externí zdroje dat, se využívají mechanismy sledování změn ve zdrojových systémech pro přírůstkové indexování. Pro [úložiště objektů Blob v Azure](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection), `lastModified` pole se používá. Na [Azure Table storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection), `timestamp` slouží stejnému účelu. Podobně i [indexer pro Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) a [indexeru služby Azure Cosmos DB](search-howto-index-cosmosdb.md#indexing-changed-documents) obsahovat pole pro nastavení příznaku aktualizace řádku. Další informace o indexerech najdete v tématu [přehled indexerů](search-indexer-overview.md).


## <a name="see-also"></a>Další informace najdete v tématech

+ [Přehled indexeru](search-indexer-overview.md)
+ [Index velkých datových sad ve velkém měřítku](search-howto-large-index.md)
+ [Indexování na portálu](search-import-data-portal.md)
+ [Indexer pro Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Indexer databáze Azure Cosmos](search-howto-index-cosmosdb.md)
+ [Indexer pro Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexer pro Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Zabezpečení ve službě Azure Search](search-security-overview.md)