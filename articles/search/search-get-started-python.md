---
title: 'Úvodní příručka: Vytvoření vyhledávacího indexu v Pythonu pomocí rest API'
titleSuffix: Azure Cognitive Search
description: Vysvětluje, jak vytvořit index, načíst data a spustit dotazy pomocí Pythonu, Jupyter poznámkových bloků a rozhraní REST API Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 04/01/2020
ms.openlocfilehash: fd87dbe125e84c171cc35a2b242879c44bc50fd9
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585927"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Úvodní příručka: Vytvoření indexu Azure Cognitive Search v Pythonu pomocí poznámkových bloků Jupyter

> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [Prostředí PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Pošťák (REST)](search-get-started-postman.md)
> * [Portál](search-create-index-portal.md)
> 

Vytvořte poznámkový blok Jupyter, který vytvoří, načte a dotazuje index Azure Cognitive Search pomocí Pythonu a [virtuálních api Azure Cognitive Search REST](https://docs.microsoft.com/rest/api/searchservice/). Tento článek vysvětluje, jak vytvořit poznámkový blok krok za krokem. Případně si můžete [stáhnout a spustit hotový jupyter python notebook](https://github.com/Azure-Samples/azure-search-python-samples).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Pro tento rychlý start jsou vyžadovány následující služby a nástroje. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), poskytující python 3.x a jupyternotebooky.

+ [Vytvořte službu Azure Cognitive Search](search-create-service-portal.md) nebo [najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý start můžete použít úroveň Free. 

## <a name="get-a-key-and-url"></a>Získání klíče a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do předplatného přidali Azure Cognitive Search, postupujte podle následujících kroků a získejte potřebné informace:

1. [Přihlaste se na portál Azure](https://portal.azure.com/)portal a na stránce **Přehled** vyhledávací služby získáte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč správce pro úplná práva ke službě. Existují dva zaměnitelné klíče pro správu, které jsou k dispozici pro kontinuitu podnikání v případě, že potřebujete převrátit jeden. Primární nebo sekundární klíč můžete použít při požadavcích na přidávání, úpravy a odstranění objektů.

![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-postman/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní api na každý požadavek odeslaný do vaší služby. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="connect-to-azure-cognitive-search"></a>Připojení k Azure Cognitive Search

V této úloze spusťte poznámkový blok Jupyter a ověřte, že se můžete připojit k Azure Cognitive Search. Uděláte to tak, že požádáte o seznam indexů z vaší služby. Ve Windows s Anaconda3 můžete k spuštění poznámkového bloku použít anakonda Navigator.

1. Vytvořte nový poznámkový blok Pythonu3.

1. V první buňce načtěte knihovny používané pro práci s JSON a formulování požadavků HTTP.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. V druhé buňce zadejte prvky požadavku, které budou konstanty na každý požadavek. Nahraďte název vyhledávací služby (YOUR-SEARCH-SERVICE-NAME) a klíč rozhraní API pro správu (YOUR-ADMIN-API-KEY) platnými hodnotami. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   Pokud získáte ConnectionError `"Failed to establish a new connection"`, ověřte, zda je klíč rozhraní api primárním nebo sekundárním klíčem správce a zda jsou na místě všechny úvodní a koncové znaky (`?` a `/`).

1. Ve třetí buňce zformulovat požadavek. Tento požadavek GET cílí na kolekci indexů vyhledávací služby a vybere vlastnost název existujících indexů.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Spusťte každý krok. Pokud existují indexy, odpověď obsahuje seznam názvů indexů. Na následujícím snímku obrazovky služba již má azureblob-index a index realestate-us-sample.

   ![Skript Pythonu v poznámkovém bloku Jupyter s http požadavky na Azure Cognitive Search](media/search-get-started-python/connect-azure-search.png "Skript Pythonu v poznámkovém bloku Jupyter s http požadavky na Azure Cognitive Search")

   Naproti tomu kolekce prázdný index vrátí tuto odpověď:`{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1. Vytvoření indexu

Pokud nepoužíváte portál, musí existovat index ve službě před načtením dat. Tento krok používá [vytvořit index ROZHRANÍ REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) k nabízení schématu indexu do služby.

Mezi požadované prvky indexu patří název, kolekce polí a klíč. Kolekce polí definuje strukturu *dokumentu*. Každé pole má název, typ a atributy, které určují způsob použití pole (například zda je fulltextové prohledávatelné, filtrovatelné nebo načítatelné ve výsledcích hledání). V rámci indexu musí být `Edm.String` jedno z polí typu označeno jako *klíč* pro identitu dokumentu.

Tento index se nazývá "hotely-rychlý start" a má definice polí, které vidíte níže. Jedná se o podmnožinu většího [indexu Hotels,](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) který se používá v jiných návodech. Ořezáni jsme to v tomto rychlém startu pro stručnost.

1. V další buňce vložte následující příklad do buňky, abyste poskytli schéma. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. V jiné buňce zformulovat požadavek. Tento požadavek POST cílí na kolekci indexů vyhledávací služby a vytvoří index založený na schématu indexu, které jste zadali v předchozí buňce.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Spusťte každý krok.

   Odpověď zahrnuje reprezentaci JSON schématu. Následující snímek obrazovky zobrazuje pouze část odpovědi.

    ![Požadavek na vytvoření indexu](media/search-get-started-python/create-index.png "Požadavek na vytvoření indexu")

> [!Tip]
> Dalším způsobem, jak ověřit vytvoření indexu, je zkontrolovat seznam Indexy na portálu.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Načíst dokumenty

Chcete-li vyžádat dokumenty, použijte požadavek HTTP POST do koncového bodu url indexu. Rozhraní REST API je [Přidat, Aktualizovat nebo odstranit dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Dokumenty pocházejí z [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) na GitHubu.

1. V nové buňce zadejte čtyři dokumenty, které odpovídají schématu indexu. Zadejte akci nahrávání pro každý dokument.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
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
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
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

2. V jiné buňce zformulovat požadavek. Tento požadavek POST cílí na kolekci dokumentů indexu rychlého startu hotels a odešle dokumenty poskytnuté v předchozím kroku.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Spuštěním každého kroku přenesete dokumenty do indexu ve vyhledávací službě. Výsledky by měly vypadat podobně jako v následujícím příkladu. 

    ![Odeslání dokumentů do rejstříku](media/search-get-started-python/load-index.png "Odeslání dokumentů do rejstříku")

## <a name="3---search-an-index"></a>3. Prohledání indexu

Tento krok ukazuje, jak zadat dotaz na index pomocí [rozhraní REST API pro hledání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. V buňce zadejte výraz dotazu, který provede prázdné hledání (search=*) a vrátí nehodnocený seznam (skóre hledání = 1,0) libovolných dokumentů. Ve výchozím nastavení Azure Cognitive Search vrátí 50 shod najednou. Jako strukturovaný tento dotaz vrátí celou strukturu dokumentu a hodnoty. Přidejte $count=true, abyste získali počet všech dokumentů ve výsledcích.

   ```python
   searchstring = '&search=*&$count=true'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. V nové buňce uveďte následující příklad pro vyhledávání termínů "hotely" a "wifi". Přidejte $select a určete, která pole mají být zahrnuta do výsledků hledání.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)   
   ```

   Výsledky by měly vypadat podobně jako následující výstup. 

    ![Prohledání indexu](media/search-get-started-python/search-index.png "Prohledání indexu")

1. Dále použijte $filter výraz, který vybere pouze ty hotely s hodnocením vyšším než 4. 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)     
   ```

1. Ve výchozím nastavení vyhledávač vrátí 50 nejlepších dokumentů, ale můžete použít horní a přeskočit přidat stránkování a zvolit, kolik dokumentů v každém výsledku. Tento dotaz vrátí dva dokumenty v každé sadě výsledků.

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. V tomto posledním příkladu použijte $orderby k seřazení výsledků podle města. Tento příklad obsahuje pole z kolekce Adresa.

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

## <a name="clean-up"></a>Vyčištění

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu **Všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, nezapomeňte, že jste omezeni na tři indexy, indexery a zdroje dat. Můžete odstranit jednotlivé položky na portálu, abyste zůstali pod limitem. 

## <a name="next-steps"></a>Další kroky

Jako zjednodušení tento rychlý start používá zkrácenou verzi indexu Hotels. Můžete vytvořit plnou verzi vyzkoušet další zajímavé dotazy. Chcete-li získat plnou verzi a všech 50 dokumentů, spusťte Průvodce **importem dat** a vyberte *ukázku hotelů* z předdefinovaných ukázkových zdrojů dat.

> [!div class="nextstepaction"]
> [Úvodní příručka: Vytvoření indexu na webu Azure Portal](search-get-started-portal.md)
