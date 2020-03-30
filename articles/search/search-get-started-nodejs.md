---
title: 'Úvodní příručka: Vytvoření indexu vyhledávání v souboru Node.js pomocí api REST'
titleSuffix: Azure Cognitive Search
description: V tomto node.js rychlý start, zjistěte, jak vytvořit index, načíst data a spustit dotazy na Azure Cognitive Search pomocí JavaScriptu a REST API.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.devlang: nodejs
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/25/2020
ms.openlocfilehash: cbef6029b93f134f95ee54aa87ce0dd65bcdf50d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77623994"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-nodejs-using-rest-apis"></a>Úvodní příručka: Vytvoření indexu Azure Cognitive Search v souboru Node.js pomocí api REST
> [!div class="op_single_selector"]
> * [Javascript](search-get-started-nodejs.md)
> * [C #](search-get-started-dotnet.md)
> * [Portál](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Vytvořte aplikaci Node.js, která vytvoří, načte a dotazuje index Azure Cognitive Search. Tento článek ukazuje, jak vytvořit aplikaci krok za krokem. Případně můžete [stáhnout zdrojový kód a data](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) a spustit aplikaci z příkazového řádku.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

K sestavení a testování tohoto rychlého startu jsme použili následující software a služby:

+ [Node.js](https://nodejs.org)

+ [NPM](https://www.npmjs.com) by měla být nainstalována node.js

+ Vzorová struktura indexu a odpovídající dokumenty jsou k dispozici v tomto článku nebo [z adresáře **rychlého startu** úložiště](https://github.com/Azure-Samples/azure-search-javascript-samples/)

+ [Vytvořte službu Azure Cognitive Search](search-create-service-portal.md) nebo [najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý start můžete využít bezplatnou službu.

Doporučené:

* [Kód visual studia](https://code.visualstudio.com)

* [Hezčí](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) a [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) rozšíření pro VSCode.

<a name="get-service-info"></a>

## <a name="get-keys-and-urls"></a>Získání klíčů a adres URL

Volání služby vyžadují koncový bod adresy URL a přístupový klíč na každém požadavku. Vyhledávací služba se vytvoří s oběma, takže pokud jste do předplatného přidali Azure Cognitive Search, postupujte podle následujících kroků a získejte potřebné informace:

1. [Přihlaste se na portál Azure](https://portal.azure.com/)Portal a na stránce **Přehled** vyhledávací služby získáte název vyhledávací služby. Název služby můžete potvrdit kontrolou adresy URL koncového bodu. Pokud by adresa `https://mydemo.search.windows.net`URL koncového bodu `mydemo`byla , byl by název služby .

2. V **nastavení** > **klíče**, získat klíč správce pro úplná práva ke službě. Existují dva zaměnitelné klíče pro správu, které jsou k dispozici pro kontinuitu podnikání v případě, že potřebujete převrátit jeden. Primární nebo sekundární klíč můžete použít při požadavcích na přidávání, úpravy a odstranění objektů.

    Získejte také klíč dotazu. Je osvědčeným postupem pro vydávání požadavků na dotazy s přístupem jen pro čtení.

![Získání názvu služby a klíčů pro správce a dotazy](media/search-get-started-nodejs/service-name-and-keys.png)

Všechny požadavky vyžadují klíč rozhraní api v záhlaví každé žádosti odeslané do vaší služby. Platný klíč vytvoří vztah důvěryhodnosti na základě požadavku mezi aplikací odesílající požadavek a službou, která ji zpracovává.

## <a name="set-up-your-environment"></a>Nastavení prostředí

Začněte otevřením konzoly Powershell nebo jiného prostředí, ve kterém jste nainstalovali soubor Node.js.

1. Vytvořte adresář vývoje s `quickstart` názvem :

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Inicializovat prázdný projekt s `npm init`NPM spuštěním . Přijměte výchozí hodnoty, s výjimkou licence, kterou byste měli nastavit na "MIT". 

1. Přidejte balíčky, na kterých bude kód a podpora při vývoji záviset:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Zkontrolujte, zda soubor **package.json** vypadá podobně jako následující:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
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

5. Vytvořte soubor **azure_search_config.json** pro uložení dat vyhledávací služby:

    ```json
    {
        "serviceName" : "[SEARCH_SERVICE_NAME]",
        "adminKey" : "[ADMIN_KEY]",
        "queryKey" : "[QUERY_KEY]",
        "indexName" : "hotels-quickstart"
    }
    ```

Nahraďte hodnotu `[SERVICE_NAME]` názvem vyhledávací služby. Nahraďte `[ADMIN_KEY]` a `[QUERY_KEY]` s hodnotami klíče, které jste zaznamenali dříve. 

## <a name="1---create-index"></a>1 - Vytvořit index 

Vytvořte soubor **hotels_quickstart_index.json**.  Tento soubor definuje, jak Azure Cognitive Search funguje s dokumenty, které budete načítat v dalším kroku. Každé pole bude identifikováno `name` a `type`bude mít zadaný . Každé pole má také řadu atributů indexu, které určují, zda azure kognitivní vyhledávání můžete vyhledávat, filtrovat, řadit a omezující vlastnost na pole. Většina polí jsou jednoduché datové typy, `AddressType` ale některé, jako jsou složité typy, které umožňují vytvářet bohaté datové struktury v indexu.  Další informace o [podporovaných datových typech](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) a [atributech indexu](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes)naleznete v 

Do **souboru hotels_quickstart_index.json** nebo [stáhněte následující položku](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels_quickstart_index.json). 

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
    

Je vhodné oddělit specifika konkrétní scénář od kódu, který bude široce použitelné. Třída `AzureSearchClient` definovaná v souboru **AzureSearchClient.js** bude vědět, jak vytvořit adresy URL požadavků, provést požadavek pomocí rozhraní API pro načtení a reagovat na stavový kód odpovědi.

Začněte pracovat na **AzureSearchClient.js** importem balíčku **načtení uzlu** a vytvořením jednoduché třídy. Izolujte proměnlivé části `AzureSearchClient` třídy předáním do konstruktoru různé hodnoty konfigurace:

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

První odpovědností třídy je vědět, jak vytvořit adresy URL, na které mají být odeslány různé požadavky. Vytvořte tyto adresy URL pomocí metod instance, které používají konfigurační data předaná konstruktoru třídy. Všimněte si, že adresa URL, kterou vytvoří, je specifická pro verzi `2019-05-06`rozhraní API a musí mít argument určující tuto verzi (v této aplikaci). 

První z těchto metod vrátí adresu URL pro samotný index. Do těla třídy přidejte následující metodu:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

Další odpovědnost `AzureSearchClient` je provedení asynchronní požadavek s načíst rozhraní API. Asynchronní statická `request` metoda přebírá adresu URL, řetězec určující metodu HTTP ("GET", "PUT", "POST", "DELETE"), klíč, který má být použit v požadavku, a volitelný objekt JSON. Proměnná `headers` mapuje `queryKey` (zda klíč správce nebo klíč dotazu jen pro čtení) na hlavičku požadavku HTTP "api-key". Možnosti požadavku vždy `method` obsahují pro `headers`použití a . Pokud `bodyJson` není `null`, tělo požadavku HTTP je nastavena na `bodyJson`řetězcovou reprezentaci . Metoda `request` vrátí načíst api slib ke spuštění požadavku HTTP.

```javascript
static async request(url, method, apiKey, bodyJson = null) {
    // Uncomment the following for request details:
    /*
    console.log(`\n${method} ${url}`);
    console.log(`\nKey ${apiKey}`);
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

Pro účely ukázky stačí vyvolat výjimku, pokud požadavek HTTP není úspěšný. V reálné aplikaci byste pravděpodobně provést některé protokolování a `response` diagnostiku stavového kódu HTTP v požadavku na vyhledávací službu. 
    
```javascript
static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
}
```

Nakonec přidejte metody pro detekci, odstranění a vytvoření indexu Azure Cognitive Search. Všechny tyto metody mají stejnou strukturu:

* Získejte koncový bod, ke kterému bude požadavek proveden.
* Vygenerujte požadavek s příslušným koncovým bodem, slovesem HTTP, klíčem rozhraní API a případně tělem JSON. `indexExistsAsync()`a `deleteIndexAsync()` nemají tělo JSON, `createIndexAsync(definition)` ale nemá.
* `await`odpověď na žádost.  
* Zákon o stavovém kódu odpovědi.
* Vrátí příslib některé odpovídající hodnoty (logická hodnota , `this`nebo výsledky dotazu). 

```javascript
async indexExistsAsync() { 
    console.log("\n Checking if index exists...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "GET", this.adminKey);
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
```

Zkontrolujte, zda jsou vaše metody uvnitř třídy a zda ji exportujete. Nejvzdálenější rozsah **AzureSearchClient.js** by měl být:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Objektově orientovaná třída byla dobrou volbou pro potenciálně opakovaně použitelný modul **AzureSearchClient.js,** ale není nezbytná pro hlavní program, který byste měli vložit do souboru s názvem **index.js**. 

Vytvořte **soubor index.js** a začněte tím, že zavedete:

* Balíček **nconf,** který poskytuje flexibilitu pro určení konfigurace pomocí JSON, proměnných prostředí nebo argumentů příkazového řádku.
* Data ze souboru **hotels_quickstart_index.json.**
* Modul `AzureSearchClient`.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

[ **Balíček nconf** ](https://github.com/indexzero/nconf) umožňuje určit konfigurační data v různých formátech, jako jsou proměnné prostředí nebo příkazový řádek. Tato ukázka používá **nconf** základním způsobem ke čtení souboru **azure_search_config.json** a vrátit obsah tohoto souboru jako slovník. Pomocí `get(key)` funkce **nconf**můžete provést rychlou kontrolu, zda byly informace o konfiguraci správně přizpůsobeny. Nakonec funkce vrátí konfiguraci:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME]' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

Funkce `sleep` vytvoří, `Promise` který řeší po zadané množství času. Pomocí této funkce umožňuje aplikaci pozastavit při čekání na operace asynchronní index k dokončení a k dispozici. Přidání takové zpoždění je obvykle nutné pouze v ukázky, testy a ukázkové aplikace.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Nakonec zadejte a zavolejte hlavní `run` asynchronní funkci. Tato funkce volá další funkce v pořadí, `Promise`čeká podle potřeby vyřešit s.

* Načtení konfigurace `getAzureConfiguration()` pomocí dříve napěcovaných
* Vytvoření nové `AzureSearchClient` instance, předávání hodnot z konfigurace
* Zkontrolujte, zda index existuje, a pokud ano, odstraňte jej
* Vytvoření indexu pomocí `indexDefinition` načteného z **hotels_quickstart_index.json**

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
    } catch (x) {
        console.log(x);
    }
}

run();
```

Nezapomeňte na poslední výzvu! `run()` Je to vstupní bod do vašeho `node index.js` programu, když spustíte v dalším kroku.

Všimněte `AzureSearchClient.indexExistsAsync()` `AzureSearchClient.deleteIndexAsync()` si, že a neberou parametry. Tyto funkce `AzureSearchClient.request()` volání `bodyJson` bez argumentu. V `AzureSearchClient.request()`rámci `bodyJson === null` `true`, `init` protože je , struktura je nastavena pouze `indexExistsAsync()` http sloveso ("GET" pro a "DELETE" pro `deleteIndexAsync()`) a záhlaví, které určují klíč požadavku.  

Naproti tomu `AzureSearchClient.createIndexAsync(indexDefinition)` metoda _trvá_ parametr. Funkce `run` v `index.js`aplikaci předá `AzureSearchClient.createIndexAsync(indexDefinition)` metodě obsah souboru **hotels_quickstart_index.json.** Metoda `createIndexAsync()` předá tuto `AzureSearchClient.request()`definici . V `AzureSearchClient.request()`, `bodyJson === null` since `false`je `init` nyní , struktura obsahuje nejen http sloveso ("PUT") a záhlaví, ale nastaví `body` na data definice indexu.

### <a name="prepare-and-run-the-sample"></a>Příprava a spuštění ukázky

Následující příkazy použijte okno terminálu.

1. Přejděte do složky, která obsahuje soubor **package.json** a zbytek kódu.
1. Nainstalujte balíčky pro `npm install`vzorek s .  Tento příkaz stáhne balíčky, na kterých závisí kód.
1. Spusťte `node index.js`program pomocí aplikace .

Měli byste vidět řadu zpráv popisujících akce prováděné programem. Pokud chcete zobrazit další podrobnosti o požadavcích, můžete odkomentovat [řádky na začátku `AzureSearchClient.request()` metody]https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27) v **AzureSearchClient.js**. 

Otevřete **přehled** vyhledávací služby na webu Azure Portal. Vyberte kartu **Indexy.** Měli byste vidět něco jako následující:

![Snímek obrazovky s portálem Azure, přehled vyhledávací služby, karta Indexy](media/search-get-started-nodejs/create-index-no-data.png)

V dalším kroku přidáte data do indexu. 

## <a name="2---load-documents"></a>2 - Načíst dokumenty 

V Azure Cognitive Search jsou dokumenty datové struktury, které jsou vstupy pro indexování a výstupy z dotazů. Musíte post tato data do indexu. To používá jiný koncový bod než operace provedené v předchozím kroku. Otevřete **soubor AzureSearchClient.js** a `getIndexUrl()`za následující způsob přidejte následující metodu:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Stejně jako `AzureSearchClient.createIndexAsync(definition)`, budete `AzureSearchClient.request()` potřebovat funkci, která volá a předává v hotelových datech, aby se jeho tělo. V **AzureSearchClient.js** přidat `postDataAsync(hotelsData)` po `createIndexAsync(definition)`:

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 Vstupy dokumentu mohou být řádky v databázi, objekty BLOB v úložišti objektů Blob nebo dokumenty JSON na disku, jako v této ukázce. Můžete si buď stáhnout [hotels.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) nebo vytvořit svůj vlastní soubor **hotels.json** s následujícím obsahem:

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

Chcete-li tato data načíst do programu, upravte soubor `hotelData` **index.js** přidáním řádku, který odkazuje na v horní části:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Nyní upravte `run()` funkci v **souboru index.js**. Může trvat několik sekund, než bude index k dispozici, proto `AzureSearchClient.postDataAsync(hotelData)`před voláním přidejte 2sekundovou pauzu :

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
    } catch (x) {
        console.log(x);
    }
}
```

Spusťte program `node index.js`znovu s programem . Měli byste vidět mírně odlišnou sadu zpráv od těch, které jste viděli v kroku 1. Tentokrát _index_ existuje a měli byste vidět zprávu o jeho odstranění před aplikací vytvoří nový index a odešle data do něj. 

## <a name="3---search-an-index"></a>3. Prohledání indexu

Vraťte se na kartu **Indexy** v **přehledu** vyhledávací služby na webu Azure Portal. Index nyní obsahuje čtyři dokumenty a spotřebovává určité množství úložiště (může trvat několik minut, než se uI správně odráží základní stav indexu). Klikněte na název indexu, který má být převeden do **Průzkumníka vyhledávání**. Tato stránka umožňuje experimentovat s datovými dotazy. Zkuste hledat na dotaz `*&$count=true` řetězec a měli byste získat zpět všechny dokumenty a počet výsledků. Zkuste s řetězcem `historic&highlight=Description&$filter=Rating gt 4` dotazu a měli byste získat zpět jeden dokument, se slovem "historické" zabalené ve `<em></em>` značkách. Přečtěte si další informace o [tom, jak vytvořit dotaz v Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-query-overview). 

Reprodukovat tyto dotazy v kódu otevřením **index.js** a přidáním tohoto kódu v horní části:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

Do stejného souboru **index.js** napište níže uvedenou `doQueriesAsync()` funkci. Tato funkce `AzureSearchClient` přebírá objekt a `AzureSearchClient.queryAsync` použije metodu pro `queries` každou z hodnot v poli. Používá `Promise.all()` funkci vrátit jeden, `Promise` který řeší pouze v případě, že všechny dotazy byly vyřešeny. Volání formátuje `JSON.stringify(body, null, 4)` výsledek dotazu, aby byl čitelnější.

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

Upravte `run()` funkci tak, aby byla pozastavována `doQueriesAsync(client)` dostatečně dlouho, aby indexer fungoval, a potom funkci volá:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
        // Data availability can take a few seconds
        await sleep(5000);
        await doQueriesAsync(client);
    } catch (x) {
        console.log(x);
    }
}
```

Chcete-li implementovat `AzureSearchClient.queryAsync(query)`, upravte soubor **AzureSearchClient.js**. Vyhledávání vyžaduje jiný koncový bod a hledané termíny se `getSearchUrl(searchTerm)` stanou argumenty adresy URL, proto přidejte funkci vedle metod `getIndexUrl()` a `getPostDataUrl()` metod, které jste již napsali.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

Funkce `queryAsync(searchTerm)` také přejde v **AzureSearchClient.js** a `postDataAsync(data)` sleduje stejnou strukturu jako a další funkce dotazování: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

Vyhledávání se provádí pomocí slovesa "GET" a bez těla, protože hledaný termín je součástí adresy URL. Všimněte `queryAsync(searchTerm)` `this.queryKey`si, že používá , na rozdíl od jiných funkcí, které používají klíč správce. Klíče dotazu, jak název napovídá, lze použít pouze pro dotazování indexu a nelze použít k úpravě indexu v žádném případě. Klíče dotazu jsou proto bezpečnější distribuovat do klientských aplikací.

Spusťte `node index.js`program s programem . Nyní, kromě předchozích kroků, budou odeslány dotazy a výsledky zapsány do konzoly.

### <a name="about-the-sample"></a>O vzorku

Ukázka používá malé množství hotelových dat, které jsou dostatečné k předvedení základů vytváření a dotazování na index Azure Cognitive Search.

Třída **AzureSearchClient** zapouzdřuje konfiguraci, adresy URL a základní požadavky HTTP pro vyhledávací službu. Soubor **index.js** načte konfigurační data pro službu Azure Cognitive Search, data hotelu, `run` která budou odeslána pro indexování, a ve své funkci objednávky a provede různé operace.

Celkové chování `run` funkce je odstranit index Azure Cognitive Search, pokud existuje, vytvořit index, přidat některá data a provést některé dotazy.  

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu **Všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, nezapomeňte, že jste omezeni na tři indexy, indexery a zdroje dat. Můžete odstranit jednotlivé položky na portálu, abyste zůstali pod limitem. 

## <a name="next-steps"></a>Další kroky

V tomto node.js rychlý start jste pracovali prostřednictvím řady úkolů k vytvoření indexu, načíst s dokumenty a spustit dotazy. Udělali jsme určité kroky, jako je například čtení konfigurace a definování dotazů, nejjednodušším možným způsobem. V reálné aplikaci byste chtěli umístit tyto obavy do samostatných modulů, které by poskytly flexibilitu a zapouzdření. 
 
Pokud už máte nějaké pozadí v Azure Cognitive Search, můžete použít tuto ukázku jako odrazový můstek pro vyzkoušení návrže (dotazy typu dopředu nebo automatické dokončování), filtry a fazetované navigace. Pokud jste s Azure Cognitive Search noví, doporučujeme vyzkoušet další kurzy, abyste mohli pochopit, co můžete vytvořit. Pokud hledáte další zdroje, přejděte na [stránku dokumentace](https://azure.microsoft.com/documentation/services/search/). 

> [!div class="nextstepaction"]
> [Volání Azure Cognitive Search z webové stránky pomocí Javascriptu](https://github.com/liamca/azure-search-javascript-samples)
