---
title: 'Rychlý start pro PowerShell: Vytvoření, načtení a dotazování indexů Azure Search REST API – Azure Search pomocí'
description: Vysvětluje, jak vytvořit index, načtení dat a spouštění dotazů pomocí Powershellu Invoke-RestMethod a rozhraní REST API Azure Search.
ms.date: 07/11/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: c8a49fe5d334b5752b9272e480fb2502a980b0a4
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840173"
---
# <a name="quickstart-create-an-azure-search-index-in-powershell-using-rest-apis"></a>Rychlý start: Vytvoření indexu Azure Search v prostředí PowerShell pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [Azure Portal](search-create-index-portal.md)
> 

Tento článek vás provede procesem vytvoření, načtení a dotazování indexu Azure Search pomocí prostředí PowerShell a [rozhraní REST API Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Tento článek vysvětluje, jak interaktivně spusťte příkazy Powershellu. Alternativně můžete [stáhněte a spusťte skript prostředí Powershell](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) , který provede stejné operace.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Tyto služby a nástroje se používají v tomto rychlém startu. 

+ [Prostředí PowerShell 5.1 nebo novější](https://github.com/PowerShell/PowerShell)s použitím [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) pro sekvenční a interaktivní kroky.

+ [Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službou pro tento rychlý start. 

## <a name="get-a-key-and-url"></a>Získejte klíč a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/)a ve vyhledávací službě **přehled** stránce, získat adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

2. V **nastavení** > **klíče**, získat klíč pro úplná práva správce na službu. Existují dva klíče zaměnitelné správce, v případě, že budete potřebovat k výměně jeden k dispozici zajišťuje nepřetržitý chod podniků. U požadavků můžete použít buď primární nebo sekundární klíč pro přidání, úpravy a odstraňování objektů.

![Získejte koncový bod a přístupový klíč rozhraní HTTP](media/search-get-started-postman/get-url-key.png "získat HTTP koncový bod a přístupový klíč")

Všechny požadavky vyžaduje klíč rozhraní api na každou požadavku odeslaného do vaší služby. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="connect-to-azure-search"></a>Připojení ke službě Azure Search

1. V prostředí PowerShell, vytvořte **$headers** objekt pro uložení typu obsahu a klíč rozhraní API. Klíč rozhraní API pro správu (YOUR-ADMIN-API-KEY) nahraďte klíčem, který je platný pro vaši vyhledávací službu. Budete muset nastavit toto záhlaví jednou po dobu trvání relace, ale které přidáte do každého požadavku. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Vytvoření **$url** objekt, který určuje služby indexování kolekce. Název služby (YOUR-SEARCH-SERVICE-NAME) nahraďte platný vyhledávací služby.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name"
    ```

3. Spustit **Invoke-RestMethod** odeslat požadavek GET na službu a ověření připojení. Přidat **ConvertTo-Json** tak, abyste mohli zobrazit odpovědi odeslané ze služby.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Pokud služba je prázdný a nemá žádné indexy, výsledky jsou podobné následujícímu příkladu. V opačném případě uvidíte JSON s reprezentací provedených definic indexu.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1\. Vytvoření indexu

Pokud používáte portál, index, musí existovat ve službě můžete načíst data. Tento krok definuje index a nasdílí změny do služby. [Vytvořit Index rozhraní REST API služby](https://docs.microsoft.com/rest/api/searchservice/create-index) se používá pro tento krok.

Požadované elementy indexu patří název a kolekci polí. Kolekce polí definuje strukturu *dokumentu*. Každé pole má název, typ a atributy, které určují, jak se používá (například, zda je fulltextově prohledávatelné, filtrovatelné nebo retrievable ve výsledcích hledání). V rámci indexu, jeden z pole typu `Edm.String` musí být určena jako *klíč* pro identitu dokumentu.

Tento index je s názvem "hotely – rychlý start" a obsahuje definice pole, které vidíte níže. Je podmnožinou větší [indexu Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) použít v dalších kurzech. Jsme oříznut v tomto rychlém startu pro zkrácení.

1. Vložte tento příklad do prostředí PowerShell k vytvoření **$body** objekt, který obsahuje schéma indexu.

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

2. Nastavte identifikátor URI kolekce indexů pro vaši službu a *hotels-quickstart* indexu.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Spuštění příkazu s **$url**, **$headers**, a **$body** pro vytvoření indexu ve službě. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Výsledky by měly vypadat podobně jako tento (zkrácen na první dvě pole pro zkrácení):

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
> Pro ověření můžete také zkontrolovat seznam indexů na portálu.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – nahrání dokumentů

Pro vkládání dokumentů, pomocí požadavku HTTP POST do koncového bodu adresy URL vašeho indexu. Rozhraní REST API pro tuto úlohu je [přidání, aktualizace nebo odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Vložte tento příklad do prostředí PowerShell k vytvoření **$body** objekt obsahující dokumenty, které chcete nahrát. 

    Tato žádost obsahuje dvě úplné a částečné jeden záznam. Částečné záznam ukazuje, že můžou nahrávat dokumenty, neúplné. `@search.action` Parametr určuje, jak se dělá indexování. Platné hodnoty jsou odeslání, sloučení, mergeOrUpload a delete. Chování mergeOrUpload buď vytvoří nový dokument pro hotelId = 3, nebo aktualizuje obsah, pokud již existuje.

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

1. Nastavte na koncový bod *hotels-quickstart* dokumentace kolekce a zahrnují operace s indexem (indexy/hotels – rychlý start/docs/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. Spuštění příkazu s **$url**, **$headers**, a **$body** načtení dokumentů do indexu hotels – rychlý start.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Výsledky by měly vypadat podobně jako v následujícím příkladu. Měli byste vidět [stavový kód 201](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

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

## <a name="3---search-an-index"></a>3\. Prohledání indexu

Tento krok ukazuje, jak zadávat dotazy na index pomocí [rozhraní API pro vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Ujistěte se, že jste na hledání $urls použijte jednoduché uvozovky. Řetězce dotazu obsahovat **$** znaky a můžete vynechat, nechte museli řídicí je celý řetězec je uzavřen do jednoduchých uvozovek a být...

1. Nastavit koncový bod na *hotels-quickstart* dokumentace kolekce a přidejte **vyhledávání** parametr předat řetězec dotazu. 
  
   Tento řetězec spustí prázdné vyhledávání (vyhledávání = *), vrátí unranked seznamu (skóre vyhledávání = 1.0) libovolný dokumentů. Azure Search ve výchozím nastavení, vrátí 50 shodnými najednou. Jako strukturované tento dotaz vrátí strukturu celého dokumentu a hodnoty. Přidat **$count = true** zobrazíte počet všechny dokumenty ve výsledcích.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. Spusťte příkaz k odeslání **$url** ke službě.

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

Zkuste několik další příklady dotazů syntaxe získat představu. Můžete řetězec hledání, dotazy verbatim $filter, omezit sadu výsledků, obor hledání na konkrétní pole a další.

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
## <a name="clean-up"></a>Vyčištění 

Pokud pracujete ve svém vlastním předplatném, je vhodné na konci projektu a zjistěte, jestli stále potřebují prostředky, že kterou jste vytvořili. Levé může spuštěné prostředky nákladů peníze. Můžete odstranit prostředky jednotlivě nebo odstranit skupinu prostředků, kterou chcete odstranit celou sadu prostředků.

Můžete najít a spravovat prostředky na portálu pro použití **všechny prostředky** nebo **skupiny prostředků** odkaz v levém navigačním podokně.

Pokud používáte bezplatné služby, mějte na paměti, že jste omezeni na tři indexy, indexery a datového zdroje. Můžete odstranit jednotlivé položky na portálu, abychom dodrželi omezení. 

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste použili prostředí PowerShell pro jednotlivé kroky v základní pracovní postup pro vytváření a přístup k obsahu ve službě Azure Search. Koncepty v úvahu doporučujeme zvolit pokročilejších scénářích, jako jsou indexování ze zdrojů dat Azure;

> [!div class="nextstepaction"]
> [Kurz REST pro: Index a prohledávání částečně strukturovaných dat (objektů BLOB JSON) ve službě Azure Search](search-semi-structured-data.md)