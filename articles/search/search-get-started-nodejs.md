---
title: 'Rychlý Start: vytvoření indexu vyhledávání v Node.js pomocí rozhraní REST API'
titleSuffix: Azure Cognitive Search
description: V tomto Node.js rychlém startu se dozvíte, jak vytvořit index, načíst data a spustit dotazy v Azure Kognitivní hledání pomocí JavaScriptu a rozhraní REST API.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.devlang: nodejs
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: devx-track-js
ms.openlocfilehash: ab3142426b12f86b9f8afa84ce0ea05fc1939eec
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400600"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-nodejs-using-rest-apis"></a>Rychlý Start: vytvoření indexu služby Azure Kognitivní hledání v Node.js pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Azure Portal](search-get-started-portal.md)
> * [PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Vytvořte aplikaci Node.js, která vytvoří, načte a zadá dotaz na index Azure Kognitivní hledání. Tento článek ukazuje, jak vytvořit aplikaci krok za krokem. Alternativně můžete [stáhnout zdrojový kód a data](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) a spustit aplikaci z příkazového řádku.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Pro sestavování a testování tohoto rychlého startu jsme použili následující software a služby:

+ [Node.js](https://nodejs.org)

+ [Npm](https://www.npmjs.com) by se měl nainstalovat pomocí Node.js

+ V tomto článku se uvádí ukázková struktura indexu a vyhovující dokumenty, nebo v adresáři [ **rychlého** startu úložiště.](https://github.com/Azure-Samples/azure-search-javascript-samples/)

+ [Vytvořte službu Azure kognitivní hledání](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu.

Doporučené:

* [Visual Studio Code](https://code.visualstudio.com)

* Rozšíření [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) a [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) pro VSCode.

<a name="get-service-info"></a>

## <a name="get-keys-and-urls"></a>Získání klíčů a adres URL

Volání služby vyžaduje koncový bod adresy URL a přístupový klíč pro každý požadavek. Vyhledávací služba se vytvoří s oběma, takže pokud jste do svého předplatného přidali Azure Kognitivní hledání, postupujte podle těchto kroků a získejte potřebné informace:

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte název vaší vyhledávací služby. Název služby můžete potvrdit zkontrolováním adresy URL koncového bodu. Pokud byla adresa URL koncového bodu `https://mydemo.search.windows.net` , název vaší služby by byl `mydemo` .

2. V části **Nastavení**  >  **klíče**Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

    Získejte taky klíč dotazu. Osvědčeným postupem je vystavovat požadavky na dotazy s přístupem jen pro čtení.

:::image type="content" source="media/search-get-started-nodejs/service-name-and-keys.png" alt-text="Získání názvu služby a klíčů pro správu a dotazy" border="false":::

Všechny požadavky vyžadují klíč rozhraní API-Key v hlavičce všech požadavků odeslaných službě. Platný klíč vytváří vztah důvěryhodnosti na základě jednotlivých požadavků mezi aplikací odesílající požadavek a službu, která ho zpracovává.

## <a name="set-up-your-environment"></a>Nastavení prostředí

Začněte tím, že otevřete konzolu prostředí PowerShell nebo jiné prostředí, ve kterém jste nainstalovali Node.js.

1. Vytvořte adresář pro vývoj, který mu dává název `quickstart` :

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Inicializujte prázdný projekt pomocí NPM spuštěním `npm init` . Přijměte výchozí hodnoty, s výjimkou licence, kterou byste měli nastavit na hodnotu MIT. 

1. Přidejte balíčky, které budou záviset na kódu a pomoci při vývoji:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Ověřte, že jste nakonfigurovali projekty a její závislosti tím, že zkontrolujete, že  **package.jsv** souboru vypadá podobně jako v následujícím příkladu:

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

5. Vytvořte soubor **azure_search_config.js** pro uložení dat vyhledávací služby:

    ```json
    {
        "serviceName" : "[SEARCH_SERVICE_NAME]",
        "adminKey" : "[ADMIN_KEY]",
        "queryKey" : "[QUERY_KEY]",
        "indexName" : "hotels-quickstart"
    }
    ```

Nahraďte `[SERVICE_NAME]` hodnotu názvem vaší vyhledávací služby. `[ADMIN_KEY]`Hodnoty a nahraďte `[QUERY_KEY]` klíči, které jste si poznamenali dříve. 

## <a name="1---create-index"></a>1. vytvoření indexu 

Vytvořte soubor **hotels_quickstart_index.jsv**.  Tento soubor definuje, jak Azure Kognitivní hledání pracuje s dokumenty, které budete načítat v dalším kroku. Jednotlivá pole budou identifikována `name` a zadána `type` . Každé pole má také řadu atributů indexu, které určují, zda může Azure Kognitivní hledání Hledat, filtrovat, třídit a omezující vlastnosti na poli. Většina polí je jednoduchý datový typ, ale některé, jako `AddressType` jsou komplexní typy, které umožňují vytvářet struktury s bohatou datovou strukturou v indexu.  Můžete si přečíst další informace o [podporovaných datových typech](/rest/api/searchservice/supported-data-types) a [atributech indexu](./search-what-is-an-index.md#index-attributes). 

Přidejte následující **hotels_quickstart_index.js** nebo [Stáhněte soubor](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels_quickstart_index.json). 

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
    

Je vhodné oddělit konkrétní konkrétní scénář od kódu, který bude široce použitelný. `AzureSearchClient`Třída definovaná v souboru **AzureSearchClient.js** bude obsahovat informace o tom, jak vytvořit adresy URL požadavku, vytvořit žádost pomocí rozhraní API pro načtení a reagovat na stavový kód odpovědi.

Začněte pracovat na **AzureSearchClient.js** importem balíčku **Node-Fetch** a vytvořením jednoduché třídy. Izolujte měnitelné části `AzureSearchClient` třídy předáním jeho konstruktoru k různým hodnotám konfigurace:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    constructor(searchServiceName, adminKey, queryKey, indexName) {
        this.searchServiceName = searchServiceName;
        this.adminKey = adminKey;
        // The query key is used for read-only requests and so can be distributed with less risk of abuse.
        this.queryKey = queryKey;
        this.indexName = indexName;
        this.apiVersion = '2020-06-30';
    }

    // All methods go inside class body here!
}

module.exports = AzureSearchClient;
```

První odpovědností třídy je zjistit, jak vytvořit adresy URL, na které se mají odesílat různé požadavky. Sestavujte tyto adresy URL pomocí metod instance, které používají konfigurační data předaná konstruktoru třídy. Všimněte si, že adresa URL, kterou vytvoří, je specifická pro verzi rozhraní API a musí mít Argument určující tuto verzi (v této aplikaci `2020-06-30` ). 

První z těchto metod vrátí adresu URL pro samotný index. Do těla třídy přidejte následující metodu:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

Další zodpovědnost za `AzureSearchClient` provádí asynchronní požadavek s rozhraním API pro načtení. Asynchronní statická metoda `request` přijímá adresu URL, řetězec, který určuje metodu HTTP ("Get", "Put", "post", "Delete"), klíč, který se má použít v žádosti, a volitelný objekt JSON. `headers`Proměnná mapuje `queryKey` (zda klíč správce nebo klíč dotazu jen pro čtení) do HLAVIČKY požadavku HTTP API-Key. Možnosti žádosti vždycky obsahují, `method` které se mají použít, a `headers` . Pokud `bodyJson` není `null` , tělo požadavku HTTP je nastaveno na řetězcové vyjádření `bodyJson` . `request`Metoda vrátí příslib rozhraní API pro načtení a provede požadavek HTTP.

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

V případě demonstračních účely stačí vyvolat výjimku, pokud požadavek HTTP není úspěšný. V reálné aplikaci byste pravděpodobně proznamenali protokolování a diagnostiku stavového kódu HTTP v rámci žádosti o `response` vyhledávací službu. 
    
```javascript
static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
}
```

Nakonec přidejte metody pro detekci, odstranění a vytvoření indexu služby Azure Kognitivní hledání. Všechny tyto metody mají stejnou strukturu:

* Získejte koncový bod, na který bude požadavek proveden.
* Vygenerujte požadavek s příslušným koncovým bodem, příkazem HTTP, klíčem rozhraní API a v případě potřeby text JSON. `indexExistsAsync()` a neobsahují `deleteIndexAsync()` tělo JSON, ale `createIndexAsync(definition)` má.
* `await` odpověď na požadavek.  
* Pracovat s kódem stavu odpovědi.
* Vrátí příslib nějaké vhodné hodnoty (logická hodnota, `this` nebo výsledky dotazu). 

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

Potvrďte, že vaše metody jsou uvnitř třídy a že tuto třídu exportujete. Nejvzdálenější rozsah **AzureSearchClient.js** by měl být:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Objektově orientovaná třída byla vhodnou volbou pro potenciálně opakovaně použitelný **AzureSearchClient.js** modul, ale není nutná pro hlavní program, který byste měli umístit do souboru s názvem **index.js**. 

Vytvořte **index.js** a začněte tím, že naplníte:

* Balíček **NConf** , který poskytuje flexibilitu pro zadání konfigurace s JSON, proměnnými prostředí nebo argumenty příkazového řádku.
* Data z **hotels_quickstart_index.jsv** souboru.
* Modul `AzureSearchClient`.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

Balíček [ **NConf** ](https://github.com/indexzero/nconf) umožňuje zadat konfigurační data v nejrůznějších formátech, jako jsou například proměnné prostředí nebo příkazový řádek. Tato ukázka používá **NConf** základního způsobu pro čtení souboru **azure_search_config.jsna** a vrácení obsahu tohoto souboru jako slovníku. Pomocí **nconf**funkce NConf `get(key)` můžete rychle ověřit, zda byly informace o konfiguraci správně přizpůsobené. Nakonec funkce vrátí konfiguraci:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME]' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

`sleep`Funkce vytvoří objekt `Promise` , který se vyřeší po zadaném časovém intervalu. Pomocí této funkce lze aplikaci pozastavit při čekání na dokončení a zpřístupnění asynchronních operací indexu. Přidání takového zpoždění je obvykle nutné pouze v ukázkách, testech a ukázkových aplikacích.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Nakonec zadejte a zavolejte hlavní asynchronní `run` funkci. Tato funkce volá další funkce v pořadí, které čekají podle potřeby pro řešení `Promise` s.

* Načtěte konfiguraci pomocí `getAzureConfiguration()` dříve zapsaného.
* Vytvoří novou `AzureSearchClient` instanci, která předává hodnoty z vaší konfigurace.
* Ověřte, zda index existuje, a pokud ano, odstraňte jej
* Vytvoří index pomocí `indexDefinition` **hotels_quickstart_index.js** načtený z

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
    } catch (x) {
        console.log(x);
    }
}

run();
```

Nezapomeňte, že konečné volání `run()` ! Je to vstupní bod programu při spuštění `node index.js` v dalším kroku.

Všimněte si, že `AzureSearchClient.indexExistsAsync()` a `AzureSearchClient.deleteIndexAsync()` nepřebírají parametry. Tyto funkce volají `AzureSearchClient.request()` bez `bodyJson` argumentu. V rámci `AzureSearchClient.request()` , protože `bodyJson === null` je `true` , `init` struktura je nastavená tak, aby byla pouze příkaz HTTP ("Get" pro `indexExistsAsync()` a "Delete" pro `deleteIndexAsync()` ) a hlavičkám, které určují klíč požadavku.  

Naproti tomu `AzureSearchClient.createIndexAsync(indexDefinition)` Metoda přijímá _does_ parametr. `run`Funkce v systému `index.js` předá obsah souboru **hotels_quickstart_index.js** do `AzureSearchClient.createIndexAsync(indexDefinition)` metody. `createIndexAsync()`Metoda předá tuto definici do `AzureSearchClient.request()` . V `AzureSearchClient.request()` , protože `bodyJson === null` je nyní `false` struktura, `init` zahrnuje nejen příkaz HTTP ("Put") a záhlaví, ale nastaví `body` data definice indexu.

### <a name="prepare-and-run-the-sample"></a>Příprava a spuštění ukázky

Pro následující příkazy použijte okno terminálu.

1. Přejděte do složky, která obsahuje **package.js** souboru, a zbývající část kódu.
1. Nainstalujte balíčky pro ukázku pomocí `npm install` .  Tento příkaz stáhne balíčky, na kterých závisí kód.
1. Spusťte program pomocí programu `node index.js` .

Měli byste vidět řadu zpráv popisujících akce prováděné programem. Pokud chcete zobrazit podrobnější informace o požadavcích, můžete zrušit komentář na [řádky na začátku `AzureSearchClient.request()` metody] https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27) v **AzureSearchClient.js**. 

Otevřete **Přehled** služby vyhledávání v Azure Portal. Vyberte kartu **indexy** . Měl by se zobrazit něco podobného následujícímu:

:::image type="content" source="media/search-get-started-nodejs/create-index-no-data.png" alt-text="Snímek obrazovky Azure Portal, přehled služby Search, karta indexy" border="false":::

V dalším kroku přidáte data do indexu. 

## <a name="2---load-documents"></a>2. načtení dokumentů 

V Azure Kognitivní hledání jsou dokumenty datové struktury, které jsou ve dvou vstupech k indexování a výstupy z dotazů. Taková data musíte publikovat do indexu. Používá jiný koncový bod než operace provedené v předchozím kroku. Otevřete **AzureSearchClient.js** a přidejte následující metodu za `getIndexUrl()` :

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Například `AzureSearchClient.createIndexAsync(definition)` potřebujete funkci, která zavolá `AzureSearchClient.request()` a předává údaje o hotelu do jejich těla. Do **AzureSearchClient.js** přidat `postDataAsync(hotelsData)` za `createIndexAsync(definition)` :

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 Vstupy dokumentů můžou být řádky v databázi, objekty BLOB v úložišti objektů BLOB nebo, jako v této ukázce, dokumenty JSON na disku. Můžete buď stáhnout [hotels.js](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) nebo vytvořit vlastní **hotels.jsv** souboru s následujícím obsahem:

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

Chcete-li načíst tato data do programu, upravte **index.js** přidáním řádku, který odkazuje na `hotelData` poblíž horního okraje:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Nyní upravte `run()` funkci v **index.js**. Může trvat několik sekund, než bude index k dispozici, proto před voláním přidejte pauzu o 2 sekundu `AzureSearchClient.postDataAsync(hotelData)` :

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

Spusťte program znovu s `node index.js` . Měla by se zobrazit trochu odlišná sada zpráv z těch, které jste viděli v kroku 1. Tentokrát _index existuje_ a měla by se vám zobrazit zpráva o jeho odstranění, než aplikace vytvoří nový index a odešle do něj data. 

## <a name="3---search-an-index"></a>3. Prohledání indexu

Vraťte se na kartu **indexy** v **přehledu** služby vyhledávání na Azure Portal. Index teď obsahuje čtyři dokumenty a spotřebovává určitou velikost úložiště (může trvat několik minut, než uživatelské rozhraní správně odrážejí základní stav indexu). Klikněte na název indexu, který se má považovat do **Průzkumníka hledání**. Tato stránka umožňuje experimentovat s dotazy na data. Zkuste hledat v řetězci dotazu `*&$count=true` a měli byste získat zpátky všechny své dokumenty a počet výsledků. Zkuste s řetězcem dotazu `historic&highlight=Description&$filter=Rating gt 4` a měli byste se vrátit do jednoho dokumentu, ve kterém je Word "historické" zabalený do `<em></em>` značek. Přečtěte si další informace o [tom, jak vytvořit dotaz v Azure kognitivní hledání](./search-query-overview.md). 

Reprodukování těchto dotazů v kódu otevřením **index.js** a přidáním tohoto kódu poblíž horního okraje:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

Do stejného **index.js** souboru zapište `doQueriesAsync()` funkci zobrazenou níže. Tato funkce přebírá `AzureSearchClient` objekt a aplikuje `AzureSearchClient.queryAsync` metodu na každou hodnotu v `queries` poli. Používá `Promise.all()` funkci k vrácení jediného `Promise` , který se vyřeší pouze v případě, že byly vyřešeny všechny dotazy. Volání pro `JSON.stringify(body, null, 4)` Formátování výsledku dotazu bude čitelnější.

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

Upravte `run()` funkci tak, aby pozastavila dostatečně dlouho, aby indexer fungoval, a pak zavolejte `doQueriesAsync(client)` funkci:

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

Chcete-li implementovat `AzureSearchClient.queryAsync(query)` , upravte soubor **AzureSearchClient.js**. Hledání vyžaduje jiný koncový bod a hledané výrazy se stanou argumenty adresy URL, takže přidejte funkci `getSearchUrl(searchTerm)` spolu s `getIndexUrl()` `getPostDataUrl()` již zapsanými metodami a.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

`queryAsync(searchTerm)`Funkce také směřuje do **AzureSearchClient.js** a řídí se stejnou strukturou jako `postDataAsync(data)` a dalšími funkcemi pro dotazování: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

Hledání se provádí pomocí příkazu GET a bez těla, protože hledaný termín je součástí adresy URL. Všimněte si, že na `queryAsync(searchTerm)` `this.queryKey` rozdíl od ostatních funkcí, které používaly klíč správce, se na rozdílovém používání. Klíče dotazů, jak název implikuje, lze použít pouze pro dotazování indexu a nelze je použít k žádným způsobem pro úpravu indexu. Klíče dotazů jsou proto bezpečnější pro distribuci klientských aplikací.

Spusťte program pomocí nástroje `node index.js` . Nyní se dotazy odesílají spolu s předchozími kroky a výsledky se zapisují do konzoly.

### <a name="about-the-sample"></a>O ukázce

Ukázka používá malé množství dat hotelového data dostačující k předvedení základních informací o vytváření a dotazování indexu služby Azure Kognitivní hledání.

Třída **AzureSearchClient** zapouzdřuje konfiguraci, adresy URL a základní požadavky HTTP pro vyhledávací službu. Soubor **index.js** načte konfigurační data pro službu Azure kognitivní hledání, hotelová data, která se budou načítat pro indexování, a ve své `run` funkci objednávky a provede různé operace.

Celkové chování `run` funkce je odstranit index služby Azure kognitivní hledání, pokud existuje, vytvořit index, přidat data a provést některé dotazy.  

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu Node.js jste pracovali pomocí řady úkolů, abyste mohli vytvořit index, načíst ho s dokumenty a spouštět dotazy. Provedli jsme některé kroky, jako je například čtení konfigurace a definování dotazů, a to nejjednodušším možným způsobem. V reálné aplikaci byste chtěli tyto aspekty umístit do samostatných modulů, které by poskytovaly flexibilitu a zapouzdření. 
 
Pokud již máte v Azure Kognitivní hledání nějaké pozadí, můžete tuto ukázku použít jako odrazový můstek pro pokus o vyzkoušení návrhů (dotazování typu dopředu nebo automatické dokončování), filtrů a omezující navigaci. Pokud s Azure Kognitivní hledání začínáte, doporučujeme vyzkoušet si další kurzy, které vám pomůžou s vývojem toho, co můžete vytvořit. Pokud hledáte další zdroje, přejděte na [stránku dokumentace](https://azure.microsoft.com/documentation/services/search/). 

> [!div class="nextstepaction"]
> [Volání Azure Kognitivní hledání z webové stránky pomocí JavaScriptu](https://github.com/liamca/azure-search-javascript-samples)