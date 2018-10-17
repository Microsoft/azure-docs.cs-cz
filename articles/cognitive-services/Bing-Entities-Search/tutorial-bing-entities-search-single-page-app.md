---
title: 'Kurz: Vyhledávání entit Bingu pomocí jednostránkové webové aplikace'
titlesuffix: Azure Cognitive Services
description: Ukazuje, jak používat rozhraní API Bingu pro vyhledávání entit v jednostránkové webové aplikaci.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: tutorial
ms.date: 12/08/2017
ms.author: v-jerkin
ms.openlocfilehash: 9aabecbec144797b9fbafdff7179213b68921447
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815541"
---
# <a name="tutorial-single-page-web-app"></a>Kurz: Jednostránková webová aplikace

Rozhraní API Bingu pro vyhledávání entit umožňuje vyhledávat na webu informace o *entitách* a *místech.* Daným dotazem můžete požadovat jeden z druhů výsledku, nebo oba. Definice míst a entit jsou uvedeny níže.

|||
|-|-|
|Entity|Známí lidé, místa a věci, které najdete podle jména|
|Místa|Restaurace, hotely a další místní firmy, které najdete podle názvu *nebo* podle typu (italské restaurace)|

V tomto kurzu sestavíme jednostránkovou webovou aplikaci, která používá rozhraní API Bingu pro vyhledávání entit k zobrazení výsledků hledání přímo na stránce. Aplikace zahrnuje komponenty HTML, CSS a JavaScriptu.

Rozhraní API umožňuje určit prioritu výsledků podle polohy. V mobilní aplikaci můžete požádat zařízení o jeho vlastní polohu. Ve webové aplikaci můžete použít funkci `getPosition()`. Tato funkce ale funguje jenom v zabezpečeném kontextu. Může se stát, že neposkytne přesnou polohu. Uživatel by také mohl chtít hledat entity blízko jiných umístění, než je jeho poloha.

Naše aplikace proto volá službu Map Bingu k získání zeměpisné šířky a délky z umístění zadaného uživatelem. Uživatel může buď zadat název památky (Space Needle) nebo celou či částečnou adresu (New York City). Rozhraní API pro Mapy Bingu poskytne souřadnice.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> Hlavičky JSON a HTTP v dolní části stránky při kliknutí zobrazí informace o odpovědi JSON a požadavku HTTP. Tyto podrobnosti jsou užitečné při prozkoumávání služby.

Ukázková aplikace předvádí, jak:

> [!div class="checklist"]
> * Provést volání rozhraní API Bingu pro vyhledávání entit v JavaScriptu
> * Provést volání rozhraní API `locationQuery` Map Bing v JavaScriptu
> * Předat možnosti hledání těmto voláním rozhraní API
> * Zobrazit výsledky hledání
> * Používat ID klienta Bingu a klíče předplatného rozhraní API
> * Vyřešit problémy, které by mohly nastat

Stránka kurzu je zcela samostatná. Nepoužívá žádná externí rozhraní, šablony stylů ani soubory obrázků. Používá jenom běžně podporované funkce jazyka JavaScript a funguje s aktuálními verzemi všech hlavních webových prohlížečů.

V tomto kurzu probereme jen vybrané části zdrojového kódu. Úplný zdrojový kód je k dispozici na [samostatné stránce](tutorial-bing-entities-search-single-page-app-source.md). Zkopírujte a vložte tento kód do textového editoru a uložte ho jako `bing.html`.

> [!NOTE]
> Tento kurz je velmi podobný [kurzu Jednostránková aplikace Bingu pro vyhledávání na webu](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), ale zabývá se jenom výsledky hledání entit.

## <a name="app-components"></a>Komponenty aplikace

Stejně jako každá jednostránková webová aplikace i tato obsahuje tři části:

> [!div class="checklist"]
> * HTML – definuje strukturu a obsah stránky
> * Šablony stylů CSS – definují vzhled stránky
> * JavaScript – definuje chování stránky

Tento kurz se většinou nezabývá podrobně kódem HTML nebo šablonami stylů CSS, protože jsou docela jednoduché.

