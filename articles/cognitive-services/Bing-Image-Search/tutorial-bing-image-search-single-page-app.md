---
title: 'Kurz: Vytvoření webové jednostránkové aplikace - API pro vyhledávání obrázků Bingu'
titleSuffix: Azure cognitive services
description: Rozhraní API Bingu pro vyhledávání obrázků umožňuje hledat na webu vysoce kvalitní relevantní obrázky. V tomto kurzu vytvoříte jednostránkovou webovou aplikaci, která může odesílat vyhledávací dotazy do rozhraní API a zobrazovat výsledky v rámci webové stránky.
services: cognitive-services
author: aahi
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: tutorial
ms.date: 9/12/2018
ms.author: aahi
ms.openlocfilehash: e2013b28e8c829d49efe662a9b0eba245c6d5fab
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53253948"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-image-search-api"></a>Kurz: Vytvoření jednostránkové aplikace pomocí rozhraní API Bingu pro vyhledávání obrázků

Rozhraní API Bingu pro vyhledávání obrázků umožňuje hledat na webu vysoce kvalitní relevantní obrázky. V tomto kurzu vytvoříte jednostránkovou webovou aplikaci, která může odesílat vyhledávací dotazy do rozhraní API a zobrazovat výsledky v rámci webové stránky. Tento kurz je podobný [odpovídajícímu kurzu](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md) pro rozhraní API Bingu pro vyhledávání na webu.

Ukázková aplikace předvádí, jak:

> [!div class="checklist"]
> * Provést volání rozhraní API Bingu pro vyhledávání obrázků v JavaScriptu
> * Zlepšit výsledky hledání pomocí možností hledání
> * Zobrazit a procházet stránky výsledků hledání
> * Vyžádat a používat klíč předplatného rozhraní API a ID klienta Bingu

