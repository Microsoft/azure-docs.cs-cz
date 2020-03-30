---
title: 'Úvodní příručka: Vytvoření indexu vyhledávání v Postman pomocí REST API'
titleSuffix: Azure Cognitive Search
description: V tomto rychlém startu rozhraní REST API se dozvíte, jak volat rozhraní API AZURE Cognitive Search REST pomocí Postman a ukázkových dat a definic.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: c502886aac9d13f7a470a9b83f1fc12334913beb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121629"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-postman-using-rest-apis"></a>Úvodní příručka: Vytvoření indexu Azure Cognitive Search v Postman pomocí rest API
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C #](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portál](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

Jedním z nejjednodušších způsobů, jak prozkoumat [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice) používá Postman nebo jiný nástroj pro testování webu formulovat požadavky HTTP a zkontrolovat odpovědi. S využitím správných nástrojů a pokynů můžete odesílat žádosti a zobrazovat odpovědi, ještě než začnete psát kód.

Tento článek vysvětluje, jak interaktivně formulovat požadavky. Případně můžete [stáhnout a importovat kolekci Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) a použít předdefinované požadavky.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Pro tento rychlý start jsou vyžadovány následující služby a nástroje. 

+ [Desktopová aplikace Pošťák](https://www.getpostman.com/) se používá pro odesílání požadavků do Azure Cognitive Search.

+ [Vytvořte službu Azure Cognitive Search](search-create-service-portal.md) nebo [najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý start můžete využít bezplatnou službu. 

## <a name="get-a-key-and-url"></a>Získání klíče a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do předplatného přidali Azure Cognitive Search, postupujte podle následujících kroků a získejte potřebné informace:

1. [Přihlaste se na portál Azure](https://portal.azure.com/)portal a na stránce **Přehled** vyhledávací služby získáte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč správce pro úplná práva ke službě. Existují dva zaměnitelné klíče pro správu, které jsou k dispozici pro kontinuitu podnikání v případě, že potřebujete převrátit jeden. Primární nebo sekundární klíč můžete použít při požadavcích na přidávání, úpravy a odstranění objektů.

![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní api na každý požadavek odeslaný do vaší služby. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="connect-to-azure-cognitive-search"></a>Připojení k Azure Cognitive Search

V této části můžete pomocí webového nástroje nastavit připojení k Azure Cognitive Search. Každý nástroj zachová informace záhlaví požadavku pro relaci, což znamená, že stačí zadat api-key a Content-Type jednou.

Pro oba nástroje, musíte zvolit příkaz (GET, POST, PUT a tak dále), zadejte koncový bod adresy URL a pro některé úkoly, zadejte JSON v těle požadavku. Nahraďte název vyhledávací služby (YOUR-SEARCH-SERVICE-NAME) platnou hodnotou. Přidejte, `$select=name` chcete-li vrátit pouze název každého indexu. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Všimněte si předpony HTTPS, názvu služby, názvu objektu (v tomto případě kolekce indexů) a [verze rozhraní API](search-api-versions.md). Verze rozhraní api je povinný řetězec s `?api-version=2019-05-06` malou písmena určený jako pro aktuální verzi. Verze rozhraní API se pravidelně aktualizují. Zahrnutím verze api-version v každé žádosti získáte úplnou kontrolu nad tím, která se použije.  

Složení hlavičky požadavku obsahuje dva prvky, typ obsahu a klíč api, který se používá k ověření azure kognitivního vyhledávání. Nahraďte klíč rozhraní API správce (YOUR-AZURE-SEARCH-ADMIN-API-KEY) platnou hodnotou. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

V Pošťáku zformulovat požadavek, který vypadá jako následující snímek obrazovky. Zvolte **GET** jako sloveso, zadejte adresu URL a klepněte na **tlačítko Odeslat**. Tento příkaz se připojí k Azure Cognitive Search, přečte kolekci indexů a vrátí stavový kód HTTP 200 na úspěšné připojení. Pokud vaše služba již obsahuje indexy, odpověď bude také obsahovat definice indexu.

![Adresa URL a záhlaví požadavku pošťáka](media/search-get-started-postman/postman-url.png "Adresa URL a záhlaví požadavku pošťáka")

## <a name="1---create-an-index"></a>1. Vytvoření indexu

V Azure Cognitive Search obvykle vytvoříte index před jeho načtením s daty. Pro tuto úlohu se používá [rozhraní API PRO VYTVOŘENÍ INDEXU REST.](https://docs.microsoft.com/rest/api/searchservice/create-index) 

Adresa URL je rozšířena tak, aby zahrnovala `hotels` název indexu.

Chcete-li to v Pošťák:

1. Změňte sloveso na **PUT**.

2. Zkopírujte tuto `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06`adresu URL .

3. Zadejte definici indexu (kód připravený k kopírování je uveden níže) v textu požadavku.

4. Klikněte na **Odeslat**.

![Index JSON dokument v textu požadavku](media/search-get-started-postman/postman-request.png "Index JSON dokument v textu požadavku")

### <a name="index-definition"></a>Definice indexu

Kolekce polí definuje strukturu dokumentu. Každý dokument musí mít tato pole a každé pole musí mít datový typ. Pole řetězců se používají ve fulltextovém vyhledávání, takže pokud chcete umožnit prohledávání číselných údajů, přetypujte je na řetězce.

Atributy pole určují povolenou akci. Rozhraní REST API ve výchozím nastavení povolují mnoho akcí. Například všechny řetězce ve výchozím nastavení podporují prohledávání, načítání, filtrování a omezující vlastnosti. Často stačí nastavit atributy, když potřebujete vypnout chování.

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

## <a name="2---load-documents"></a>2 - Načíst dokumenty

Vytvoření indexu a jeho naplnění jsou samostatné kroky. V Azure Cognitive Search index obsahuje všechna prohledávatelná data, která můžete poskytnout jako dokumenty JSON. Pro tuto úlohu se používá [rozhraní REST API pro přidání, aktualizaci nebo odstranění dokumentů.](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 

Adresa URL je rozšířena tak, aby zahrnovala `docs` kolekce a `index` operace.

Chcete-li to v Pošťák:

1. Změňte operaci na **POST**.

2. Zkopírujte tuto `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`adresu URL .

3. Zadejte dokumenty JSON (kód připravený k kopírování je níže) v textu požadavku.

4. Klikněte na **Odeslat**.

![Dokumenty JSON v textu požadavku](media/search-get-started-postman/postman-docs.png "Dokumenty JSON v textu požadavku")

### <a name="json-documents-to-load-into-the-index"></a>Dokumenty JSON, které se načtou do indexu

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

Během několika sekund by se měla zobrazit odpověď HTTP 201 v seznamu relací. To znamená, že se dokumenty úspěšně vytvořily. 

Pokud se zobrazí kód 207, nejméně jeden dokument nešel nahrát. Pokud se zobrazí kód 404, hlavička nebo text žádosti obsahuje syntaktickou chybu. Ověřte, že jste změnili koncový bod tak, aby zahrnoval `/docs/index`.

> [!Tip]
> Pro vybrané zdroje dat můžete zvolit přístup s použitím alternativního *indexeru*, který zjednodušuje a snižuje množství kódu vyžadovaného pro indexování. Další informace najdete v tématu [Operace indexeru](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3. Prohledání indexu

Nyní, když jsou načteny index a dokumenty, můžete na ně vyřazovat dotazy pomocí [rozhraní REST API pro hledání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Adresa URL je rozšířena tak, aby zahrnovala výraz dotazu určený pomocí vyhledávacího operátoru.

Chcete-li to v Pošťák:

1. Změňte sloveso na **GET**.

2. Zkopírujte tuto `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`adresu URL .

3. Klikněte na **Odeslat**.

Tento dotaz je prázdný a vrátí počet dokumentů ve výsledcích hledání. Požadavek a odpověď by měly vypadat podobně jako následující snímek obrazovky pro Pošťák po klepnutí na tlačítko **Odeslat**. Stavový kód by měl být 200.

 ![GET s vyhledávacím řetězcem na adrese URL](media/search-get-started-postman/postman-query.png "GET s vyhledávacím řetězcem na adrese URL")

Zkuste několik dalších příkladů dotazu získat cit pro syntaxi. Můžete provést hledání řetězce, doslovně $filter dotazy, omezit sadu výsledků, obor hledání na konkrétní pole a další.

Zaměnujte aktuální adresu URL za níže uvedené adresy URL a kliknutím na **Odeslat** pokaždé zobrazte výsledky.

```
# Query example 1 - Search on restaurant and wifi
# Return only the HotelName, Description, and Tags fields
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=restaurant wifi&$count=true&$select=HotelName,Description,Tags&api-version=2019-05-06

# Query example 2 - Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$filter=Rating gt 4&$select=HotelName,Rating&api-version=2019-05-06

# Query example 3 - Take the top two results, and show only HotelName and Category in the results
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=boutique&$top=2&$select=HotelName,Category&api-version=2019-05-06

# Query example 4 - Sort by a specific field (Address/City) in ascending order
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating&api-version=2019-05-06
```

## <a name="get-index-properties"></a>Získat vlastnosti indexu
Pomocí funkce [Získat statistiku](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) můžete také zadat dotaz na počty dokumentů a velikost indexu: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06
```

Přidání `/stats` do adresy URL vrátí informace o indexu. V nástroji Postman by vaše žádost měla vypadat podobně jako v následujícím příkladu a odpověď by měla obsahovat počet dokumentů a využité místo v bajtech.

 ![Získat informace o indexu](media/search-get-started-postman/postman-system-query.png "Získat informace o indexu")

Všimněte si, že syntaxe api-version se liší. Pro tuto žádost použijte k připojení verze api-version znak `?`. Odděluje `?` cestu URL od řetězce dotazu, zatímco & odděluje každý pár "name=value" v řetězci dotazu. V tomto dotazu je api-version první a také jedinou položkou v řetězci dotazu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu **Všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, nezapomeňte, že jste omezeni na tři indexy, indexery a zdroje dat. Můžete odstranit jednotlivé položky na portálu, abyste zůstali pod limitem. 

## <a name="next-steps"></a>Další kroky

Nyní, když víte, jak provádět základní úkoly, můžete pokročit s dalšími voláními rozhraní REST API pro pokročilejší funkce, jako jsou indexery nebo [nastavení kanálu kognitivního vyhledávání](cognitive-search-tutorial-blob.md). Pro další krok doporučujeme následující odkaz:

> [!div class="nextstepaction"]
> [Kurz REST: Indexování a vyhledávání částečně strukturovaných dat (objekty BLOB JSON) v Azure Cognitive Search](search-semi-structured-data.md)
