---
title: Náhled funkcí v rozhraní REST API
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search service REST API verze 2019-05-06-Preview obsahuje experimentální funkce, jako je úložiště znalostí a ukládání do mezipaměti indexeru pro přírůstkové obohacení.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162272"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Náhled funkcí v Azure Cognitive Search

V tomto článku jsou uvedeny funkce aktuálně ve verzi Preview. Z tohoto seznamu budou odebrány funkce, které přecházejí z náhledu na obecnou dostupnost. Můžete zkontrolovat [aktualizace služeb](https://azure.microsoft.com/updates/?product=search) nebo Co je [nového](whats-new.md) pro oznámení týkající se obecné dostupnosti.

Zatímco některé funkce náhledu mohou být k dispozici na portálu a .NET SDK, rozhraní REST API má vždy funkce náhledu.

+ Pro operace [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) vyhledávání je aktuální verze náhledu.
+ Pro operace [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) správy je aktuální verze náhledu.

> [!IMPORTANT]
> Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Funkce obohacení umělou a id

Prozkoumejte nejnovější vylepšení obohacení umělou a iniciérou pomocí [rozhraní PREVIEW Search API](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview).

|||
|-|-|
| [Dovednost Vyhledávání vlastních entit (náhled)](cognitive-search-skill-custom-entity-lookup.md ) | Kognitivní dovednost, která hledá text z vlastního, uživatelem definovaného seznamu slov a frází. Pomocí tohoto seznamu označí všechny dokumenty odpovídajícími entitami. Dovednost také podporuje stupeň fuzzy odpovídající, které lze použít k nalezení shody, které jsou podobné, ale ne zcela přesné. | 
| [Dovednost detekce pii (náhled)](cognitive-search-skill-pii-detection.md) | Kognitivní dovednosti používané při indexování, které extrahuje osobně identifikovatelné informace ze vstupního textu a poskytuje možnost maskovat z tohoto textu různými způsoby.| 
| [Přírůstkové obohacení (náhled)](cognitive-search-incremental-indexing-conceptual.md) | Přidá ukládání do mezipaměti do kanálu obohacení, což umožňuje znovu použít existující výstup, pokud cílená změna, například aktualizace sady dovedností nebo jiného objektu, nezmění obsah. Ukládání do mezipaměti se vztahuje pouze na obohacené dokumenty vytvořené skillset.| 
| [Knowledge Store (Preview)](knowledge-store-concept-intro.md) | Nový cíl kanálu obohacení založeného na umělou tak. Fyzická datová struktura existuje v úložišti objektů Blob Azure a azure table storage a je vytvořena a naplněna, když spustíte indexer, který má připojenou kognitivní skillset. Definice samotného úložiště znalostí je specifikována v rámci definice skillset. V rámci definice úložiště znalostí řídíte fyzické struktury dat prostřednictvím *prvků projekce,* které určují, jak jsou data tvarována, zda jsou data uložena v úložišti table storage nebo blob a zda existuje více zobrazení.| 

## <a name="indexing-and-query-features"></a>Funkce indexování a dotazů

Funkce náhledu indexeru jsou k dispozici ve verzi Preview Search API. 

|||
|-|-|
| [Indexer pro Cosmos DB](search-howto-index-cosmosdb.md) | Podpora pro mongoDB API (preview), Gremlin API (preview) a Cassandra API (preview) typy rozhraní API. | 
|  [Indexer Úložiště datových jezer Azure Gen2 (preview)](search-howto-index-azure-data-lake-storage.md) | Indexujte obsah a metadata z úložiště datového jezera Gen2.| 
| [moreLikeTento parametr dotazu (náhled)](search-more-like-this.md) | Vyhledá dokumenty, které jsou relevantní pro konkrétní dokument. Tato funkce byla v dřívějších náhledech. | 

## <a name="management-features"></a>Funkce správy

|||
|-|-|
| [Podpora privátního koncového bodu](service-create-private-endpoint.md) | Můžete vytvořit virtuální síť se zabezpečeným klientem (například virtuálním počítačem) a pak vytvořit vyhledávací službu, která používá privátní koncový bod. |
| Omezení přístupu k ip adres | Pomocí [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) rozhraní REST API pro správu můžete vytvořit službu, která má omezení, na kterých je povolen přístup k IP adresám. |

## <a name="earlier-preview-features"></a>Dřívější funkce náhledu

Funkce oznámené v dřívějších náhledech, pokud nepřešly na obecnou dostupnost, jsou stále ve verzi Public Preview. Pokud voláte rozhraní API s dřívější verzí rozhraní API preview, můžete pokračovat `2019-05-06-Preview` v používání této verze nebo přepnout na beze změn očekávaného chování.

## <a name="how-to-call-a-preview-api"></a>Jak volat rozhraní PREVIEW API

Starší náhledy jsou stále funkční, ale časem se stanou zastaralými. Pokud váš `api-version=2016-09-01-Preview` kód `api-version=2017-11-11-Preview`volá nebo , tyto hovory jsou stále platné. Vylepšení však aktualizují pouze nejnovější verze preview. 

Následující příklad syntaxe ilustruje volání verze rozhraní API preview.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Služba Azure Cognitive Search je dostupná ve více verzích. Další informace naleznete v [tématu VERZE ROZHRANÍ API](search-api-versions.md).

## <a name="next-steps"></a>Další kroky

Projděte si referenční dokumentaci rozhraní REST API. Pokud narazíte na problémy, požádejte nás o pomoc na [StackOverflow](https://stackoverflow.com/) nebo [kontaktujte podporu](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Vyhledávací služba REST API Reference](https://docs.microsoft.com/rest/api/searchservice/)