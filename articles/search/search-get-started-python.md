---
title: 'Rychlý Start: vytvoření indexu vyhledávání v Pythonu'
titleSuffix: Azure Cognitive Search
description: Naučte se vytvářet vyhledávací index, načítat data a spouštět dotazy pomocí Pythonu, Jupyter Notebook a Azure.Documents. Vyhledat klientskou knihovnu pro Python
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2021
ms.custom: devx-track-python
ms.openlocfilehash: eb5de33fd41d3a454f4d0b8d44325ed30f9c5d47
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071626"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebook"></a>Rychlý Start: vytvoření indexu služby Azure Kognitivní hledání v Pythonu pomocí Jupyter Notebook

> [!div class="op_single_selector"]
> * [Python](search-get-started-python.md)
> * [PowerShell (REST)](search-get-started-powershell.md)
> * [C#](search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Azure Portal](search-get-started-portal.md)
>

Vytvoření poznámkového bloku, který vytváří, načítá a odesílá dotazy do indexu služby Azure Kognitivní hledání pomocí Pythonu a [knihovny Azure-Search-Documents](/python/api/overview/azure/search-documents-readme) v sadě Azure SDK pro Python. Tento článek vysvětluje, jak vytvořit Poznámkový blok krok za krokem. Případně můžete [Stáhnout a spustit dokončený Poznámkový blok Pythonu Jupyter](https://github.com/Azure-Samples/azure-search-python-samples).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu jsou vyžadovány následující služby a nástroje.

* [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section), který poskytuje Python 3. x a Jupyter notebook.

* [Azure-Search-Documents – balíček](https://pypi.org/project/azure-search-documents/)

* [Vytvořte vyhledávací službu](search-create-service-portal.md) nebo [vyhledejte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Úroveň Free můžete použít pro tento rychlý Start. 

## <a name="copy-a-key-and-url"></a>Zkopírování klíče a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V části **Nastavení**  >  **klíče** Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   ![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-rest/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="connect-to-azure-cognitive-search"></a>Připojení k Azure Kognitivní hledání

V této úloze spusťte Jupyter Notebook a ověřte, že se můžete připojit k Azure Kognitivní hledání. Provedete to tak, že si vyžádáte seznam indexů z vaší služby. Ve Windows s Anaconda3 můžete použít Anaconda Navigator ke spuštění poznámkového bloku.

1. Vytvoření nového poznámkového bloku python3

1. V první buňce načtěte knihovny ze sady Azure SDK pro Python, včetně [Azure-Search-Documents](/python/api/azure-search-documents).

   ```python
    !pip install azure-search-documents --pre
    !pip show azure-search-documents
    
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.search.documents.indexes import SearchIndexClient 
    from azure.search.documents import SearchClient
    from azure.search.documents.indexes.models import (
        ComplexField,
        CorsOptions,
        SearchIndex,
        ScoringProfile,
        SearchFieldDataType,
        SimpleField,
        SearchableField
    )
   ```

1. Do druhé buňky zadejte prvky požadavku, které budou konstanty u všech požadavků. Zadejte název vyhledávací služby, klíč rozhraní API pro správu a klíč rozhraní API pro dotaz, který jste zkopírovali v předchozím kroku. Tato buňka také nastaví klienty, které budete používat pro konkrétní operace: [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) k vytvoření indexu a [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) k dotazování indexu.

   ```python
    service_name = "YOUR-SEARCH-SERIVCE-NAME"
    admin_key = "YOUR-SEARCH-SERVICE-ADMIN-API-KEY"
    
    index_name = "hotels-quickstart"
    
    # Create an SDK client
    endpoint = "https://{}.search.windows.net/".format(service_name)
    admin_client = SearchIndexClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
    
    search_client = SearchClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
   ```

1. V třetí buňce spusťte operaci delete_index, abyste vymazali službu všech stávajících indexů *rychlého* spuštění v hotelů. Odstranění indexu vám umožní vytvořit další index pro *rychlé* spuštění se stejným názvem.

   ```python
    try:
        result = admin_client.delete_index(index_name)
        print ('Index', index_name, 'Deleted')
    except Exception as ex:
        print (ex)
   ```

1. Spusťte jednotlivé kroky.

## <a name="1---create-an-index"></a>1. Vytvoření indexu

Požadované prvky indexu zahrnují název, kolekci polí a klíč. Kolekce pole definuje strukturu logického *vyhledávacího dokumentu*, která se používá pro načítání dat a vracení výsledků. 

Každé pole má název, typ a atributy, které určují, jak se pole používá (například zda je fulltextově prohledávatelné, filtrovatelné nebo dá být možné ve výsledcích hledání). V indexu musí být jedno z polí typu `Edm.String` určeno jako *klíč* pro identitu dokumentu.

Tento index má název "hotely-rychlý Start" a obsahuje definice polí, které vidíte níže. Jedná se o podmnožinu většího [indexu hotelů](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) používaných v jiných návodech. V tomto rychlém startu jsme ho pro zkrácení vystříhat.

1. V další buňce vložte následující příklad do buňky pro zadání schématu.

    ```python
    # Specify the index schema
    name = index_name
    fields = [
            SimpleField(name="HotelId", type=SearchFieldDataType.String, key=True),
            SearchableField(name="HotelName", type=SearchFieldDataType.String, sortable=True),
            SearchableField(name="Description", type=SearchFieldDataType.String, analyzer_name="en.lucene"),
            SearchableField(name="Description_fr", type=SearchFieldDataType.String, analyzer_name="fr.lucene"),
            SearchableField(name="Category", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
        
            SearchableField(name="Tags", collection=True, type=SearchFieldDataType.String, facetable=True, filterable=True),
    
            SimpleField(name="ParkingIncluded", type=SearchFieldDataType.Boolean, facetable=True, filterable=True, sortable=True),
            SimpleField(name="LastRenovationDate", type=SearchFieldDataType.DateTimeOffset, facetable=True, filterable=True, sortable=True),
            SimpleField(name="Rating", type=SearchFieldDataType.Double, facetable=True, filterable=True, sortable=True),
    
            ComplexField(name="Address", fields=[
                SearchableField(name="StreetAddress", type=SearchFieldDataType.String),
                SearchableField(name="City", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="StateProvince", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="PostalCode", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="Country", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
            ])
        ]
    cors_options = CorsOptions(allowed_origins=["*"], max_age_in_seconds=60)
    scoring_profiles = []
    suggester = [{'name': 'sg', 'source_fields': ['Tags', 'Address/City', 'Address/Country']}]
    ```

1. V jiné buňce formulujte požadavek. Tato create_index požadavek cílí na kolekci indexů vaší vyhledávací služby a vytvoří [SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) založenou na schématu indexu, které jste zadali v předchozí buňce.

    ```python
    index = SearchIndex(
        name=name,
        fields=fields,
        scoring_profiles=scoring_profiles,
        suggesters = suggester,
        cors_options=cors_options)
    
    try:
        result = admin_client.create_index(index)
        print ('Index', result.name, 'created')
    except Exception as ex:
        print (ex)
    ```

1. Spusťte jednotlivé kroky.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2. načtení dokumentů

Chcete-li načíst dokumenty, vytvořte kolekci dokumentů pomocí [Akce indexu](/python/api/azure-search-documents/azure.search.documents.models.indexaction) pro typ operace (nahrávání, sloučení a nahrání a tak dále). Dokumenty pocházejí z [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) na GitHubu.

1. V nové buňce poskytněte čtyři dokumenty, které odpovídají schématu indexu. Zadejte akci odeslání pro každý dokument.

    ```python
    documents = [
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
    ```  

1. V jiné buňce formulujte požadavek. Tato upload_documents požadavek cílí na kolekci docs v indexu pro rychlé zprovoznění a nahraje dokumenty v předchozím kroku do indexu Kognitivní hledání.

    ```python
    try:
        result = search_client.upload_documents(documents=documents)
        print("Upload of new document succeeded: {}".format(result[0].succeeded))
    except Exception as ex:
        print (ex.message)
    ```

1. Spusťte jednotlivé kroky a nahrajte dokumenty do indexu ve vyhledávací službě.

## <a name="3---search-an-index"></a>3. Prohledání indexu

V tomto kroku se dozvíte, jak zadat dotaz na index pomocí [vyhledávacích dokumentů (REST)](/rest/api/searchservice/search-documents).

1. Pro tuto operaci použijte search_client. Tento dotaz spustí prázdné hledání ( `search=*` ) a vrátí Neseřazený seznam (skóre hledání = 1,0) libovolných dokumentů. Vzhledem k tomu, že nejsou k dispozici žádná kritéria, jsou do výsledků zahrnuty všechny dokumenty. Tento dotaz vytiskne pouze dvě pole v každém dokumentu. Také přidá `include_total_count=True` k získání počtu všech dokumentů (4) ve výsledcích.

    ```python
    results =  search_client.search(search_text="*", include_total_count=True)
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. Další dotaz přidá celé výrazy do vyhledávacího výrazu ("WiFi"). Tento dotaz určuje, že výsledky obsahují pouze pole v `select` příkazu. Omezením polí, která se vrátí, minimalizuje množství dat odesílaných zpět po drátu a zkracuje latenci hledání.

    ```python
    results =  search_client.search(search_text="wifi", include_total_count=True, select='HotelId,HotelName,Tags')
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}: {}".format(result["HotelId"], result["HotelName"], result["Tags"]))
    ```

1. Dále použijte výraz filtru, který vrátí pouze hotely s hodnocením větším než 4 seřazené v sestupném pořadí.

    ```python
    results =  search_client.search(search_text="hotels", select='HotelId,HotelName,Rating', filter='Rating gt 4', order_by='Rating desc')
    
    for result in results:
        print("{}: {} - {} rating".format(result["HotelId"], result["HotelName"], result["Rating"]))
    ```

1. Přidat `search_fields` k oboru dotazu odpovídajícího jednomu poli

    ```python
    results =  search_client.search(search_text="sublime", search_fields='HotelName', select='HotelId,HotelName')
    
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. Omezující vlastnosti jsou popisky, které lze použít k vytvoření struktury navigace omezující vlastnosti. Tento dotaz vrací charakteristiky a počty pro kategorii.

    ```python
    results =  search_client.search(search_text="*", facets=["Category"])
    
    facets = results.get_facets()
    
    for facet in facets["Category"]:
        print("    {}".format(facet))
    ```

1. V tomto příkladu vyhledejte konkrétní dokument založený na jeho klíči. Obvykle byste chtěli vrátit dokument, když uživatel klikne na dokument ve výsledku hledání.

    ```python
    result = search_client.get_document(key="3")
    
    print("Details for hotel '3' are:")
    print("Name: {}".format(result["HotelName"]))
    print("Rating: {}".format(result["Rating"]))
    print("Category: {}".format(result["Category"]))
    ```

1. V tomto příkladu použijeme funkci automatického dokončování. Obvykle se používá ve vyhledávacím poli, které umožňuje automatické dokončování potenciálních shod jako uživatelských typů do vyhledávacího pole.

   Při vytvoření indexu byl v rámci žádosti vytvořen i modul pro návrhy s názvem "SG". Definice modulu pro návrhy určuje, která pole lze použít k nalezení potenciálních shod pro žádosti navrhovat. V tomto příkladu jsou tato pole "Tags", "adresa/město", "adresa/země". Chcete-li simulovat automatické dokončování, předejte písmena "sa" jako částečný řetězec. Metoda AutoComplete [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) odesílá zpět potenciální shodné termíny.

    ```python
    search_suggestion = 'sa'
    results = search_client.autocomplete(search_text=search_suggestion, suggester_name="sg", mode='twoTerms')
    
    print("Autocomplete for:", search_suggestion)
    for result in results:
        print (result['text'])
    ```

## <a name="clean-up"></a>Vyčištění

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

V rámci zjednodušení se v tomto rychlém startu používá zkrácená verze indexu hotelů. Můžete vytvořit úplnou verzi a vyzkoušet si zajímavější dotazy. Chcete-li získat úplnou verzi a všechny dokumenty 50, spusťte průvodce **importem dat** a vyberte možnost *hotely-ukázka* z vestavěných ukázkových zdrojů dat.

> [!div class="nextstepaction"]
> [Rychlý Start: vytvoření indexu v Azure Portal](search-get-started-portal.md)