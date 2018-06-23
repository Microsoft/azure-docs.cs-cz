---
title: Hledání Entity Bing jednostránkovou webovou aplikaci | Microsoft Docs
description: Ukazuje, jak používat rozhraní API služby Bing Entity Search v jednostránkovou webovou aplikaci.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/08/2017
ms.author: v-jerkin
ms.openlocfilehash: 91c60913cd806baf100e5511cbf59299bf9a84f0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343544"
---
# <a name="tutorial-single-page-web-app"></a>Kurz: Jednostránkovou webovou aplikaci

Rozhraní API služby Bing Entity Search umožňuje vyhledávat webu informace o *entity* a *umístí.* Může požádat o buď typ výsledku, nebo obojí, v daný dotaz. Definice místech a entity jsou uvedeny níže.

|||
|-|-|
|Entity|Známé osob, míst a věcí, které zjistíte podle názvu|
|Míst|Restaurace, hotels a jiné místní firmám, které zjistíte podle názvu *nebo* podle typu (italská restaurace)|

V tomto kurzu jsme sestavení jednostránkovou webovou aplikaci, která používá rozhraní API služby Bing Entity Search zobrazit výsledky vyhledávání přímo na stránce. Aplikace obsahuje součásti HTML, CSS a JavaScript.

Rozhraní API umožňuje nastavit prioritu výsledky podle umístění. V mobilní aplikaci můžete pokládat zařízení pro vlastní umístění. Ve webové aplikaci, můžete použít `getPosition()` funkce. Ale toto volání lze použít pouze v kontextu zabezpečení a jeho nemusí poskytnout přesné umístění. Uživatel také potřebovat hledat pro entity blízkosti umístění než vlastní.

Naše aplikace volá proto při službu Bing Maps získat zeměpisnou šířku a délku z umístění zadanou uživatelem. Uživatel potom můžete zadat název významné ("místo ručička") nebo celé nebo jeho část adresy ("New Yorku") a rozhraní API map Bing poskytuje souřadnice.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> Záhlaví JSON a HTTP v dolní části stránky odhalit odpověď JSON a informace o požadavku HTTP při kliknutí na. Tyto podrobnosti jsou užitečné při prozkoumávání služby.

Aplikace kurz ukazuje, jak:

> [!div class="checklist"]
> * Provádění volání rozhraní API služby Bing Entity Search v jazyce JavaScript
> * Provedení mapy Bing `locationQuery` volání rozhraní API v jazyce JavaScript
> * Předat možnosti vyhledávání na volání rozhraní API
> * Zobrazení výsledků
> * Zpracování Bing klienta ID a rozhraní API předplatné klíče
> * Řešit všechny chyby, které můžou nastat

Kurz stránka je zcela samostatné; nepoužívá se žádné externí rozhraní, šablony stylů nebo i soubory bitové kopie. Používá jenom široce podporované funkce jazyka JavaScript a pracuje s aktuálními verzemi všechny hlavní prohlížeče.

V tomto kurzu probereme pouze vybrané části zdrojového kódu. Úplný zdrojový kód je k dispozici [na samostatné stránce](tutorial-bing-entities-search-single-page-app-source.md). Zkopírujte a vložte tento kód do textového editoru a uložte ho jako `bing.html`.

> [!NOTE]
> V tomto kurzu je podstatně podobná [jednostránkové vyhledávání Bing webové aplikace kurzu](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), ale zabývá pouze výsledky hledání entity.

## <a name="app-components"></a>Součásti aplikace

Stejně jako jakoukoli jednostránkovou webovou aplikaci kurz aplikace obsahuje tři části:

> [!div class="checklist"]
> * HTML – definuje struktuře a obsahu stránky
> * Šablon stylů CSS – definuje vzhled stránky
> * JavaScript – definuje chování stránky

V tomto kurzu nezahrnuje většinu kódu HTML nebo šablon stylů CSS podrobně, protože se jedná o přehledné.

HTML obsahující formulář vyhledávání, ve kterém uživatel zadá dotaz a vybere možností hledání. Formulář je připojený k JavaScript, která ve skutečnosti provádí vyhledávání podle `<form>` tagu `onsubmit` atribut:

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

`onsubmit` Obslužná rutina vrátí `false`, který udržuje formuláře z odeslání na server. Kód jazyka JavaScript skutečně funguje shromažďování nezbytné informace z formuláře a provádění hledání.

