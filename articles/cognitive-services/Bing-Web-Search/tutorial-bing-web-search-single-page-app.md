---
title: Hledání webové služby Bing jednostránkovou webovou aplikaci | Microsoft Docs
description: Ukazuje, jak používat rozhraní API služby Bing webové Search v jednostránkovou webovou aplikaci.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: f22e38a1d6ee4042684b9822b58669bed6fe29a0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343438"
---
# <a name="tutorial-single-page-web-app"></a>Kurz: Jednostránkovou webovou aplikaci

Rozhraní API služby Bing webové Search umožňuje hledat na webu a získat výsledky různých typů, které jsou relevantní pro vyhledávací dotaz. V tomto kurzu jsme sestavení jednostránkovou webovou aplikaci, která používá rozhraní API služby Bing webové Search zobrazit výsledky vyhledávání přímo na stránce. Aplikace obsahuje součásti HTML, CSS a JavaScript.

<!-- Remove until this can be replaced with a sanitized version.
![[Single-page Bing Web Search app]](media/cognitive-services-bing-web-api/web-search-spa-demo.png)
-->

> [!NOTE]
> Záhlaví JSON a HTTP v dolní části stránky odhalit odpověď JSON a informace o požadavku HTTP při kliknutí na. Tyto podrobnosti jsou užitečné při prozkoumávání služby.

Aplikace kurz ukazuje, jak:

> [!div class="checklist"]
> * Provádění volání rozhraní API služby Bing webové Search v jazyce JavaScript
> * Předat možnosti hledání do rozhraní API služby Bing webové Search
> * Webové zobrazení, zprávy, image a výsledky hledání video
> * Stránka prostřednictvím výsledky hledání
> * Popisovač Bing ID a rozhraní API předplatné klíč klienta
> * Zpracování chyb, které můžou nastat

Kurz stránka je zcela samostatné; nepoužívá se žádné externí rozhraní, šablony stylů nebo i soubory bitové kopie. Používá jenom široce podporované funkce jazyka JavaScript a pracuje s aktuálními verzemi všechny hlavní prohlížeče.

V tomto kurzu probereme pouze vybrané části zdrojového kódu. Úplný zdrojový kód je k dispozici [na samostatné stránce](tutorial-bing-web-search-single-page-app-source.md). Zkopírujte a vložte tento kód do textového editoru a uložte ho jako `bing.html`.

## <a name="app-components"></a>Součásti aplikace

Stejně jako jakoukoli jednostránkovou webovou aplikaci kurz aplikace obsahuje tři části:

> [!div class="checklist"]
> * HTML – definuje struktuře a obsahu stránky
> * Šablon stylů CSS – definuje vzhled stránky
> * JavaScript – definuje chování stránky

V tomto kurzu nezahrnuje většinu kódu HTML nebo šablon stylů CSS podrobně, protože se jedná o přehledné.

HTML obsahující formulář vyhledávání, ve kterém uživatel zadá dotaz a vybere možností hledání. Formulář je připojený k JavaScript, která ve skutečnosti provádí vyhledávání podle `<form>` tagu `onsubmit` atribut:

```html
<form name="bing" onsubmit="return newBingWebSearch(this)">
```

`onsubmit` Obslužná rutina vrátí `false`, který udržuje formuláře z odeslání na server. Kód jazyka JavaScript skutečně funguje shromažďování nezbytné informace z formuláře a provádění hledání.

HTML také obsahuje divizí (HTML `<div>` značky) kde se zobrazí výsledky hledání.

## <a name="managing-subscription-key"></a>Správa klíč předplatného

Abyste se vyhnuli nutnosti obsahovat klíč rozhraní API služby Bing Search předplatného v kódu, používáme k uložení klíče trvalého úložiště v prohlížeči. Pokud žádný klíč je uložený, jsme vyzvat, aby klíče uživatele a uloží jej pro pozdější použití. Pokud později klíč rozhraní API zamítnul, jsme zneplatnit uložené klíč, uživateli se znovu zobrazí výzva.

Jsme definovali `storeValue` a `retrieveValue` funkce, které používají buď `localStorage` objektu (Pokud je prohlížeč podporuje ji) nebo soubor cookie. Naše `getSubscriptionKey()` funkce používá tyto funkce pro ukládání a načítání klíče uživatele.

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

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