Úplný zdrojový kód k tomuto kurzu je dostupný na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Image-Search).

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [Node.js](https://nodejs.org/).
* Architektura [Express.js](https://expressjs.com/) pro Node.js. V souboru readme Githubu ukázky jsou k dispozici pokyny k instalaci pro zdrojový kód.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="manage-and-store-user-subscription-keys"></a>Správa a ukládání uživatelských klíčů předplatného

Tato aplikace používá k uložení klíčů předplatného rozhraní API trvalé úložiště webových prohlížečů. Pokud není uložen žádný klíč, webová stránka vyzve uživatele, aby zadal svůj klíč a uloží ho pro pozdější použití. Pokud je klíč později odmítnut rozhraním API, aplikace jej odebere z úložiště.


Definujte funkce `storeValue` a `retrieveValue`, které používají buď objekt `localStorage` (když je podporovaný prohlížečem), nebo soubor cookie.

```javascript
// Cookie names for data being stored
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";
// The Bing Image Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

try { //Try to use localStorage first
    localStorage.getItem;   

    window.retrieveValue = function (name) {
        return localStorage.getItem(name) || "";
    }
    window.storeValue = function(name, value) {
        localStorage.setItem(name, value);
    }
} catch (e) {
    //If the browser doesn't support localStorage, try a cookie
    window.retrieveValue = function (name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) return keyvalue[1];
        }
        return "";
    }
    window.storeValue = function (name, value) {
        var expiry = new Date();
        expiry.setFullYear(expiry.getFullYear() + 1);
        document.cookie = name + "=" + value.trim() + "; expires=" + expiry.toUTCString();
    }
}
```

Funkce `getSubscriptionKey()` se pokusí načíst dříve uložený klíč pomocí `retrieveValue`. Pokud není nalezen žádný klíč, zobrazí výzvu uživateli k zadání klíče a uloží ho pomocí `storeValue`.

```javascript

// Get the stored API subscription key, or prompt if it's not found
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

Značka HTML `<form>` `onsubmit` volá funkci `bingWebSearch` k vrácení výsledků hledání. `bingWebSearch` používá `getSubscriptionKey` k ověření každého dotazu. Jak je vidět v předchozí definici, `getSubscriptionKey` vyzve uživatele k zadání klíče, pokud klíč nebyl zadán. Klíč se pak uloží pro další používání ze strany aplikace.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value,
bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="send-search-requests"></a>Odeslání žádostí o vyhledávání

Tato aplikace používá HTML `<form>` k počátečnímu odeslání uživatelských žádostí o vyhledávání, přičemž k volání `newBingImageSearch()` používá atribut `onsubmit`.

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

Ve výchozím nastavení obslužná rutina `onsubmit` vrátí `false`, což zabraňuje odeslání formuláře.

## <a name="select-search-options"></a>Výběr možností hledání

![[Formulář Bingu pro vyhledávání obrázků]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

Rozhraní API Bingu pro vyhledávání obrázků nabízí několik [parametrů pro filtrování dotazů](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#filter-query-parameters), které umožňují zúžit a filtrovat výsledky hledání. Formulář HTML v této aplikaci používá a zobrazuje následující parametry:

|              |                                                                                                                                                                                    |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `where`      | Rozevírací nabídka pro výběr trhu (polohy a jazyka) pro vyhledávání.                                                                                             |
| `query`      | Textové pole pro zadání hledaných termínů.                                                                                                                                 |
| `aspect`     | Přepínač pro výběr poměrů stran nalezených snímků: zhruba čtvercový, široký nebo vysoký.                                                                                     |
| `color`      |                                                                                                                                                                                    |
| `when`       | Rozevírací nabídka pro volitelné omezení vyhledávání na poslední den, týden nebo měsíc.                                                                                          |
| `safe`       | Zaškrtávací políčko označující, jestli se má používat funkce Bingu Bezpečné hledání k filtrování výsledků „pro dospělé“.                                                                                      |
| `count`      | Skryté pole. Počet výsledků vyhledávání, které se mají vrátit pro jednotlivé požadavky. Můžete změnit, aby se na stránce zobrazovalo méně nebo více výsledků.                                                            |
| `offset`     | Skryté pole. Posun prvního výsledku hledání v požadavku, sloužící ke stránkování. Při novém požadavku se resetuje na `0`.                                                           |
| `nextoffset` | Skryté pole. Při přijetí výsledku hledání je toto pole je nastaveno na hodnotu `nextOffset` v odpovědi. Použitím tohoto pole se vyhnete překrývajícím se výsledkům na po sobě jdoucích stránkách. |
| `stack`      | Skryté pole. Zakódovaný seznam JSON posunu předchozích stránek výsledků hledání, pro přechod zpět na předchozí stránky.                                                      |

Funkce `bingSearchOptions()` tyto možnosti zformátuje na řetězec částečného dotazu, který lze použít v požadavcích rozhraní API aplikace.  

```javascript
// Build query options from the HTML form
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

## <a name="performing-the-request"></a>Provedení požadavku

Pomocí vyhledávacího dotazu, řetězce možností a klíče rozhraní API funkce `BingImageSearch()` použije objekt XMLHttpRequest k odeslání požadavku na koncový bod Bingu pro vyhledávání obrázků.


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

Při úspěšném dokončení požadavku HTTP volá JavaScript obslužnou rutinu události `handleBingResponse()` ke zpracování úspěšného požadavku HTTP GET.

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
> Úspěšné požadavky HTTP můžou obsahovat informace o neúspěšných hledání. Pokud během operace vyhledávání dojde k chybě, rozhraní API Bingu pro vyhledávání zpráv vrátí stavový kód HTTP jiný než 200 a informace o chybě v odpovědi JSON. Kromě toho, pokud byl požadavek omezený rychlostí, vrátí rozhraní API prázdnou odpověď.

## <a name="display-the-search-results"></a>Zobrazení výsledků hledání

Výsledky hledání se zobrazují pomocí funkce `renderSearchResults()`, která převezme JSON vrácený službou Bingu pro vyhledávání obrázků a vyvolá odpovídající funkci rendereru na všech vrácených obrázcích a souvisejících vyhledáváních.

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

Výsledky hledání obrázků jsou obsaženy v objektu `value` nejvyšší úrovně v rámci odpovědi JSON. Ty jsou předány funkci `renderImageResults()`, která prochází výsledky a převádí jednotlivé položky do HTML.

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

Rozhraní API Bingu pro vyhledávání obrázků může vrátit čtyři typy návrhů vyhledávání, které pomáhají uživatelům s vyhledáváním, každý ve vlastním objektu nejvyšší úrovně:

| Návrh         | Popis                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pivotSuggestions` | Dotazy, které nahradí pivotové slovo v původním vyhledávání jiným. Pokud třeba vyhledáváte „červené květiny“, pivotové slovo může být „červené“ a pivotový návrh může být „žluté květiny“. |
| `queryExpansions`  | Dotazy, které původní hledání zúží přidáním dalších výrazů. Pokud třeba vyhledáváte „Microsoft Surface“, rozšíření dotazu může být „Microsoft Surface Pro“.                                   |
| `relatedSearches`  | Dotazy, které také zadali ostatní uživatelé, kteří zadali původní vyhledávání. Pokud třeba vyhledáváte „Mount Rainier“, související hledání může být „Mt. Saint Helens“.                       |
| `similarTerms`     | Dotazy, které mají podobný význam jako původní vyhledávání. Pokud třeba vyhledáváte „školy“, podobný výraz může být „vzdělávání“.                                                                   |

Tato aplikace pouze vykreslí návrhy `relatedItems` a umístí výsledné odkazy na bočním panelu na stránce.

## <a name="rendering-search-results"></a>Vykreslení výsledků hledání

V této aplikaci objekt `searchItemRenderers` obsahuje funkce rendereru, které generují kód HTML pro každý druh výsledku hledání.

```javascript
searchItemRenderers = {
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

Funkce rendereru může přijímat tyto parametry:

| Parametr         | Popis                                                                                              |
|---------|----------------------------------------------------------------------------------------------|
| `item`  | Objekt JavaScriptu obsahující vlastnosti položky, jako je její adresa URL a popis. |
| `index` | Index položky výsledků v rámci jeho kolekce.                                          |
| `count` | Počet položek v kolekci položek výsledků hledání.                                  |

Parametry `index` a `count` se používají k vyjádření počtu výsledků, generování kódu HTML pro kolekce a organizaci obsahu. Konkrétně:

* Vypočítá velikost miniatury obrázku (šířka se liší, minimálně činí 120 pixelů, výška je pevně nastavená na 90 pixelů).
* Vytvoří značku HTML `<img>` k zobrazení miniatury obrázku.
* Vytvoří značky HTML `<a>`, které odkazují na obrázek a na stránku, která ho obsahuje.
* Vytvoří popis, který zobrazuje informace o obrázku a webu, na kterém se nachází.

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

`height` a `width` obrázku miniatury se používají ve značce `<img>` i v polích `h` a `w` v adrese URL miniatury. To Bingu umožňuje vrátit [miniaturu](resize-and-crop-thumbnails.md) přesně této velikosti.

## <a name="persisting-client-id"></a>Zachování ID klienta

Odpovědi z rozhraní API Bingu pro vyhledávání můžou zahrnovat hlavičku `X-MSEdge-ClientID`, která by se měla v následujících požadavcích posílat zpět do rozhraní API. Pokud se používá více rozhraní API pro vyhledávání Bingu, mělo by se pro všechny používat stejné ID klienta, pokud je to možné.

Poskytnutí hlavičky `X-MSEdge-ClientID` umožňuje rozhraním API Bingu spojit si všechna uživatelova vyhledávání. To má důležité výhody.

Zaprvé to umožňuje, aby vyhledávací web Bing na vyhledávání použil minulý kontext a našel výsledky, které uživatele více uspokojí. Pokud uživatel v minulosti vyhledával třeba výrazy týkající se lodí, pozdější vyhledání „uzlů“ může přednostně vrátit informace o uzlech používaných při plavbě lodí.

Za druhé může Bing náhodně vybírat uživatele k vyzkoušení nových funkcí, než budou všeobecně dostupné. Poskytnutí stejného ID klienta s každým požadavkem zajistí, že uživatelé vybraní tuto funkci vidět, ji vidí vždy. Bez ID klienta může uživatel funkci ve svých výsledcích hledání zdánlivě náhodně někdy vidět a jindy ne.

Zásady zabezpečení prohlížeče (CORS) můžou bránit tomu, aby byla hlavička `X-MSEdge-ClientID` pro JavaScript dostupná. K tomuto omezení dochází, když odpověď na vyhledávání má jiný zdroj než stránka, která o ni požádala. V produkčním prostředí je potřeba tyto zásady vyřešit hostováním skriptu na straně serveru, který provádí volání rozhraní API ve stejné doméně jako webová stránka. Protože tento skript má stejný původ jako webová stránka, hlavička `X-MSEdge-ClientID` je pak pro JavaScript dostupná.

> [!NOTE]
> Při tvorbě webové aplikace byste měli požadavek provádět na straně serveru tak jako tak. Jinak musí být klíč rozhraní API Bingu pro vyhledávání součástí webové stránky, kde je k dispozici každému, kdo si zobrazí zdroj. Účtuje se vám veškeré využívání vašeho klíče předplatného rozhraní API, dokonce i požadavky provedené neoprávněnými stranami, proto je důležité klíč nezveřejňovat.

Pro účely vývoje můžete požadavek na rozhraní API Bingu pro vyhledávání na webu provést prostřednictvím proxy serveru CORS. Odpověď z takového proxy serveru má hlavičku `Access-Control-Expose-Headers`, která přidává hlavičky odpovědí na seznam povolených a zpřístupňuje je pro JavaScript.

Nainstalovat proxy server CORS a povolit naší ukázkové aplikaci přístup k hlavičce ID klienta je snadné. Nejdřív [nainstalujte Node.js](https://nodejs.org/en/download/), pokud jste to ještě neudělali. Pak zadejte v příkazovém okně tento příkaz:

    npm install -g cors-proxy-server

V dalším kroku změňte koncový bod vyhledávání na webu Bingu v souboru HTML na:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Nakonec spusťte proxy server CORS pomocí tohoto příkazu:

    cors-proxy-server

Při používání ukázkové aplikace nechte příkazové okno otevřené. Zavřením okna se zastaví proxy server. V rozbalitelné sekci hlaviček HTTP pod výsledky hledání teď uvidíte hlavičku `X-MSEdge-ClientID` (mimo jiné) a můžete zkontrolovat, jestli je stejná pro každý požadavek.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Extrahování podrobností o obrázku pomocí rozhraní API Bingu pro vyhledávání obrázků](tutorial-image-post.md)

## <a name="see-also"></a>Další informace najdete v tématech

* [Referenční informace k rozhraní API Bingu pro vyhledávání obrázků](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