Hledání probíhá ve dvou fázích. První Pokud uživatel zadá omezení umístění, mapy Bing dotaz provádí převést na souřadnice. Zpětné volání pro tento dotaz se pak spustí Bing Entity vyhledávací dotaz.

HTML také obsahuje divizí (HTML `<div>` značky) kde se zobrazí výsledky hledání.

## <a name="managing-subscription-keys"></a>Správa předplatného klíče

> [!NOTE]
> Tato aplikace vyžaduje předplatné klíče pro rozhraní API služby Bing Search a rozhraní API map Bing. Můžete použít [zkušební klíč hledání Bing](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) a [základní mapy Bing klíč](https://www.microsoft.com/maps/create-a-bing-maps-key).

Abyste nemuseli zahrnout Bing vyhledávání a rozhraní API map Bing předplatné klíče v kódu, používáme k uložení je trvalé úložiště v prohlížeči. Pokud ani jeden klíč nebyl uložený, jsme vyzvat, aby ho a uloží jej pro pozdější použití. Pokud později klíč rozhraní API zamítnul, jsme zneplatnit uložené klíč, uživateli se zobrazí výzva pro něj při své další vyhledávání.

Jsme definovali `storeValue` a `retrieveValue` funkce, které používají buď `localStorage` objektu (Pokud je prohlížeč podporuje ji) nebo soubor cookie. Naše `getSubscriptionKey()` funkce používá tyto funkce pro ukládání a načítání klíče uživatele.

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "http://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

HTML `<body>` zahrnuje značky `onload` atribut, který volá `getSearchSubscriptionKey()` a `getMapsSubscriptionKey()` při dokončení načítání stránky. Tyto volání sloužit k okamžitě výzvu uživatele pro jejich klíče v případě, že je ještě nezadali.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>Výběrem možnosti hledání

![[Vyhledávací Entity Bing formulář]](media/entity-search-spa-form.png)

Formuláře HTML obsahuje následující prvky:

| | |
|-|-|
|`where`|Rozevírací nabídka pro výběr na trhu (umístění a jazyk) používá pro vyhledávání.|
|`query`|Textové pole, do kterého chcete zadejte hledaný text.|
|`safe`|Zaškrtávací políčko označující, zda je zapnuta bezpečné hledání (omezuje "pro dospělé" výsledky)|
|`what`|Nabídka pro výběr pro vyhledávání pro entity a místech.|
|`mapquery`|Textové pole, ve kterém může uživatel zadat adresu celé nebo jeho část, významné, atd. pomohou návratový více relevantní výsledky hledání Entity Bing.|

> [!NOTE]
> Výsledky míst jsou aktuálně k dispozici pouze ve Spojených státech amerických. `where` a `what` nabídky mají kód vynutit toto omezení. Pokud zvolíte trhu mimo USA míst je vybráno v `what` nabídce `what` změní na jinou hodnotu. Pokud se rozhodnete míst mimo USA trhu vybráno v `where` nabídce `where` změny ve Spojených státech.

Naše JavaScript funkce `bingSearchOptions()` převede těchto polí s řetězcem dotazu částečné pro rozhraní API služby Bing Search.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

Například může být funkci bezpečné hledání `strict`, `moderate`, nebo `off`, s `moderate` se výchozí hodnota. Ale naše formulář používá zaškrtávací políčko, který má jenom dva stavy. Kód jazyka JavaScript převede toto nastavení buď `strict` nebo `off` (jsme nepoužívejte `moderate`).

`mapquery` Pole nejsou zpracovávány v `bingSearchOptions()` protože je používán pro dotaz umístění mapy Bing, ne pro hledání Entity Bing.

## <a name="obtaining-a-location"></a>Získání umístění

Rozhraní API map Bing nabízí [ `locationQuery` metoda](//msdn.microsoft.com/library/ff701711.aspx), které jsou používány za účelem vyhledání zeměpisnou a zadání zeměpisné umístění uživatele. Tyto souřadnice jsou předána do rozhraní API pro vyhledávání Bing Entity požadavku uživatele. Výsledky hledání prioritu entity a míst, na které blíží v zadaném umístění.

Nemohli jsme získat přístup pomocí běžný rozhraní API map Bing `XMLHttpRequest` dotazu ve webové aplikaci, protože služba nepodporuje dotazy nepůvodního zdroje. Naštěstí podporuje JSONP ("P" je pro "vyplní"). Odpověď JSONP je odpověď obyčejnou JSON zabalené ve volání funkce. Požadavku vložením pomocí `<script>` značka do dokumentu. (Načítání skripty není předmětem zásad zabezpečení prohlížeče.)

`bingMapsLocate()` Funkce vytvoří a vloží `<script>` značky pro dotaz. `jsonp=bingMapsCallback` Segmentu řetězce dotazu určuje název funkce, která má být volána s odpovědí.

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Pokud rozhraní API map Bing neodpoví, `bingMapsCallBack()` volána funkce. To obvykle znamená, `bingEntitySearch()` není volán, a entity výsledky hledání se nezobrazí. Chcete-li zamezit tomuto scénáři `bingMapsLocate()` také nastaví časovač pro volání `bingEntitySearch()` po pět sekund. Funkce zpětného volání, aby se zabránilo prohledávání dvakrát entity není logiku.

Po dokončení dotazu `bingMapsCallback()` funkce je volána, jak si vyžádal.

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

Společně s zeměpisné šířky a délky, vyžaduje vyhledávací Entity Bing dotaz *radius* určující přesnost informace o umístění. Jsme vypočítat pomocí protokolu radius *ohraničujícího pole* zadaná v odpovědi mapy Bing. Pole ohraničující je obdélníku, která obklopuje celý umístění. Například, pokud uživatel zadá `NYC`, výsledek obsahuje zhruba centrální souřadnice New Yorku a ohraničující pole, která zahrnuje města. 

Nám nejdřív vypočítat daleko od primární souřadnice všechny čtyři rohy ohraničujícího rámečku pomocí funkce `haversineDistance()` (není vidět). Používáme jako poloměr největší těchto čtyř vzdáleností. Minimální radius je kilometr. Tato hodnota se také používá jako výchozí, pokud je k dispozici žádné ohraničující pole v odpovědi.

Po obdržení souřadnice a poloměr, pak říkáme `bingEntitySearch()` skutečné vyhledávání.

## <a name="performing-the-search"></a>Provádění hledání

Zadaný dotaz, umístění, řetězec možnosti a klíč rozhraní API `BingEntitySearch()` funkce provede požadavek hledání Entity Bing.

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

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
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

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
> V případě úspěšné žádosti HTTP nemá *není* nutně znamenají, že celé hledání úspěšné. Pokud dojde k chybě v operaci vyhledávání, rozhraní API služby Bing Entity Search vrátí stavový kód 200 HTTP a obsahuje informace o chybě v odpovědi JSON. Kromě toho pokud se požadavek míra limited, rozhraní API vrátí prázdnou odpověď.

Většinu kódu v obou těchto funkcí jsou vyhrazené pro zpracování chyb. Může dojít k chybám v těchto fází:

|Krok|Potenciální chyby|Zpracovává|
|-|-|-|
|Objekt požadavku sestavení jazyka JavaScript|Neplatná adresa URL|`try`/`catch` blok|
|Vytvoření požadavku|Chyby sítě, přerušené připojení|`error` a `abort` obslužné rutiny událostí|
|Provádění hledání|Neplatný požadavek, neplatný formát JSON, omezení přenosové rychlosti|testů v `load` obslužné rutiny události|

Řeší chyby volání `renderErrorMessage()` s nějaké podrobnosti o této chybě známé. Pokud odpověď úspěšně projde úplné gauntlet chyba testů, říkáme `renderSearchResults()` zobrazit výsledky vyhledávání na stránce.

## <a name="displaying-search-results"></a>Zobrazení výsledků vyhledávání

Rozhraní API služby Bing Entity Search [vyžaduje, abyste zobrazit výsledky v zadaném pořadí](use-display-requirements.md). Vzhledem k tomu, že rozhraní API může vrátit dva různé druhy odpovědi, není dostatek k iteraci v rámci nejvyšší úrovně `Entities` nebo `Places` kolekce v odpovědi JSON a zobrazení výsledků. (Pokud chcete, aby pouze jeden typ výsledku, použijte `responseFilter` parametr dotazu.)

Místo toho používáme `rankingResponse` kolekce ve výsledcích hledání pro řazení výsledků pro zobrazení. Tento objekt odkazuje na položky ve `Entitiess` nebo `Places` kolekce.

`rankingResponse` může obsahovat až tři kolekce výsledky hledání, určené `pole`, `mainline`, a `sidebar`. 

`pole`, pokud existuje, je nejdůležitější výsledek hledání a měla by se zobrazit přednostně. `mainline` odkazuje k hromadnému výsledků vyhledávání. Nejdůležitějších výsledky mají být zobrazeny ihned po `pole` (nebo první, pokud `pole` není k dispozici). 

Nakonec. `sidebar` odkazuje na výsledky pomocného hledání. Může se zobrazit skutečné bočním panelu nebo jednoduše po nejdůležitějších výsledky. Jsme zvolili k tomu pro naše kurz aplikaci.

Každá položka v `rankingResponse` kolekce odkazuje na položky výsledků vyhledávání skutečné dvěma způsoby jiné, ale ekvivalentní.

| | |
|-|-|
|`id`|`id` Vypadá jako adresu URL, ale by nemělo být použito pro odkazy. `id` Typ výsledku hodnocení odpovídá `id` buď vyhledávání výsledek položky v kolekci odpovědí *nebo* kolekci celé odpovědi (například `Entities`).
|`answerType`<br>`resultIndex`|`answerType` Odkazuje na kolekci nejvyšší úrovně odpovědí, která obsahuje výsledek (například `Entities`). `resultIndex` Odkazuje výsledek index v rámci této kolekce. Pokud `resultIndex` je tento parametr vynechán, výsledek hodnocení odkazuje na celou kolekci.

> [!NOTE]
> Další informace v této části odpovědi vyhledávání najdete v tématu [pořadí výsledky](rank-results.md).

Můžete použít kteroukoli metodu vyhledání položku výsledek odkazované vyhledávání je nejvhodnější pro vaši aplikaci. V našem kódu, použijeme `answerType` a `resultIndex` najít každý výsledek hledání.

Nakonec je na čase podívat se na naše funkce `renderSearchResults()`. Tato funkce iteruje nad tří `rankingResponse` kolekce, které představují na tři části výsledků vyhledávání. Pro každý oddíl říkáme `renderResultsItems()` k vykreslení výsledky pro tuto část.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>Vykreslování položky výsledků

V našem JavaScript kód je objekt, `searchItemRenderers`, který obsahuje *nástroji pro vykreslování:* výsledek hledání funkce, které generují kód HTML pro každý typ.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

Funkce zobrazovací jednotky může přijímat následující parametry:

| | |
|-|-|
|`item`|JavaScript objekt obsahující vlastnosti položky, například jeho adresa URL a její popis.|
|`index`|Index položky výsledek v rámci jeho kolekce.|
|`count`|Počet položek v kolekci položku výsledek hledání.|

`index` a `count` parametry můžete použít k počet výsledků, generovat speciální HTML pro začátku nebo na konec kolekce, chcete-li vložit konce řádků po určitý počet položek a tak dále. Pokud vykreslovací modul není nutné tuto funkci, není nutné přijmout tyto dva parametry. Ve skutečnosti jsme nepoužívejte je v nástroji pro vykreslování pro naše kurz aplikaci.

Podívejme bližší pohled na `entities` zobrazovací jednotky:

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

Naše entity vykreslovací funkce:

> [!div class="checklist"]
> * Sestavení HTML `<img>` značky zobrazíte miniaturu obrázku, pokud existuje. 
> * Sestavení HTML `<a>` značky, který odkazuje na stránku, který obsahuje bitovou kopii.
> * Popis, který zobrazí informace o bitové kopie a na server, který se nachází na sestavení.
> * Zahrnuje entity klasifikace pomocí Rady k zobrazení, pokud existuje.
> * Zahrnuje odkaz na hledání Bing získat další informace o entitě.
> * Zobrazí všechny licenčních smluv nebo porušení informace požaduje zdroje dat.

## <a name="persisting-client-id"></a>Zachování ID klienta

Může zahrnovat odpovědí z hledání Bing rozhraní API `X-MSEdge-ClientID` záhlaví, který by měly být odeslány zpět do rozhraní API s následující požadavky. Pokud se používají rozhraní API pro vyhledávání více Bing, musí být stejné ID klienta použit s všechny z nich, pokud je to možné.

Poskytuje `X-MSEdge-ClientID` záhlaví umožňuje rozhraní API Bingu pro všechny uživatele vyhledávání, která má dvě důležité výhody přidružení.

Nejprve umožňuje Bing vyhledávacího webu použít po kontextu hledání nalézt výsledky, které lépe odpovídají uživatele. Pokud uživatel má dříve hledali podmínky týkající se řízení, například novější vyhledejte "přepraviště" může přednostně vrácení informací o místa a ukotvení plachetnice.

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
> [Referenční dokumentace rozhraní API vyhledávání Entity Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [Dokumentace rozhraní API map Bing](//msdn.microsoft.com/library/dd877180.aspx)
