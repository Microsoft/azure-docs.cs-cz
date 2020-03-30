---
title: 'Úvodní příručka: Vytvoření indexu vyhledávání v PowerShellu pomocí rest API'
titleSuffix: Azure Cognitive Search
description: V tomto rychlém startu rozhraní REST API se dozvíte, jak vytvořit index, načíst data a spustit dotazy pomocí metody Invoke-RestMethod prostředí PowerShell a rozhraní REST API Azure Cognitive Search.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: 612751c2405cd55ad0b3760aa8e093e434a22f57
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121604"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-powershell-using-rest-apis"></a>Úvodní příručka: Vytvoření indexu Azure Cognitive Search v PowerShellu pomocí rest API
> [!div class="op_single_selector"]
> * [Prostředí PowerShell (REST)](search-create-index-rest-api.md)
> * [C #](search-create-index-dotnet.md)
> * [Pošťák (REST)](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [Portál](search-create-index-portal.md)
> 

Tento článek vás provede procesem vytváření, načítání a dotazování indexu Azure Cognitive Search pomocí PowerShellu a [azure cognitive search REST API](https://docs.microsoft.com/rest/api/searchservice/). Tento článek vysvětluje, jak interaktivně spouštět příkazy prostředí PowerShell. Případně můžete [stáhnout a spustit skript Powershell,](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) který provádí stejné operace.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Pro tento rychlý start jsou vyžadovány následující služby a nástroje. 

+ [Prostředí PowerShell 5.1 nebo novější](https://github.com/PowerShell/PowerShell)pomocí [metody Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) pro sekvenční a interaktivní kroky.

+ [Vytvořte službu Azure Cognitive Search](search-create-service-portal.md) nebo [najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý start můžete využít bezplatnou službu. 

## <a name="get-a-key-and-url"></a>Získání klíče a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do předplatného přidali Azure Cognitive Search, postupujte podle následujících kroků a získejte potřebné informace:

1. [Přihlaste se na portál Azure](https://portal.azure.com/)portal a na stránce **Přehled** vyhledávací služby získáte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

2. V **nastavení** > **klíče**, získat klíč správce pro úplná práva ke službě. Existují dva zaměnitelné klíče pro správu, které jsou k dispozici pro kontinuitu podnikání v případě, že potřebujete převrátit jeden. Primární nebo sekundární klíč můžete použít při požadavcích na přidávání, úpravy a odstranění objektů.

![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní api na každý požadavek odeslaný do vaší služby. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="connect-to-azure-cognitive-search"></a>Připojení k Azure Cognitive Search

1. V PowerShellu vytvořte **$headers** objekt pro uložení typu obsahu a klíče rozhraní API. Nahraďte klíč rozhraní API pro správce (YOUR-ADMIN-API-KEY) klíčem, který je platný pro vaši vyhledávací službu. Toto záhlaví je nutné nastavit pouze jednou po dobu trvání relace, ale přidáte ji ke každému požadavku. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Vytvořte **objekt $url,** který určuje kolekci indexů služby. Nahraďte název služby (YOUR-SEARCH-SERVICE-NAME) platnou vyhledávací službou.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name"
    ```

3. Spusťte **Invoke-RestMethod** k odeslání požadavku GET službě a ověření připojení. Přidejte **ConvertTo-Json,** abyste mohli zobrazit odpovědi odeslané zpět ze služby.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Pokud je služba prázdná a nemá žádné indexy, výsledky jsou podobné v následujícím příkladu. V opačném případě se zobrazí json reprezentace definice indexu.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1. Vytvoření indexu

Pokud nepoužíváte portál, musí existovat index ve službě před načtením dat. Tento krok definuje index a odešle jej do služby. Pro tento krok se používá [rozhraní API PRO VYTVOŘENÍ INDEXU REST.](https://docs.microsoft.com/rest/api/searchservice/create-index)

Požadované prvky indexu zahrnují název a kolekci polí. Kolekce polí definuje strukturu *dokumentu*. Každé pole má název, typ a atributy, které určují, jak se používá (například zda je fulltextové prohledávatelné, filtrovatelné nebo načítatelné ve výsledcích hledání). V rámci indexu musí být `Edm.String` jedno z polí typu označeno jako *klíč* pro identitu dokumentu.

Tento index se nazývá "hotely-rychlý start" a má definice polí, které vidíte níže. Jedná se o podmnožinu většího [indexu Hotels,](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) který se používá v jiných návodech. Ořezáni jsme to v tomto rychlém startu pro stručnost.

1. Vložte tento příklad do prostředí PowerShell a vytvořte **$body** objekt obsahující schéma indexu.

    ```powershell
    $body = @"
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
    "@
    ```

2. Nastavte identifikátor URI na kolekci indexů ve vaší službě a index *rychlého startu hotelů.*

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Spusťte příkaz s **$url**, **$headers**a **$body** vytvořte index ve službě. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Výsledky by měly vypadat podobně jako tento (zkrácenna první dvě pole pro stručnost):

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> Pro ověření můžete také zkontrolovat seznam Indexy na portálu.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Načíst dokumenty

Chcete-li vyžádat dokumenty, použijte požadavek HTTP POST do koncového bodu url indexu. Rozhraní REST API pro tento úkol je [Přidat, Aktualizovat nebo odstranit dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Vložte tento příklad do prostředí PowerShell a vytvořte **$body** objekt obsahující dokumenty, které chcete nahrát. 

    Tento požadavek zahrnuje dva úplné a jeden částečný záznam. Částečný záznam ukazuje, že můžete nahrát neúplné dokumenty. Parametr `@search.action` určuje způsob provádění indexování. Mezi platné hodnoty patří nahrávání, sloučení, sloučeníNebo nahrávání a odstranění. MergeOrUpload chování buď vytvoří nový dokument pro hotelId = 3 nebo aktualizuje obsah, pokud již existuje.

    ```powershell
    $body = @"
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
    "@
    ```

1. Nastavte koncový bod na *kolekci dokumenty rychlý start a* zahrnout operace indexu (indexy/hotels-quickstart/docs/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. Spusťte příkaz s **$url**, **$headers**a **$body** načíst dokumenty do indexu rychlého startu hotelů.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Výsledky by měly vypadat podobně jako v následujícím příkladu. Měl by vidět [stavový kód 201](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3. Prohledání indexu

Tento krok ukazuje, jak zadat dotaz na index pomocí [rozhraní API pro hledání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Ujistěte se, že při hledání používáte jednoduché uvozovky $urls. Řetězce dotazu **$** obsahují znaky a můžete vynechat nutnost uniknout, pokud je celý řetězec uzavřen v jednoduchých uvozovkách.

1. Nastavte koncový bod na *kolekci dokumenty _quickstart hotels* a přidejte parametr **hledání,** který předá v řetězci dotazu. 
  
   Tento řetězec provede prázdné hledání (search=*), vrací nehodnocený seznam (skóre hledání = 1,0) libovolných dokumentů. Ve výchozím nastavení Azure Cognitive Search vrátí 50 shod najednou. Jako strukturovaný tento dotaz vrátí celou strukturu dokumentu a hodnoty. Přidejte **$count=true,** abyste získali počet všech dokumentů ve výsledcích.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. Spusťte příkaz a odešlete **$url** do služby.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Výsledky by měly vypadat podobně jako následující výstup.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

Zkuste několik dalších příkladů dotazu získat cit pro syntaxi. Můžete provést hledání řetězce, doslovně $filter dotazy, omezit sadu výsledků, obor hledání na konkrétní pole a další.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu **Všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, nezapomeňte, že jste omezeni na tři indexy, indexery a zdroje dat. Můžete odstranit jednotlivé položky na portálu, abyste zůstali pod limitem. 

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí PowerShellu prošli základní pracovní postup pro vytváření a přístup k obsahu v Azure Cognitive Search. S ohledem na koncepty doporučujeme přejít k pokročilejším scénářům, jako je indexování ze zdrojů dat Azure;

> [!div class="nextstepaction"]
> [Kurz REST: Indexování a vyhledávání částečně strukturovaných dat (objekty BLOB JSON) v Azure Cognitive Search](search-semi-structured-data.md)