---
title: Nahrání dat (REST API – Azure Search) | Dokumentace Microsoftu
description: Zjistěte, jak odesílat data do indexu Azure Search pomocí REST API.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 12/08/2016
ms.openlocfilehash: 5322faf04d29643bba2d1371cef23ab224675adb
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Odesílání dat do služby Azure Search pomocí REST API
> [!div class="op_single_selector"]
>
> * [Přehled](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
>
>

Tento článek vám ukáže, jak používat [REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/) k importu dat do indexu Azure Search.

Před zahájením tohoto názorného průvodce byste již měli mít [vytvořený index Azure Search](search-what-is-an-index.md).

Pro vkládání dokumentů do indexu pomocí REST API budete vydávat na URL koncového bodu indexu požadavek HTTP POST. Text žádosti požadavku HTTP je objekt JSON obsahující dokumenty, které se mají přidat, upravit nebo odstranit.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identifikace klíče rozhraní API správce služby Azure Search
Při odesílání požadavků HTTP na vaši službu pomocí REST API musí *každá* žádost na rozhraní API obsahovat klíč api-key, který byl vygenerovaný pro zřízenou službu Vyhledávání. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

1. Pokud chcete najít klíče api-key svojí služby, přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte do okna služby Azure Search.
3. Klikněte na ikonu klíčů.

Vaše služba bude mít *klíče správce* a *klíče dotazů*.

* Primární a sekundární *klíče správce* udělují úplná práva ke všem operacím, včetně možnosti spravovat službu, vytvářet a odstraňovat indexy, indexery a zdroje dat. Existují dva klíče, takže pokud se rozhodnete znovu vygenerovat primární klíč, můžete dál používat sekundární klíč, a naopak.
* Vaše *klíče dotazů* udělují přístup jen pro čtení k indexům a dokumentům a obvykle se distribuují klientským aplikacím, které vydávají požadavky hledání.

Pro účely importování dat do indexu můžete použít primární nebo sekundární klíč správce.

## <a name="decide-which-indexing-action-to-use"></a>Rozhodněte, jakou akci indexování použít
Při používání REST API budete na URL koncového bodu indexu Azure Search vydávat požadavky HTTP POST s textem žádosti ve formátu JSON. Objekt JSON v požadavku HTTP bude obsahovat jedno pole JSON s názvem „value“ s objekty JSON reprezentujícími dokumenty, které si přejete přidat do indexu, aktualizovat nebo odstranit.

Každý objekt JSON v poli „value“ reprezentuje dokument, který se má indexovat. Každý z těchto objektů obsahuje klíč dokumentu a určuje požadovanou akci indexování (odeslání, sloučení, odstranění atd.). V závislosti na zvolené akci musí objekt pro každý dokument obsahovat pouze určitá pole.

| @search.action | Popis | Potřebná pole pro každý dokument | Poznámky |
| --- | --- | --- | --- |
| `upload` |Akce `upload` je podobná akci „upsert“, kdy je dokument vložený, pokud je nový a aktualizovaný nebo nahrazený, pokud již existuje. |klíč a další pole, která si přejete definovat |Pokud aktualizujete nebo nahrazujete stávající dokument, bude každé pole, které není zadané v žádosti, nastavené na `null`. K tomu dojde i v případě, že bylo pole dříve nastavené na nenulovou hodnotu. |
| `merge` |Aktualizuje stávající dokument se zadanými poli. Pokud dokument v indexu neexistuje, sloučení selže. |klíč a další pole, která si přejete definovat |Každé pole zadané ve sloučení nahradí stávající pole v dokumentu. To zahrnuje i pole typu `Collection(Edm.String)`. Například pokud dokument obsahuje pole `tags` s hodnotou `["budget"]` a vy spustíte sloučení s polem `tags` s hodnotou `["economy", "pool"]`, konečná hodnota pole `tags` bude `["economy", "pool"]`. Hodnota nebude `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Pokud již dokument s daným klíčem v indexu existuje, chová se tato akce jako `merge`. Pokud dokument neexistuje, chová se s novým dokumentem jako `upload`. |klíč a další pole, která si přejete definovat |- |
| `delete` |Odebere z indexu zadaný dokument. |pouze klíč |Všechna zadaná pole kromě pole klíče budou ignorována. Chcete-li odebrat z dokumentu jednotlivá pole, použijte místo toho `merge` a jednoduše nastavte hodnotu pole na „null“. |

## <a name="construct-your-http-request-and-request-body"></a>Konstrukce požadavku HTTP a textu žádosti
Nyní, když jste shromáždili potřebné hodnoty polí pro akce indexu, jste připraveni vytvořit vlastní požadavek HTTP a text žádosti ve formátu JSON pro import vašich dat.

#### <a name="request-and-request-headers"></a>Požadavek a hlavičky požadavku
V URL budete muset poskytnout název služby, název indexu (v tomto případě „hotels“) a správnou verzi rozhraní API (v době publikování tohoto dokumentu je aktuální verze rozhraní API `2016-09-01`). Budete muset definovat `Content-Type` a hlavičky požadavku `api-key`. K tomu použijte jeden z klíčů správce vaší služby.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>Text žádosti
```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

V tomto případě používáme jako akce hledání `upload`, `mergeOrUpload`, a `delete`.

Předpokládejme, že je ukázkový index „hotels“ již naplněný řadou dokumentů. Všimněte si, že při použití `mergeOrUpload` nebylo nutné zadat všechna možná pole dokumentu, a při použití `delete` jsme zadali pouze klíč dokumentu (`hotelId`).

Mějte také na paměti, že můžete v jedné žádosti indexování zahrnout maximálně 1000 dokumentů (nebo 16 MB).

## <a name="understand-your-http-response-code"></a>Pochopení kódu odpovědi HTTP
#### <a name="200"></a>200
Po odeslání úspěšné žádosti indexování obdržíte odpověď protokolu HTTP se stavovým kódem `200 OK`. Text JSON odpovědi protokolu HTTP bude následující:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
Stavový kód `207` se vrátí, pokud nebyla alespoň jedna položka úspěšně indexovaná. Text JSON odpovědi protokolu HTTP bude obsahovat informace o neúspěšných dokumentech.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [!NOTE]
> Často to znamená, že zatížení vaší služby vyhledávání dosahuje bodu, kdy začnou požadavky indexování vracet odpovědi `503`. V tom případě důrazně doporučujeme, aby se váš klientský kód stáhnul a chvíli počkal před tím, než to zkusí znovu. Systému tak poskytnete čas k obnovení, což zvýší šanci na úspěšné provedení dalších požadavků. Rychlé opakování požadavků pouze prodlouží situaci.
>
>

#### <a name="429"></a>429
Stavový kód `429` bude vrácen, pokud jste překročili kvótu pro počet dokumentů na index.

#### <a name="503"></a>503
Stavový kód `503` bude vrácen, pokud nedošlo k úspěšné indexaci položek v požadavku. Tato chyba znamená, že je systém velmi zatížen a váš požadavek nelze v tuto chvíli zpracovat.

> [!NOTE]
> V tom případě důrazně doporučujeme, aby se váš klientský kód stáhnul a chvíli počkal před tím, než to zkusí znovu. Systému tak poskytnete čas k obnovení, což zvýší šanci na úspěšné provedení dalších požadavků. Rychlé opakování požadavků pouze prodlouží situaci.
>
>

Další informace o akcích dokumentu a úspěšných/neúspěšných odpovědích naleznete v tématu [Přidání, aktualizování nebo odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents). Další informace o stavových kódech HTTP, které se mohou vrátit v případě selhání, naleznete v tématu [Stavové kódy HTTP (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

## <a name="next-steps"></a>Další kroky
Po naplnění indexu Azure Search budete připraveni začít vydávat dotazy pro vyhledávání dokumentů.  Podrobnosti naleznete v tématu [Dotazování indexu Azure Search](search-query-overview.md).
