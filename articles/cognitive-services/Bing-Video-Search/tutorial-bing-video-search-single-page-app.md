---
title: Aplikace Bing jednostránkové Video hledání | Microsoft Docs
description: Vysvětluje, jak používat rozhraní API služby Bing Video Search v jednostránkovou webovou aplikaci.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 11/01/2017
ms.author: v-gedod
ms.openlocfilehash: 55f662721e007e03c8f43f19d8b905e755cfe1d8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343443"
---
# <a name="tutorial-single-page-video-search-app"></a>Kurz: Video vyhledávání jednostránkové aplikace
Rozhraní API služby Bing Video vyhledávání umožňuje hledat na webu a získat výsledky video relevantní pro vyhledávací dotaz. V tomto kurzu jsme sestavení jednostránkové webové aplikace, která používá rozhraní API služby Bing hledání zobrazit výsledky vyhledávání na stránce. Aplikace obsahuje součásti HTML, CSS a JavaScript.

<!-- Remove until it can be replaced with a sanitized version.
![Single-page Bing Video Search app](./media/video-search-singlepage.png)
-->

> [!NOTE]
> V dolní části stránky při kliknutí na záhlaví JSON a HTTP zobrazit odpověď JSON a informace o požadavku HTTP. Tyto podrobnosti může být užitečné při prozkoumávání služby.

![JSON, nezpracovaná výsledky HTTP](./media/json-http-raw-results.png)

Tuto aplikaci kurz ukazuje, jak:
> [!div class="checklist"]
> * Provádění volání rozhraní API služby Bing Video Search v jazyce JavaScript
> * Předat možnosti hledání do rozhraní API služby Bing Search
> * Zobrazit výsledky vyhledávání video nebo volitelně zahrnovat webové stránky, zprávy nebo bitové kopie
> * Hledání časových intervalů 24 hodin, uplynulý týden, měsíc nebo všech dostupných času
> * Stránka prostřednictvím výsledky hledání
> * Popisovač Bing ID a rozhraní API předplatné klíč klienta
> * Zpracování chyb, které můžou nastat

Kurz stránka je zcela samostatné; nepoužívá se žádné externí rozhraní, šablony stylů nebo soubory bitové kopie. Používá jenom široce podporované funkce jazyka JavaScript a pracuje s aktuálními verzemi všechny hlavní prohlížeče.

V tomto kurzu probereme vybraných částí zdrojového kódu. Kompletní [zdrojový kód](tutorial-bing-video-search-single-page-app-source.md) je k dispozici. Spuštění příkladu, zkopírujte a vložte do textového editoru, zdrojový kód a uložte ho jako `bing.html`.

## <a name="app-components"></a>Součásti aplikace
Stejně jako jakoukoli jednostránkovou webovou aplikaci tento kurz aplikace obsahuje tři části:

> [!div class="checklist"]
> * HTML – definuje struktuře a obsahu stránky
> * Šablon stylů CSS – definuje vzhled stránky
> * JavaScript – definuje chování stránky

Většina HTML a CSS je běžné, takže tento kurz se nezabývá ho. HTML obsahující formulář vyhledávání, ve kterém uživatel zadá dotaz a vybere možností hledání. Formulář je připojený k JavaScript, která provádí vyhledávání pomocí `onsubmit` atribut `<form>` značky:

```html
<form name="bing" onsubmit="return bingWebSearch(this)">
```
`onsubmit` Obslužná rutina vrátí `false`, který udržuje formuláře z odeslání na server. Kód jazyka JavaScript funguje shromažďování nezbytné informace z formuláře a provádění hledání.

HTML také obsahuje divizí (HTML `<div>` značky) kde se zobrazí výsledky hledání.

## <a name="managing-subscription-key"></a>Správa klíč předplatného

Abyste se vyhnuli nutnosti obsahovat klíč rozhraní API služby Bing Search předplatného v kódu, používáme k uložení klíče trvalého úložiště v prohlížeči. Předtím, než je klíč uložen, jsme výzvu pro klíče uživatele. Pokud později klíč rozhraní API zamítnul, jsme zneplatnit uložené klíč, uživateli se znovu zobrazí výzva.

