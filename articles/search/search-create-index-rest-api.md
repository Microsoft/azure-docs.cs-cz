---
title: Vytvořte index v kódu pomocí rozhraní REST API – Azure Search
description: Vytvoření textu v plném znění prohledávatelný index v kódu pomocí HTTP reqeusts a rozhraní REST API Azure Search.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: f47aead95d7135e2528fea11c116effa93df4c4c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309208"
---
# <a name="create-an-azure-search-index-using-the-rest-api"></a>Vytvoření indexu Azure Search pomocí rozhraní REST API
> [!div class="op_single_selector"]
>
> * [Přehled](search-what-is-an-index.md)
> * [Azure Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
>
>

Tento článek vás provede procesem vytvoření [indexu](https://docs.microsoft.com/rest/api/searchservice/Create-Index) Azure Search pomocí rozhraní REST API služby Azure Search.

Předtím, než podle těchto pokynů vytvoříte index, byste už měli mít [vytvořenou službu Azure Search](search-create-service-portal.md).

Pokud chcete vytvořit index Azure Search pomocí rozhraní REST API, vydáte jednu žádost HTTP POST do koncového bodu adresy URL služby Azure Search. Definice indexu bude obsažená v textu žádosti jako obsah JSON ve správném formátu.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identifikace klíče rozhraní API správce služby Azure Search
Teď, když máte zřízenou službu Azure Search, můžete vydávat žádosti HTTP na koncový bod adresy URL služby pomocí rozhraní REST API. *Všechny* žádosti rozhraní API musí obsahovat klíč rozhraní API (api-key) vygenerovaný pro službu Search, kterou jste zřídili. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

1. Pokud chcete najít klíče api-key svojí služby, musíte se přihlásit k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte do okna služby Azure Search.
3. Klikněte na ikonu klíčů.

Vaše služba bude mít *klíče správce* a *klíče dotazů*.

* Primární a sekundární *klíče správce* udělují úplná práva ke všem operacím, včetně možnosti spravovat službu, vytvářet a odstraňovat indexy, indexery a zdroje dat. Existují dva klíče, takže pokud se rozhodnete znovu vygenerovat primární klíč, můžete dál používat sekundární klíč, a naopak.
* Vaše *klíče dotazů* udělují přístup jen pro čtení k indexům a dokumentům a obvykle se distribuují klientským aplikacím, které vydávají požadavky hledání.

Pro účely vytvoření indexu můžete použít primární nebo sekundární klíč správce.

## <a name="define-your-azure-search-index-using-well-formed-json"></a>Definování indexu Azure Search pomocí správného formátu JSON
Jedna žádost HTTP POST do služby vytvoří váš index. Text žádosti HTTP POST bude obsahovat jeden objekt JSON, který definuje index Azure Search.

1. První vlastností tohoto objektu JSON je název indexu.
2. Druhou vlastností tohoto objektu JSON je pole JSON s názvem `fields`, které obsahuje samostatný objekt JSON pro každé pole v indexu. Každý z těchto objektů JSON obsahuje více párů název/hodnota pro každý atribut pole, včetně „name“, „type“ atd.

Při navrhování indexu je důležité zohlednit uživatelskou práci při vyhledávání a potřeby podniku, protože každému poli se musí přiřadit [správné atributy](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Tyto atributy určují, které funkce vyhledávání (filtrování, používání faset, řazení fulltextového vyhledávání atd.) se použijí u kterých polí. Pokud kterýkoli atribut nezadáte, příslušná funkce hledání se ve výchozím nastavení povolí, ledaže ji výslovně zakážete.

V našem příkladu jsme nazvali index „hotels“ a pole jsme definovali takto:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Atributy indexu jsme pro každé pole pečlivě zvolili podle toho, jak se pravděpodobně použijí v aplikaci. Například `hotelId` je jedinečný klíč, který lidé hledající hotely nejspíš neznají, a proto jsme u tohoto pole zakázali fulltextové vyhledávání tím, že jsme `searchable` nastavili na `false`, což šetří místo v indexu.

Upozorňujeme, že právě jedno pole v indexu typu `Edm.String` musí být určené jako klíčové pole.

Výše uvedená definice indexu používá pro pole `description_fr` analyzátor jazyka, protože je určené k ukládání francouzského textu. Další informace o analyzátorech jazyka najdete v [tématu jazykové podpory](https://docs.microsoft.com/rest/api/searchservice/Language-support) a příslušném [příspěvku na blogu](https://azure.microsoft.com/blog/language-support-in-azure-search/).

## <a name="issue-the-http-request"></a>Vydání žádosti HTTP
1. Použijte definici indexu jako text žádosti a vydejte žádost HTTP POST do adresy URL koncového bodu služby Azure Search. V adrese URL nezapomeňte použít název služby jako název hostitele a vložit správné `api-version` jako parametr řetězce dotazu (v době publikování tohoto dokumentu je aktuální verze rozhraní API `2017-11-11`).
2. V hlavičkách žádostí zadejte `Content-Type` jako `application/json`. V hlavičce `api-key` budete taky muset zadat klíč správce služby, který jste identifikovali v kroku I.

Abyste mohli vydat níže uvedenou žádost, budete muset zadat vlastní název služby a klíč rozhraní API:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [api-key]


V případě úspěšné žádosti by se měl zobrazit stavový kód 201 (vytvořeno). Další informace o vytvoření indexu prostřednictvím rozhraní REST API najdete v [referenčních informacích k rozhraní API](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Další informace o stavových kódech HTTP, které se mohou vrátit v případě selhání, naleznete v tématu [Stavové kódy HTTP (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

Pokud jste s indexem hotovi a chcete ho odstranit, stačí vydat žádost HTTP DELETE. Takto bychom například odstranili index „hotels“:

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11
    api-key: [api-key]


## <a name="next-steps"></a>Další postup
Po vytvoření indexu Azure Search budete připravení [nahrát do indexu obsah](search-what-is-data-import.md), abyste mohli začít prohledávat data.
