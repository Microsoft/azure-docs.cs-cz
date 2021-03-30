---
title: Hledání v obsahu služby Azure Blob Storage
titleSuffix: Azure Cognitive Search
description: Přečtěte si o extrakci textu z objektů blob Azure a o fulltextovém vyhledávání v indexu Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: f61bf635cc61a2153a7bb016ef4b4711d7ba7391
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91355291"
---
# <a name="search-over-azure-blob-storage-content"></a>Hledání v obsahu služby Azure Blob Storage

Hledání v nejrůznějších typech obsahu uložených ve službě Azure Blob Storage může být obtížné vyřešit problém. V tomto článku si Projděte základní pracovní postup pro extrakci obsahu a metadat z objektů BLOB a odeslání do indexu vyhledávání v Azure Kognitivní hledání. Výsledný index lze dotazovat pomocí fulltextového vyhledávání.

> [!NOTE]
> Už znáte pracovní postup a kompozici? [Jak nakonfigurovat indexer objektů BLOB](search-howto-indexing-azure-blob-storage.md) je váš další krok.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Co znamená přidání fulltextového vyhledávání do dat objektů BLOB

Azure Kognitivní hledání je vyhledávací služba, která podporuje indexování a dotazování úloh v rámci uživatelsky definovaných indexů, které obsahují váš vzdálený prohledávatelný obsah, který je hostovaný v cloudu. Společné umístění vyhledávaného obsahu pomocí dotazovacího stroje je nezbytné pro výkon a vrácení výsledků na rychlost, jakou uživatelé chtějí očekávat od vyhledávacích dotazů.

Kognitivní hledání se integruje s úložištěm objektů BLOB v Azure, a to tak, že se obsah objektu BLOB importuje jako dokumenty pro hledání, které se indexují na *obrácené indexy* a jiné struktury dotazů, které podporují textové dotazy a výrazy filtru pro volné formuláře. Vzhledem k tomu, že obsah objektu BLOB je indexovaný do indexu vyhledávání, můžete v Azure Kognitivní hledání využít celou škálu funkcí dotazů a vyhledat informace v obsahu objektů BLOB.

Vstupy jsou vaše objekty BLOB v jednom kontejneru v úložišti objektů BLOB v Azure. Objekty blob můžou být skoro libovolný druh textových dat. Pokud objekty blob obsahují obrázky, můžete [do indexování objektů BLOB přidat rozšíření AI](search-blob-ai-integration.md) a vytvořit a extrahovat text z imagí.

Výstupem je vždy index služby Azure Kognitivní hledání, který se používá pro rychlé vyhledávání textu, načítání a průzkum v klientských aplikacích. V mezi je samotná architektura kanálu indexování. Kanál je založen na funkci *indexeru* , která je popsána dále v tomto článku.

Po vytvoření a naplnění indexu existuje nezávisle na vašem kontejneru objektů blob, ale můžete znovu znovu spustit operace indexování a aktualizovat svůj index na základě změněných dokumentů. Informace o časovém razítku jednotlivých objektů BLOB se používají ke zjištění změn. Jako mechanismus obnovení můžete zvolit plánované spuštění nebo indexování na vyžádání.

## <a name="required-resources"></a>Požadované prostředky

Budete potřebovat Azure Kognitivní hledání i úložiště objektů BLOB v Azure. V úložišti objektů BLOB budete potřebovat kontejner, který poskytuje zdrojový obsah.

Můžete začít přímo na stránce portálu účtu úložiště. Na levé navigační stránce v části **BLOB Service** klikněte na **Přidat Azure kognitivní hledání** a vytvořte novou službu nebo vyberte existující. 

