---
title: Funkce ve verzi Preview v REST API
titleSuffix: Azure Cognitive Search
description: Služba Azure Kognitivní hledání REST API verze 2019-05-06-Preview zahrnuje experimentální funkce, jako jsou úložiště znalostí a ukládání do mezipaměti indexerů pro přírůstkové obohacení.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 9985e7ac70c5851699839a95d1e23af4dcca35e7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935107"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funkce ve verzi Preview v Azure Kognitivní hledání

Tento článek obsahuje seznam funkcí, které jsou v současnosti ve verzi Preview. Funkce, které přecházejí z verze Preview na obecnou dostupnost, se z tohoto seznamu odeberou. Můžete kontrolovat [aktualizace služby](https://azure.microsoft.com/updates/?product=search) nebo [novinky](whats-new.md) pro oznámení týkající se všeobecné dostupnosti.

I když některé funkce verze Preview můžou být dostupné na portálu a .NET SDK, má REST API vždycky funkce ve verzi Preview. Aktuální verze Preview rozhraní API je `2019-05-06-Preview`.

> [!IMPORTANT]
> Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="features-in-public-preview"></a>Funkce ve verzi Public Preview

+ [Vlastní vyhledávání entit (Preview)](cognitive-search-skill-custom-entity-lookup.md ) vyhledává text z vlastního uživatelsky definovaného seznamu slov a frází. Pomocí tohoto seznamu jsou všechny dokumenty označeny všemi vyhovujícími entitami. Dovednost také podporuje stupeň přibližné shody, které lze použít pro hledání shod, které jsou podobné, ale nejsou zcela přesné. 

+ [Detekce PII (Preview)](cognitive-search-skill-pii-detection.md) je způsob rozpoznávání, který se používá při indexování, která extrahuje identifikovatelné osobní údaje ze vstupního textu a poskytuje možnost jejich maskování z tohoto textu různými způsoby.

+ [Přírůstkové obohacení (Preview)](cognitive-search-incremental-indexing-conceptual.md) přidá do kanálu pro rozšíření ukládání do mezipaměti, což vám umožní znovu použít stávající výstup, pokud cílené změny, jako je například aktualizace dovednosti nebo jiného objektu, nemění obsah. Ukládání do mezipaměti se týká pouze obohacených dokumentů vyprodukovaných dovednosti.

+ [Cosmos DB indexer](search-howto-index-cosmosdb.md) podporuje rozhraní MongoDB API (Preview), rozhraní Gremlin API (Preview) a rozhraní API Cassandra (Preview).

+ [Azure Data Lake Storage Gen2 indexer (Preview)](search-howto-index-azure-data-lake-storage.md) může indexovat obsah a metadata z Data Lake Storage Gen2.

+ [Znalostní báze Knowledge Store (Preview)](knowledge-store-concept-intro.md) je nový cíl kanálu pro rozšíření na bázi AI. Fyzická datová struktura existuje v úložišti objektů BLOB v Azure a v úložišti tabulek Azure a je vytvořená a naplněná při spuštění indexeru, který má připojené rozpoznávání dovednosti. Definice samotného úložiště znalostí je specifikována v rámci definice dovednosti. V rámci definice znalostní báze Knowledge Store můžete řídit fyzické struktury dat prostřednictvím prvků *projekce* , které určují způsob, jakým jsou data uložená v úložišti tabulek nebo objektů blob, a to, jestli existuje více zobrazení.

+ [parametr dotazu moreLikeThis (Preview)](search-more-like-this.md) vyhledá dokumenty, které jsou relevantní pro určitý dokument. Tato funkce je ve starších verzích Preview. 

## <a name="earlier-preview-features"></a>Dřívější funkce ve verzi Preview

Funkce oznámené v předchozích verzích Preview, pokud nejsou převedené na obecnou dostupnost, jsou stále ve verzi Public Preview. Pokud voláte rozhraní API s dřívější verzí rozhraní API Preview, můžete tuto verzi dál používat nebo přepnout na `2019-05-06-Preview` bez jakýchkoli změn očekávaného chování.

## <a name="how-to-call-a-preview-api"></a>Jak volat rozhraní API pro verzi Preview

Starší verze Preview jsou pořád funkční, ale v průběhu času se stanou zastaralé. Pokud váš kód volá `api-version=2016-09-01-Preview` nebo `api-version=2017-11-11-Preview`, jsou tato volání stále platná. Jenom nejnovější verze Preview se ale aktualizuje s vylepšeními. 

Následující příklad syntaxe znázorňuje volání rozhraní API verze Preview.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Služba Azure Kognitivní hledání je dostupná ve více verzích. Další informace najdete v tématu [verze rozhraní API](search-api-versions.md).

## <a name="next-steps"></a>Další kroky

Projděte si referenční dokumentaci REST API hledání. Pokud narazíte na problémy, požádejte nás o pomoc na [StackOverflow](https://stackoverflow.com/) nebo [kontaktujte podporu](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Odkaz na REST API služby Search](https://docs.microsoft.com/rest/api/searchservice/)