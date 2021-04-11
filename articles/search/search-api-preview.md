---
title: Seznam funkcí Preview
titleSuffix: Azure Cognitive Search
description: Funkce verze Preview se uvolňují, aby zákazníci mohli poskytovat zpětnou vazbu ke svým návrhům a nástrojům. Tento článek obsahuje úplný seznam všech funkcí, které jsou v současnosti ve verzi Preview.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: e0bbc9fc1e6259b70e1f1d46b545300a568601d2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109794"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Funkce ve verzi Preview v Azure Kognitivní hledání

Tento článek obsahuje úplný seznam všech funkcí, které jsou ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Funkce ve verzi Preview, které se převádějí do všeobecné dostupnosti, se z tohoto seznamu odeberou. Pokud funkce není uvedená níže, můžete předpokládat, že je všeobecně dostupná. Oznámení týkající se obecné dostupnosti najdete v tématu [aktualizace služby](https://azure.microsoft.com/updates/?product=search) nebo [novinky](whats-new.md).

|Zapnut&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategorie | Popis | Dostupnost  |
|---------|------------------|-------------|---------------|
| [**Sémantické vyhledávání**](semantic-search-overview.md) | Relevance (bodování) | Sémantické hodnocení výsledků, titulků a odpovědí. | [Search REST API 2020-06-30-Preview](/rest/api/searchservice/preview-api/search-documents) a Průzkumník vyhledávání (portál). |
| [**kontrolu pravopisu**](cognitive-search-aml-skill.md) | Dotaz | Volitelná Oprava pravopisu pro termíny dotazu pro jednoduché, úplné a sémantické dotazy. | [Search REST API 2020-06-30 – Preview](/rest/api/searchservice/preview-api/search-documents) |
| [**Indexer SharePointu Online**](search-howto-index-sharepoint-online.md) | Zdroj dat indexeru | Nový zdroj dat pro indexování obsahu SharePointu založeného na indexeru | [Search REST API 2020-06-30 – Preview](/rest/api/searchservice/preview-api/create-indexer) |
| [**Azure Machine Learning (AML) – dovednost**](cognitive-search-aml-skill.md) | Obohacení AI| Nový typ dovednosti pro integraci koncového bodu Inferencing z Azure Machine Learning. Začněte s [tímto kurzem](cognitive-search-tutorial-aml-custom-skill.md). | Použijte [Search REST API 2020-06-30-Preview](/rest/api/searchservice/) nebo 2019-05-06-Preview. K dispozici také na portálu v návrhu dovednosti, za předpokladu, že Kognitivní hledání a služby Azure ML se nasazují ve stejném předplatném. |
| [**parametr featuresMode**](/rest/api/searchservice/preview-api/search-documents#query-parameters) | Relevance (bodování) | Rozšíření skóre relevance tak, aby zahrnovalo detaily: skóre podle pole, frekvence termínu pro pole a počet jedinečných tokenů, které se shodují. Tyto datové body můžete využívat ve [vlastních řešeních pro bodování](https://github.com/Azure-Samples/search-ranking-tutorial). | Přidejte tento parametr dotazu pomocí [vyhledávacích dokumentů (REST)](/rest/api/searchservice/preview-api/search-documents) s rozhraním API-Version = 2020-06 -30-preview nebo 2019-05-06-Preview. |
| [**Relace ladění**](cognitive-search-debug-session.md) | Portál, obohacení AI (dovednosti) | Editor dovednosti v relaci, který se používá k prozkoumání a řešení problémů s dovednosti. Opravy, které se použijí během relace ladění, se dají uložit do dovednosti ve službě. | Pouze portál, pomocí odkazů na střední stránku na stránce Přehled otevřete relaci ladění. |
| [**Obnovitelné odstranění nativního objektu BLOB**](search-howto-index-changed-deleted-blobs.md) | Indexery, objekty blob Azure| Indexovací člen služby Azure Blob Storage v Azure Kognitivní hledání rozpozná objekty blob, které jsou ve stavu undeleteded, a během indexování odebere odpovídající hledaný dokument. | Přidejte toto nastavení konfigurace pomocí [Create indexer (REST)](/rest/api/searchservice/create-indexer) s rozhraním API-Version = 2020-06 -30-Preview nebo API-Version = 2019-05 -06-Preview. |
| [**Dovednost pro detekci PII**](cognitive-search-skill-pii-detection.md) | Rozšíření AI (dovednosti) | Vnímání odbornosti, která se používá při indexování, která extrahuje osobní údaje ze vstupního textu a poskytuje možnost jejich maskování z tohoto textu různými způsoby. | Na tuto dovednost ve verzi Preview se odkazuje pomocí editoru dovednosti na portálu nebo [Vytvoření dovednosti (REST)](/rest/api/searchservice/create-skillset) s rozhraním API-Version = 2020-06 -30-Preview nebo API-Version = 2019-05 -06-Preview. |
| [**Přírůstkové obohacení**](cognitive-search-incremental-indexing-conceptual.md) | Konfigurace indexeru| Přidá do kanálu pro rozšíření ukládání do mezipaměti, což vám umožní znovu použít stávající výstup, pokud cílené změny, jako je například aktualizace dovednosti nebo jiného objektu, nezmění obsah. Ukládání do mezipaměti se týká pouze obohacených dokumentů vyprodukovaných dovednosti.| Přidejte toto nastavení konfigurace pomocí [Create indexer (REST)](/rest/api/searchservice/create-indexer) s rozhraním API-Version = 2020-06 -30-Preview nebo API-Version = 2019-05 -06-Preview. |
| [**Cosmos DB indexer: rozhraní API MongoDB, rozhraní Gremlin API rozhraní API Cassandra**](search-howto-index-cosmosdb.md) | Zdroj dat indexeru | Pro Cosmos DB je SQL API všeobecně dostupné, ale rozhraní API pro MongoDB, Gremlin a Cassandra jsou ve verzi Preview. | Jenom pro Gremlin a Cassandra se [nejdřív Zaregistrujte](https://aka.ms/azure-cognitive-search/indexer-preview) , aby bylo možné povolit podporu pro vaše předplatné na back-endu. Zdroje dat MongoDB můžete nakonfigurovat na portálu. V opačném případě je konfigurace zdroje dat pro všechna tři rozhraní API podporovaná pomocí funkcí [vytvořit zdroj dat (REST)](/rest/api/searchservice/create-data-source) s rozhraním API-Version = 2020-06 -30-Preview nebo API-Version = 2019-05 -06-Preview. |
|  [**Azure Data Lake Storage Gen2 indexer**](search-howto-index-azure-data-lake-storage.md) | Zdroj dat indexeru | Indexuje obsah a metadata z Data Lake Storage Gen2.| Vyžaduje se [registrace](https://aka.ms/azure-cognitive-search/indexer-preview) , aby bylo možné povolit podporu pro vaše předplatné na back-endu. Přístup k tomuto zdroji dat pomocí [Vytvoření zdroje dat (REST)](/rest/api/searchservice/create-data-source) s rozhraním API-Version = 2020-06 -30-Preview nebo API-Version = 2019-05 -06-Preview. |
| [**moreLikeThis**](search-more-like-this.md) | Dotaz | Vyhledá dokumenty, které jsou relevantní pro určitý dokument. Tato funkce je ve starších verzích Preview. | Tento parametr dotazu přidejte do volání [vyhledávacích dokumentů (REST)](/rest/api/searchservice/search-documents) pomocí API-Version = 2020-06 -30-preview, 2019-05-06-preview, 2016-09-01-preview nebo 2017-11-11-Preview. |

## <a name="how-to-call-a-preview-rest-api"></a>Jak volat REST API verze Preview

Azure Kognitivní hledání vždy předem vydává experimentální funkce přes REST API a pak prostřednictvím předprodejní verze sady .NET SDK.

Funkce ve verzi Preview jsou k dispozici pro testování a experimentování s cílem shromažďovat zpětnou vazbu k návrhu a implementaci funkcí. Z tohoto důvodu se funkce verze Preview můžou v průběhu času měnit, možná způsobem, který přeruší zpětnou kompatibilitu. To je na rozdíl od funkcí ve verzi GA, které jsou stabilní a nepravděpodobné, že se nemění s výjimkou malých a nekompatibilních oprav a vylepšení. Funkce ve verzi Preview se také nedělají vždy do verze GA.

I když některé funkce verze Preview můžou být dostupné na portálu a .NET SDK, má REST API vždycky funkce ve verzi Preview.

+ Pro operace vyhledávání [**`2020-06-30-Preview`**](/rest/api/searchservice/index-preview) je aktuální verze Preview.

+ Pro operace správy [**`2019-10-01-Preview`**](/rest/api/searchmanagement/index-2019-10-01-preview) je aktuální verze Preview.

Starší verze Preview jsou pořád funkční, ale v průběhu času se stanou zastaralé. Pokud váš kód volá `api-version=2019-05-06-Preview` nebo `api-version=2016-09-01-Preview` nebo `api-version=2017-11-11-Preview` , jsou tato volání stále platná. Jenom nejnovější verze Preview se ale aktualizuje s vylepšeními.

Následující příklad syntaxe znázorňuje volání rozhraní API verze Preview.

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2020-06-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

Služba Azure Kognitivní hledání je dostupná ve více verzích. Další informace najdete v tématu [verze rozhraní API](search-api-versions.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si referenční dokumentaci k rozhraní API pro hledání REST Preview. Pokud narazíte na problémy, požádejte nás, abychom vám pomohli [Stack Overflow](https://stackoverflow.com/) nebo [kontaktujte podporu](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Odkaz na REST API vyhledávací služby (Preview)](/rest/api/searchservice/index-preview)