---
title: 'Rychlý start: Vytvoření, načtení a dotazování indexů pomocí nástroje Postman a rozhraní REST API – Azure Search'
description: Zjistěte, jak volat rozhraní API REST Azure Search pomocí nástroje Postman a ukázková data a definice.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 07/11/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 015dd3631322978d6416041a3eea8390a72b0c17
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840193"
---
# <a name="quickstart-create-an-azure-search-index-in-postman-using-rest-apis"></a>Rychlý start: Vytvoření indexu Azure Search v nástroji Postman pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Azure Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

Jeden z nejjednodušších způsobů zkoumání [rozhraní REST API Azure Search](https://docs.microsoft.com/rest/api/searchservice) využívá Postman nebo jiný webový nástroj pro testování k formulování požadavků HTTP a zkontrolovat odpovědi. S využitím správných nástrojů a pokynů můžete odesílat žádosti a zobrazovat odpovědi, ještě než začnete psát kód.

Tento článek vysvětluje, jak interaktivně formulování požadavků. Alternativně můžete [stažení a import kolekce Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) použití předdefinovaných požadavků.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Tyto služby a nástroje se používají v tomto rychlém startu. 

+ [Desktopová aplikace postman](https://www.getpostman.com/) slouží k odesílání žádostí do služby Azure Search.

+ [Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službou pro tento rychlý start. 

## <a name="get-a-key-and-url"></a>Získejte klíč a adresy URL

Volání REST vyžadují pro každý požadavek adresu URL služby a přístupový klíč. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. [Přihlaste se k webu Azure portal](https://portal.azure.com/)a ve vyhledávací službě **přehled** stránce, získat adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

1. V **nastavení** > **klíče**, získat klíč pro úplná práva správce na službu. Existují dva klíče zaměnitelné správce, v případě, že budete potřebovat k výměně jeden k dispozici zajišťuje nepřetržitý chod podniků. U požadavků můžete použít buď primární nebo sekundární klíč pro přidání, úpravy a odstraňování objektů.

![Získejte koncový bod a přístupový klíč rozhraní HTTP](media/search-get-started-postman/get-url-key.png "získat HTTP koncový bod a přístupový klíč")

Všechny požadavky vyžaduje klíč rozhraní api na každou požadavku odeslaného do vaší služby. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

## <a name="connect-to-azure-search"></a>Připojení ke službě Azure Search

V této části použijte nástroj web podle výběru nastavení připojení do služby Azure Search. Každý nástroj uchovává informace o této relaci, což znamená, že budete muset zadat klíč api-key a Content-Type jednou hlavičce žádosti.

Pro kteréhokoli z těchto nástrojů můžete potřebovat zvolte příkazu (GET, POST, PUT a tak dále), zadejte koncový bod adresy URL a některé úlohy, poskytují JSON v textu požadavku. Nahraďte název vyhledávací služby (YOUR-SEARCH-SERVICE-NAME) s platnou hodnotou. Přidat `$select=name` vrátit pouze název každý index. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Všimněte si, že předpona HTTPS, název služby, název objektu (v tomto případě kolekci indexů) a [verze api-version](search-api-versions.md). Verze api-version je povinný, malá písmena řetězec zadán jako `?api-version=2019-05-06` pro aktuální verzi. Verze rozhraní API jsou pravidelně aktualizovány. Zahrnutím verze api-version v každé žádosti získáte úplnou kontrolu nad tím, která se použije.  

Složení záhlaví požadavku obsahuje dva prvky, typu obsahu a klíč rozhraní api používá k ověření do služby Azure Search. Klíč rozhraní API pro správu (YOUR-AZURE-SEARCH-ADMIN-API-KEY) nahraďte platnou hodnotu. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

V nástroji Postman by zformulujte podobnou žádost, která vypadá jako na následujícím snímku obrazovky. Zvolte **získat** jako příkaz, zadejte adresu URL a klikněte na tlačítko **odeslat**. Tento příkaz se připojí k Azure Search, načte kolekci indexů a vrátí stavový kód HTTP 200 úspěšném připojení. Pokud vaše služba již má indexy, odpověď bude také obsahovat definice indexu.

![Adresa URL žádosti postman a záhlaví](media/search-get-started-postman/postman-url.png "adrese URL žádosti Postman a záhlaví")

## <a name="1---create-an-index"></a>1\. Vytvoření indexu

Ve službě Azure Search obvykle vytvořit index před načtením s daty. [Vytvořit Index rozhraní REST API služby](https://docs.microsoft.com/rest/api/searchservice/create-index) se používá pro tuto úlohu. 

Adresa URL je rozšířit `hotels` název indexu.

Chcete-li to provést v nástroji Postman:

1. Změňte operaci na **UMÍSTIT**.

2. Kopírují se tato adresa URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels?api-version=2019-05-06`.

3. Zadejte definici indexu (připravené pro kopírování kódu jsou uvedeny níže) v textu požadavku.

4. Klikněte na tlačítko **odeslat**.

![Dokumentu indexu JSON v textu požadavku](media/search-get-started-postman/postman-request.png "dokumentu indexu JSON v textu požadavku")

### <a name="index-definition"></a>Definice indexu

Kolekce polí definuje strukturu dokumentu. Každý dokument musí mít tato pole a každý pole musí mít datový typ. Pole řetězců se používají ve fulltextovém vyhledávání, takže pokud chcete umožnit prohledávání číselných údajů, přetypujte je na řetězce.

Atributy pole určují povolenou akci. Rozhraní REST API ve výchozím nastavení povolují mnoho akcí. Například všechny řetězce ve výchozím nastavení podporují prohledávání, načítání, filtrování a omezující vlastnosti. Často stačí nastavit atributy, pokud potřebujete vypnout chování.

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

## <a name="2---load-documents"></a>2 – nahrání dokumentů

Vytvoření indexu a jeho naplnění jsou samostatné kroky. Ve službě Azure Search obsahuje index veškerá prohledávatelná data, která můžete zadat jako dokumenty JSON. [Přidání, aktualizace nebo odstranění dokumentů REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) se používá pro tuto úlohu. 

Adresa URL je rozšířit `docs` kolekce a `index` operace.

Chcete-li to provést v nástroji Postman:

1. Změňte operaci na **POST**.

2. Kopírují se tato adresa URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`.

3. Poskytují (kód připravený pro kopírování je níže) dokumenty JSON v textu požadavku.

4. Klikněte na tlačítko **odeslat**.

![Dokumenty JSON v textu požadavku](media/search-get-started-postman/postman-docs.png "dokumenty JSON v textu požadavku")

### <a name="json-documents-to-load-into-the-index"></a>Dokumenty JSON do indexu načíst

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

Během několika sekund měla zobrazit odpověď HTTP 201 v seznamu relace. To znamená, že se dokumenty úspěšně vytvořily. 

Pokud se zobrazí kód 207, nejméně jeden dokument nešel nahrát. Pokud se zobrazí kód 404, hlavička nebo text žádosti obsahuje syntaktickou chybu. Ověřte, že jste změnili koncový bod tak, aby zahrnoval `/docs/index`.

> [!Tip]
> Pro vybrané zdroje dat můžete zvolit přístup s použitím alternativního *indexeru*, který zjednodušuje a snižuje množství kódu vyžadovaného pro indexování. Další informace najdete v tématu [Operace indexeru](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3\. Prohledání indexu

Teď, když se načtou index a dokumenty, můžete posílat dotazy proti nim pomocí [REST API služby Search dokumenty](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Adresa URL je rozšířen o výraz dotazu, který je určen pomocí operátoru vyhledávání.

Chcete-li to provést v nástroji Postman:

1. Změňte operaci na **získat**.

2. Kopírují se tato adresa URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`.

3. Klikněte na tlačítko **odeslat**.

Tento dotaz je prázdný a vrátí počet dokumentů ve výsledcích hledání. Žádost a odpověď by měl vypadat podobně jako na následujícím snímku obrazovky pro nástroj Postman po kliknutí na **odeslat**. Stavový kód by měl být 200.

 ![ZAČÍT s hledaný řetězec na adresu URL](media/search-get-started-postman/postman-query.png "začít s hledaný řetězec na adresu URL")

Zkuste několik další příklady dotazů syntaxe získat představu. Můžete řetězec hledání, dotazy verbatim $filter, omezit sadu výsledků, obor hledání na konkrétní pole a další.

Přepnutí na aktuální adresy URL na základě těch níže, kliknutím na **odeslat** pokaždé, když chcete zobrazit výsledky.

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

## <a name="get-index-properties"></a>Získání vlastností indexu
Můžete také použít [získat statistiky](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) dotazovat na počet dokumentů a velikost indexu: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06`
```

Přidání `/stats` k adrese URL vrátí index informace. V nástroji Postman by vaše žádost měla vypadat podobně jako v následujícím příkladu a odpověď by měla obsahovat počet dokumentů a využité místo v bajtech.

 ![Získání informací o index](media/search-get-started-postman/postman-system-query.png "získat informace o indexu")

Všimněte si, že syntaxe api-version se liší. Pro tuto žádost použijte k připojení verze api-version znak `?`. `?` Odděluje cestu URL a z řetězce dotazu, zatímco znak & odděluje každý ' název = hodnota "pár v řetězci dotazu. V tomto dotazu je api-version první a také jedinou položkou v řetězci dotazu.

## <a name="clean-up"></a>Vyčištění

Pokud pracujete ve svém vlastním předplatném, je vhodné na konci projektu a zjistěte, jestli stále potřebují prostředky, že kterou jste vytvořili. Levé může spuštěné prostředky nákladů peníze. Můžete odstranit prostředky jednotlivě nebo odstranit skupinu prostředků, kterou chcete odstranit celou sadu prostředků.

Můžete najít a spravovat prostředky na portálu pro použití **všechny prostředky** nebo **skupiny prostředků** odkaz v levém navigačním podokně.

Pokud používáte bezplatné služby, mějte na paměti, že jste omezeni na tři indexy, indexery a datového zdroje. Můžete odstranit jednotlivé položky na portálu, abychom dodrželi omezení. 

## <a name="next-steps"></a>Další postup

Klienti REST jsou neocenitelní při zkoumání bez přípravy, ale teď, když víte, jak fungují rozhraní REST API, můžete pokračovat s kódem. Dalším krokem najdete na následující odkaz:

+ [Rychlé zprovoznění: Vytvoření indexu pomocí .NET SDK](search-get-started-dotnet.md)
