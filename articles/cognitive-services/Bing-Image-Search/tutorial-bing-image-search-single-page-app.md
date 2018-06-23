---
title: Hledání bitové kopie Bingu jednostránkovou webovou aplikaci | Microsoft Docs
description: Ukazuje, jak používat rozhraní API služby Bing Image Search v jednostránkovou webovou aplikaci.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: d0e1dc24513c8fc3a405cf1c18f531a0c58fad13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343437"
---
# <a name="tutorial-single-page-web-app"></a>Kurz: Jednostránkovou webovou aplikaci

Rozhraní API služby Bing Image Search umožňuje hledat na webu a získat výsledky bitové kopie, které jsou relevantní pro vyhledávací dotaz. V tomto kurzu jsme jednostránkovou webovou aplikaci, která používá rozhraní API vyhledávání bitové kopie Bingu pro zobrazení výsledků hledání sestavení přímo na stránce. Aplikace obsahuje součásti HTML, CSS a JavaScript.

<!-- Remove until we can sanitize images
![[Single-page Bing Image Search app]](media/cognitive-services-bing-images-api/image-search-spa-demo.png)
-->

> [!NOTE]
> Záhlaví JSON a HTTP v dolní části stránky odhalit odpověď JSON a informace o požadavku HTTP při kliknutí na. Tyto podrobnosti jsou užitečné při prozkoumávání služby.

Aplikace kurz ukazuje, jak:

> [!div class="checklist"]
> * Provádění volání rozhraní API Search bitové kopie Bingu v jazyce JavaScript
> * Předat možnosti hledání do rozhraní API vyhledávání bitové kopie Bingu
> * Zobrazení výsledků
> * Stránka prostřednictvím výsledky hledání
> * Popisovač Bing ID a rozhraní API předplatné klíč klienta
> * Zpracování chyb, které můžou nastat

Kurz stránka je zcela samostatné; nepoužívá se žádné externí rozhraní, šablony stylů nebo i soubory bitové kopie. Používá jenom široce podporované funkce jazyka JavaScript a pracuje s aktuálními verzemi všechny hlavní prohlížeče.

V tomto kurzu probereme pouze vybrané části zdrojového kódu. Úplný zdrojový kód je k dispozici [na samostatné stránce](tutorial-bing-image-search-single-page-app-source.md). Zkopírujte a vložte tento kód do textového editoru a uložte ho jako `bing.html`.

> [!NOTE]
> V tomto kurzu je podstatně podobná [jednostránkové vyhledávání Bing webové aplikace kurzu](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), ale zabývá pouze výsledky hledání bitové kopie.

## <a name="app-components"></a>Součásti aplikace

Stejně jako jakoukoli jednostránkovou webovou aplikaci kurz aplikace obsahuje tři části:

> [!div class="checklist"]
> * HTML – definuje struktuře a obsahu stránky
> * Šablon stylů CSS – definuje vzhled stránky
> * JavaScript – definuje chování stránky

V tomto kurzu nezahrnuje většinu kódu HTML nebo šablon stylů CSS podrobně, protože se jedná o přehledné.

HTML obsahující formulář vyhledávání, ve kterém uživatel zadá dotaz a vybere možností hledání. Formulář je připojený k JavaScript, která ve skutečnosti provádí vyhledávání podle `<form>` tagu `onsubmit` atribut:

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

`onsubmit` Obslužná rutina vrátí `false`, který udržuje formuláře z odeslání na server. Kód jazyka JavaScript skutečně funguje shromažďování nezbytné informace z formuláře a provádění hledání.

HTML také obsahuje divizí (HTML `<div>` značky) kde se zobrazí výsledky hledání.

## <a name="managing-subscription-key"></a>Správa klíč předplatného

Abyste se vyhnuli nutnosti obsahovat klíč rozhraní API služby Bing Search předplatného v kódu, používáme k uložení klíče trvalého úložiště v prohlížeči. Pokud žádný klíč je uložený, jsme vyzvat, aby klíče uživatele a uloží jej pro pozdější použití. Pokud později klíč rozhraní API zamítnul, jsme zneplatnit uložené klíč tak, aby uživatel znovu.

Jsme definovali `storeValue` a `retrieveValue` funkce, které používají buď `localStorage` objektu (Pokud je prohlížeč podporuje ji) nebo soubor cookie. Naše `getSubscriptionKey()` funkce používá tyto funkce pro ukládání a načítání klíče uživatele.

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
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

HTML `<form>` značka `onsubmit` volání `bingWebSearch` funkce vracet výsledky vyhledávání. `bingWebSearch` používá `getSubscriptionKey` k ověření každý dotaz. Jak je znázorněno v definici předchozí `getSubscriptionKey` vyzve uživatele k klíč, pokud klíč nebyl zadán. Klíč je pak uloženy pokračovat v používání aplikace.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>Výběrem možnosti hledání

