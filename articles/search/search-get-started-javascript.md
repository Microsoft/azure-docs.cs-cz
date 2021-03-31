---
title: 'Rychlý Start: vytvoření indexu vyhledávání v JavaScriptu'
titleSuffix: Azure Cognitive Search
description: V tomto rychlém startu JavaScriptu se dozvíte, jak vytvořit index, načíst data a spustit dotazy v Azure Kognitivní hledání pomocí JavaScriptu.
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.devlang: javascript
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/02/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7d31201f9d1a4519538aba3ac57ddfd340d936d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509397"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-the-javascript-sdk"></a>Rychlý Start: vytvoření indexu služby Azure Kognitivní hledání pomocí sady JavaScript SDK
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [Azure Portal](search-get-started-portal.md)
> * [PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [REST](search-get-started-rest.md)


Pomocí [JavaScriptu/TYPSCRIPT SDK pro Azure kognitivní hledání](/javascript/api/overview/azure/search-documents-readme) vytvořit aplikaci Node.js v JavaScriptu, která vytvoří, načte a dotazuje index vyhledávání.

Tento článek ukazuje, jak vytvořit aplikaci krok za krokem. Alternativně můžete [stáhnout zdrojový kód a data](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) a spustit aplikaci z příkazového řádku.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete mít k dispozici následující nástroje a služby:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).