HTML `form` značka `onsubmit` volání `bingWebSearch` funkce vracet výsledky vyhledávání. `bingWebSearch` používá `getSubscriptionKey` k ověření každý dotaz. Jak je znázorněno v definici předchozí `getSubscriptionKey` vyzve uživatele k klíč, pokud klíč nebyl zadán. Klíč je pak uloženy pokračovat v používání aplikace.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>Výběrem možnosti hledání

![[Vyhledávání Bing webové formuláře]](media/cognitive-services-bing-web-api/web-search-spa-form.png)

Formuláře HTML obsahuje prvky s těmito názvy:

| | |
|-|-|
| `where` | Rozevírací nabídka pro výběr na trhu (umístění a jazyk) používá pro vyhledávání. |
| `query` | Textové pole, do kterého chcete zadejte hledaný text. |
| `what` | Zaškrtávací políčka pro zvýšení úrovně konkrétní typy výsledků. Povýšení bitové kopie, například zvyšuje hodnocení bitových kopií. |
| `when` | Rozevírací nabídky můžete omezit výsledky vyhledávání na poslední den, týden nebo měsíc. |
| `safe` | Zaškrtávací políčko označující, zda na Bing bezpečné hledání funkce slouží k filtrování "pro dospělé" výsledky. |
| `count` | Skryté pole. Počet výsledků hledání se vrátíte na každý požadavek. Změna zobrazíte výsledky méně či více na stránce. |
| `offset` | Skryté pole. Posun první výsledek hledání v požadavku; použít pro stránkování. Se resetují na `0` na novou žádost. |

> [!NOTE]
> Hledání webové služby Bing nabízí mnoho další parametry dotazu. Tady používáme pouze několik z nich.

Funkce JavaScript, která `bingSearchOptions()` převede těchto polí na formát vyžadují rozhraní API služby Bing Search.

```javascript
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
        options.push("promote=" + what.join(","));
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

Pokud platí jedna z **povýšit** zaškrtávací políčka jsou označené, jsme také přidat `answerCount` parametru dotazu. `answerCount` je potřeba při použití `promote` parametr. Můžeme jednoduše nastavte ji na `9` (počet typy výsledků podporovaná rozhraním API vyhledávání webové služby Bing) a ujistěte se nám získat maximální možný počet typy výsledků.

> [!NOTE]
> Povýšení typ výsledku nemá *zaručit* , výsledky hledání obsahují tento druh výsledek. Místo toho povýšení zvyšuje pořadí těchto druhů výsledky vzhledem k jejich obvyklé hodnocení. Chcete-li omezit na konkrétní typy výsledků hledání, použijte `responseFilter` parametr dotazu nebo proveďte volání koncový bod konkrétnější například vyhledávání bitové kopie Bingu nebo vyhledávání zprávy Bing.

Můžeme také poslat `textDecoration` a `textFormat` dotaz parametry způsobí hledaný termín k být tučně ve výsledcích hledání. Tyto hodnoty jsou pevně zakódované ve skriptu.

## <a name="performing-the-request"></a>Provádění požadavku

Zadaný dotaz, string možnosti a klíč rozhraní API `BingWebSearch` využívá `XMLHttpRequest` objekt, který má být odeslán požadavek na koncový bod hledání webové služby Bing.

```javascript
// perform a search given query, options string, and API key
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

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
        return;
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
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
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
> V případě úspěšné žádosti HTTP nemá *není* nutně znamenají, že celé hledání úspěšné. Pokud dojde k chybě v operaci vyhledávání, rozhraní API služby Bing webové Search vrátí stavový kód 200 HTTP a obsahuje informace o chybě v odpovědi JSON. Kromě toho pokud se požadavek míra limited, rozhraní API vrátí prázdnou odpověď.

Většinu kódu v obou těchto funkcí jsou vyhrazené pro zpracování chyb. Může dojít k chybám v těchto fází:

|Krok|Potenciální chyby|Zpracovává|
|-|-|-|
|Objekt požadavku sestavení jazyka JavaScript|Neplatná adresa URL|`try`/`catch` blok|
|Vytvoření požadavku|Chyby sítě, přerušené připojení|`error` a `abort` obslužné rutiny událostí|
|Provádění hledání|Neplatný požadavek, neplatný formát JSON, omezení přenosové rychlosti|testů v `load` obslužné rutiny události|

