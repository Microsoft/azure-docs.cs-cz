---
title: Verze Preview REST API Azure Search 2019-05-06 – Preview – Azure Search
description: Služba Azure Search REST API verze 2019-05-06-Preview zahrnuje experimentální funkce, jako jsou úložiště znalostí a šifrovací klíče spravované zákazníkem.
services: search
author: HeidiSteen
manager: nitinme
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7fa280742556b7bc42d2c7fb30c880f836eef62b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649983"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Rozhraní REST API služby Azure Search – verze 2019-05-06 – Preview
Tento článek popisuje `api-version=2019-05-06-Preview` verzi služby Azure Search REST API, která nabízí experimentální funkce, které ještě nejsou všeobecně dostupné.

> [!NOTE]
> Funkce ve verzi Preview jsou k dispozici pro testování a experimentování s cílem shromažďování názorů a mohou se změnit. Důrazně doporučujeme používat rozhraní API ve verzi Preview v produkčních aplikacích.


## <a name="new-in-2019-05-06-preview"></a>Novinka ve verzi 2019-05-06 – Preview

[**Znalostní báze Knowledge Store**](knowledge-store-concept-intro.md) je nový cíl kanálu pro rozšíření na bázi AI. Kromě indexu teď můžete zachovat naplněné datové struktury vytvořené při indexování ve službě Azure Storage. Fyzické struktury dat můžete řídit prostřednictvím prvků v dovednosti, včetně způsobu, jakým jsou data uložená v úložišti tabulek nebo objektů blob, a to, jestli existuje více zobrazení.

[**Šifrovací klíče spravované zákazníkem**](search-security-manage-encryption-keys.md) pro šifrování na straně služby je také novou funkcí ve verzi Preview. Kromě integrovaného šifrování v klidovém formátu, které spravuje Microsoft, můžete použít další vrstvu šifrování, kde jste jediným vlastníkem klíčů.

## <a name="other-preview-features"></a>Další funkce ve verzi Preview

Funkce oznámené v předchozích verzích Preview jsou stále ve verzi Public Preview. Pokud voláte rozhraní API s dřívější verzí rozhraní API Preview, můžete tuto verzi dál používat nebo přepnout na `2019-05-06-Preview` bez jakýchkoli změn očekávaného chování.

+ [parametr dotazu moreLikeThis](search-more-like-this.md) vyhledá dokumenty, které jsou relevantní pro určitý dokument. Tato funkce je ve starších verzích Preview. 
* [Indexování objektů BLOB ve formátu CSV](search-howto-index-csv-blobs.md) vytvoří jeden dokument na řádek, na rozdíl od jednoho dokumentu u objektu BLOB.
* [Podpora MONGODB API pro indexery Cosmos DB](search-howto-index-cosmosdb.md) je ve verzi Preview.


## <a name="how-to-call-a-preview-api"></a>Jak volat rozhraní API pro verzi Preview

Starší verze Preview jsou pořád funkční, ale v průběhu času se stanou zastaralé. Pokud váš kód volá `api-version=2016-09-01-Preview` nebo `api-version=2017-11-11-Preview`, jsou tato volání stále platná. Jenom nejnovější verze Preview se ale aktualizuje s vylepšeními. 

Následující příklad syntaxe znázorňuje volání rozhraní API verze Preview.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Služba Azure Search je dostupná ve více verzích. Další informace najdete v tématu [verze rozhraní API](search-api-versions.md).

## <a name="next-steps"></a>Další postup

Přečtěte si referenční dokumentaci ke službě Azure Search REST API. Pokud narazíte na problémy, požádejte nás o pomoc na [StackOverflow](https://stackoverflow.com/) nebo [kontaktujte podporu](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Odkaz na REST API služby Search](https://docs.microsoft.com/rest/api/searchservice/)