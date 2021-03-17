---
title: 'Kurz: Vytvoření webové aplikace s jednou stránkou pomocí rozhraní API Bingu pro vyhledávání zpráv'
titleSuffix: Azure Cognitive Services
description: Tento kurz slouží k vytvoření jednostránkové webové aplikace, která může odesílat vyhledávací dotazy do rozhraní API služby Bing News a zobrazit výsledky v rámci webové stránky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js
ms.openlocfilehash: cb9e68efd27deb3bf66d3c286c0cd7a128d8bf59
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430577"
---
# <a name="tutorial-create-a-single-page-web-app"></a>Kurz: Vytvoření webové aplikace s jednou stránkou

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Rozhraní API Bingu pro vyhledávání zpráv umožňuje hledat na webu a získávat výsledky v podobě zpráv relevantních pro vyhledávací dotaz. V tomto kurzu sestavíme jednostránkovou webovou aplikaci, která používá rozhraní API Bingu pro vyhledávání zpráv k zobrazení výsledků hledání na stránce. Aplikace zahrnuje komponenty HTML, CSS a JavaScriptu. Zdrojový kód pro tuto ukázku je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingNewsSearchApp.html).

<!-- Remove until we can replace it with sanitized copy
![Single-page Bing News Search app](media/news-search-singlepage.png)
-->

> [!NOTE]
> Hlavičky JSON a HTTP v dolní části stránky při kliknutí zobrazí informace odpovědi JSON a požadavku HTTP. Tyto podrobnosti můžou být užitečné při prozkoumávání služby.

Ukázková aplikace předvádí, jak:
> [!div class="checklist"]
> * Provést volání rozhraní API Bingu pro vyhledávání zpráv v JavaScriptu
> * Předat možnosti hledání do rozhraní API Bingu pro vyhledávání zpráv
> * Zobrazit výsledky hledání zpráv ze čtyř kategorií (libovolný typ, obchod, zdraví nebo politika) a různých časových intervalů (posledních 24 hodin, minulý týden, měsíc nebo všechny dostupné bez ohledu na čas)
> * Procházet stránky výsledků hledání
> * Používat ID klienta Bingu a klíč předplatného rozhraní API
> * Zpracovat chyby, které můžou nastat

Stránka kurzu je zcela nezávislá. Nepoužívá žádná externí rozhraní, šablony stylů ani soubory obrázků. Používá jenom běžně podporované funkce jazyka JavaScript a funguje s aktuálními verzemi všech hlavních webových prohlížečů.


## <a name="prerequisites"></a>Požadavky

Abyste mohli postupovat podle tohoto kurzu, potřebujete klíče předplatného pro rozhraní Vyhledávání Bingu API. Pokud je nemáte, budete je muset vytvořit:

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" vytvořte prostředek vyhledávání Bingu vytvoření prostředku "  target="_blank"> Vyhledávání Bingu </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.

## <a name="app-components"></a>Komponenty aplikace
Stejně jako každá jednostránková webová aplikace i tato aplikace zahrnuje tři části:

> [!div class="checklist"]
> * HTML – definuje strukturu a obsah stránky
> * Šablony stylů CSS – definují vzhled stránky
> * JavaScript – definuje chování stránky

Většina částí HTML a šablon stylů CSS je konvenční, proto se jimi tento kurz nezabývá. Kód HTML obsahuje vyhledávací formulář, do kterého uživatel zadá dotaz a vybere možnosti hledání. Formulář je spojený s JavaScriptem, který provádí vlastní vyhledávání s využitím atributu `onsubmit` značky `<form>`:

```html
<form name="bing" onsubmit="return newBingNewsSearch(this)">
```
Obslužná rutina `onsubmit` vrátí `false`. Díky tomu se formulář neodesílá na server. Kód JavaScriptu shromažďuje nezbytné informace z formuláře a provádí hledání.

Kód HTML také obsahuje úseky (značky HTML `<div>`), kde se zobrazují výsledky hledání.

## <a name="managing-subscription-key"></a>Správa klíče předplatného

Aby se nemusel klíč předplatného rozhraní API pro vyhledávání Bingu zahrnout do kódu, používáme k uložení klíče trvalé úložiště prohlížeče. Před uložením klíče vyzveme k zadání uživatelova klíče. Pokud rozhraní API klíč později odmítne, uložený klíč zneplatníme, takže uživateli se znovu zobrazí výzva.