Jsme definovali `storeValue` a `retrieveValue` funkce, které používají buď `localStorage` objektu (některé prohlížeče nepodporují ji) nebo soubor cookie. `getSubscriptionKey()` Funkce používá tyto funkce pro ukládání a načítání klíče uživatele.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// ... omitted definitions of store value and retrieve value
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or prompt if it's not found.
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
HTML `<form>` značka `onsubmit` volání `bingWebSearch` funkce vracet výsledky vyhledávání. `bingWebSearch` používá `getSubscriptionKey()` k ověření každý dotaz. Jak je znázorněno v definici předchozí `getSubscriptionKey` vyzve uživatele k klíč, pokud klíč nebyl zadán. Klíč je pak uloženy pokračovat v používání aplikace.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Výběrem možnosti hledání
Následující obrázek znázorňuje dotazu textového pole a možnosti, které definují vyhledávání.

![Možnosti hledání zprávy Bing](media/video-search-options.png)

Formuláře HTML obsahuje prvky s těmito názvy:

|Element|Popis|
|-|-|
| `where` | Rozevírací nabídka pro výběr na trhu (umístění a jazyk) používá pro vyhledávání. |
| `query` | Textové pole k zadání podmínek vyhledávání. |
| `modules` | Zaškrtávací políčka pro konkrétní moduly výsledků, všechny související videa nebo výsledky povýšení. |
| `when` | Rozevírací nabídky můžete omezit výsledky vyhledávání na poslední den, týden nebo měsíc. |
| `safe` | Zaškrtávací políčko označující, zda použít funkci bezpečné Bing hledání filtrovat výsledky "pro dospělé". |
| `count` | Skryté pole. Počet výsledků hledání se vrátíte na každý požadavek. Změna zobrazíte výsledky méně či více na stránce. |
| `offset`|  Skryté pole. Posun první výsledek hledání v požadavku; použít pro stránkování. Se resetují na `0` na novou žádost. |

> [!NOTE]
> Hledání webové služby Bing nabízí další parametry dotazu. Používáme pouze několik z nich.

``` javascript
// build query options from the HTML form
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));

    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("modules=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

Například `SafeSearch` parametr skutečné volání rozhraní API může být `strict`, `moderate`, nebo `off`, s `moderate` se výchozí hodnota. Naše formulář, ale používá zaškrtávací políčko, který má jenom dva stavy. Kód jazyka JavaScript převede toto nastavení buď `strict` nebo `off` (`moderate` nepoužívá).

## <a name="performing-the-request"></a>Provádění požadavku
Zadaný dotaz, string možnosti a klíč rozhraní API `BingWebSearch` využívá `XMLHttpRequest` objekt, který má být odeslán požadavek na koncový bod služby Bing vyhledávání.

```javascript
// Search on the query, using search options, authenticated by the key.
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_headers", "paging1", "paging2", "error");

    var endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";
    var request = new XMLHttpRequest();
    var queryurl = endpoint + "?q=" + encodeURIComponent(query) + "&" + options;

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
    request.addEventListener("load", handleOnLoad);

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
Při úspěšném dokončení požadavku HTTP volání JavaScriptu `load` obslužné rutiny události, `handleOnLoad()`, aby zpracoval úspěšné žádosti HTTP GET do rozhraní API. 

