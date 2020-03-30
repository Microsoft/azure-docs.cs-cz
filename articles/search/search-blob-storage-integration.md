---
title: Přidání fulltextového vyhledávání do úložiště objektů blob Azure
titleSuffix: Azure Cognitive Search
description: Extrahujte obsah a přidejte strukturu do objektů BLOB Azure při vytváření fulltextového vyhledávacího indexu v earchu Azure Cognitive.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: af7d04bd74ada296b9f0e0f7c149c2a781cec579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496489"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Přidání fulltextového vyhledávání do dat objektů blob Azure pomocí Azure Cognitive Search

Hledání napříč různými typy obsahu uloženými v úložišti objektů blob Azure může být obtížný problém vyřešit. Obsah objektů BLOB však můžete indexovat a prohledávat pomocí azure [kognitivního vyhledávání.](search-what-is-azure-search.md) Azure Cognitive Search má integrovanou integraci pro indexování mimo úložiště objektů Blob prostřednictvím [*indexeru objektů Blob,*](search-howto-indexing-azure-blob-storage.md) který přidává funkce podporující zdroj dat do indexování.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Co znamená přidat fulltextové vyhledávání do dat objektů blob

Azure Cognitive Search je cloudvyhledávací služba, která poskytuje indexování a dotazovací stroje, které pracují přes uživatelem definované indexy hostované ve vaší vyhledávací službě. Společné vyhledání obsahu s dotazovacím strojem v cloudu je nezbytné pro výkon, vrácení výsledků rychlostí, kterou uživatelé očekávají od vyhledávacích dotazů.

Azure Cognitive Search se integruje s úložištěm objektů Blob Azure ve vrstvě indexování a importuje obsah objektu blob jako vyhledávací dokumenty, které jsou indexovány do *invertovaných indexů* a dalších struktur dotazů, které podporují textové dotazy z volného textu a výrazy filtrů. Vzhledem k tomu, že obsah objektu blob je indexován do indexu vyhledávání, přístup k obsahu objektů blob můžete využít celou škálu funkcí dotazů v Azure Cognitive Search.

Jakmile je index vytvořen a naplněn, existuje nezávisle na kontejneru objektů blob, ale můžete znovu spustit operace indexování a aktualizovat index se změnami podkladového kontejneru. Informace o časovém razítku jednotlivých objektů BLOB se používají pro detekci změn. Můžete se rozhodnout pro plánované spuštění nebo indexování na vyžádání jako mechanismus aktualizace.

Vstupy jsou objekty BLOB v jednom kontejneru v úložišti objektů blob Azure. Objekty BLOB mohou být téměř jakýkoli druh textových dat. Pokud objekty BLOB obsahují obrázky, můžete přidat [obohacení ai do indexování objektů blob](search-blob-ai-integration.md) a vytvořit a extrahovat text z obrazů.

Výstup je vždy index Azure Cognitive Search, který se používá pro rychlé vyhledávání textu, načítání a zkoumání v klientských aplikacích. Mezi je samotná architektura kanálu indexování. Kanál je založen na funkci *indexeru,* podrobně popsána v tomto článku.

## <a name="start-with-services"></a>Začněte se službami

Potřebujete Azure Cognitive Search a úložiště objektů blob Azure. V rámci úložiště objektů Blob potřebujete kontejner, který poskytuje zdrojový obsah.

Můžete začít přímo na stránce portálu účtu úložiště. Na levé navigační stránce klikněte v části **Blob service** na **Přidat Azure Cognitive Search** a vytvořte novou službu nebo vyberte existující službu. 