Řeší chyby volání `renderErrorMessage()` s nějaké podrobnosti o této chybě známé. Pokud odpověď úspěšně projde úplné gauntlet chyba testů, říkáme `renderSearchResults()` zobrazit výsledky vyhledávání na stránce.

## <a name="displaying-search-results"></a>Zobrazení výsledků vyhledávání

Rozhraní API služby Bing webové Search [vyžaduje, abyste zobrazit výsledky v zadaném pořadí](useanddisplayrequirements.md). Vzhledem k tomu, že rozhraní API může vracet různé druhy odpovědi, není dostatek k iteraci v rámci nejvyšší úrovně `WebPages` kolekce v odpovědi JSON a zobrazení výsledků. (Pokud chcete pouze jednoho typu výsledky, použijte `responseFilter` parametr dotazu nebo jiným koncovým bodem Bing vyhledávání.)

Místo toho používáme `rankingResponse` ve výsledcích hledání pro řazení výsledků pro zobrazení. Tento objekt odkazuje na položky ve `WebPages` `News`, `Images`, nebo `Videos` kolekcí, nebo v jiné kolekce odpovědí nejvyšší úrovně v odpovědi JSON.

`rankingResponse` může obsahovat až tři kolekce výsledky hledání, určené `pole`, `mainline`, a `sidebar`. 

`pole`, pokud existuje, je nejdůležitější výsledek hledání a měla by se zobrazit přednostně. `mainline` odkazuje k hromadnému výsledků vyhledávání. Nejdůležitějších výsledky mají být zobrazeny ihned po `pole` (nebo první, pokud `pole` není k dispozici). 

Nakonec. `sidebar` odkazuje na výsledky pomocného hledání. Tyto výsledky se často související hledání nebo bitové kopie. Pokud je to možné tyto výsledky, které má být zobrazena v skutečné bočním panelu. Pokud obrazovky omezení proveďte bočním panelu nepraktické (například na mobilní zařízení), musí se objeví po `mainline` výsledky.

Každá položka v `rankingResponse` kolekce odkazuje na položky výsledků vyhledávání skutečné dvěma způsoby jiné, ale ekvivalentní.

| | |
|-|-|
|`id`|`id` Vypadá jako adresu URL, ale by nemělo být použito pro odkazy. `id` Typ výsledku hodnocení odpovídá `id` buď vyhledávání výsledek položky v kolekci odpovědí *nebo* kolekci celé odpovědi (například `Images`).
|`answerType`, `resultIndex`|`answerType` Odkazuje na kolekci nejvyšší úrovně odpovědí, která obsahuje výsledek (například `WebPages`). `resultIndex` Odkazuje výsledek index v rámci této kolekce. Pokud `resultIndex` je tento parametr vynechán, výsledek hodnocení odkazuje na celou kolekci.

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

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()` naopak iteruje nad položek v každé `rankingResponse` část, každý výsledek hodnocení se mapuje na výsledek vyhledávání pomocí `answerType` a `resultIndex` polí a volání funkce odpovídající vykreslování ke generování HTML výsledek. 

Pokud `resultIndex` není zadaný pro danou hodnocení položku `renderResultsItems()` iteruje nad všechny výsledky tohoto typu a volání funkce vykreslení pro každou položku. 

V obou případech výsledné HTML se vloží do příslušné `<div>` element na stránce.

```javascript
// render search results from rankingResponse object in specified order
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` rankingResult type is in the `webPages` top-level collection
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        // must have results of the given type AND a renderer for it
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // this ranking item refers to ONE result of the specified type
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // this ranking item refers to ALL results of the specified type
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="rendering-result-items"></a>Vykreslování položky výsledků

V našem JavaScript kód je objekt, `searchItemRenderers`, který obsahuje *nástroji pro vykreslování:* výsledek hledání funkce, které generují kód HTML pro každý typ.

```javascript
// render functions for various types of search results
searchItemRenderers = { 
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!NOTE]
> Naše kurzu aplikace nemá pro vykreslování pro webové stránky, příspěvků, obrázků, videí a související hledání. Vlastní aplikace potřebuje pro všechny typy výsledků, které vám mohou být, může například výpočtů, pravopis návrhy, entity, časových pásem a definice pro vykreslování.

Některé z našich vykreslování funkce zadat pouze `item` parametr: objekt jazyka JavaScript, který představuje výsledek hledání jednoduchého. Ostatní přijímat další parametry, které se dají použít k vykreslení položky v různých kontextech odlišně. (Zobrazovací jednotky, která nepoužívá tyto informace nemusí tyto parametry přijměte.)

Kontext argumenty jsou:

| | |
|-|-|
|`section`|V části výsledky (`pole`, `mainline`, nebo `sidebar`) ve které se zobrazí položka.
|`index`<br>`count`|Dostupné, když `rankingResponse` položka určuje, že všechny výsledky v dané kolekci jsou zobrazený; `undefined` jinak. Tyto parametry získávají index položky v rámci jeho kolekce a celkový počet položek v dané kolekci. Můžete tyto informace k počet výsledků, generují jiný kód HTML pro první nebo poslední výsledek a tak dále.|

V našem kurzu aplikaci jak `images` a `relatedSearches` nástroji pro vykreslování kontextu argumenty použít k přizpůsobení generují kód HTML. Podívejme bližší pohled na `images` zobrazovací jednotky:

```javascript
searchItemRenderers = { 
    // render image result using thumbnail
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    }, // other renderers omitted
}
```

Naše funkce zobrazovací jednotky bitové kopie:

> [!div class="checklist"]
> * Vypočítá velikost miniatur obrázku (šířka se liší, zatímco výška vyřešen v 60 pixelů).
> * Vloží kód HTML, který předchází výsledek bitové kopie v závislosti na kontextu.
> * Sestavení HTML `<a>` značky, který odkazuje na stránku obsahující bitovou kopii.
> * Sestavení HTML `<img>` značky zobrazíte miniaturu obrázku. 

Používá zobrazovací jednotky bitové kopie `section` a `index` proměnné, které chcete zobrazit výsledky odlišně v závislosti na jejich umístění. Konec řádku (`<br>` značka) je vložen mezi výsledky bitové kopie na bočním panelu tak, aby na bočním panelu zobrazí sloupec bitových kopií. V dalších částech první obrázek způsobit `(index === 0)` předchází `<p>` značky. Jinak miniatur tupý zobrazení sebe navzájem a wrap podle potřeby v okně prohlížeče.

Velikost miniatur se používá v obou `<img>` značky a `h` a `w` pole v adrese URL na miniaturu. [Miniatur služby Bing](resize-and-crop-thumbnails.md) pak doručí na miniaturu přesně této velikosti. `title` a `alt` atributy (textový popis bitové kopie) se vytvářejí na základě názvu obrázku a název hostitele v adrese URL.

Zobrazí obrázky, jak je vidět tady ve výsledcích hledání nejdůležitějších.

![[Výsledky bitové kopie Bingu]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persisting-client-id"></a>Zachování ID klienta

Může zahrnovat odpovědí z hledání Bing rozhraní API `X-MSEdge-ClientID` záhlaví, který by měly být odeslány zpět do rozhraní API s následující požadavky. Pokud se používají rozhraní API pro vyhledávání více Bing, musí být stejné ID klienta použit s všechny z nich, pokud je to možné.

Poskytuje `X-MSEdge-ClientID` záhlaví umožňuje rozhraní API Bingu pro všechny uživatele vyhledávání, která má dvě důležité výhody přidružení.

Nejprve umožňuje Bing vyhledávacího webu použít po kontextu hledání nalézt výsledky, které lépe odpovídají uživatele. Pokud uživatel má dříve hledali podmínky týkající se řízení, například novější vyhledejte "uzlů" může přednostně vrácení informací o uzlů použít v řízení.

Druhý Bing může náhodně vyberte uživatele, můžete vyzkoušet nové funkce dřív, než budou k dispozici. Poskytuje stejné ID klienta s každou žádostí zajistí, že uživatelé, kteří rozhodli zobrazíte funkce vždy ji zobrazit. Bez ID klienta může uživatel zobrazit funkci Zobrazit a zmizí, zdánlivě v náhodných v příslušných výsledcích hledání.

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
> [Kurz mobilní aplikace Visual vyhledávání](computer-vision-web-search-tutorial.md)

> [!div class="nextstepaction"]
> [Referenční dokumentace rozhraní API vyhledávání webové služby Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