```javascript
// handle Bing search request results
function handleOnLoad() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and headers
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Videos") {//"SearchResponse" && "rankingResponse" in jsobj) {
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
> Pokud dojde k chybě v operaci vyhledávání, rozhraní API služby Bing zprávy Search vrátí stavový kód 200 HTTP a obsahuje informace o chybě v odpovědi JSON. Kromě toho pokud se požadavek míra limited, rozhraní API vrátí prázdnou odpověď.
V případě úspěšné žádosti HTTP nemá *není* nutně znamenají, že celé hledání úspěšné. 

Většinu kódu v obou těchto funkcí jsou vyhrazené pro zpracování chyb. Může dojít k chybám v těchto fází:

|Krok|Potenciální chyby|Zpracovává|
|-|-|-|
|Vytvoření žádosti o objekt jazyka JavaScript|Neplatná adresa URL|`try`/`catch` blok|
|Vytvoření požadavku|Chyby sítě, přerušené připojení|`error` a `abort` obslužné rutiny událostí|
|Provádění hledání|Neplatný požadavek, neplatný formát JSON, omezení přenosové rychlosti|testů v `load` obslužné rutiny události|

Řeší chyby volání `renderErrorMessage()` s nějaké podrobnosti o této chybě známé. Pokud odpověď úspěšně projde úplné gauntlet chyba testů, říkáme `renderSearchResults()` zobrazit výsledky vyhledávání na stránce.

## <a name="displaying-search-results"></a>Zobrazení výsledků vyhledávání
Hlavní funkce pro zobrazení výsledků vyhledávání je `renderSearchResults()`. Tato funkce přebírá JSON vrácený službu vyhledávání zprávy Bing a vykreslí výsledky zprávy a související hledání, pokud existuje.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the resuts to the mainline section
    for (section in { mainline: 0 }) {
         showDiv(section, renderResultsItems(section, results));
    }
}
```
Výsledky vyhledávání se vrátí jako nejvyšší úrovně `value` objektu v odpovědi JSON. Jsme předat do našich funkce `renderResultsItems()`, který iteruje je a volá funkci k vykreslení každou položku do kódu HTML. Výsledný HTML se vrátí do `renderSearchResults()`, kde je vložen do `results` dělení na stránce.

```javascript
// render search results
    function renderResultsItems(section, results) {   

        var items = results.value;
        var html = [];
        for (var i = 0; i < items.length; i++) { 
            var item = items[i];
            // collection name has lowercase first letter
            var type = "videos";
            var render = searchItemRenderers[type];
            html.push(render(item, section));  
        }
    return html.join("\n\n");
}
```

Rozhraní API služby Bing zprávy Search vrátí až čtyři různé druhy související výsledky, každý svůj vlastní objekt nejvyšší úrovně. Jsou to tyto:

|Vztah|Popis|
|-|-|
|`pivotSuggestions`|Dotazy, které nahraďte jiný pivot slova v původní vyhledávání. Například při hledání "red květy", může být pivot slovo "red" a pivot návrhu může být "žlutý květy."|
|`queryExpansions`|Dotazy, které původní vyhledávání upřesnit tak, že přidáte další podmínky. Pokud hledáte "Microsoft Surface", rozšíření dotazu může být například "Microsoft Surface profesionál."|
|`relatedSearches`|Dotazy, které byly zadány také uživatelé, kteří zadali původní vyhledávání. Při hledání "Rainier připojení", související vyhledávání může být například "strojový překladů. Svatý Helens."|
|`similarTerms`|Dotazy, které se podobají význam původní vyhledávání. Například pokud hledáte "škol", podobně jako termín, který může být "education."|

Jako dříve zobrazená v `renderSearchResults()`, jsme vykreslení pouze `relatedItems` návrhy a umístěte výsledná odkazy bočním panelu stránky.

## <a name="rendering-result-items"></a>Vykreslování položky výsledků