Po přidání Azure Cognitive Search do účtu úložiště, můžete postupovat podle standardního procesu indexovat data objektů blob. Doporučujeme Průvodce **importem dat** v Azure Cognitive Search pro snadné počáteční úvod nebo volání rozhraní API REST pomocí nástroje, jako je Postman. Tento kurz vás provede kroky volání rozhraní REST API v Postman: [Index a vyhledávání částečně strukturovaných dat (Objekty BLOB JSON) v Azure Cognitive Search](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Použití indexeru objektů blob

*Indexer* je dílčí služba podporující zdroj dat vybavená interní logikou pro vzorkování dat, čtení dat metadat, načítání dat a serializaci dat z nativních formátů do dokumentů JSON pro následný import. 

Objekty BLOB ve službě Azure Storage se indexují pomocí [indexeru úložiště objektů blob Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md). Tento indexer můžete vyvolat pomocí Průvodce **importem dat,** rozhraní REST API nebo sady .NET SDK. V kódu použijete tento indexer nastavením typu a poskytnutím informací o připojení, které zahrnuje účet Azure Storage spolu s kontejnerem objektů blob. Objekty BLOB můžete podmnožinou vytvořením virtuálního adresáře, který pak můžete předat jako parametr, nebo filtrováním přípony typu souboru.

Indexer provádí "dokument praskání", otevření objektu blob pro kontrolu obsahu. Po připojení ke zdroji dat je to první krok v kanálu. U dat objektů blob se zjistí, že se zjistí PDF, kancelářské dokumenty a další typy obsahu. Popraskání dokumentů s extrakcí textu je zdarma. Pokud objekty BLOB obsahují obsah obrázku, obrázky se ignorují, pokud [nepřidáte obohacení ai](search-blob-ai-integration.md). Standardní indexování platí pouze pro textový obsah.

Indexer objektů blob je dodáván s parametry konfigurace a podporuje sledování změn, pokud podkladová data poskytují dostatečné informace. Další informace o základních funkcích najdete v [indexeru úložiště objektů blob Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Podporované typy obsahu

Spuštěním indexeru objektů Blob přes kontejner můžete extrahovat text a metadata z následujících typů obsahu pomocí jediného dotazu:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indexování metadat objektu blob

Běžný mů e-blob libovolného typu je indexovat vlastní metadata a vlastnosti systému pro každý objekt blob. Tímto způsobem jsou informace pro všechny objekty BLOB indexovány bez ohledu na typ dokumentu uložené v indexu ve vyhledávací službě. Pomocí nového indexu pak můžete pokračovat v řazení, filtrování a omezující vlastnosti napříč veškerým obsahem úložiště objektů Blob.

### <a name="indexing-json-blobs"></a>Indexování objektů BLOB JSON
Indexery lze nakonfigurovat tak, aby extrahovat strukturovaný obsah nalezený v objektech BLOB, které obsahují JSON. Indexer může číst objekty BLOB JSON a analyzovat strukturovaný obsah do příslušných polí vyhledávacího dokumentu. Indexery můžete také trvat objekty BLOB, které obsahují pole objektů JSON a mapování každý prvek do samostatného vyhledávacího dokumentu. Můžete nastavit režim analýzy ovlivnit typ objektu JSON vytvořené indexeru.

## <a name="search-blob-content-in-a-search-index"></a>Hledání obsahu objektu blob v indexu vyhledávání 

Výstup indexování je index vyhledávání, který se používá pro interaktivní průzkum pomocí volného textu a filtrovaných dotazů v klientské aplikaci. Pro počáteční zkoumání a ověření obsahu doporučujeme začít s [Průzkumníkem vyhledávání](search-explorer.md) na portálu a prozkoumat strukturu dokumentu. V průzkumníku vyhledávání můžete použít [jednoduchou syntaxi dotazu](query-simple-syntax.md), [úplnou syntaxi dotazu](query-lucene-syntax.md)a [syntaxi výrazu filtru.](query-odata-filter-orderby-syntax.md)

Trvalejší řešení je shromažďovat vstupy dotazu a prezentovat odpověď jako výsledky hledání v klientské aplikaci. Následující kurz Jazyka C# vysvětluje, jak vytvořit vyhledávací aplikaci: [Vytvořte první aplikaci v Azure Cognitive Search](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Další kroky

+ [Nahrávání, stahování a seznam objektů BLOB pomocí portálu Azure (azure blob storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Nastavení indexeru objektů blob (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