+ Služba Azure Kognitivní hledání. [Vytvořte službu](search-create-service-portal.md) nebo [vyhledejte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Pro tento rychlý Start můžete použít bezplatnou službu. 

+ [Node.js](https://nodejs.org) a [npm](https://www.npmjs.com)

+ [Visual Studio Code](https://code.visualstudio.com) nebo jiné integrované vývojové prostředí (IDE)


## <a name="set-up-your-project"></a>Nastavení projektu

Začněte získáním koncového bodu a klíče pro vyhledávací službu. Pak vytvořte nový projekt s NPM, jak je uvedeno níže.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Zkopírování klíče a koncového bodu

Volání služby vyžaduje koncový bod adresy URL a přístupový klíč pro každý požadavek. Jako první krok najděte klíč rozhraní API a adresu URL, které chcete přidat do projektu. Při vytváření klienta v pozdějším kroku budete určovat obě hodnoty.

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte adresu URL. Příkladem koncového bodu může být `https://mydemo.search.windows.net`.

2. V části **Nastavení**  >  **klíče** Získejte klíč správce pro úplná práva ke službě, který je vyžadován při vytváření nebo odstraňování objektů. Existují dva zaměnitelné primární a sekundární klíče. Můžete použít jednu z těchto.

   ![Získání koncového bodu HTTP a přístupového klíče](media/search-get-started-rest/get-url-key.png "Získání koncového bodu HTTP a přístupového klíče")

Všechny požadavky vyžadují klíč rozhraní API na všech žádostech odeslaných službě. Platný klíč vytváří na základě žádosti vztah důvěryhodnosti mezi aplikací, která žádost odeslala, a službou, která ji zpracovává.

### <a name="create-a-new-npm-project"></a>Vytvořit nový projekt NPM

Začněte tím, že otevřete VS Code a jeho [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) nebo jiný terminál, například příkazový řádek Node.js.

1. Vytvořte adresář pro vývoj, který mu dává název `quickstart` :

    ```cmd
    mkdir quickstart
    cd quickstart
    ```

2. Inicializovat prázdný projekt pomocí NPM spuštěním 

    ```cmd
    npm init
    ```
     Přijměte výchozí hodnoty, s výjimkou licence, kterou byste měli nastavit na hodnotu MIT. 

3. Nainstalujte `@azure/search-documents` [sadu SDK pro Azure kognitivní hledání v jazyce JavaScript/Typscript](/javascript/api/overview/azure/search-documents-readme).

    ```cmd
    npm install @azure/search-documents
    ```

4. Nainstalujte `dotenv` , který se používá k importu proměnných prostředí, jako je náš název služby a klíč rozhraní API.
    ```cmd
    npm install dotenv
    ```

5. Ověřte, že jste nakonfigurovali projekty a její závislosti tím, že zkontrolujete, že  **package.jsv** souboru vypadá podobně jako v následujícím formátu JSON:

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
        "Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "@azure/search-documents": "^11.0.3",
        "dotenv": "^8.2.0"
      }
    }
    ```

6. Vytvořte soubor **. env** pro uložení parametrů vyhledávací služby:

    ```
    SEARCH_API_KEY=<search-admin-key>
    SEARCH_API_ENDPOINT=https://<search-service-name>.search.windows.net
    ```

Nahraďte `<search-service-name>` hodnotu názvem vaší vyhledávací služby. Nahraďte `<search-admin-key>` hodnotou klíče, kterou jste si poznamenali dříve. 

### <a name="create-indexjs-file"></a>Vytvořit soubor index.js

V dalším kroku vytvoříme soubor **index.js** , což je hlavní soubor, který bude hostovat náš kód.

V horní části tohoto souboru naimportujeme `@azure/search-documents` knihovnu:

```javascript
const { SearchIndexClient, SearchClient, AzureKeyCredential, odata } = require("@azure/search-documents");
```

V dalším kroku musíme vyžadovat, `dotenv` aby se balíček četl v parametrech souboru **. env** následujícím způsobem:

```javascript
// Load the .env file if it exists
require("dotenv").config();

// Getting endpoint and apiKey from .env file
const endpoint = process.env.SEARCH_API_ENDPOINT || "";
const apiKey = process.env.SEARCH_API_KEY || "";
```

Pomocí našich proměnných pro import a prostředí jsme připraveni k definování funkce main.

Většina funkcí v sadě SDK je asynchronní, takže jsme naši hlavní funkci provedli `async` . Kromě toho jsme zahrnuli `main().catch()` funkci main, která zachytí a protokoluje všechny zjištěné chyby:

```javascript
async function main() {
    console.log(`Running Azure Cognitive Search Javascript quickstart...`);
    if (!endpoint || !apiKey) {
        console.log("Make sure to set valid values for endpoint and apiKey with proper authorization.");
        return;
    }

    // remaining quickstart code will go here
}

main().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

V takovém případě jsme připraveni vytvořit index.

## <a name="1---create-index"></a>1. vytvoření indexu 

Vytvořte soubor **hotels_quickstart_index.jsv**.  Tento soubor definuje, jak Azure Kognitivní hledání pracuje s dokumenty, které budete načítat v dalším kroku. Jednotlivá pole budou identifikována `name` a zadána `type` . Každé pole má také řadu atributů indexu, které určují, zda může Azure Kognitivní hledání Hledat, filtrovat, třídit a omezující vlastnosti na poli. Většina polí je jednoduchý datový typ, ale některé, jako `AddressType` jsou komplexní typy, které umožňují vytvářet struktury s bohatou datovou strukturou v indexu.  Můžete si přečíst další informace o [podporovaných datových typech](/rest/api/searchservice/supported-data-types) a indexových atributech popsaných v tématu [vytvoření indexu (REST)](/rest/api/searchservice/create-index). 

Přidejte následující **hotels_quickstart_index.js** nebo [Stáhněte soubor](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels_quickstart_index.json). 

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

V rámci naší definice indexu chceme importovat  **hotels_quickstart_index.js** v horní části **index.js** , aby funkce main mohla získat přístup k definici indexu.

```javascript
const indexDefinition = require('./hotels_quickstart_index.json');
```

V rámci hlavní funkce vytvoříme `SearchIndexClient` , který slouží k vytváření a správě indexů pro Azure kognitivní hledání. 

```javascript
const indexClient = new SearchIndexClient(endpoint, new AzureKeyCredential(apiKey));
```

Dále chceme index odstranit, pokud již existuje. Toto je běžná praxe pro testovací/ukázkový kód.

To provedeme tak, že definujeme jednoduchou funkci, která se pokusí index odstranit.

```javascript
async function deleteIndexIfExists(indexClient, indexName) {
    try {
        await indexClient.deleteIndex(indexName);
        console.log('Deleting index...');
    } catch {
        console.log('Index does not exist yet.');
    }
}
```

Chcete-li spustit funkci, extrahujeme název indexu z definice indexu a předejte `indexName` spolu s `indexClient` `deleteIndexIfExists()` funkcí funkci.

```javascript
const indexName = indexDefinition["name"];

console.log('Checking if index exists...');
await deleteIndexIfExists(indexClient, indexName);
```

Potom jsme připraveni vytvořit index s `createIndex()` metodou.

```javascript
console.log('Creating index...');
let index = await indexClient.createIndex(indexDefinition);

console.log(`Index named ${index.name} has been created.`);
```

### <a name="run-the-sample"></a>Spuštění ukázky

V tuto chvíli jste připraveni spustit ukázku. Pomocí okna terminálu spusťte následující příkaz:

```cmd
node index.js
```

Pokud jste [stáhli zdrojový kód](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) a zatím jste nenainstalovali požadované balíčky, spusťte `npm install` nejprve příkaz.

Měli byste vidět řadu zpráv popisujících akce prováděné programem. 

Otevřete **Přehled** služby vyhledávání v Azure Portal. Vyberte kartu **indexy** . Měl by se zobrazit něco podobného následujícímu:

:::image type="content" source="media/search-get-started-javascript/create-index-no-data.png" alt-text="Snímek obrazovky Azure Portal, přehled služby Search, karta indexy" border="false":::

V dalším kroku přidáte data do indexu. 

## <a name="2---load-documents"></a>2. načtení dokumentů 


V Azure Kognitivní hledání jsou dokumenty datové struktury, které jsou ve dvou vstupech k indexování a výstupy z dotazů. Tato data můžete do indexu vložit nebo použít [indexer](search-indexer-overview.md). V tomto případě budeme programově dokumenty do indexu.

Vstupy dokumentů můžou být řádky v databázi, objekty BLOB v úložišti objektů BLOB nebo, jako v této ukázce, dokumenty JSON na disku. Můžete buď stáhnout [hotels.js](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels.json) nebo vytvořit vlastní **hotels.jsv** souboru s následujícím obsahem:

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

Podobně jako u indexDefinition je také nutné naimportovat `hotels.json` na začátek **index.js** tak, aby k datům bylo možné přicházet v naší hlavní funkci.

```javascript
const hotelData = require('./hotels.json');
```


Abychom mohli indexovat data do indexu hledání, teď je potřeba vytvořit `SearchClient` . I když se `SearchIndexClient` používá k vytvoření a správě indexu, `SearchClient` slouží k nahrání dokumentů a dotazování indexu.

Existují dva způsoby, jak vytvořit `SearchClient` . První možností je vytvořit `SearchClient` od začátku:

```javascript
 const searchClient = new SearchClient(endpoint, indexName, new AzureKeyCredential(apiKey));
```

Alternativně můžete použít `getSearchClient()` metodu `SearchIndexClient` pro vytvoření `SearchClient` :

```javascript
const searchClient = indexClient.getSearchClient(indexName);
```

Teď, když je klient definovaný, nahrajte dokumenty do indexu vyhledávání. V tomto případě používáme `mergeOrUploadDocuments()` metodu, která odešle dokumenty nebo sloučí s existujícím dokumentem, pokud už existuje dokument se stejným klíčem.

```javascript
console.log('Uploading documents...');
let indexDocumentsResult = await searchClient.mergeOrUploadDocuments(hotelData['value']);

console.log(`Index operations succeeded: ${JSON.stringify(indexDocumentsResult.results[0].succeeded)}`);
```

Spusťte program znovu s `node index.js` . Měla by se zobrazit trochu odlišná sada zpráv z těch, které jste viděli v kroku 1. Tentokrát *index existuje* a měla by se vám zobrazit zpráva o jeho odstranění, než aplikace vytvoří nový index a odešle do něj data. 

Než spustíme dotazy v dalším kroku, definujte funkci, která bude program čekat na jednu sekundu. To se provádí jenom pro účely testování/ukázky, aby se zajistilo dokončení indexování a aby byly dokumenty dostupné v indexu pro naše dotazy.

```javascript
function sleep(ms) {
    var d = new Date();
    var d2 = null;
    do {
        d2 = new Date();
    } while (d2 - d < ms);
}
```

Chcete-li, aby program čekal na jednu sekundu, zavolejte `sleep` funkci podobnou této:

```javascript
sleep(1000);
```

## <a name="3---search-an-index"></a>3. Prohledání indexu

Když jste vytvořili index a nahráli dokumenty, jste připraveni odeslat dotazy do indexu. V této části pošleme do indexu vyhledávání pět různých dotazů, abychom předvedli různé funkce dotazů, které máte k dispozici.

Dotazy jsou zapsány ve `sendQueries()` funkci, kterou zavoláme do hlavní funkce následujícím způsobem:

```javascript
await sendQueries(searchClient);
```

Dotazy jsou odesílány pomocí `search()` metody `searchClient` . Prvním parametrem je hledaný text a druhý parametr jsou jakékoli další možnosti hledání.

První dotaz provede hledání `*` , který je ekvivalentní k hledání všeho a vybere tři pole v indexu. Osvědčeným postupem jsou pouze ta `select` pole, která potřebujete, protože navrácení zbytečných dat může do dotazů přidat latenci.

`searchOptions`Pro tento dotaz je také `includeTotalCount` nastaveno na hodnotu `true` , která vrátí počet nalezených výsledků hledání.

```javascript
async function sendQueries(searchClient) {
    console.log('Query #1 - search everything:');
    let searchOptions = {
        includeTotalCount: true,
        select: ["HotelId", "HotelName", "Rating"]
    };

    let searchResults = await searchClient.search("*", searchOptions);
    for await (const result of searchResults.results) {
        console.log(`${JSON.stringify(result.document)}`);
    }
    console.log(`Result count: ${searchResults.count}`);

    // remaining queries go here
}
```

Zbývající dotazy, které jsou uvedeny níže, by měly být také přidány do `sendQueries()` funkce. Jsou zde oddělené pro čitelnost.

V dalším dotazu určíme hledaný termín `"wifi"` a také zahrnete filtr, který vrátí jenom výsledky, u kterých je stav roven `'FL'` . Výsledky jsou také seřazené podle hotelu `Rating` .

```javascript
console.log('Query #2 - Search with filter, orderBy, and select:');
let state = 'FL';
searchOptions = {
    filter: odata`Address/StateProvince eq ${state}`,
    orderBy: ["Rating desc"],
    select: ["HotelId", "HotelName", "Rating"]
};

searchResults = await searchClient.search("wifi", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

V dalším kroku je hledání omezené na jediné prohledávatelné pole pomocí `searchFields` parametru. Je to skvělý způsob, jak dotaz zefektivnit, pokud víte, že máte jenom shody v určitých polích. 

```javascript
console.log('Query #3 - Limit searchFields:');
searchOptions = {
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("sublime cliff", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
console.log();
```

Další běžnou možností pro zahrnutí do dotazu je `facets` . Omezující vlastnosti umožňují vytvářet filtry na uživatelském rozhraní, které uživatelům usnadňují znalost hodnot, na které mohou filtrovat.

```javascript
console.log('Query #4 - Use facets:');
searchOptions = {
    facets: ["Category"],
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("*", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

Konečný dotaz používá `getDocument()` metodu `searchClient` . To vám umožní efektivně načíst dokument pomocí klíče. 

```javascript
console.log('Query #5 - Lookup document:');
let documentResult = await searchClient.getDocument(key='3')
console.log(`HotelId: ${documentResult.HotelId}; HotelName: ${documentResult.HotelName}`)
```

### <a name="run-the-sample"></a>Spuštění ukázky

Spusťte program pomocí nástroje `node index.js` . Nyní se dotazy odesílají spolu s předchozími kroky a výsledky se zapisují do konzoly.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu JavaScriptu jste pracovali pomocí řady úkolů, abyste mohli vytvořit index, načíst ho s dokumenty a spouštět dotazy. 

Pokud již máte v Azure Kognitivní hledání nějaké pozadí, můžete tuto ukázku použít jako odrazový můstek pro pokus o vyzkoušení návrhů (dotazování typu dopředu nebo automatické dokončování), filtrů a omezující navigaci. Pokud s Azure Kognitivní hledání začínáte, doporučujeme vyzkoušet si další kurzy, které vám pomůžou s vývojem toho, co můžete vytvořit. Pokud hledáte další zdroje, přejděte na [stránku dokumentace](https://azure.microsoft.com/documentation/services/search/). 

> [!div class="nextstepaction"]
> [Vytvoření reakce front-endu pro Azure Kognitivní hledání](https://github.com/dereklegenzoff/azure-search-react-template)