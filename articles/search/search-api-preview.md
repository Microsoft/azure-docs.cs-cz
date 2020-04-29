---
title: Funkce ve verzi Preview v REST API
titleSuffix: Azure Cognitive Search
description: Služba Azure Kognitivní hledání REST API verze 2019-05-06-Preview zahrnuje experimentální funkce, jako jsou úložiště znalostí a ukládání do mezipaměti indexerů pro přírůstkové obohacení.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162272"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funkce ve verzi Preview v Azure Kognitivní hledání

Tento článek obsahuje seznam funkcí, které jsou v současnosti ve verzi Preview. Funkce, které přecházejí z verze Preview na obecnou dostupnost, se z tohoto seznamu odeberou. Můžete kontrolovat [aktualizace služby](https://azure.microsoft.com/updates/?product=search) nebo [novinky](whats-new.md) pro oznámení týkající se všeobecné dostupnosti.

I když některé funkce verze Preview můžou být dostupné na portálu a .NET SDK, má REST API vždycky funkce ve verzi Preview.

+ Pro operace vyhledávání [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) je aktuální verze Preview.
+ Pro operace správy [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) je aktuální verze Preview.

> [!IMPORTANT]
> Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Funkce obohacení AI

Prozkoumejte nejnovější vylepšení rozšíření AI prostřednictvím [rozhraní API pro vyhledávání ve verzi Preview](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview).

|||
|-|-|
| [Dovednosti při vyhledávání vlastních entit (Preview)](cognitive-search-skill-custom-entity-lookup.md ) | Vnímání dovedností, která hledá text z vlastního uživatelsky definovaného seznamu slov a frází. Pomocí tohoto seznamu jsou všechny dokumenty označeny všemi vyhovujícími entitami. Dovednost také podporuje stupeň přibližné shody, které lze použít pro hledání shod, které jsou podobné, ale nejsou zcela přesné. | 
| [Dovednost pro detekci PII (Preview)](cognitive-search-skill-pii-detection.md) | Vnímání odbornosti, která se používá při indexování, která extrahuje osobní údaje ze vstupního textu a poskytuje možnost jejich maskování z tohoto textu různými způsoby.| 
| [Přírůstkové obohacení (Preview)](cognitive-search-incremental-indexing-conceptual.md) | Přidá do kanálu pro rozšíření ukládání do mezipaměti, což vám umožní znovu použít stávající výstup, pokud cílené změny, jako je například aktualizace dovednosti nebo jiného objektu, nezmění obsah. Ukládání do mezipaměti se týká pouze obohacených dokumentů vyprodukovaných dovednosti.| 
| [Knowledge Store (Preview)](knowledge-store-concept-intro.md) | Nový cíl kanálu rozšíření na bázi AI. Fyzická datová struktura existuje v úložišti objektů BLOB v Azure a v úložišti tabulek Azure a je vytvořená a naplněná při spuštění indexeru, který má připojené rozpoznávání dovednosti. Definice samotného úložiště znalostí je specifikována v rámci definice dovednosti. V rámci definice znalostní báze Knowledge Store můžete řídit fyzické struktury dat prostřednictvím prvků *projekce* , které určují způsob, jakým jsou data uložená v úložišti tabulek nebo objektů blob, a to, jestli existuje více zobrazení.| 

## <a name="indexing-and-query-features"></a>Indexování a funkce dotazů

Funkce ve verzi Preview indexeru jsou dostupné v rozhraní API pro vyhledávání ve verzi Preview. 

|||
|-|-|
| [Indexer pro Cosmos DB](search-howto-index-cosmosdb.md) | Podpora rozhraní API pro MongoDB (Preview), rozhraní Gremlin API (Preview) a rozhraní API Cassandra (Preview) typů rozhraní API. | 
|  [Azure Data Lake Storage Gen2 indexer (Preview)](search-howto-index-azure-data-lake-storage.md) | Indexuje obsah a metadata z Data Lake Storage Gen2.| 
| [parametr dotazu moreLikeThis (Preview)](search-more-like-this.md) | Vyhledá dokumenty, které jsou relevantní pro určitý dokument. Tato funkce je ve starších verzích Preview. | 

## <a name="management-features"></a>Funkce správy

|||
|-|-|
| [Podpora privátních koncových bodů](service-create-private-endpoint.md) | Můžete vytvořit virtuální síť se zabezpečeným klientem (například virtuálním počítačem) a pak vytvořit vyhledávací službu, která používá privátní koncový bod. |
| Omezení přístupu IP | Pomocí [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) REST API pro správu můžete vytvořit službu, která má omezení přístupu k povoleným IP adresám. |

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