Až přidáte Azure Kognitivní hledání do svého účtu úložiště, můžete postupovat podle standardního procesu indexování dat objektů BLOB. Průvodce **importem dat** v Azure kognitivní hledání pro snadné počáteční zavedení doporučujeme, nebo můžete volat rozhraní REST API pomocí nástroje, jako je například post. Tento kurz vás provede jednotlivými kroky volání REST API v části post: [index a hledání částečně strukturovaných dat (JSON BLOBS) ve službě Azure kognitivní hledání](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Použití indexeru objektů BLOB

*Indexer* je podslužba s podporou zdrojů dat v kognitivní hledání, která je vybavená interní logikou pro vzorkování dat, čtení dat metadat, načítání dat a serializaci dat z nativních formátů do dokumentů JSON pro následné importy. 

Objekty BLOB v Azure Storage jsou indexované pomocí [indexeru Azure kognitivní hledání BLOB Storage](search-howto-indexing-azure-blob-storage.md). Tento indexer můžete vyvolat pomocí průvodce **importem dat** , REST API nebo sady .NET SDK. V kódu použijete Tento indexer nastavením typu a poskytnutím informací o připojení, které zahrnují účet Azure Storage společně s kontejnerem objektů BLOB. Objekty blob můžete podmnožinou vytvořit tak, že vytvoříte virtuální adresář, který pak můžete předat jako parametr, nebo filtrováním přípony typu souboru.

Indexer provádí "trhliny dokumentů", otevírání objektu BLOB pro kontrolu obsahu. Po připojení ke zdroji dat je prvním krokem v kanálu. V případě dat objektů BLOB se v takovém případě zjišťují soubory PDF, docs Office a další typy obsahu. Trhlina dokumentu s extrakcí textu je bezplatná. Pokud objekty blob obsahují obsah obrázků, budou se obrázky ignorovat, pokud [nepřidáte rozšíření AI](search-blob-ai-integration.md). Standardní indexování platí pouze pro text obsahu.

Indexer objektů BLOB obsahuje konfigurační parametry a podporuje sledování změn, pokud podkladová data poskytují dostatek informací. Další informace o základních funkcích najdete v části [Azure kognitivní hledání BLOB Storage indexer](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Podporované typy obsahu

Spuštěním indexeru objektů BLOB v kontejneru můžete extrahovat text a metadata z následujících typů obsahu jediným dotazem:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indexování metadat objektů BLOB

Běžný scénář, který usnadňuje řazení pomocí objektů BLOB libovolného typu obsahu, je vytvoření indexu vlastních metadat i systémových vlastností pro každý objekt BLOB. Tímto způsobem jsou informace o všech objektech blob indexovány bez ohledu na typ dokumentu uložený v indexu vyhledávací služby. Pomocí nového indexu pak můžete pokračovat v řazení, filtrování a omezující podmínky v celém obsahu služby Blob Storage.

> [!NOTE]
> Značky indexu objektu BLOB jsou nativně indexované službou BLOB Storage a jsou vystavené pro dotazování. Pokud atributy objektů BLOB (klíč/hodnota) vyžadují funkce indexování a filtrování, měly by se místo metadat používat značky indexu objektů BLOB.
>
> Další informace o indexu objektů BLOB najdete v tématu [Správa a hledání dat v Azure Blob Storage s využitím indexu objektů BLOB](../storage/blobs/storage-manage-find-blobs.md).

### <a name="indexing-json-blobs"></a>Indexování objektů BLOB JSON

Indexery je možné nakonfigurovat pro extrakci strukturovaného obsahu nalezeného v objektech blob, které obsahují JSON. Indexer může číst objekty blob JSON a analyzovat strukturovaný obsah do příslušných polí vyhledávacího dokumentu. Indexery mohou také přebírat objekty blob, které obsahují pole objektů JSON a mapovat jednotlivé prvky do samostatného dokumentu hledání. Můžete nastavit režim analýzy tak, aby ovlivnil typ objektu JSON vytvořený indexerem.

## <a name="search-blob-content-in-a-search-index"></a>Prohledat obsah objektu BLOB ve vyhledávacím indexu 

Výstup indexeru je index vyhledávání, který se používá pro interaktivní zkoumání pomocí bezplatného textu a filtrovaných dotazů v klientské aplikaci. V případě prvotního průzkumu a ověření obsahu doporučujeme začít v [Průzkumníku služby Search](search-explorer.md) na portálu a ověřit strukturu dokumentu. V Průzkumníku pro hledání můžete použít [jednoduchou syntaxi dotazů](query-simple-syntax.md), [úplnou syntaxi dotazů](query-lucene-syntax.md)a [syntaxi výrazu Filter](query-odata-filter-orderby-syntax.md) .

Dalším trvalým řešením je shromáždit vstupy dotazů a prezentovat odpověď jako výsledky hledání v klientské aplikaci. Následující kurz C# vysvětluje, jak vytvořit vyhledávací aplikaci: [vytvořte svou první aplikaci v Azure kognitivní hledání](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Další kroky

+ [Nahrávání, stahování a výpis objektů BLOB pomocí Azure Portal (úložiště objektů BLOB v Azure)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Nastavení indexeru objektů BLOB (Azure Kognitivní hledání)](search-howto-indexing-azure-blob-storage.md)