Kód HTML obsahuje vyhledávací formulář, do kterého uživatel zadá dotaz a vybere možnosti hledání. Formulář je propojený s JavaScriptem, který pak skutečně provádí hledání podle atributu `onsubmit` ve značce `<form>`:

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

Obslužná rutina `onsubmit` vrátí `false`. Díky tomu se formulář neodesílá na server. Kód JavaScriptu shromažďuje nezbytné informace z formuláře a provádí hledání.

Hledání probíhá ve dvou fázích. V případě, že uživatel zadal omezení umístění, dotaz Mapám Bing je nejprve převede na souřadnice. Zpětné volání tohoto dotazu pak spustí dotaz rozhraní API Bingu pro vyhledávání entit.

Kód HTML také obsahuje úseky (značky HTML `<div>`), kde se zobrazují výsledky hledání.

## <a name="managing-subscription-keys"></a>Správa klíčů předplatného

> [!NOTE]
> Tato aplikace vyžaduje klíče předplatného jak pro rozhraní API pro vyhledávání Bingu, tak pro rozhraní API Map Bingu. Můžete použít [zkušební klíč vyhledávání Bingu](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) a [základní klíč Map Bing](https://www.microsoft.com/maps/create-a-bing-maps-key).

Aby se nemusely klíče předplatného rozhraní API pro vyhledávání Bingu a Map Bing zahrnout do kódu, používáme k uložení klíčů trvalé úložiště prohlížeče. Pokud není žádný z klíčů uložený, vyzveme k jeho zadání a uložíme ho pro pozdější použití. Když později rozhraní API klíč odmítne, zneplatníme uložený klíč. Uživatel o něj bude při příštím hledání požádán znovu.

Definujeme funkce `storeValue` a `retrieveValue`, které používají buď objekt `localStorage` (když je podporovaný prohlížečem), nebo soubor cookie. Naše funkce `getSubscriptionKey()` tyto funkce používá k ukládání a načítání uživatelova klíče.

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

Značka HTML `<body>` obsahuje atribut `onload`, který po dokončení načítání stránky volá funkce `getSearchSubscriptionKey()` a `getMapsSubscriptionKey()`. Toto volání okamžitě vyzve uživatele k zadání klíčů, pokud už je nezadal.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>Výběr možností hledání

![[Formulář rozhraní API Bingu pro vyhledávání entit]](media/entity-search-spa-form.png)

HTML formulář obsahuje následující ovládací prvky:

| | |
|-|-|
|`where`|Rozevírací nabídka pro výběr trhu (polohy a jazyka) pro vyhledávání.|
|`query`|Textové pole pro zadání hledaných termínů.|
|`safe`|Zaškrtávací políčko, které indikuje zapnutí bezpečného hledání (omezení výsledků „pro dospělé“)|
|`what`|Nabídka výběru hledání entit, míst nebo obou.|
|`mapquery`|Textové pole, do kterého může uživatel zadat celou nebo částečnou adresu, památku, atd. Tím pomůže rozhraní API Bingu pro vyhledávání entit vracet relevantnější výsledky.|

> [!NOTE]
> Výsledky pro místa jsou aktuálně dostupné pouze pro Spojené státy. Nabídky `where` a `what` obsahují kód pro vynucení tohoto omezení. Když zvolíte trh mimo USA při současně vybrané možnosti Places v nabídce `what`, změní se `what`na Anything. Když zvolíte místa při současně vybraném trhu mimo USA v nabídce `where`, změní se `where` na US.

Naše JavaScriptová funkce `bingSearchOptions()` převede tato pole na řetězec částečného dotazu rozhraní API pro vyhledávání Bingu.

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

Například funkce bezpečného hledání může být `strict`, `moderate` nebo `off`, s výchozí hodnotou `moderate`. Náš formulář ale používá zaškrtávací políčko, které má jenom dva stavy. Kód JavaScriptu toto nastavení převede na `strict` nebo `off` (`moderate` nepoužíváme).

Funkce `bingSearchOptions()` nepracuje s polem `mapquery`. To se použije pro dotaz na polohu Mapám Bing, nikoli rozhraní API Bingu pro vyhledávání entit.

## <a name="obtaining-a-location"></a>Získání polohy

Rozhraní API Map Bing nabízí [`locationQuery` metodu](//msdn.microsoft.com/library/ff701711.aspx). Použijeme ji k najití zeměpisné šířky a délky polohy, které zadá uživatel. Tyto souřadnice se pak s požadavkem uživatele předají rozhraní API Bingu pro vyhledávání entit. Výsledky hledání upřednostňují entity a místa, která jsou blízko zadané polohy.

Ve webové aplikaci nemůžeme přistupovat k rozhraní API Map Bing pomocí běžného dotazu `XMLHttpRequest`, protože tato služba nepodporuje dotazy nepůvodního zdroje. JSONP (kde P znamená odsazený (padded)) ji naštěstí podporuje. Odpověď JSONP je běžná odpověď JSON zabalená do volání funkce. Požadavek se vytvoří vložením značky `<script>` do dokumentu. (Načítání skriptů nepodléhá zásadám zabezpečení prohlížeče)

Funkce `bingMapsLocate()` vytvoří a vloží značku `<script>` pro dotaz. Segment `jsonp=bingMapsCallback` řetězce dotazu určuje název funkce volané s odpovědí.

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
> Funkce `bingMapsCallBack()` se nevolá, pokud rozhraní API Map Bing neodpovídá. Obvykle by to znamenalo, že se funkce `bingEntitySearch()` nevolá a výsledky hledání entit se nezobrazí. Funkce `bingMapsLocate()` proto také spustí časovač, aby volala po pěti sekundách funkci `bingEntitySearch()`. Tak tomuto scénáři zabrání. Logika funkce zpětného volání zabraňuje provedení dvojnásobného hledání entit.

Po dokončení dotazu se volá funkce `bingMapsCallback()` podle požadavku.

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

Dotaz rozhraní API Bingu pro vyhledávání entit vyžaduje společně se zeměpisnou šířkou a délkou také *poloměr*, který určuje přesnost informací o poloze. Poloměr počítáme pomocí *ohraničujícího rámečku*, který poskytla odpověď Map Bing. Ohraničující rámeček je obdélník, který ohraničuje celé umístění. Když například uživatel zadá `NYC`, výsledkem budou zhruba souřadnice středu města New York a ohraničující rámeček, který město obklopuje. 

Nejprve spočítáme vzdálenosti primárních souřadnic od všech čtyř rohů ohraničujícího rámečku pomocí funkce `haversineDistance()` (neukázáno). Největší z těchto čtyř vzdáleností použijeme jako poloměr. Nejmenší velikost poloměru je jeden kilometr. Tato hodnota je zároveň použitá jako výchozí, když v odpovědi není ohraničující rámeček.

Když jsme získali souřadnice a poloměr, voláním funkce `bingEntitySearch()` provedeme skutečné hledání.

## <a name="performing-the-search"></a>Provedení vyhledávání

Funkce `BingEntitySearch()` s daným dotazem, polohou, řetězcem možností a klíčem rozhraní API vyšle požadavek rozhraní API Bingu pro vyhledávání entit.

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

Při úspěšném dokončení požadavku HTTP volá JavaScript naši obslužnou rutinu události `load`, funkci `handleBingResponse()`, ke zpracování úspěšného požadavku HTTP GET na rozhraní API. 

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
> Úspěšný požadavek HTTP *nemusí* nutně znamenat, že bylo úspěšné samotné vyhledávání. Pokud v operaci vyhledávání dojde k chybě, rozhraní API Bingu pro vyhledávání entit vrátí stavový kód HTTP jiný než 200 zahrnující informace o chybě v odpovědi JSON. Kromě toho, pokud byl požadavek omezený rychlostí, vrátí rozhraní API prázdnou odpověď.

Velká část kódu v obou předchozích funkcích je vyhrazená zpracování chyb. K chybám může dojít v těchto fázích:

|Krok|Potenciální chyby|Čím se zpracuje|
|-|-|-|
|Kompilace JavaScriptu vyžaduje objekt|Neplatná adresa URL|Blok `try`/`catch`|
|Provedení požadavku|Chyby sítě, přerušená připojení|Obslužné rutiny událostí `error` a `abort`|
|Provedení vyhledávání|Neplatný požadavek, neplatný JSON, omezení rychlosti|Testy v obslužné rutině události `load`|

Chyby se zpracovávají voláním `renderErrorMessage()` se všemi známými podrobnostmi o chybě. Pokud odpověď úspěšné projde kompletní řadou testů chyb, voláme `renderSearchResults()` k zobrazení výsledků hledání na stránce.

## <a name="displaying-search-results"></a>Zobrazení výsledků hledání

Rozhraní API Bingu pro vyhledávání entit [vyžaduje zobrazení výsledků v určeném pořadí](use-display-requirements.md). Vzhledem k tomu, že rozhraní API může vrátit dva různé druhy odpovědí, nestačí iterovat jenom nejvyšší úroveň kolekce `Entities` nebo `Places` v odpovědi JSON a pak tyto výsledky zobrazit. (Pokud chcete jenom jeden typ výsledku, použijte parametr dotazu `responseFilter`.)

Místo toho použijeme kolekci `rankingResponse` výsledků hledání k řazení výsledků pro zobrazení. Tento objekt odkazuje na položky v kolekcích `Entitiess` nebo `Places`.

`rankingResponse` může obsahovat až tři kolekce výsledků hledání označené `pole`, `mainline` a `sidebar`. 

`pole`, pokud je k dispozici, je nejvíce relevantní výsledek, který by měl být zobrazený přednostně. `mainline` odkazuje na hlavní skupinu výsledky hledání. Tyto hlavní výsledky by se měly zobrazit hned po `pole` (nebo jako první, když není `pole` k dispozici). 

A nakonec. `sidebar` odkazuje na pomocné výsledky hledání. Ty se můžou zobrazit buď na skutečném bočním panelu, nebo prostě po hlavních výsledcích. Pro náš kurz jsme zvolili druhou variantu.

Každá položka v kolekci `rankingResponse` odkazuje k položce skutečných výsledků hledání dvěma různými, ale rovnocennými způsoby.

| | |
|-|-|
|`id`|`id` vypadá jako adresa URL, ale pro odkazy by se používat nemělo. `id` typ výsledku hledání odpovídá `id` položky výsledku hledání v kolekci odpovědí, *nebo* celé kolekce odpovědí (jako například `Entities`).
|`answerType`<br>`resultIndex`|`answerType` odkazuje na nejvyšší úroveň kolekce odpovědí, která obsahuje výsledky (například `Entities`). `resultIndex` odkazuje na index výsledků v této kolekci. Když vynecháme `resultIndex`, bude výsledek hodnocení odkazovat na celou kolekci.

> [!NOTE]
> Další informace o této části odpovědi hledání naleznete v [Výsledky hodnocení](rank-results.md).

K lokalizaci zmíněné položky výsledku hledání použijte takovou metodu, která bude pro vaši aplikaci nejvhodnější. V našem ukázkovém kódu použijeme `answerType` a `resultIndex` k lokalizaci výsledku hledání.

Konečně je čas podívat se na naši funkci `renderSearchResults()`. Tato funkce iteruje přes tři kolekce `rankingResponse`, které představují tři sekce výsledků hledání. Voláme `renderResultsItems()` pro každou sekci. Zobrazíme tak výsledky každé z nich.

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

## <a name="rendering-result-items"></a>Vykreslování položek výsledků

V našem kódu JavaScriptu je objekt `searchItemRenderers` obsahující *renderery* - funkce, které generují kód HTML pro každý druh výsledku hledání.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

Funkce rendereru bude přijímat tyto parametry:

| | |
|-|-|
|`item`|Objekt JavaScriptu obsahující vlastnosti položky, jako je její adresa URL a popis.|
|`index`|Index položky výsledků v rámci jeho kolekce.|
|`count`|Počet položek v kolekci položek výsledků hledání.|

Parametry `index` a `count` se můžou použít k číslování výsledků, generování zvláštního kódu HTML pro začátek nebo konec kolekce, vložení konců řádků za určitý počet položek a tak dále. Pokud renderer tuto funkci nepotřebuje, nepotřebuje tyto dva parametry přijímat. Fakticky je v rendererech naší ukázkové aplikace nepoužíváme.

Podívejme se na renderer `entities` zblízka:

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

Naše funkce rendereru entity:

> [!div class="checklist"]
> * Vytvoří značku HTML `<img>` k zobrazení miniatury obrázku, pokud existuje. 
> * Vytvoří značku HTML `<a>` odkazující na stránku, která obsahuje obrázek.
> * Vytvoří popis, který zobrazuje informace o obrázku a webu, na kterém se nachází.
> * Obsahuje klasifikaci entity pomocí zobrazení tipů, pokud existují.
> * Zahrnuje odkaz na hledání Bingu s dalšími informacemi o entitě.
> * Zobrazí všechna licencování nebo informace o přiřazení podle zdrojů dat.

## <a name="persisting-client-id"></a>Zachování ID klienta

Odpovědi z rozhraní API pro vyhledávání Bingu můžou zahrnovat hlavičku `X-MSEdge-ClientID`, která by se měla odesílat zpět do rozhraní API v následných požadavcích. Pokud se používá více rozhraní API pro vyhledávání Bingu, mělo by se pro všechny používat stejné ID klienta, pokud je to možné.

Poskytnutí hlavičky `X-MSEdge-ClientID` umožňuje rozhraním API Bingu spojit si všechna uživatelova vyhledávání. To má dvě důležité výhody.

Zaprvé to umožňuje, aby vyhledávací web Bing na vyhledávání aplikoval minulý kontext a našel výsledky, které uživatele více uspokojí. Pokud uživatel v minulosti vyhledával třeba výrazy týkající se lodí, pozdější vyhledání „doků“ může přednostně vrátit informace o docích používaných pro kotvení lodí.

Za druhé může Bing náhodně vybírat uživatele k vyzkoušení nových funkcí, než budou všeobecně dostupné. Poskytnutí stejného ID klienta s každým požadavkem zajistí, že uživatelé vybraní tuto funkci vidět, ji vidí vždy. Bez ID klienta může uživatel funkci ve svých výsledcích hledání někdy vidět a jindy ne, zdánlivě náhodně.

Zásady zabezpečení prohlížeče (CORS) můžou bránit tomu, aby byla hlavička `X-MSEdge-ClientID` pro JavaScript dostupná. K tomuto omezení dochází, když odpověď na vyhledávání má jiný zdroj než stránka, která o ni požádala. V produkčním prostředí je potřeba tyto zásady vyřešit hostováním skriptu na straně serveru, který provádí volání rozhraní API ve stejné doméně jako webová stránka. Protože tento skript má stejný původ jako webová stránka, hlavička `X-MSEdge-ClientID` je pak pro JavaScript dostupná.

> [!NOTE]
> Při tvorbě webové aplikace byste měli požadavek provádět na straně serveru tak jako tak. Jinak musí být klíč rozhraní API pro vyhledávání Bingu součástí webové stránky, kde je k dispozici každému, kdo si zobrazí zdroj. Účtuje se vám veškeré využívání vašeho klíče předplatného rozhraní API, dokonce i požadavky provedené neoprávněnými stranami, proto je důležité klíč nezveřejňovat.

Pro účely vývoje můžete požadavek na rozhraní API Bingu pro vyhledávání na webu provést prostřednictvím proxy serveru CORS. Odpověď z takového proxy serveru má hlavičku `Access-Control-Expose-Headers`, která přidává hlavičky odpovědí na seznam povolených a zpřístupňuje je pro JavaScript.

Proxy server CORS nainstalovat a povolit naší ukázkové aplikaci přístup k ID klienta je snadné. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Pak zadejte v příkazovém okně tento příkaz:

    npm install -g cors-proxy-server

V dalším kroku změňte koncový bod vyhledávání na webu Bingu v souboru HTML na:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

    cors-proxy-server

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V rozbalitelné sekci hlaviček HTTP pod výsledky hledání teď uvidíte hlavičku `X-MSEdge-ClientID` (mimo jiné) a můžete zkontrolovat, jestli je stejná pro každý požadavek.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference rozhraní API Bingu pro vyhledávání entit](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [Dokumentace rozhraní API Map Bing](//msdn.microsoft.com/library/dd877180.aspx)
