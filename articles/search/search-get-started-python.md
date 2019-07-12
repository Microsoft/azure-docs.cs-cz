---
title: 'Rychlý start Pythonu: Vytvoření, načtení a dotazování indexů Azure Search REST API – Azure Search pomocí'
description: Vysvětluje, jak vytvořit index, načtení dat a spouštění dotazů pomocí Pythonu, poznámkové bloky Jupyter a rozhraní REST API Azure Search.
ms.date: 07/11/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 123afa2452c3e492b85292514e64f84d3baec390
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840288"
---
# <a name="quickstart-create-an-azure-search-index-in-python-using-jupyter-notebooks"></a>Rychlý start: Vytvoření indexu Azure Search v Pythonu pomocí poznámkových bloků Jupyter
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [Azure Portal](search-create-index-portal.md)
> 

Vytvoření poznámkového bloku Jupyter, který vytvoří, načte a dotazy index Azure Search pomocí Pythonu a [rozhraní REST API Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Tento článek vysvětluje, jak vytvořit poznámkový blok krok za krokem. Alternativně můžete [stažení a spuštění poznámkového bloku Jupyter Python dokončení](https://github.com/Azure-Samples/azure-search-python-samples).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Tyto služby a nástroje se používají v tomto rychlém startu. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), poskytování Python 3.x a poznámkové bloky Jupyter.

+ [Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Úroveň Free můžete použít pro tento rychlý start. 

## <a name="get-a-key-and-url"></a>Získejte klíč a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/)a ve vyhledávací službě **přehled** stránce, získat adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč pro úplná práva správce na službu. Existují dva klíče zaměnitelné správce, v případě, že budete potřebovat k výměně jeden k dispozici zajišťuje nepřetržitý chod podniků. U požadavků můžete použít buď primární nebo sekundární klíč pro přidání, úpravy a odstraňování objektů.

![Získejte koncový bod a přístupový klíč rozhraní HTTP](media/search-get-started-postman/get-url-key.png "získat HTTP koncový bod a přístupový klíč")

Všechny požadavky vyžaduje klíč rozhraní api na každou požadavku odeslaného do vaší služby. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="connect-to-azure-search"></a>Připojení ke službě Azure Search

V této úloze spustit Poznámkový blok Jupyter a ověřte, že se můžete připojit k Azure Search. Uděláte si vyžádá seznam indexů z vaší služby. Na Windows s Anaconda3 vám pomůže Anaconda Navigátor spuštění poznámkového bloku.

1. Vytvoření nového poznámkového bloku Python3.

1. V první buňky načtěte knihovny použité pro práci s JSON a formulování požadavků HTTP.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. V druhé buňce vstupní požadavek prvky, které budou konstanty u každého požadavku. Platné hodnoty nahraďte název vyhledávací služby (YOUR-SEARCH-SERVICE-NAME) a klíč rozhraní API pro správu (YOUR-ADMIN-API-KEY). 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. Ve třetí buňce vydávat žádosti. Tento požadavek GET cílí na kolekci indexů vaší služby search a vybere vlastnost name atributu stávající indexy.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Spuštění každého kroku. Pokud existují indexy odpověď obsahuje seznam názvů indexu. Na snímku obrazovky níže služba již má indexu azureblob a index realestate-us-sample.

   ![Skript v jazyce Python do poznámkového bloku Jupyter s protokolem HTTP žádosti do služby Azure Search](media/search-get-started-python/connect-azure-search.png "skriptu Pythonu do poznámkového bloku Jupyter s protokolem HTTP žádosti do služby Azure Search")

   Naproti tomu kolekci prázdný index vrátí tuto odpověď: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1\. Vytvoření indexu

Pokud používáte portál, index, musí existovat ve službě můžete načíst data. Tento krok používá [vytvořit Index rozhraní REST API služby](https://docs.microsoft.com/rest/api/searchservice/create-index) tak, aby nabízel schématu indexu ve službě.

Požadované elementy indexu zahrnují název, kolekci polí a klíč. Kolekce polí definuje strukturu *dokumentu*. Každé pole má název, typ a atributy, které určují, jak se pole používá (například, zda je fulltextově prohledávatelné, filtrovatelné nebo retrievable ve výsledcích hledání). V rámci indexu, jeden z pole typu `Edm.String` musí být určena jako *klíč* pro identitu dokumentu.

Tento index je s názvem "hotely – rychlý start" a obsahuje definice pole, které vidíte níže. Je podmnožinou větší [indexu Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) použít v dalších kurzech. Jsme oříznut v tomto rychlém startu pro zkrácení.

1. V další buňky vložte následující příklad do buňky zadejte schéma. 

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

2. Jiné buňky vydávat žádosti. Tato operace požadavku cílí na kolekci indexů vaší služby search a vytvoří index založený na schéma indexu podle předcházejí buňky.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Spuštění každého kroku.

   Odpověď obsahuje reprezentaci JSON dané schéma. Na následujícím snímku obrazovky se zobrazuje pouze část odpovědi.

    ![Požadavek na vytvoření indexu](media/search-get-started-python/create-index.png "žádost o vytvoření indexu")

> [!Tip]
> Dalším způsobem, jak ověřit vytvoření indexu je kontrola seznamu indexů na portálu.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – nahrání dokumentů

Pro vkládání dokumentů, pomocí požadavku HTTP POST do koncového bodu adresy URL vašeho indexu. Rozhraní REST API je [přidání, aktualizace nebo odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Dokumenty pocházejí z [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) na Githubu.

1. V nové buňky zadejte čtyři dokumenty, které odpovídají schématu indexu. Zadejte akci nahrávání pro každý dokument.

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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
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

2. Jiné buňky vydávat žádosti. Tento požadavek POST cílí na kolekci dokumentace indexu hotels – rychlý start a oznámení dokumenty v předchozím kroku.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Spuštění každého kroku a vkládání dokumentů do indexu ve vyhledávací službě. Výsledky by měly vypadat podobně jako v následujícím příkladu. 

    ![Odeslání dokumentů do indexu](media/search-get-started-python/load-index.png "odeslání dokumentů do indexu")

## <a name="3---search-an-index"></a>3\. Prohledání indexu

Tento krok ukazuje, jak zadávat dotazy na index pomocí [REST API služby Search dokumenty](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. Do buňky zadejte výraz dotazu, který se spustí prázdné vyhledávání (vyhledávání = *), vrací unranked seznamu (hledání skóre = 1.0) libovolný dokumentů. Azure Search ve výchozím nastavení, vrátí 50 shodnými najednou. Jako strukturované tento dotaz vrátí strukturu celého dokumentu a hodnoty. Přidat $count = true, pokud chcete získat počet všechny dokumenty ve výsledcích.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. Novou buňku zadejte následující příklad, chcete-li vyhledat podmínky "hotels" a "Wi-Fi". Přidáte $select k určení pole, která chcete zahrnout do výsledků hledání.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. Jiné buňky zformulujte podobnou žádost. Tento požadavek GET cílí na kolekci dokumentace indexu hotels-quickstart a připojí dotaz, který jste zadali v předchozím kroku.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Spuštění každého kroku. Výsledky by měly vypadat podobně jako následující výstup. 

    ![Prohledání indexu](media/search-get-started-python/search-index.png "prohledání indexu")

1. Zkuste několik další příklady dotazů syntaxe získat představu. Můžete nahradit `searchstring` s příklady a potom spusťte znovu požadavku hledání. 

   Použijte filtr: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Využijte nejlepší dva výsledky:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Seřadit podle určitého pole:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Vyčištění

Pokud pracujete ve svém vlastním předplatném, je vhodné na konci projektu a zjistěte, jestli stále potřebují prostředky, že kterou jste vytvořili. Levé může spuštěné prostředky nákladů peníze. Můžete odstranit prostředky jednotlivě nebo odstranit skupinu prostředků, kterou chcete odstranit celou sadu prostředků.

Můžete najít a spravovat prostředky na portálu pro použití **všechny prostředky** nebo **skupiny prostředků** odkaz v levém navigačním podokně.

Pokud používáte bezplatné služby, mějte na paměti, že jste omezeni na tři indexy, indexery a datového zdroje. Můžete odstranit jednotlivé položky na portálu, abychom dodrželi omezení. 

## <a name="next-steps"></a>Další postup

Tento rychlý start využívá jako zjednodušení, zkrácenou verzi indexu Hotels. Můžete vytvořit na plnou verzi pro vyzkoušení najdete další zajímavé dotazy. Plná verze a všechny dokumenty 50 získáte spuštěním **importovat data** Průvodce výběrem *hotels-sample* ze zdrojů dat integrovanou ukázkovou.

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Vytvoření indexu na webu Azure Portal](search-get-started-portal.md)
