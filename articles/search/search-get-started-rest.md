---
title: 'Rychlý Start: vytvoření indexu vyhledávání pomocí rozhraní REST API'
titleSuffix: Azure Cognitive Search
description: V tomto rychlém startu REST API se dozvíte, jak volat rozhraní REST API Azure Kognitivní hledání pomocí post nebo Visual Studio Code.
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: b701a80c3c3b7e5a558d875aca2fb34aea89edff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98119624"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>Rychlý Start: vytvoření indexu služby Azure Kognitivní hledání pomocí rozhraní REST API

Tento článek vysvětluje, jak pomocí [rozhraní REST API služby Azure kognitivní hledání](/rest/api/searchservice) a klienta API pro posílání a přijímání požadavků formulovat požadavky na REST API. Pomocí klienta rozhraní API a těchto pokynů můžete odesílat žádosti a zobrazovat odpovědi před zápisem kódu.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

V tomto článku se používá aplikace po pracovní ploše. Můžete [Stáhnout a importovat kolekci post](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) , pokud chcete použít předdefinované požadavky.

## <a name="prerequisites"></a>Předpoklady

V tomto rychlém startu jsou vyžadovány následující služby a nástroje. 

+ [Aplikace po pracovní ploše](https://www.getpostman.com/) se používá k odesílání požadavků do Azure kognitivní hledání.

+ [Vytvořte službu Azure kognitivní hledání](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu. 

## <a name="copy-a-key-and-url"></a>Zkopírování klíče a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení**  >  **klíče** Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-rest/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="connect-to-azure-cognitive-search"></a>Připojení k Azure Kognitivní hledání

V této části můžete pomocí svého webového nástroje vybrat nastavení připojení k Azure Kognitivní hledání. Každý nástroj uchovává informace hlavičky žádosti pro relaci, což znamená, že stačí zadat jenom klíč rozhraní API a typ Content-Type.

Pro kterýkoli nástroj musíte zvolit příkaz (GET, POST, PUT a tak dále), poskytnout koncový bod adresy URL a pro některé úlohy v těle žádosti zadat JSON. Nahraďte název vyhledávací služby (název služby-SEARCH-SERVICE-NAME) platnou hodnotou. Přidat `$select=name` pro vrácení pouze názvu každého indexu. 

> `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2020-06-30&$select=name`

Všimněte si předpony HTTPS, názvu služby, názvu objektu (v tomto případě kolekce indexů) a [verze API-Version](search-api-versions.md). Verze API-Version je povinný, malý řetězec zadaný jako `?api-version=2020-06-30` aktuální verze. Verze rozhraní API se pravidelně aktualizují. Zahrnutím verze api-version v každé žádosti získáte úplnou kontrolu nad tím, která se použije.  

Sestavování hlaviček žádosti zahrnuje dva prvky: `Content-Type` a `api-key` slouží k ověření v kognitivní hledání Azure. Nahraďte klíč rozhraní API pro správu (vaše – AZURE-SEARCH-ADMIN-API-KEY) platnou hodnotou. 

```http
api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
Content-Type: application/json
```

V poli pro odeslání si formulujte požadavek, který vypadá jako na následujícím snímku obrazovky. Jako příkaz vyberte **Get (získat** ), zadejte adresu URL a klikněte na **Odeslat**. Tento příkaz se připojí k Azure Kognitivní hledání, přečte kolekci indexů a vrátí stavový kód HTTP 200 po úspěšném připojení. Pokud vaše služba již indexy obsahuje, bude odpověď zahrnovat také definice indexu.

![Adresa URL a záhlaví žádosti post](media/search-get-started-rest/postman-url.png "Adresa URL a záhlaví žádosti post")

## <a name="1---create-an-index"></a>1. Vytvoření indexu

V Azure Kognitivní hledání obvykle index vytvoříte předtím, než ho načtete s daty. Pro tento úkol se používá [REST API pro vytvoření indexu](/rest/api/searchservice/create-index) . 

Adresa URL je rozšířena tak, aby obsahovala `hotels` název indexu.

Provedete to po:

1. Změňte příkaz na **Put**.

2. Kopírovat v této adrese URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2020-06-30` .

3. Zadejte definici indexu (kód připravený ke kopírování je uveden níže) v těle žádosti.

4. Klikněte na **Odeslat**.

![Dokument JSON indexu v textu požadavku](media/search-get-started-rest/postman-request.png "Dokument JSON indexu v textu požadavku")

### <a name="index-definition"></a>Definice indexu

Kolekce Fields definuje strukturu dokumentu. Každý dokument musí mít tato pole a každé pole musí mít datový typ. Pole řetězců se používají při fulltextovém vyhledávání. Pokud potřebujete číselná data, která je možné prohledávat, budete muset jako řetězce přetypování číselných dat.

Atributy pole určují povolenou akci. Rozhraní REST API ve výchozím nastavení povolují mnoho akcí. Například všechny řetězce ve výchozím nastavení podporují prohledávání, načítání, filtrování a omezující vlastnosti. Často je třeba nastavit atributy pouze v případě, že je nutné vypnout chování.

```json
{
    "name": "hotels-quickstart",  
    "fields": [
        {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
        {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
        {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
        {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
        {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
        {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
        {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
        {"name": "Address", "type": "Edm.ComplexType", 
        "fields": [
        {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
        {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
        ]
     }
  ]
}
```

Po odeslání této žádosti byste měli získat odpověď HTTP 201, která značí úspěšné vytvoření indexu. Tuto akci můžete ověřit na portálu, ale nezapomeňte, že stránka portálu má intervaly obnovení, takže aktualizace může pár minut trvat.

> [!TIP]
> Pokud se zobrazí kód HTTP 504, ověřte, jestli je v adrese URL určený protokol HTTPS. Pokud se zobrazí kód HTTP 400 nebo 404, zkontrolujte, jestli v textu žádosti nejsou chyby způsobené kopírováním a vkládáním. Kód HTTP 403 obvykle znamená potíže s klíčem rozhraní API (buď je klíč neplatný, nebo je při určení klíče rozhraní API použitá nesprávná syntaxe).

## <a name="2---load-documents"></a>2. načtení dokumentů

Vytvoření indexu a jeho naplnění jsou samostatné kroky. V Azure Kognitivní hledání index obsahuje všechna hledaná data. V tomto scénáři se data poskytují jako dokumenty JSON. Pro tento úkol se používá [REST API přidat, aktualizovat nebo odstranit dokumenty](/rest/api/searchservice/addupdate-or-delete-documents) . 

Adresa URL je rozšířena tak, aby zahrnovala `docs` kolekce a `index` operace.

Provedete to po:

1. Změňte příkaz na **post**.

2. Kopírovat v této adrese URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2020-06-30` .

3. Poskytněte dokumenty JSON (kód připravený k kopírování je níže) v těle žádosti.

4. Klikněte na **Odeslat**.

![Dokumenty JSON v textu požadavku](media/search-get-started-rest/postman-docs.png "Dokumenty JSON v textu požadavku")

### <a name="json-documents-to-load-into-the-index"></a>Dokumenty JSON, které se mají načíst do indexu

Text žádosti obsahuje čtyři dokumenty, které se mají přidat do indexu hotels.

```json
{
    "value": [
    {
    "@search.action": "upload",
    "HotelId": "1",
    "HotelName": "Secret Point Motel",
    "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
    "Category": "Boutique",
    "Tags": [ "pool", "air conditioning", "concierge" ],
    "ParkingIncluded": false,
    "LastRenovationDate": "1970-01-18T00:00:00Z",
    "Rating": 3.60,
    "Address": 
        {
        "StreetAddress": "677 5th Ave",
        "City": "New York",
        "StateProvince": "NY",
        "PostalCode": "10022",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "2",
    "HotelName": "Twin Dome Motel",
    "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
    "Category": "Boutique",
    "Tags": [ "pool", "free wifi", "concierge" ],
    "ParkingIncluded": false,
    "LastRenovationDate": "1979-02-18T00:00:00Z",
    "Rating": 3.60,
    "Address": 
        {
        "StreetAddress": "140 University Town Center Dr",
        "City": "Sarasota",
        "StateProvince": "FL",
        "PostalCode": "34243",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Category": "Resort and Spa",
    "Tags": [ "air conditioning", "bar", "continental breakfast" ],
    "ParkingIncluded": true,
    "LastRenovationDate": "2015-09-20T00:00:00Z",
    "Rating": 4.80,
    "Address": 
        {
        "StreetAddress": "3393 Peachtree Rd",
        "City": "Atlanta",
        "StateProvince": "GA",
        "PostalCode": "30326",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "4",
    "HotelName": "Sublime Cliff Hotel",
    "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
    "Category": "Boutique",
    "Tags": [ "concierge", "view", "24-hour front desk service" ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1960-02-06T00:00:00Z",
    "Rating": 4.60,
    "Address": 
        {
        "StreetAddress": "7400 San Pedro Ave",
        "City": "San Antonio",
        "StateProvince": "TX",
        "PostalCode": "78216",
        "Country": "USA"
        }
    }
  ]
}
```

Během několika sekund by se v seznamu relace měla zobrazit odpověď HTTP 201. To znamená, že se dokumenty úspěšně vytvořily. 

Pokud se zobrazí kód 207, nejméně jeden dokument nešel nahrát. Pokud se zobrazí kód 404, hlavička nebo text žádosti obsahuje syntaktickou chybu. Ověřte, že jste změnili koncový bod tak, aby zahrnoval `/docs/index`.

> [!Tip]
> Pro vybrané zdroje dat můžete zvolit přístup s použitím alternativního *indexeru*, který zjednodušuje a snižuje množství kódu vyžadovaného pro indexování. Další informace najdete v tématu [Operace indexeru](/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3. Prohledání indexu

Teď, když je načtený index a sada dokumentů, můžete pro ně vystavovat dotazy pomocí [hledání dokumentů REST API](/rest/api/searchservice/search-documents).

Adresa URL je rozšířena tak, aby zahrnovala výraz dotazu, zadaný pomocí operátoru hledání.

Provedete to po:

1. Změňte příkaz tak, aby se **získal**.

2. Kopírovat v této adrese URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2020-06-30` .

3. Klikněte na **Odeslat**.

Tento dotaz je prázdný a vrátí počet dokumentů ve výsledcích hledání. Po kliknutí na **Odeslat** může žádost a odpověď vypadat podobně jako na následujícím snímku obrazovky. Stavový kód by měl být 200.

 ![ZÍSKAT pomocí vyhledávacího řetězce na adrese URL](media/search-get-started-rest/postman-query.png "ZÍSKAT pomocí vyhledávacího řetězce na adrese URL")

Vyzkoušejte si několik dalších příkladů dotazů, které vám pomohou s syntaxí. Můžete provést hledání v řetězci, doslovné znění $filter dotazů, omezit sadu výsledků, určit rozsah hledání na konkrétní pole a další.

Aktuální adresu URL zaměňte tak, že na ně kliknete níže, a zobrazíte výsledky kliknutím na **Odeslat** .

```
# Query example 1 - Search on restaurant and wifi
# Return only the HotelName, Description, and Tags fields
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=restaurant wifi&$count=true&$select=HotelName,Description,Tags&api-version=2020-06-30

# Query example 2 - Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$filter=Rating gt 4&$select=HotelName,Rating&api-version=2020-06-30

# Query example 3 - Take the top two results, and show only HotelName and Category in the results
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=boutique&$top=2&$select=HotelName,Category&api-version=2020-06-30

# Query example 4 - Sort by a specific field (Address/City) in ascending order
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating&api-version=2020-06-30
```

## <a name="get-index-properties"></a>Získat vlastnosti indexu

K dotazování na počty dokumentů a velikost indexu můžete použít také možnost [získat statistiku](/rest/api/searchservice/get-index-statistics) : 

```http
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2020-06-30
```

Přidání `/stats` do adresy URL vrátí informace o indexu. V nástroji Postman by vaše žádost měla vypadat podobně jako v následujícím příkladu a odpověď by měla obsahovat počet dokumentů a využité místo v bajtech.

 ![Získat informace o indexu](media/search-get-started-rest/postman-system-query.png "Získat informace o indexu")

Všimněte si, že syntaxe api-version se liší. Pro tuto žádost použijte k připojení verze api-version znak `?`. `?`Odděluje cestu URL od řetězce dotazu, zatímco & odděluje každou dvojici název = hodnota v řetězci dotazu. V tomto dotazu je api-version první a také jedinou položkou v řetězci dotazu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

Když teď víte, jak provádět základní úlohy, můžete přejít vpřed pomocí dalších REST API volání pro pokročilejší funkce, jako jsou indexery nebo [Nastavení kanálu pro rozšíření](cognitive-search-tutorial-blob.md) , který přidává transformace obsahu k indexování. V dalším kroku doporučujeme následující odkaz:

> [!div class="nextstepaction"]
> [Kurz: použití REST a AI k vygenerování prohledávatelných obsahu z objektů blob Azure](cognitive-search-tutorial-blob.md)