---
title: REST API verze 2019-05-06-Preview
titleSuffix: Azure Cognitive Search
description: Služba Azure Kognitivní hledání REST API verze 2019-05-06-Preview zahrnuje experimentální funkce, jako jsou znalostní báze Knowledge Store a zákaznické šifrovací klíče spravované zákazníky.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/04/2020
ms.openlocfilehash: ee8e11670d5b3e0f0edd57e3f87566faa60d4bc4
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754415"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Rozhraní REST API pro službu Azure Kognitivní hledání – verze 2019-05-06 – Preview

Tento článek popisuje REST API `api-version=2019-05-06-Preview` verzi služby Search, která nabízí experimentální funkce, které ještě nejsou všeobecně dostupné.

> [!NOTE]
> Funkce ve verzi Preview jsou k dispozici pro testování a experimentování s cílem shromažďování názorů a mohou se změnit. Důrazně doporučujeme používat rozhraní API ve verzi Preview v produkčních aplikacích.


## <a name="new-in-2019-05-06-preview"></a>Novinka ve verzi 2019-05-06 – Preview

+ [Přírůstkové obohacení (Preview)](cognitive-search-incremental-indexing-conceptual.md) přidá do kanálu pro rozšíření ukládání do mezipaměti, což vám umožní znovu použít stávající výstup, pokud cílené změny, jako je například aktualizace dovednosti nebo jiného objektu, nemění obsah. Ukládání do mezipaměti se týká pouze obohacených dokumentů vyprodukovaných dovednosti.

+ [Cosmos DB indexer](search-howto-index-cosmosdb.md) podporuje rozhraní MongoDB API (Preview), rozhraní Gremlin API (Preview) a rozhraní API Cassandra (Preview).

+ [Azure Data Lake Storage Gen2 indexer (Preview)](search-howto-index-azure-data-lake-storage.md) může indexovat obsah a metadata z Data Lake Storage Gen2.

+ [Extrakce dokumentu (ve verzi Preview)](cognitive-search-skill-document-extraction.md) je vnímání dovedností, která se používá při indexování, která umožňuje extrakci obsahu souboru v rámci dovednosti. Dříve došlo k odhalení dokumentu pouze před dovednosti prováděním. Při přidání této dovednosti můžete tuto operaci provést i v rámci provádění dovednosti.

+ [Překlad textu (ve verzi Preview)](cognitive-search-skill-text-translation.md) je vnímání dovedností použitou při indexování, která vyhodnocuje text a pro každý záznam vrátí text přeložený do zadaného cílového jazyka.

+ [Znalostní báze Knowledge Store](knowledge-store-concept-intro.md) je nový cíl kanálu pro rozšíření na bázi AI. Fyzická datová struktura existuje v úložišti objektů BLOB v Azure a v úložišti tabulek Azure a je vytvořená a naplněná při spuštění indexeru, který má připojené rozpoznávání dovednosti. Definice samotného úložiště znalostí je specifikována v rámci definice dovednosti. V rámci definice znalostní báze Knowledge Store můžete řídit fyzické struktury dat prostřednictvím prvků *projekce* , které určují způsob, jakým jsou data uložená v úložišti tabulek nebo objektů blob, a to, jestli existuje více zobrazení.

+ [Šifrovací klíče spravované zákazníkem](search-security-manage-encryption-keys.md) pro šifrování na straně služby je také novou funkcí ve verzi Preview. Kromě integrovaného šifrování v klidovém formátu, které spravuje Microsoft, můžete použít další vrstvu šifrování, kde jste jediným vlastníkem klíčů.

## <a name="earlier-preview-features"></a>Dřívější funkce ve verzi Preview

Funkce oznámené v předchozích verzích Preview jsou stále ve verzi Public Preview. Pokud voláte rozhraní API s dřívější verzí rozhraní API Preview, můžete tuto verzi dál používat nebo přepnout na `2019-05-06-Preview` bez jakýchkoli změn očekávaného chování.

+ [parametr dotazu moreLikeThis](search-more-like-this.md) vyhledá dokumenty, které jsou relevantní pro určitý dokument. Tato funkce je ve starších verzích Preview. 

+ [Indexování objektů BLOB ve formátu CSV](search-howto-index-csv-blobs.md) vytvoří jeden dokument na řádek, na rozdíl od jednoho dokumentu u objektu BLOB.

## <a name="how-to-call-a-preview-api"></a>Jak volat rozhraní API pro verzi Preview

Starší verze Preview jsou pořád funkční, ale v průběhu času se stanou zastaralé. Pokud váš kód volá `api-version=2016-09-01-Preview` nebo `api-version=2017-11-11-Preview`, jsou tato volání stále platná. Jenom nejnovější verze Preview se ale aktualizuje s vylepšeními. 

Následující příklad syntaxe znázorňuje volání rozhraní API verze Preview.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Služba Azure Kognitivní hledání je dostupná ve více verzích. Další informace najdete v tématu [verze rozhraní API](search-api-versions.md).

## <a name="next-steps"></a>Další kroky

Projděte si referenční dokumentaci REST API hledání. Pokud narazíte na problémy, požádejte nás o pomoc na [StackOverflow](https://stackoverflow.com/) nebo [kontaktujte podporu](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Odkaz na REST API služby Search](https://docs.microsoft.com/rest/api/searchservice/)