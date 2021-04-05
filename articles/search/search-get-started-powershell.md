---
title: 'Rychlý Start: vytvoření indexu vyhledávání v PowerShellu pomocí rozhraní REST API'
titleSuffix: Azure Cognitive Search
description: V tomto REST API rychlém startu se dozvíte, jak vytvořit index, načíst data a spustit dotazy pomocí Invoke-RestMethod prostředí PowerShell a REST API Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9dd9de9dcb01e9be200e07e5925d8b856432b620
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94742374"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-powershell-using-rest-apis"></a>Rychlý Start: vytvoření indexu Azure Kognitivní hledání v PowerShellu pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [PowerShell (REST)]()
> * [C#](./search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Python](search-get-started-python.md)
> * [Azure Portal](search-get-started-portal.md)
> 

Tento článek vás provede procesem vytvoření, načtení a dotazování indexu služby Azure Kognitivní hledání pomocí prostředí PowerShell a [rozhraní REST API azure kognitivní hledání](/rest/api/searchservice/). Tento článek vysvětluje, jak interaktivně spustit příkazy prostředí PowerShell. Případně můžete [Stáhnout a spustit skript prostředí PowerShell](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) , který provede stejné operace.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu jsou vyžadovány následující služby a nástroje. 

+ [PowerShell 5,1 nebo novější, pomocí rutiny](https://github.com/PowerShell/PowerShell) [Invoke-RestMethod](/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) pro sekvenční a interaktivní kroky.

+ [Vytvořte službu Azure kognitivní hledání](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu. 

## <a name="copy-a-key-and-url"></a>Zkopírování klíče a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

2. V části **Nastavení**  >  **klíče** Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-rest/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="connect-to-azure-cognitive-search"></a>Připojení k Azure Kognitivní hledání

1. V PowerShellu vytvořte objekt **$Headers** pro uložení typu obsahu a klíče rozhraní API. Nahraďte klíč rozhraní API pro správu (klíč-správce-API-KEY) klíčem, který je platný pro vaši vyhledávací službu. Tuto hlavičku musíte nastavit jenom jednou po dobu trvání relace, ale přidáte ji do každé žádosti. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Vytvořte objekt **$URL** , který určuje kolekci indexů služby. Nahraďte název služby (služba-SEARCH-SERVICE-NAME) platnou vyhledávací službou.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2020-06-30&$select=name"
    ```

3. Spuštěním rutiny **Invoke-RestMethod** odešlete službě požadavek GET a ověřte připojení. Přidejte **ConvertTo-JSON** , abyste mohli zobrazit odpovědi, které se ze služby odesílají zpátky.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Pokud je služba prázdná a nemá žádné indexy, výsledky jsou podobné jako v následujícím příkladu. V opačném případě se zobrazí reprezentace definice indexu ve formátu JSON.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1. Vytvoření indexu

Pokud portál nepoužíváte, musí ve službě existovat index, aby bylo možné načíst data. Tento krok definuje index a odešle ho do služby. Pro tento krok se používá [REST API vytvoření indexu](/rest/api/searchservice/create-index) .

Požadované prvky indexu zahrnují název a kolekci polí. Kolekce polí definuje strukturu *dokumentu*. Každé pole má název, typ a atributy, které určují, jak se používá (například zda je fulltextově prohledávatelné, filtrovatelné nebo získatelné ve výsledcích hledání). V indexu musí být jedno z polí typu `Edm.String` určeno jako *klíč* pro identitu dokumentu.

Tento index má název "hotely-rychlý Start" a obsahuje definice polí, které vidíte níže. Jedná se o podmnožinu většího [indexu hotelů](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) , který se používá v dalších podrobných prodaných článcích. V tomto rychlém startu pro zkrácení se tyto definice polí oříznou.

1. Vložte tento příklad do PowerShellu a vytvořte objekt **$body** obsahující schéma indexu.

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

2. Nastavte identifikátor URI pro kolekci indexů ve vaší službě a index *rychlé* spuštění v hotelů.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2020-06-30"
    ```

3. Spusťte příkaz s **$URL**, **$Headers** a **$body** k vytvoření indexu ve službě. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Výsledky by měly vypadat podobně jako v tomto příkladu (zkrácený na první dvě pole pro zkrácení):

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
> Pro ověření můžete také na portálu vyhledat seznam indexy.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2. načtení dokumentů

K odeslání dokumentu použijte požadavek HTTP POST na koncový bod adresy URL vašeho indexu. REST API pro tento úkol je [Přidání, aktualizace nebo odstranění dokumentů](/rest/api/searchservice/addupdate-or-delete-documents).

1. Vložte tento příklad do PowerShellu a vytvořte objekt **$body** obsahující dokumenty, které chcete nahrát. 

    Tato žádost obsahuje dva úplné a jeden částečný záznam. Částečný záznam ukazuje, že můžete nahrávat nedokončené dokumenty. `@search.action`Parametr určuje, jak je indexování provedeno. Platné hodnoty zahrnují nahrávání, sloučení, mergeOrUpload a odstranění. Chování mergeOrUpload buď vytvoří nový dokument pro hotelId = 3, nebo aktualizuje obsah, pokud již existuje.

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

1. Nastavte koncový bod do kolekce dokumentů pro *rychlé zprovoznění hotelů* a zahrňte operaci indexu (indexy/hotely – rychlý Start, dokumentace/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2020-06-30"
    ```

1. Spusťte příkaz s **$URL**, **$Headers** a **$body** k načtení dokumentů do indexu pro rychlé spuštění v hotelů.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Výsledky by měly vypadat podobně jako v následujícím příkladu. Měl by se zobrazit [stavový kód 201](/rest/api/searchservice/HTTP-status-codes).

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

V tomto kroku se dozvíte, jak zadat dotaz na index pomocí [rozhraní API pro hledání dokumentů](/rest/api/searchservice/search-documents).

Nezapomeňte použít jednoduché uvozovky při hledání $urls. Řetězce dotazů obsahují **$** znaky a je možné je vynechat, pokud je celý řetězec uzavřen v jednoduchých uvozovkách.

1. Nastavte koncový bod do kolekce dokumentů pro *rychlé zprovoznění hotelů* a přidejte parametr **hledání** , který se bude předávat v řetězci dotazu. 
  
   Tento řetězec spustí prázdné hledání (Search = *) a vrátí Neseřazený seznam (skóre hledání = 1,0) libovolných dokumentů. Ve výchozím nastavení Azure Kognitivní hledání vrátí 50 shod v čase. Jako strukturovaný tento dotaz vrátí celou strukturu dokumentů a hodnot. Přidejte **$Count = true** pro získání počtu všech dokumentů ve výsledcích.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=*&$count=true'
    ```

1. Spuštěním příkazu odešlete **$URL** službě.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Výsledky by měly vypadat podobně jako v následujícím výstupu.

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

Vyzkoušejte si několik dalších příkladů dotazů, které vám pomohou s syntaxí. Můžete provést hledání v řetězci, doslovné znění $filter dotazů, omezit sadu výsledků, určit rozsah hledání na konkrétní pole a další.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or higher
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí PowerShellu procházeli základní pracovní postup pro vytváření a přístup k obsahu v Azure Kognitivní hledání. V případě konceptů doporučujeme přejít k pokročilejším scénářům, jako je například indexování ze zdrojů dat Azure;

> [!div class="nextstepaction"]
> [Kurz REST: indexování a hledání částečně strukturovaných dat (blobů JSON) v Azure Kognitivní hledání](search-semi-structured-data.md)