![[Hledání bitové kopie Bingu formuláře]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

Formuláře HTML obsahuje následující prvky:

| | |
|-|-|
|`where`|Rozevírací nabídka pro výběr na trhu (umístění a jazyk) používá pro vyhledávání.|
|`query`|Textové pole, do kterého chcete zadejte hledaný text.|
|`aspect`|Přepínač tlačítka pro výběr proporce nalezen bitové kopie: zhruba Čtvereček celý nebo instalovat.|
|`color`|Vybere nebo černobílý nebo na převládá barvu.
|`when`|Rozevírací nabídky můžete omezit výsledky vyhledávání na poslední den, týden nebo měsíc.|
|`safe`|Zaškrtávací políčko označující, zda na Bing bezpečné hledání funkce slouží k filtrování "pro dospělé" výsledky.|
|`count`|Skryté pole. Počet výsledků hledání se vrátíte na každý požadavek. Změna zobrazíte výsledky méně či více na stránce.|
|`offset`|Skryté pole. Posun první výsledek hledání v požadavku; použít pro stránkování. Se resetují na `0` na novou žádost.|
|`nextoffset`|Skryté pole. Po přijetí výsledek hledání, v tomto poli je nastavena na hodnotu `nextOffset` v odpovědi. Použití tohoto pole zabraňuje překrývající se výsledky na následných stránkách.|
|`stack`|Skryté pole. Seznam posunutí předchozích stránkách s výsledky hledání pro přechod zpět na předchozí stránky zakódovaná ve formátu JSON.|

> [!NOTE]
> Hledání bitové kopie Bingu nabízí mnoho další parametry dotazu. Tady používáme pouze několik z nich.

Naše JavaScript funkce `bingSearchOptions()` převede těchto polí s řetězcem dotazu částečné ve formátu vyžadují rozhraní API služby Bing Search.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

Například může být funkci bezpečné hledání `strict`, `moderate`, nebo `off`, s `moderate` se výchozí hodnota. Ale naše formulář používá zaškrtávací políčko, který má jenom dva stavy. Kód jazyka JavaScript převede toto nastavení buď `strict` nebo `off` (jsme nepoužívejte `moderate`).

## <a name="performing-the-request"></a>Provádění požadavku

Zadaný dotaz, string možnosti a klíč rozhraní API `BingImageSearch` využívá `XMLHttpRequest` objekt, který má být odeslán požadavek na koncový bod hledání bitové kopie Bingu.

```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

Po úspěšném požadavku HTTP, volání JavaScriptu naše `load` obslužné rutiny události, `handleBingResponse()` funkce pro zpracování úspěšné žádosti HTTP GET do rozhraní API. 

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
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
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
> V případě úspěšné žádosti HTTP nemá *není* nutně znamenají, že celé hledání úspěšné. Pokud dojde k chybě v operaci vyhledávání, rozhraní API služby Bing Image Search vrátí stavový kód 200 HTTP a obsahuje informace o chybě v odpovědi JSON. Kromě toho pokud se požadavek míra limited, rozhraní API vrátí prázdnou odpověď.

Většinu kódu v obou těchto funkcí jsou vyhrazené pro zpracování chyb. Může dojít k chybám v těchto fází:

|Krok|Potenciální chyby|Zpracovává|
|-|-|-|
|Objekt požadavku sestavení jazyka JavaScript|Neplatná adresa URL|`try`/`catch` blok|
|Vytvoření požadavku|Chyby sítě, přerušené připojení|`error` a `abort` obslužné rutiny událostí|
|Provádění hledání|Neplatný požadavek, neplatný formát JSON, omezení přenosové rychlosti|testů v `load` obslužné rutiny události|

Řeší chyby volání `renderErrorMessage()` s nějaké podrobnosti o této chybě známé. Pokud odpověď úspěšně projde úplné gauntlet chyba testů, říkáme `renderSearchResults()` zobrazit výsledky vyhledávání na stránce.

## <a name="displaying-search-results"></a>Zobrazení výsledků vyhledávání

Hlavní funkce pro zobrazení výsledků vyhledávání je `renderSearchResults()`. Tato funkce přebírá JSON vrácený službu vyhledávání bitové kopie Bingu a vykreslí bitové kopie a související hledání, pokud existuje.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

Výsledky hledání hlavní bitové kopie se vrátí jako nejvyšší úrovně `value` objektu v odpovědi JSON. Jsme předat do našich funkce `renderImageResults()`, který iteruje je a volá samostatnou funkci k vykreslení každou položku do kódu HTML. Výsledný HTML se vrátí do `renderSearchResults()`, kde je vložen do `results` dělení na stránce.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

Rozhraní API služby Bing Image Search vrátí až čtyři různé druhy související výsledky, každý svůj vlastní objekt nejvyšší úrovně. Jsou to tyto:

|||
|-|-|
|`pivotSuggestions`|Dotazy, které nahraďte jiný pivot slova v původní vyhledávání. Například při hledání "red květy", může být pivot slovo "red" a pivot návrhu může být "žlutý květy."|
|`queryExpansions`|Dotazy, které původní vyhledávání upřesnit tak, že přidáte další podmínky. Pokud hledáte "Microsoft Surface", rozšíření dotazu může být například "Microsoft Surface profesionál."|
|`relatedSearches`|Dotazy, které byly zadány také uživatelé, kteří zadali původní vyhledávání. Při hledání "Rainier připojení", související vyhledávání může být například "strojový překladů. Svatý Helens."|
|`similarTerms`|Dotazy, které se podobají význam původní vyhledávání. Při hledání "koťata", podobné termín může být například "cute."|

Jako dříve zobrazená v `renderSearchResults()`, jsme vykreslení pouze `relatedItems` návrhy a umístěte výsledná odkazy bočním panelu stránky.

## <a name="rendering-result-items"></a>Vykreslování položky výsledků

V našem JavaScript kód je objekt, `searchItemRenderers`, který obsahuje *nástroji pro vykreslování:* výsledek hledání funkce, které generují kód HTML pro každý typ.

```javascript
searchItemRenderers = { 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

Funkce zobrazovací jednotky může přijímat následující parametry:

| | |
|-|-|
|`item`|JavaScript objekt obsahující vlastnosti položky, například jeho adresa URL a její popis.|
|`index`|Index položky výsledek v rámci jeho kolekce.|
|`count`|Počet položek v kolekci položku výsledek hledání.|

`index` a `count` parametry můžete použít k počet výsledků, generovat speciální HTML pro začátku nebo na konec kolekce, chcete-li vložit konce řádků po určitý počet položek a tak dále. Pokud vykreslovací modul není nutné tuto funkci, není nutné přijmout tyto dva parametry.

Podívejme bližší pohled na `images` zobrazovací jednotky:

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

Naše funkce zobrazovací jednotky bitové kopie:

> [!div class="checklist"]
> * Vypočítá velikost miniatur obrázku (šířka se liší, s nejméně 120 pixelů, zatímco výška vyřešen v 90 pixelů).
> * Sestavení HTML `<img>` značky zobrazíte miniaturu obrázku. 
> * Sestavení HTML `<a>` značky, které odkazují na bitovou kopii a stránky, která ji obsahuje.
> * Popis, který zobrazí informace o bitové kopie a na server, který se nachází na sestavení.

Testujeme `index` proměnné, aby bylo možné vložit `<p>` značky před první výsledek bitové kopie. Jinak miniatur tupý zobrazení sebe navzájem a wrap podle potřeby v okně prohlížeče.

Velikost miniatur se používá v obou `<img>` značky a `h` a `w` pole v adrese URL na miniaturu. [Miniatur služby Bing](resize-and-crop-thumbnails.md) pak doručí na miniaturu přesně této velikosti.

## <a name="persisting-client-id"></a>Zachování ID klienta

Může zahrnovat odpovědí z hledání Bing rozhraní API `X-MSEdge-ClientID` záhlaví, který by měly být odeslány zpět do rozhraní API s následující požadavky. Pokud se používají rozhraní API pro vyhledávání více Bing, musí být stejné ID klienta použit s všechny z nich, pokud je to možné.

Poskytuje `X-MSEdge-ClientID` záhlaví umožňuje rozhraní API Bingu pro všechny uživatele vyhledávání, která má dvě důležité výhody přidružení.

Nejprve umožňuje Bing vyhledávacího webu použít po kontextu hledání nalézt výsledky, které lépe odpovídají uživatele. Pokud uživatel má dříve hledali podmínky týkající se řízení, například novější vyhledejte "uzlů" může přednostně vrácení informací o uzlů použít v řízení.

Druhý Bing může náhodně vyberte uživatele, můžete vyzkoušet nové funkce dřív, než budou k dispozici. Poskytuje stejné ID klienta s každou žádostí zajistí, že ji uživatelé, které byly vybrány zobrazíte funkce vždy zobrazit. Bez ID klienta může uživatel zobrazit funkci Zobrazit a zmizí, zdánlivě v náhodných v příslušných výsledcích hledání.

Zásady zabezpečení prohlížeče (CORS) může zabránit `X-MSEdge-ClientID` záhlaví dostupný pro JavaScript. Toto omezení nastane při hledání odpovědi má jiný počátek ze stránky, která je požadována. V produkčním prostředí je potřeba vyřešit tuto zásadu hostováním skript na straně serveru, který provede volání rozhraní API ve stejné doméně jako webovou stránku. Vzhledem k tomu, že skript má stejný původ jako webovou stránku, `X-MSEdge-ClientID` záhlaví je pak možné JavaScript.

> [!NOTE]
> V produkční webové aplikace měli byste provést serverovou žádost přesto. Klíč rozhraní API služby Bing Search, jinak hodnota musí být součástí webové stránky, kde je k dispozici všem uživatelům zobrazení zdroje. Fakturuje se pro všechny využití v rámci předplatného klíč rozhraní API, i požadavkům neoprávněným stranami, proto je důležité, abyste vystavit váš klíč.

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
> [Referenční dokumentace rozhraní API vyhledávání bitové kopie Bingu](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