V JavaScript code objekt, `searchItemRenderers`, můžete obsahuje *nástroji pro vykreslování:* funkce, které generují kód HTML pro každý typ vyhledávání výsledek. Video vyhledávání vyberte pouze používá `videos`. Najdete v dalších kurzech pro různé typy nástroji pro vykreslování.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    videos: function (item, section, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
Funkce zobrazovací jednotky může přijmout následující parametry:

|Parametr|Popis|
|-|-|
|`item`| JavaScript objekt obsahující vlastnosti položky, například jeho adresa URL a její popis.|
|`index`| Index položky výsledek v rámci jeho kolekce.|
|`count`| Počet položek v kolekci položku výsledek hledání.|

`index` a `count` parametry můžete použít k počet výsledků, generovat speciální HTML pro začátku nebo na konec kolekce, chcete-li vložit konce řádků po určitý počet položek a tak dále. Pokud vykreslovací modul není nutné tuto funkci, není nutné přijmout tyto dva parametry.

`video` Zobrazovací jednotky se zobrazí v následující výňatek javascript. Používá koncový bod videa, jsou všechny výsledky typu `Videos`. `searchItemRenderers` Jsou uvedené v následující segment kódu.

```javascript
// render functions for various types of search results
    searchItemRenderers = {

    videos: function (item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];

        html.push("<p class='images'>");
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escapeQuotes(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='" + item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Video page source</a> - ");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }
}
```

Vykreslovací funkce:
> [!div class="checklist"]
> * Vytvoří značku odstavce a přiřadí ji k `images` třídy a doručí do pole html.
> * Vypočítá velikost miniatur bitové kopie (šířka vyřešen v 60 pixelů, výška vypočítat úměrně).
> * Sestavení HTML `<img>` značky zobrazíte miniaturu obrázku. 
> * Sestavení HTML `<a>` značky, které odkazují na bitovou kopii a stránky, která ji obsahuje.
> * Popis, který zobrazí informace o bitové kopie a na server, který se nachází na sestavení.

Velikost miniatur se používá v obou `<img>` značky a `h` a `w` pole v adrese URL na miniaturu. [Miniatur služby Bing](resize-and-crop-thumbnails.md) pak doručí na miniaturu přesně této velikosti.

## <a name="persisting-client-id"></a>Zachování ID klienta
Může zahrnovat odpovědí z hledání Bing rozhraní API `X-MSEdge-ClientID` záhlaví, který by měly být odeslány zpět do rozhraní API s následující požadavky. Pokud se používají rozhraní API pro vyhledávání více Bing, musí být stejné ID klienta použit s všechny z nich, pokud je to možné.

Poskytuje `X-MSEdge-ClientID` záhlaví umožňuje rozhraní API Bingu pro všechny uživatele vyhledávání, která má dvě důležité výhody přidružení.

Nejprve umožňuje Bing vyhledávacího webu použít po kontextu hledání nalézt výsledky, které lépe odpovídají uživatele. Pokud uživatel má dříve hledali podmínky týkající se řízení, například novější vyhledejte "uzlů" může přednostně vrácení informací o uzlů použít v řízení.

Druhý Bing může náhodně vyberte uživatele, můžete vyzkoušet nové funkce dřív, než budou k dispozici. Poskytovat stejné ID klienta každý požadavek zajistí, že uživatelé, kteří vždy najdete v části funkce uvidí ho. Bez ID klienta může uživatel zobrazit funkci Zobrazit a zmizí, zdánlivě v náhodných v příslušných výsledcích hledání.

Zásady zabezpečení prohlížeče (CORS) může zabránit `X-MSEdge-ClientID` záhlaví dostupný pro JavaScript. Toto omezení nastane při hledání odpovědi má jiný počátek ze stránky, která je požadována. V produkčním prostředí je potřeba vyřešit tuto zásadu hostováním skript na straně serveru, který provede volání rozhraní API ve stejné doméně jako webovou stránku. Vzhledem k tomu, že skript má stejný původ jako webovou stránku, `X-MSEdge-ClientID` záhlaví je pak možné JavaScript.

> [!NOTE]
> V produkční webové aplikace měli byste provést žádost straně serveru. Klíč rozhraní API služby Bing Search, jinak hodnota musí být součástí webové stránky, kde je k dispozici všem uživatelům zobrazení zdroje. Fakturuje se pro všechny využití v rámci předplatného klíč rozhraní API, i požadavkům neoprávněným stranami, proto je důležité, abyste vystavit váš klíč.

Pro účely vývoje můžete provést žádost Bing webového vyhledávání rozhraní API prostřednictvím proxy serveru CORS. Odpověď proxy serveru má `Access-Control-Expose-Headers` záhlaví této hlavičky odpovědi povolených programů a jejich zpřístupní JavaScript.

Je snadné se má nainstalovat proxy CORS umožňující našem kurzu aplikaci pro přístup klienta do záhlaví ID. První, pokud ještě nemáte, [instalace softwaru Node.js](https://nodejs.org/en/download/). Potom vydejte následující příkaz v příkazovém okně:

    npm install -g cors-proxy-server

V dalším kroku změňte hledání webové služby Bing koncový bod v souboru HTML na:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Nakonec spusťte CORS proxy pomocí následujícího příkazu:

    cors-proxy-server

Nechte Otevřete příkazové okno při používání kurz aplikace; zavřením okna zastaví proxy serveru. V rozšíření hlavičky protokolu HTTP části níže výsledky hledání, se nyní zobrazí `X-MSEdge-ClientID` hlavičky (mimo jiné) a ověřte, zda je stejný pro každý požadavek.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Referenční dokumentace rozhraní API služby Bing Video Search](//docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference)