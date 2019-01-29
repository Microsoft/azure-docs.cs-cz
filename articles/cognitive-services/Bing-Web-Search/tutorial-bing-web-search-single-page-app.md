---
title: 'Kurz: Vytvoření webové jednostránkové aplikace – rozhraní API webové vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Na této jednostránkové aplikaci můžete vidět, jak se dá použít rozhraní API Bingu pro vyhledávání na webu k načtení, analýze a zobrazení relevantních výsledků vyhledávání v jednostránkové aplikaci.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 09/12/2018
ms.author: aahi
ms.openlocfilehash: 13040cf7f75b8bf2c0bf898dc4f3b16c99a80560
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55172472"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-web-search-api"></a>Kurz: Vytvoření jednostránkové aplikace pomocí rozhraní API webové vyhledávání Bingu

Na této jednostránkové aplikaci můžete vidět, jak načíst, analyzovat a zobrazit výsledky vyhledávání z rozhraní API Bingu pro vyhledávání na webu. V tomto kurzu se používají standardní jazyk HTML a šablony stylů CSS a kurz se zaměřuje na kód jazyka JavaScript. Jazyk HTML, šablony stylů CSS a soubory JS jsou k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) s pokyny pro rychlý start.

Tato ukázková aplikace může provádět následující akce:

> [!div class="checklist"]
> * Volání rozhraní API Bingu pro vyhledávání na webu s možnostmi pro hledání
> * Zobrazení výsledků webu, obrázků, zpráv a videa
> * Stránkování výsledků
> * Správa klíčů předplatného
> * Zpracování chyb

Abyste mohli použít tuto aplikaci, potřebujete [účet služby Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s rozhraními API Bingu pro vyhledávání. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="prerequisites"></a>Požadavky

Tady je pár věcí, které budete potřebovat ke spuštění aplikace:

* Node.js 8 nebo novější
* Klíč předplatného

## <a name="get-the-source-code-and-install-dependencies"></a>Získání zdrojového kódu a instalace závislostí

Prvním krokem je naklonování úložiště se zdrojovým kódem ukázkové aplikace.

```console
git clone https://github.com/Azure-Samples/cognitive-services-REST-api-samples.git
```

Potom spusťte `npm install`. Pro účely tohoto kurzu je jediná závislost Express.js.

```console
cd <path-to-repo>/cognitive-services-REST-api-samples/Tutorials/Bing-Web-Search
npm install
```

## <a name="app-components"></a>Komponenty aplikace

Ukázková aplikace, kterou vytváříme, se skládá ze čtyř částí:

* `bing-web-search.js` – Naše aplikace Express.js. Zpracovává logiku žádost/odpověď a směrování.
* `public/index.html` – Kostra naší aplikace. Definuje, jak se budou zobrazovat data uživateli.
* `public/css/styles.css` – Definuje styly stránky, jako jsou například písma, barvy a velikost textu.
* `public/js/scripts.js` – Obsahuje logiku provádění žádostí na rozhraní API Bingu pro vyhledávání na webu, správy klíčů předplatného, zpracování a analýzy odpovědí a zobrazení výsledků.

Tento kurz se zaměřuje na `scripts.js` a logiku potřebnou k volání rozhraní API Bingu pro vyhledávání na webu a zpracování odpovědi.

## <a name="html-form"></a>Formulář HTML

`index.html` obsahuje formulář, který umožňuje uživateli vyhledávat a vybírat možnosti hledání. Když se formulář odešle, aktivuje se atribut `onsubmit` a zavolá metodu `bingWebSearch()` definovanou v `scripts.js`. Funkce má tři argumenty:

* Vyhledávací dotaz
* Vybrané možnosti
* Klíč předplatného

```html
<form name="bing" onsubmit="return bingWebSearch(this.query.value,
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="query-options"></a>Možnosti proxy

Formulář HTML obsahuje možnosti, které se mapují k parametrům dotazu v [rozhraní API Bingu pro vyhledávání na webu verze 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query-parameters). Tato tabulka obsahuje podrobný přehled, jak můžou uživatelé filtrovat výsledky hledání pomocí ukázkové aplikace:

| Parametr | Popis |
|-----------|-------------|
| `query` | Textové pole pro zadání řetězce dotazu |
| `where` | Rozevírací nabídka pro výběr trhu (místo a jazyk) |
| `what` | Zaškrtávací políčka pro upřednostnění konkrétních typů výsledků. Když například upřednostníte obrázky, zvýší se hodnocení obrázků ve výsledcích hledání. |
| `when` | Rozevírací nabídka, která umožní uživateli omezit výsledky vyhledávání na dnešek, tento týden nebo tento měsíc |
| `safe` | Zaškrtávací políčko pro povolení funkce Bezpečné hledání v Bingu, která filtruje obsah pro dospělé |
| `count` | Skryté pole. Počet výsledků hledání, který se má vrátit u jednotlivých žádostí. Pokud chcete zobrazit méně nebo více výsledků na stránku, můžete tuto hodnotu změnit. |
| `offset` | Skryté pole. Odsazení prvního výsledku hledání v žádosti, které slouží ke stránkování. S každou novou žádostí se hodnota resetuje na `0`. |

> [!NOTE]
> Rozhraní API Bingu pro vyhledávání na webu nabízí další parametry dotazu umožňující zpřesnit výsledky hledání. Tato ukázka jich používá jenom pár. Úplný seznam dostupných parametrů najdete v tématu s [referenční dokumentací k rozhraní API Bingu pro vyhledávání na webu verze 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query-parameters).

Funkce `bingSearchOptions()` převede tyto možnosti tak, aby odpovídaly formátu, který vyžaduje rozhraní API Bingu pro vyhledávání.

```javascript
// Build query options from selections in the HTML form.
function bingSearchOptions(form) {

    var options = [];
    // Where option.
    options.push("mkt=" + form.where.value);
    // SafeSearch option.
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    // Freshness option.
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++)
        if (form.what[i].checked) what.push(form.what[i].value);
    // Promote option.
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    // Count option.
    options.push("count=" + form.count.value);
    // Offset option.
    options.push("offset=" + form.offset.value);
    // Hardcoded text decoration option.
    options.push("textDecorations=true");
    // Hardcoded text format option.
    options.push("textFormat=HTML");
    return options.join("&");
}
```

`SafeSearch` je možné nastavit na `strict`, `moderate` nebo `off` a výchozí nastavení pro vyhledávání na webu Bingu je `moderate`. Tento formulář používá zaškrtávací políčko, které má dva stavy. V tomto fragmentu kódu je u Bezpečného hledání nastavené `strict` nebo `off`, `moderate` se nepoužívá.

Pokud je vybrané kterékoliv ze zaškrtávacích políček **upřednostnění**, přidá se k dotazu parametr `answerCount`. Když je použitý parametr `promote`, vyžaduje se `answerCount`. Aby se vrátily všechny dostupné typy výsledků, je v tomto fragmentu kódu nastavená hodnota `9`.
> [!NOTE]
> Upřednostnění typu výsledku *nezaručuje*, že bude tento typ zahrnutý ve výsledcích hledání. Upřednostnění ve srovnání s obvyklým hodnocením spíše zvýší hodnocení daných druhů výsledků. Pokud chcete omezit hledání na konkrétní typy výsledků, použijte parametr dotazu `responseFilter` nebo volejte konkrétnější koncový bod, jako je například Vyhledávání obrázků Bingu nebo Vyhledávání zpráv Bingu.

Do skriptu jsou pevně zakódované parametry dotazu `textDecoration` a `textFormat` a díky tomu je hledaný termín ve výsledcích hledání napsaný tučně. Tyto parametry nejsou povinné.

## <a name="manage-subscription-keys"></a>Správa klíčů předplatného

Aby se nemusel pevně kódovat klíč předplatného rozhraní API Bingu pro vyhledávání, používá se v této ukázkové aplikaci k uložení klíče předplatného trvalé úložiště prohlížeče. Pokud není uložený žádný klíč předplatného, zobrazí se uživateli výzva, aby ho zadal. Pokud rozhraní API klíč předplatného odmítne, zobrazí se uživateli výzva, aby klíč předplatného zadal znovu.

Funkce `getSubscriptionKey()` používá k uložení a načtení klíče předplatného určitého uživatele funkce `storeValue` a `retrieveValue`. Tyto funkce používají objekt `localStorage`, pokud se tato možnost podporuje, nebo soubory cookie.

```javascript
// Cookie names for stored data.
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// See source code for storeValue and retrieveValue definitions.

// Get stored subscription key, or prompt if it isn't found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // Always set the cookie in order to update the expiration date.
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

Jak jsme viděli dříve, aktivuje `onsubmit` při odeslání formuláře volání `bingWebSearch`. Tato funkce inicializuje a odešle žádost. K ověření žádosti se při každém odeslání volá `getSubscriptionKey`.

## <a name="call-bing-web-search"></a>Volání vyhledávání na webu Bingu

Na základě dotazu, řetězce možností a klíče předplatného vytvoří funkce `BingWebSearch` objekt `XMLHttpRequest` k volání koncového bodu vyhledávání na webu Bingu.

```javascript
// Perform a search constructed from the query, options, and subscription key.
function bingWebSearch(query, options, key) {
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // Initialize the request.
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // Add request headers.
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // Event handler for successful response.
    request.addEventListener("load", handleBingResponse);

    // Event handler for errors.
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // Event handler for an aborted request.
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // Send the request.
    request.send();
    return false;
}
```

Po úspěšné žádosti se aktivuje obslužná rutina události `load` a zavolá funkce `handleBingResponse`. `handleBingResponse` analyzuje objekt výsledku, zobrazí výsledky a obsahuje logiku chyb pro chybné žádosti.

```javascript
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // Try to parse results object.
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // Show raw JSON and the HTTP request.
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // If the HTTP response is 200 OK, try to render the results.
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

    // Any other HTTP response is considered an error.
    else {
        // 401 is unauthorized; force a re-prompt for the user's subscription
        // key on the next request.
        if (this.status === 401) invalidateSubscriptionKey();

        // Some error responses don't have a top-level errors object, if absent
        // create one.
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // Display the HTTP status code.
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // Add all fields from all error responses.
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // Display Bing Trace ID if it isn't blocked by CORS.
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // Display the error message.
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Úspěšná žádost HTTP *neznamená*, že bylo úspěšné samotné vyhledávání. Pokud v operaci vyhledávání dojde k chybě, vrátí rozhraní API Bingu pro vyhledávání na webu stavový kód HTTP jiný než 200 a zahrne do odpovědi JSON informace o chybě. Pokud byla u žádosti omezena rychlost, vrátí rozhraní API prázdnou odpověď.

Velká část kódu v obou předchozích funkcích je vyhrazená zpracování chyb. V následujících fázích můžou nastat chyby:

| Krok | Potenciální chyby | Čím se zpracují |
|-------|--------------------|------------|
| Vytváření objektu žádosti | Neplatná adresa URL | Blok `try` / `catch` |
| Provedení žádosti | Chyby sítě, přerušená připojení | Obslužné rutiny událostí `error` a `abort` |
| Provedení vyhledávání | Neplatná žádost, neplatný JSON, omezení rychlosti | Testy v obslužné rutině události `load` |

Chyby se zpracují voláním `renderErrorMessage()`. Pokud odpověď úspěšně projde všemi testy chyb, volá se `renderSearchResults()` k zobrazení výsledků hledání.

## <a name="display-search-results"></a>Zobrazení výsledků hledání

Pro výsledky vrácené rozhraním API Bingu pro vyhledávání na webu existují [požadavky týkající se použití a zobrazení](useanddisplayrequirements.md). Protože odpověď může obsahovat různé typy výsledků, nestačí to k iteraci v rámci kolekce `WebPages` na nejvyšší úrovni. Místo toho použije ukázková aplikace k řazení výsledků podle specifikace `RankingResponse`.

> [!NOTE]
> Pokud chcete pouze jeden typ výsledku, použijte parametr dotazu `responseFilter` nebo zvažte možnost použít jeden z koncových bodů Vyhledávání Bingu, jako je například Vyhledávání obrázků Bingu.

Každá odpověď má objekt `RankingResponse`, který může obsahovat až tři kolekce: `pole`, `mainline` a `sidebar`. Pokud je k dispozici `pole`, je to nejrelevantnější výsledek hledání a musí se zobrazit na předním místě. `mainline` obsahuje většinu výsledků hledání a zobrazí se hned za `pole`. `sidebar` zahrnuje doplňující výsledky hledání. Pokud je to možné, měly by se tyto výsledky zobrazit na bočním panelu. Pokud není kvůli omezení obrazovky boční panel praktický, měly by se tyto výsledky zobrazit za výsledky `mainline`.

Všechny `RankingResponse` zahrnují pole `RankingItem`, které určuje, jak mají být seřazené výsledky. Naše ukázková aplikace používá k určení výsledku parametry `answerType` a `resultIndex`.

> [!NOTE]
> Existují další způsoby, jak identifikovat a řadit výsledky. Další informace najdete v tématu [Použití řazení k zobrazení výsledků](rank-results.md).

Pojďme se podívat na kód:

```javascript
// Render the search results from the JSON response.
function renderSearchResults(results) {

    // If spelling was corrected, update the search field.
    if (results.queryContext.alteredQuery)
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // Add Prev / Next links with result count.
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results for each section.
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

Funkce `renderResultsItems()` se iteruje mezi položkami v jednotlivých kolekcích `RankingResponse`, mapuje každý výsledek řazení na výsledek vyhledávání pomocí hodnot `answerType` a `resultIndex` a volá příslušnou funkci vykreslení, aby se vygeneroval kód HTML. Pokud nemá položka zadané `resultIndex`, iteruje se v rámci všech výsledků tohoto typu `renderResultsItems()` a pro každou položku se volá funkce vykreslení. Výsledný kód HTML se vloží do příslušného prvku `<div>` v `index.html`.

```javascript
// Render search results from the RankingResponse object per rank response and
// use and display requirements.
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // Collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` RankingResult type is in the `webPages` top-level collection.
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // This ranking item refers to ONE result of the specified type.
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // This ranking item refers to ALL results of the specified type.
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

## <a name="review-renderer-functions"></a>Pohled na funkce rendereru

V naší ukázkové aplikaci obsahuje objekt `searchItemRenderers` funkce, které generují kód HTML pro každý typ výsledku hledání.

```javascript
// Render functions for each result type.
searchItemRenderers = {
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!IMPORTANT]
> Ukázková aplikace má renderery pro webové stránky, zprávy, obrázky, videa a související hledání. Vaše aplikace bude potřebovat renderery pro každý typ výsledků, který může dostat. Ty můžou zahrnovat výpočty, návrhy pravopisu, entity, časová pásma a definice.

Některé funkce vykreslování přijímají pouze parametr `item`. Ostatní přijímají další parametry, které je možné použít k vykreslení položky různým způsobem v závislosti na kontextu. Renderer, který tyto informace nepoužívá, nepotřebuje přijímat tyto parametry.

Argumenty kontextu jsou následující:

| Parametr  | Popis |
|------------|-------------|
| `section` | Oddíl s výsledky (`pole`, `mainline` nebo `sidebar`), ve kterém se zobrazí položka. |
| `index`<br>`count` | K dispozici, pokud položka `RankingResponse` určuje, že se mají zobrazit všechny výsledky v dané kolekci. Jinak `undefined`. Index položky v rámci svojí kolekce a celkový počet položek v dané kolekci. Tyto informace můžete použít k vyčíslení výsledků, generování různých kódů HTML pro první nebo poslední výsledek a tak dále. |

V ukázkové aplikaci používají renderery `images` i `relatedSearches` argumenty kontextu k přizpůsobení generovaného kódu HTML. Podívejme se na renderer `images` zblízka:

```javascript
searchItemRenderers = {
    // Render image result with thumbnail.
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
    },
    // Other renderers are omitted from this sample...
}
```

Renderer obrázků:

* Vypočítá velikost miniatury obrázku (šířka se liší, ale výška je pevně nastavená na 60 pixelů).
* Vloží kód HTML, který předchází výsledku obrázku na základě kontextu.
* Vytvoří značku HTML `<a>` odkazující na stránku, která obsahuje obrázek.
* Vytvoří značku HTML `<img>` k zobrazení miniatury obrázku.

Renderer obrázků používá proměnné `section` a `index` k zobrazení výsledků různým způsobem v závislosti na jejich umístění. Mezi výsledky obrázků na bočním panelu se vloží konec řádku (značka `<br>`) tak, aby se na bočním panelu zobrazil sloupec s obrázky. V dalších částech je před prvním výsledkem obrázku `(index === 0)` značka `<p>`.

Velikost miniatury se používá ve značce `<img>` i v polích `h` a `w` v adrese URL miniatury. Atributy `title` a `alt` (textový popis obrázku) se vytvářejí z názvu obrázku a názvu hostitele v adrese URL.

Tady je příklad zobrazení obrázků v ukázkové aplikaci:

![[Výsledky obrázků Bingu]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persist-the-client-id"></a>Zachování ID klienta

Odpovědi z rozhraní API Bingu pro vyhledávání můžou zahrnovat hlavičku `X-MSEdge-ClientID`, která by se měla s každou následující žádostí posílat zpátky do rozhraní API. Pokud používá vaše aplikace více než jedno rozhraní API Bingu pro vyhledávání na webu, je nutné poslat s každou žádostí napříč službami stejné ID klienta.

Poskytnutí hlavičky `X-MSEdge-ClientID` umožňuje rozhraním API Bingu spojit si všechna vyhledávání určitého uživatele. Zaprvé to umožňuje, aby vyhledávací web Bing použil při vyhledávání minulý kontext a našel výsledky, které lépe vyhoví žádosti. Pokud uživatel v minulosti vyhledával třeba výrazy týkající se lodí, může pozdější vyhledání „uzlů“ přednostně vrátit informace o uzlech používaných při plavbě lodí. Za druhé může Bing náhodně vybírat uživatele k vyzkoušení nových funkcí, než budou všeobecně dostupné. Poskytnutí stejného ID klienta s každou žádostí zajistí, že uživatelé vybraní k tomu, aby danou funkci viděli, ji uvidí vždy. Bez ID klienta může uživatel funkci ve svých výsledcích hledání zdánlivě náhodně někdy vidět a jindy ne.

Zásady zabezpečení prohlížeče, jako je například sdílení prostředků mezi zdroji (CORS), můžou zabránit ukázkové aplikaci v přístupu k hlavičce `X-MSEdge-ClientID`. K tomuto omezení dochází, když má odpověď na vyhledávání jiný zdroj než stránka, která o ni požádala. V produkčním prostředí je potřeba tyto zásady vyřešit hostováním skriptu na straně serveru, který provádí volání rozhraní API ve stejné doméně jako webová stránka. Protože tento skript má stejný původ jako webová stránka, hlavička `X-MSEdge-ClientID` je pak pro JavaScript dostupná.

> [!NOTE]
> Při tvorbě webové aplikace byste měli provádět žádost na straně serveru tak jako tak. Jinak musí být klíč předplatného rozhraní API Bingu pro vyhledávání součástí webové stránky, kde je k dispozici každému, kdo si zobrazí zdroj. Účtuje se veškeré využívání vašeho klíče předplatného rozhraní API, dokonce i žádosti provedené neoprávněnými stranami, proto je důležité klíč nezveřejňovat.

Pro účely vývoje můžete žádost provést prostřednictvím proxy serveru CORS. Odpověď z takového typu proxy serveru má hlavičku `Access-Control-Expose-Headers`, která přidává hlavičky odpovědí na seznam povolených a zpřístupňuje je pro JavaScript.

Nainstalovat proxy server CORS a povolit naší ukázkové aplikaci přístup k hlavičce ID klienta je snadné. Spusťte tento příkaz:

```console
npm install -g cors-proxy-server
```

V dalším kroku změňte koncový bod vyhledávání na webu Bingu v `script.js` na:

```javascript
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search
```

Spuštění proxy serveru CORS pomocí tohoto příkazu:

```console
cors-proxy-server
```

Při používání ukázkové aplikace nechejte otevřené příkazové okno. Pokud okno zavřete, proxy server se zastaví. V rozbalitelné části hlaviček protokolu HTTP pod výsledky hledání byste měli vidět hlavičku `X-MSEdge-ClientID`. Ověřte, že je u všech žádostí stejná.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Referenční informace k rozhraní API Bingu pro vyhledávání na webu verze 7](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
