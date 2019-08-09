---
title: 'Rychlý Start pro Node. js: Vytváření, načítání a dotazování indexů pomocí Azure Search rozhraní REST API – Azure Search'
description: Ukázka Node. js pro Azure Search, která demonstruje, jak vytvořit, načíst data do a dotazovat se z JavaScriptu
author: lobrien
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: laobri
ms.openlocfilehash: f1420bd4ebf4ef586a8f306d4a2037fc3247c9c0
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882617"
---
# <a name="quickstart-create-an-azure-search-index-in-nodejs"></a>Rychlý start: Vytvoření indexu Azure Search v Node. js
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Azure Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Vytvořte aplikaci Node. js, která vytvoří, načte a dotazuje index Azure Search. Tento článek ukazuje, jak vytvořit aplikaci krok za krokem. Alternativně můžete [stáhnout zdrojový kód a data](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/) a spustit aplikaci z příkazového řádku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu se používají následující služby, nástroje a data.

+ [Node.js](https://nodejs.org).
+ [Npm](https://www.npmjs.com) by měl nainstalovat Node. js.
+ V tomto článku nebo v [úložišti](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/)se uvádí ukázková struktura indexu a vyhovující dokumenty.
+ [Vytvořte službu Azure Search](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu.

Doporučil

* [Visual Studio Code](https://code.visualstudio.com).
* Rozšíření [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) a [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) pro VSCode.

<a name="get-service-info"></a>
## <a name="get-keys-and-urls"></a>Získání klíčů a adres URL

Volání služby vyžaduje koncový bod adresy URL a přístupový klíč pro každý požadavek. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali službu Azure Search, získejte potřebné informace pomocí následujícího postupu:

1. Přihlaste se [k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte název vaší vyhledávací služby. Název služby můžete potvrdit zkontrolováním adresy URL koncového bodu. Pokud byla `https://mydemo.search.windows.net`adresa URL koncového bodu, název vaší služby `mydemo`by byl.

2. V části **Nastavení** > **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

    Získejte taky klíč dotazu. Osvědčeným postupem je vystavovat požadavky na dotazy s přístupem jen pro čtení.

![Získání názvu služby a klíčů pro správu a dotazy](media/search-get-started-nodejs/service-name-and-keys.png)

Všechny požadavky vyžadují klíč rozhraní API-Key v hlavičce všech požadavků odeslaných službě. Platný klíč vytváří vztah důvěryhodnosti na základě jednotlivých požadavků mezi aplikací odesílající požadavek a službu, která ho zpracovává.

## <a name="set-up-your-environment"></a>Nastavení prostředí

Začněte otevřením konzoly PowerShellu nebo jiného prostředí, ve kterém máte nainstalovanou Node. js.

1. Vytvořte adresář pro vývoj, který mu dává název `quickstart` :

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Inicializujte prázdný projekt pomocí NPM spuštěním `npm init`. Přijměte výchozí hodnoty, s výjimkou licence, kterou byste měli nastavit na hodnotu MIT. 

1. Přidejte balíčky, které budou záviset na kódu a pomoci při vývoji:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Ověřte, že jste nakonfigurovali projekty a její závislosti kontrolou, že soubor **Package. JSON** vypadá podobně jako v následujícím příkladu:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Azure_Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "nconf": "^0.10.0",
        "node-fetch": "^2.6.0"
      },
      "devDependencies": {
        "eslint": "^6.1.0",
        "eslint-config-airbnb-base": "^13.2.0",
        "eslint-config-prettier": "^6.0.0",
        "eslint-plugin-import": "^2.18.2",
        "prettier": "^1.18.2"
      }
    }
    ```

<a name="configure"></a>

## <a name="1---define-and-create-index"></a>1\. definování a vytvoření indexu 

Vytvořte soubor **azure_search_config. JSON** , který bude uchovávat data služby Search:

    ```json
    {
        "serviceName" : "[SERVICE_NAME]",
        "adminKey" : "[ADMIN_KEY]",
        "queryKey" : "[QUERY_KEY]",
        "indexName" : "hotels-quickstart"
    }
    ```

Nahraďte `[SERVICE_NAME]` hodnotu názvem vaší vyhledávací služby. Hodnoty `[ADMIN_KEY]` a`[QUERY_KEY]` nahraďte klíči, které jste si poznamenali dříve. 

V Azure Search jsou dokumenty datové struktury, které jsou ve dvou vstupech k indexování a výstupy z dotazů. Vstupy dokumentů můžou být řádky v databázi, objekty BLOB v úložišti objektů BLOB nebo, jako v této ukázce, dokumenty JSON na disku. Můžete buď stáhnout soubor [hotelů. JSON](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/blob/master/hotels.json) , nebo vytvořit vlastní soubor **hotelů. JSON** s následujícím obsahem:

    ```json
    {
        "value": [
            {
                "HotelId": "1",
                "HotelName": "Secret Point Motel",
                "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                "Category": "Boutique",
                "Tags": ["pool", "air conditioning", "concierge"],
                "ParkingIncluded": false,
                "LastRenovationDate": "1970-01-18T00:00:00Z",
                "Rating": 3.6,
                "Address": {
                    "StreetAddress": "677 5th Ave",
                    "City": "New York",
                    "StateProvince": "NY",
                    "PostalCode": "10022"
                }
            },
            {
                "HotelId": "2",
                "HotelName": "Twin Dome Motel",
                "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                "Category": "Boutique",
                "Tags": ["pool", "free wifi", "concierge"],
                "ParkingIncluded": "false",
                "LastRenovationDate": "1979-02-18T00:00:00Z",
                "Rating": 3.6,
                "Address": {
                    "StreetAddress": "140 University Town Center Dr",
                    "City": "Sarasota",
                    "StateProvince": "FL",
                    "PostalCode": "34243"
                }
            },
            {
                "HotelId": "3",
                "HotelName": "Triple Landscape Hotel",
                "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                "Category": "Resort and Spa",
                "Tags": ["air conditioning", "bar", "continental breakfast"],
                "ParkingIncluded": "true",
                "LastRenovationDate": "2015-09-20T00:00:00Z",
                "Rating": 4.8,
                "Address": {
                    "StreetAddress": "3393 Peachtree Rd",
                    "City": "Atlanta",
                    "StateProvince": "GA",
                    "PostalCode": "30326"
                }
            },
            {
                "HotelId": "4",
                "HotelName": "Sublime Cliff Hotel",
                "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                "Category": "Boutique",
                "Tags": ["concierge", "view", "24-hour front desk service"],
                "ParkingIncluded": true,
                "LastRenovationDate": "1960-02-06T00:00:00Z",
                "Rating": 4.6,
                "Address": {
                    "StreetAddress": "7400 San Pedro Ave",
                    "City": "San Antonio",
                    "StateProvince": "TX",
                    "PostalCode": "78216"
                }
            }
        ]
    }
    
    ```

Vytvořte soubor **hotels_quickstart_index. JSON**.  Tento soubor definuje, jak Azure Search pracuje s dokumenty, které jste vytvořili v souboru **hotely. JSON**. Jednotlivá pole budou identifikována `name` a zadána. `type` Každé pole má také řadu atributů indexu, které určují, zda Azure Search mohou hledat, filtrovat, třídit a omezující vlastnosti na poli. Většina polí je jednoduchý datový typ, ale některé, jako `AddressType` jsou komplexní typy, které umožňují vytvářet struktury s bohatou datovou strukturou v indexu.  Můžete si přečíst další informace o [podporovaných datových typech](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) a [atributech indexu](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes). 

Do souboru **hotels_quickstart_index. JSON** přidejte následující a [Stáhněte soubor](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/blob/master/hotels_quickstart_index.json). 

    ```json
    {
        "name": "hotels-quickstart",
        "fields": [
            {
                "name": "HotelId",
                "type": "Edm.String",
                "key": true,
                "filterable": true
            },
            {
                "name": "HotelName",
                "type": "Edm.String",
                "searchable": true,
                "filterable": false,
                "sortable": true,
                "facetable": false
            },
            {
                "name": "Description",
                "type": "Edm.String",
                "searchable": true,
                "filterable": false,
                "sortable": false,
                "facetable": false,
                "analyzer": "en.lucene"
            },
            {
                "name": "Description_fr",
                "type": "Edm.String",
                "searchable": true,
                "filterable": false,
                "sortable": false,
                "facetable": false,
                "analyzer": "fr.lucene"
            },
            {
                "name": "Category",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "sortable": true,
                "facetable": true
            },
            {
                "name": "Tags",
                "type": "Collection(Edm.String)",
                "searchable": true,
                "filterable": true,
                "sortable": false,
                "facetable": true
            },
            {
                "name": "ParkingIncluded",
                "type": "Edm.Boolean",
                "filterable": true,
                "sortable": true,
                "facetable": true
            },
            {
                "name": "LastRenovationDate",
                "type": "Edm.DateTimeOffset",
                "filterable": true,
                "sortable": true,
                "facetable": true
            },
            {
                "name": "Rating",
                "type": "Edm.Double",
                "filterable": true,
                "sortable": true,
                "facetable": true
            },
            {
                "name": "Address",
                "type": "Edm.ComplexType",
                "fields": [
                    {
                        "name": "StreetAddress",
                        "type": "Edm.String",
                        "filterable": false,
                        "sortable": false,
                        "facetable": false,
                        "searchable": true
                    },
                    {
                        "name": "City",
                        "type": "Edm.String",
                        "searchable": true,
                        "filterable": true,
                        "sortable": true,
                        "facetable": true
                    },
                    {
                        "name": "StateProvince",
                        "type": "Edm.String",
                        "searchable": true,
                        "filterable": true,
                        "sortable": true,
                        "facetable": true
                    },
                    {
                        "name": "PostalCode",
                        "type": "Edm.String",
                        "searchable": true,
                        "filterable": true,
                        "sortable": true,
                        "facetable": true
                    },
                    {
                        "name": "Country",
                        "type": "Edm.String",
                        "searchable": true,
                        "filterable": true,
                        "sortable": true,
                        "facetable": true
                    }
                ]
            }
        ],
        "suggesters": [
            {
                "name": "sg",
                "searchMode": "analyzingInfixMatching",
                "sourceFields": [
                    "HotelName"
                ]
            }
        ]
    }
    ```
    
## <a name="2---a-class-for-azure-search"></a>2 – třída pro Azure Search 

Je vhodné oddělit konkrétní konkrétní scénář od kódu, který bude široce použitelný. Třída definovaná v souboru **AzureSearchClient. js** bude informovat o tom, jak vytvořit adresy URL požadavků, vytvořit žádost pomocí rozhraní API pro načtení a reagovat na stavový kód odpovědi. `AzureSearchClient`

Začněte pracovat na **AzureSearchClient. js** importem balíčku **Node-Fetch** a vytvořením jednoduché třídy. Izolujte měnitelné části `AzureSearchClient` třídy předáním jeho konstruktoru k různým hodnotám konfigurace:

    ```javascript
    const fetch = require('node-fetch');
    
    class AzureSearchClient {
      constructor(searchServiceName, adminKey, queryKey, indexName) {
          this.searchServiceName = searchServiceName;
          this.adminKey = adminKey;
          // The query key is used for read-only requests and so can be distributed with less risk of abuse.
          this.queryKey = queryKey;
          this.indexName = indexName;
          this.apiVersion = '2019-05-06';
      }
    
      // All methods go inside class body here!
    }
    
    module.exports = AzureSearchClient;
    ```

První odpovědností třídy je zjistit, jak vytvořit adresy URL, na které se mají odesílat různé požadavky. Sestavujte tyto adresy URL pomocí metod instance, které používají konfigurační data předaná konstruktoru třídy. Všimněte si, že adresa URL, kterou vytvoří, je specifická pro verzi rozhraní API a musí mít Argument určující tuto verzi (v `2019-05-06`této aplikaci). 

Do těla třídy přidejte následující metody:

    ```javascript
      getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }
      
      getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
    
      getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
    ```

Další zodpovědností je asynchronní požadavek pomocí rozhraní API pro načtení. Asynchronní statická metoda `request` přijímá adresu URL, řetězec, který určuje metodu HTTP ("Get", "Put", "post", "Delete"), klíč, který se má použít v žádosti, a volitelný objekt JSON. `headers` Proměnná`queryKey` mapuje (zda klíč správce nebo klíč dotazu jen pro čtení) do hlavičky požadavku HTTP API-Key. Možnosti žádosti vždycky obsahují, `method` které se mají použít `headers`, a. Pokud `bodyJson` `bodyJson`není `null`, tělo požadavku HTTP je nastaveno na řetězcové vyjádření. `request` Vrátí příslib načteného rozhraní API pro spuštění požadavku HTTP.

    ```javascript
      static async request(url, method, apiKey, bodyJson = null) {
        // Uncomment the following for request details:
        /*
        console.log(`\n${method} ${url}`);
        console.log(`\n${apiKey}`);
        if (bodyJson !== null) {
            console.log(`\ncontent: ${JSON.stringify(bodyJson, null, 4)}`);
        }
        */
      
        const headers = {
            'content-type' : 'application/json',
            'api-key' : apiKey
        };
        const init = bodyJson === null ?
            { 
                method, 
                headers
            }
            : 
            {
                method, 
                headers,
                body : JSON.stringify(bodyJson)
            };
        return fetch(url, init);
      }
    ```

Pro demonstrační účely budeme vyvolávat výjimku, pokud požadavek HTTP není úspěšný. V reálné aplikaci byste pravděpodobně proznamenali protokolování a diagnostiku stavového kódu http v rámci `response` žádosti o vyhledávací službu. 
    
    ```javascript
      static throwOnHttpError(response) {
        const statusCode = response.status;
        if (statusCode >= 300){
            console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
            throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
        }
      }
    ```

Nakonec přidejte metody, které pracují s indexem Azure Search. Všechny tyto metody mají stejnou strukturu:

* Získejte koncový bod, na který bude požadavek proveden.
* Vygenerujte požadavek s příslušným koncovým bodem, příkazem HTTP, klíčem rozhraní API a textem. `queryAsync()`použije klíč dotazu, jinak se použije klíč správce.
* `await`odpověď na požadavek.  
* Pracovat s kódem stavu odpovědi.
* Vrátí příslib nějaké vhodné hodnoty (logická hodnota, `this`nebo výsledky dotazu). 

    ```javascript
      async indexExistsAsync() { 
          console.log("\n Checking if index exists...");
          const endpoint = this.getIndexUrl();
          const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
          // Success has a few likely status codes: 200 or 204 (No Content), but accept all in 200 range...
          const exists = response.status >= 200 && response.status < 300;
          return exists;
      }
      
      async deleteIndexAsync() {
          console.log("\n Deleting existing index...");
          const endpoint = this.getIndexUrl();
          const response = await AzureSearchClient.request(endpoint, "DELETE", this.adminKey);
          AzureSearchClient.throwOnHttpError(response);
          return this;
      }
      
      async createIndexAsync(definition) {
          console.log("\n Creating index...");
          const endpoint = this.getIndexUrl();
          const response = await AzureSearchClient.request(endpoint, "PUT", this.adminKey, definition);
          AzureSearchClient.throwOnHttpError(response);
          return this;
      }
      
      async postDataAsync(hotelsData) {
          console.log("\n Adding hotel data...");
          const endpoint = this.getPostDataUrl();
          const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
          AzureSearchClient.throwOnHttpError(response);
          return this;
      }
      
      async queryAsync(searchTerm) {
          console.log("\n Querying...")
          const endpoint = this.getSearchUrl(searchTerm);
          const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
          AzureSearchClient.throwOnHttpError(response);
          return response;
      }
    ```

Potvrďte, že vaše metody jsou uvnitř třídy a že tuto třídu exportujete. Nejvzdálenější rozsah **AzureSearchClient. js** by měl být:

    ```javascript
    const fetch = require('node-fetch');
    
    class AzureSearchClient {
        // ... code here ...
    }
    
    module.exports = AzureSearchClient;
    ```

## <a name="3---create-a-program"></a>3\. vytvoření programu

Objektově orientovaná třída byla dobrou volbou pro potenciálně opakovaně použitelný modul **AzureSearchClient. js** , ale není nutná pro hlavní program, který zadáte do souboru s názvem **index. js**. 

Vytvořte **index. js** a začněte tím, že navážete:

* Balíček **NConf** , který poskytuje flexibilitu pro zadání konfigurace s JSON, proměnnými prostředí nebo argumenty příkazového řádku.
* Data ze souboru **hotely. JSON** .
* Data ze souboru **hotels_quickstart_index. JSON** .
* Modul `AzureSearchClient`.

    ```javascript
    const nconf = require('nconf');
    
    const hotelData = require('./hotels.json');
    const indexDefinition = require('./hotels_quickstart_index.json');
    const AzureSearchClient = require('./AzureSearchClient.js');
    ```

Teď přidejte některé základní dotazy: 

    ```javascript
    const queries = [
      "*&$count=true",
      "historic&$filter=Rating gt 4&"
    ];
    ```

První dotaz vrátí všechna data (`*`) a počet vrácených záznamů. Druhá vrátí pouze ty dokumenty, které obsahují slovo "historické" v některém z polí definovaných jako "prohledávatelné" v **hotels_quickstart_index. JSON** a jejichž `Rating` pole obsahuje hodnotu větší než 4. Přečtěte si další informace o [tom, jak vytvořit dotaz v Azure Search](https://docs.microsoft.com/azure/search/search-query-overview). 

Balíček [ **NConf** ](https://github.com/indexzero/nconf) umožňuje zadat konfigurační data v nejrůznějších formátech, jako jsou například proměnné prostředí nebo příkazový řádek. **NConf** použijeme pro základní způsob čtení souboru **azure_search_config. JSON** a vrátí obsah tohoto souboru jako slovník. Pomocí `get(key)` funkce NConf můžeme provést rychlou kontrolu, že krok [konfigurace Azure Search informací o službě](#configure) se přeskočil. Nakonec vrátíme konfiguraci:

    ```javascript
    function getAzureConfiguration() {
      const config = nconf.file({ file: 'azure_search_config.json' });
      if (config.get('serviceName') === '[SEARCH_SERVICE_NAME' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
      }
      return config;
    }
    ```

Funkce vytvoří objekt `Promise` , který se vyřeší po zadaném časovém intervalu. `sleep` Pomocí této funkce lze aplikaci pozastavit při čekání na dokončení a zpřístupnění asynchronních operací indexu. Přidání takového zpoždění je obvykle nutné pouze v ukázkách, testech a ukázkových aplikacích.

    ```javascript
    function sleep(ms)
    {
      return(
          new Promise(function(resolve, reject)
          {
              setTimeout(function() { resolve(); }, ms);
          })
      );
    }
    ```

Funkce přebírá `AzureSearchClient.queryAsync` `queries` objekt a aplikuje metodu na každou hodnotu v poli. `AzureSearchClient` `doQueries()` Používá `Promise.all()` funkci k vrácení jediného `Promise` , který se vyřeší pouze v případě, že byly vyřešeny všechny dotazy. Volání pro `JSON.stringify(body, null, 4)` formátování výsledku dotazu bude čitelnější.

    ```javascript
    async function doQueriesAsync(client) {
      return Promise.all(
          queries.map( async query => {
              const result = await client.queryAsync(query);
              const body = await result.json();
              const str = JSON.stringify( body, null, 4);
              console.log(`Query: ${query} \n ${str}`);
          })
      );
    }
    ```

Nakonec zadejte a zavolejte hlavní asynchronní `run` funkci. Tato funkce volá další funkce v pořadí, které čekají podle potřeby pro řešení `Promise`s.

* Načtěte konfiguraci pomocí `getAzureConfiguration()` dříve zapsaného.
* Vytvoří novou `AzureSearchClient` instanci, která předává hodnoty z vaší konfigurace.
* Ověřte, zda index existuje, a pokud ano, odstraňte jej
* Vytvoření indexu pomocí `indexDefinition` načteného z **hotels_quickstart_index. JSON**
* Přidejte dokumenty o hotely, které jste načetli z **hotelů. JSON.**
* Dotazování indexu Azure Search pomocí `doQueriesAsync()` vámi zapsané metody

    ```javascript
    const run = async () => {
      try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get["serviceName"]);
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        const indexDefinition = require('./hotels_quickstart_index.json');
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
        // Data availability can take a few seconds
        await sleep(5000);
        await doQueries(client);
      } catch (x) {
        console.log(x);
      }
    }
    
    run();
    ```

Nezapomeňte, že konečné volání `run()`! Je to vstupní bod programu při spuštění `node index.js` v dalším kroku.

### <a name="prepare-and-run-the-sample"></a>Příprava a spuštění ukázky

Pro následující příkazy použijte okno terminálu.

1. Přejděte do složky, která obsahuje soubor **Package. JSON** a zbytek kódu.
1. Nainstalujte balíčky pro ukázku pomocí `npm install`.  Tento příkaz stáhne balíčky, na kterých závisí kód.
1. Spusťte program pomocí `node index.js`programu.

Měli byste vidět řadu zpráv popisujících akce prováděné programem a končící výsledky některých dotazů. Pokud chcete zobrazit další podrobnosti o požadavcích, můžete odkomentovat řádky [20-26](https://github.com/Azure-Samples/azure-search-javascript-samples/quickstart/blob/master/AzureSearchClient.js#LL20-LL26) **AzureSearchClient. js**. 

### <a name="about-the-sample"></a>O ukázce

Ukázka používá malé množství dat hotelového data dostačující k předvedení základních informací o vytváření a dotazování indexu Azure Search.

Třída **AzureSearchClient** zapouzdřuje konfiguraci, adresy URL a základní požadavky HTTP pro vyhledávací službu. Soubor **index. js** načte konfigurační data pro službu Azure Search, data o hotelu, která se budou načítat pro indexování, a ve svých `run` funkcích, v objednávkách a provede různé operace.

Celkové chování `run` funkce je odstranit index Azure Search, pokud existuje, vytvořit index, přidat data a provést některé dotazy.  

## <a name="clean-up"></a>Vyčištění 

Pokud pracujete ve vlastním předplatném, je vhodné na konci projektu zjistit, zda stále potřebujete prostředky, které jste vytvořili. Prostředky, které se na něm zbývá, můžou mít náklady na peníze. Prostředky můžete odstranit jednotlivě nebo odstranit skupinu prostředků, abyste odstranili celou sadu prostředků.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.
Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu Node. js jste pracovali pomocí řady úkolů, abyste mohli vytvořit index, načíst ho s dokumenty a spouštět dotazy. Provedli jsme některé kroky, jako je například čtení konfigurace a definování dotazů, a to nejjednodušším možným způsobem. V reálné aplikaci byste chtěli tyto aspekty umístit do samostatných modulů, které by poskytovaly flexibilitu a zapouzdření. 
 
Pokud už službu Azure Search trochu znáte, tato ukázka vám může posloužit jako odrazový můstek k vyzkoušení modulů pro automatické návrhy (našeptávání nebo automatické dokončování dotazů), filtrů a fasetové navigace. Pokud s Azure Search začínáte, doporučujeme vyzkoušet si další kurzy, které vám pomůžou s vývojem toho, co můžete vytvořit. Pokud hledáte další zdroje, přejděte na [stránku dokumentace](https://azure.microsoft.com/documentation/services/search/). 

> [!div class="nextstepaction"]
> [Volání Azure Search z webové stránky pomocí JavaScriptu](https://github.com/liamca/azure-search-javascript-samples)