Definujeme funkce `storeValue` a `retrieveValue`, které používají buď objekt `localStorage` (který nepodporují všechny prohlížeče) nebo soubor cookie. Funkce `getSubscriptionKey()` tyto funkce používá k ukládání a načítání uživatelova klíče. Můžete použít globální koncový bod nebo vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// Bing Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/news";

// ... omitted definitions of storeValue() and retrieveValue()
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or 
// prompt if it's not found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```
Značka HTML `<form>``onsubmit` volá funkci `bingWebSearch` k vrácení výsledků hledání. `bingWebSearch` používá `getSubscriptionKey()` k ověření každého dotazu. Jak je vidět v předchozí definici, `getSubscriptionKey` vyzve uživatele k zadání klíče, pokud klíč nebyl zadán. Klíč se pak uloží pro další používání ze strany aplikace.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Výběr možností hledání
Následující obrázek znázorňuje textové pole dotazu a možnosti, které definují vyhledávání zpráv ohledně financování škol.

![Možnosti vyhledávání zpráv Bingu](media/news-search-categories.png)

Formulář HTML obsahuje prvky s těmito názvy:

|Element|Popis|
|-|-|
| `where` | Rozevírací nabídka pro výběr trhu (polohy a jazyka) pro vyhledávání. |
| `query` | Textového pole k zadání hledaných termínů. |
| `category` | Zaškrtávací políčka pro podporu určitých typů výsledků. Podpora typu Health (zdraví) například zvyšuje hodnocení zpráv o zdraví. |
| `when` | Rozevírací nabídka pro volitelné omezení vyhledávání na poslední den, týden nebo měsíc. |
| `safe` | Zaškrtávací políčko označující, jestli se má používat funkce Bingu Bezpečné hledání k filtrování výsledků „pro dospělé“. |
| `count` | Skryté pole. Počet výsledků hledání, který se má vrátit u jednotlivých žádostí. Můžete změnit, aby se na stránce zobrazovalo méně nebo více výsledků. |
| `offset`|  Skryté pole. Posun prvního výsledku hledání v požadavku, sloužící ke stránkování. Při novém požadavku se resetuje na `0`. |

> [!NOTE]
> Vyhledávání na webu Bingu nabízí další parametry dotazu. Používáme jenom několik z nich.

``` javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);

    for (var i = 0; i < form.category.length; i++) {
        if (form.category[i].checked) {
            category = form.category[i].value;
            break;
        }
    }
    if (category.valueOf() != "all".valueOf()) { 
        options.push("category=" + category); 
        }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

Například parametr `SafeSearch` ve skutečném volání rozhraní API může být `strict`, `moderate` nebo `off` a výchozí hodnota je `moderate`. Náš formulář ale používá zaškrtávací políčko, které má jenom dva stavy. Kód JavaScriptu toto nastavení převede na `strict` nebo `off` (`moderate` se nepoužívá).

## <a name="performing-the-request"></a>Provedení požadavku
Na základě dotazu, řetězce možností a klíče rozhraní API funkce `BingNewsSearch` použije objekt `XMLHttpRequest` k provedení požadavku na koncový bod vyhledávání zpráv Bingu.

```javascript
// perform a search given query, options string, and API key
function bingNewsSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
     if (category.valueOf() != "all".valueOf()) {
        var queryurl = BING_ENDPOINT + "/search?" + "?q=" + encodeURIComponent(query) + "&" + options;
    }
    else
    {
        if (query){
        var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;
        }
        else {
            var queryurl = BING_ENDPOINT + "?" + options;
        }
    }

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // event handler for successful response
    request.addEventListener("load", handleBingResponse);

    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```
Při úspěšném dokončení požadavku HTTP volá JavaScript obslužnou rutinu události `load`, funkci `handleBingResponse()`, ke zpracování úspěšného požadavku HTTP GET na rozhraní API. 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "News") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Úspěšný požadavek HTTP *nemusí* nutně znamenat, že bylo úspěšné samotné vyhledávání. Pokud v operaci vyhledávání dojde k chybě, rozhraní API Bingu pro vyhledávání zpráv vrátí stavový kód HTTP jiný než 200 zahrnující informace o chybě v odpovědi JSON. Kromě toho, pokud byl požadavek omezený rychlostí, vrátí rozhraní API prázdnou odpověď.

Velká část kódu v obou předchozích funkcích je vyhrazená zpracování chyb. V následujících fázích můžou nastat chyby:

|Fáze|Potenciální chyby|Čím se zpracují|
|-|-|-|
|Vytváření javascriptového objektu požadavku|Neplatná adresa URL|Blok `try`/`catch`|
|Provedení žádosti|Chyby sítě, přerušená připojení|Obslužné rutiny událostí `error` a `abort`|
|Provedení vyhledávání|Neplatný požadavek, neplatný JSON, omezení rychlosti|Testy v obslužné rutině události `load`|

Chyby se zpracovávají voláním `renderErrorMessage()` se všemi známými podrobnostmi o chybě. Pokud odpověď úspěšné projde kompletní řadou testů chyb, voláme `renderSearchResults()` k zobrazení výsledků hledání na stránce.

## <a name="displaying-search-results"></a>Zobrazení výsledků hledání
Hlavní funkcí pro zobrazení výsledků hledání je `renderSearchResults()`. Tato funkce vezme JSON vrácené službou vyhledávání zpráv Bingu a vykreslí výsledky zpráv a souvisejících hledání, pokud existují.

```javascript
// render the search results given the parsed JSON response
    function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```
Hlavní výsledky hledání se v odpovědi JSON vrátí jako objekt `value` nejvyšší úrovně. Předáme je do naší funkce `renderResults()`, která skrz ně iteruje a volá samostatnou funkci k vykreslení každé položky do kódu HTML. Výsledný kód HTML se vrátí do `renderSearchResults()`, kde se vloží do úseku `results` na stránce.

```javascript
function renderResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.news(items[i], i, len));
    }
    return html.join("\n\n");
}
```
Rozhraní API Bingu pro vyhledávání zpráv vrátí až čtyři různé druhy souvisejících výsledků, každý ve vlastním objektu nejvyšší úrovně. Jsou to tyto:

|Relace|Popis|
|-|-|
|`pivotSuggestions`|Dotazy, které nahradí pivotové slovo v původním vyhledávání jiným. Pokud třeba vyhledáváte „červené květiny“, pivotové slovo může být „červené“ a pivotový návrh může být „žluté květiny“.|
|`queryExpansions`|Dotazy, které původní hledání zúží přidáním dalších výrazů. Pokud třeba vyhledáváte „Microsoft Surface“, rozšíření dotazu může být „Microsoft Surface Pro“.|
|`relatedSearches`|Dotazy, které také zadali ostatní uživatelé, kteří zadali původní vyhledávání. Pokud třeba vyhledáváte „Mount Rainier“, související hledání může být „Mt. Saint Helens“.|
|`similarTerms`|Dotazy, které mají podobný význam jako původní vyhledávání. Pokud třeba vyhledáváte „školy“, podobný výraz může být „vzdělávání“.|

Jak jste už viděli v `renderSearchResults()`, vykreslujeme jenom návrhy `relatedItems` a výsledné odkazy umisťujeme na boční panel stránky.

## <a name="rendering-result-items"></a>Vykreslování položek výsledků

V kódu JavaScriptu objekt `searchItemRenderers` obsahuje funkce *renderers:*, které generují kód HTML pro každý druh výsledku hledání.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
Funkce rendereru může přijímat tyto parametry:

|Parametr|Popis|
|-|-|
|`item`| Objekt JavaScriptu obsahující vlastnosti položky, jako je její adresa URL a popis.|
|`index`| Index položky výsledků v rámci jeho kolekce.|
|`count`| Počet položek v kolekci položek výsledků hledání.|

Parametry `index` a `count` se můžou použít k číslování výsledků, generování zvláštního kódu HTML pro začátek nebo konec kolekce, vložení konců řádků za určitý počet položek a tak dále. Pokud renderer tuto funkci nepotřebuje, nepotřebuje tyto dva parametry přijímat.

`news`Vykreslovací modul je zobrazen v následujícím výpisu JavaScriptu:
```javascript
    // render news story
    news: function (item) {
        var html = [];
        html.push("<p class='news'>");
        if (item.image) {
            width = 60;
            height = Math.round(width * item.image.thumbnail.height / item.image.thumbnail.width);
            html.push("<img src='" + item.image.thumbnail.contentUrl +
                "&h=" + height + "&w=" + width + "' width=" + width + " height=" + height + ">");
        }
        html.push("<a href='" + item.url + "'>" + item.name + "</a>");
        if (item.category) html.push(" - " + item.category);
        if (item.contractualRules) {    // MUST display source attributions
            html.push(" (");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++)
                rules.push(item.contractualRules[i].text);
                html.push(rules.join(", "));
                html.push(")");
            }
        html.push(" (" + getHost(item.url) + ")");
        html.push("<br>" + item.description);
        return html.join("");
    },
```
Funkce rendereru zpráv:
> [!div class="checklist"]
> * Vytvoří značku odstavce, přiřadí ji ke třídě `news` a předá ji do pole html.
> * Vypočítá velikost miniatury obrázku (šířka je pevně nastavená na 60 pixelů, výška se vypočítá poměrně).
> * Vytvoří značku HTML `<img>` k zobrazení miniatury obrázku. 
> * Vytvoří značky HTML `<a>`, které odkazují na obrázek a na stránku, která ho obsahuje.
> * Vytvoří popis, který zobrazuje informace o obrázku a webu, na kterém se nachází.

Velikost miniatury se používá ve značce `<img>` i v polích `h` a `w` v adrese URL miniatury. [Služba miniatur Bingu](../bing-web-search/resize-and-crop-thumbnails.md) pak poskytne miniaturu přesně této velikosti.

## <a name="persisting-client-id"></a>Zachování ID klienta
Odpovědi z rozhraní API pro vyhledávání Bingu můžou zahrnovat hlavičku `X-MSEdge-ClientID`, která by se měla odesílat zpět do rozhraní API v následných požadavcích. Pokud se používá více rozhraní API pro vyhledávání Bingu, mělo by se pro všechny používat stejné ID klienta, pokud je to možné.

Poskytnutí hlavičky `X-MSEdge-ClientID` umožňuje rozhraním API Bingu spojit si všechna uživatelova vyhledávání. To má dvě důležité výhody.

Zaprvé to umožňuje, aby vyhledávací web Bing na vyhledávání použil minulý kontext a našel výsledky, které uživatele více uspokojí. Pokud uživatel v minulosti vyhledával třeba výrazy týkající se lodí, může pozdější vyhledání „uzlů“ přednostně vrátit informace o uzlech používaných při plavbě lodí.

Za druhé může Bing náhodně vybírat uživatele k vyzkoušení nových funkcí, než budou všeobecně dostupné. Poskytnutí stejného ID klienta s každým požadavkem zajistí, že uživatelé, kteří tuto funkci vidí, ji vidí vždy. Bez ID klienta může uživatel funkci ve svých výsledcích hledání zdánlivě náhodně někdy vidět a jindy ne.

Zásady zabezpečení prohlížeče (CORS) můžou bránit tomu, aby byla hlavička `X-MSEdge-ClientID` pro JavaScript dostupná. K tomuto omezení dochází, když odpověď na vyhledávání má jiný zdroj než stránka, která o ni požádala. V produkčním prostředí je potřeba tyto zásady vyřešit hostováním skriptu na straně serveru, který provádí volání rozhraní API ve stejné doméně jako webová stránka. Protože tento skript má stejný původ jako webová stránka, hlavička `X-MSEdge-ClientID` je pak pro JavaScript dostupná.

> [!NOTE]
> V produkční webové aplikaci byste měli požadavek provádět na straně serveru. Jinak musí být klíč rozhraní API pro vyhledávání Bingu součástí webové stránky, kde je k dispozici každému, kdo si zobrazí zdroj. Účtuje se vám veškeré využívání vašeho klíče předplatného rozhraní API, dokonce i požadavky provedené neoprávněnými stranami, proto je důležité klíč nezveřejňovat.

Pro účely vývoje můžete požadavek na rozhraní API Bingu pro vyhledávání na webu provést prostřednictvím proxy serveru CORS. Odpověď z takového serveru proxy má `Access-Control-Expose-Headers` záhlaví, které umožňuje použití hlaviček odpovědí a zpřístupňuje je pro JavaScript.

Nainstalovat proxy server CORS a povolit naší ukázkové aplikaci přístup k hlavičce ID klienta je snadné. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Pak zadejte v příkazovém okně tento příkaz:

```console
npm install -g cors-proxy-server
```

Dále změňte koncový bod Vyhledávání na webu Bingu v souboru HTML na: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

```console
cors-proxy-server
```

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V rozbalitelné sekci hlaviček HTTP pod výsledky hledání teď uvidíte hlavičku `X-MSEdge-ClientID` (mimo jiné) a můžete zkontrolovat, jestli je stejná pro každý požadavek.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Referenční informace k rozhraní API Bingu pro vyhledávání zpráv](//docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference)