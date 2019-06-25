---
title: Náhled rozhraní REST API pro Azure Search 2019-05-06-Preview – Azure Search
description: Azure Search REST pro službu 2019 verze rozhraní API-05-06-Preview zahrnuje seznámit s experimentálními funkcemi, jako je znalostní báze úložiště a klíče spravované zákazníkem šifrování.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 5374ff896613dd8f8563a2054be8a92103e63fbb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523901"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Služba Azure Search REST api-version. 2019-05-06-Preview
Tento článek popisuje `api-version=2019-05-06-Preview` verze služby Azure Search REST API, nabízí seznámit s experimentálními funkcemi, které ještě není obecně k dispozici.

> [!NOTE]
> Funkce ve verzi Preview jsou k dispozici pro testování a experimentování s cílem shromažďování zpětné vazby a můžou se změnit. Důrazně nedoporučujeme používání verze preview rozhraní API v aplikacích v produkčním prostředí.


## <a name="new-in-2019-05-06-preview"></a>Novinka v 2019-05-06-Preview

[**Znalostní báze úložiště** ](knowledge-store-concept-intro.md) je nový cíl kanál rozšíření založené na AI. Kromě indexu je teď možné zachovat naplněných daty strukturách vytvořených během indexování ve službě Azure storage. Můžete řídit fyzické struktury vašich dat pomocí prvků v dovedností, včetně jak budou data tvarovat, zda data jsou uložená v Blob storage nebo Table storage, a zda existuje několik zobrazení.

[**Klíče spravované zákazníkem šifrování** ](search-security-manage-encryption-keys.md) straně služby šifrování neaktivních je také nová funkce ve verzi preview. Kromě integrované šifrování neaktivních spravovaný microsoftem můžete použít další úroveň šifrování, kde jste jediným vlastníkem klíčů.

## <a name="other-preview-features"></a>Další funkce ve verzi preview

Funkce, které jsme oznámili v dřívějších verzích Preview jsou stále ve verzi public preview. Pokud voláte rozhraní API z předchozích verzí preview rozhraní api –, můžete nadále používat tuto verzi nebo přepněte na `2019-05-06-Preview` beze změn očekávané chování.

+ [parametr dotazu moreLikeThis](search-more-like-this.md) Vyhledá dokumenty, které souvisí s určitým dokumentem. Tato funkce byla v dřívějších verzích Preview. 
* [Indexování objektů blob CSV](search-howto-index-csv-blobs.md) vytvoří jeden dokument na řádek, na rozdíl od jednoho dokumentu na textový objekt blob.
* [Podpora rozhraní API MongoDB pro Cosmos DB indexery](search-howto-index-cosmosdb.md) je ve verzi preview.


## <a name="how-to-call-a-preview-api"></a>Jak volat rozhraní API ve verzi preview

Starší verze Preview jsou stále v provozu, ale jsou pak zastaralá v čase. Pokud váš kód volá `api-version=2016-09-01-Preview` nebo `api-version=2017-11-11-Preview`, tato volání jsou stále platné. Nicméně pouze nejnovější verzi preview se aktualizují s vylepšeními. 

Následující příklad syntaxe znázorňuje volání na verzi preview rozhraní API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Služba Azure Search je dostupná ve více verzí. Další informace najdete v tématu [verze rozhraní API](search-api-versions.md).

## <a name="next-steps"></a>Další postup

Projděte si referenční dokumentaci rozhraní REST API služby Azure Search. Pokud narazíte na potíže, požádat nás o pomoc na [StackOverflow](https://stackoverflow.com/) nebo [obraťte se na podporu](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Vyhledávací služba Reference k rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/)