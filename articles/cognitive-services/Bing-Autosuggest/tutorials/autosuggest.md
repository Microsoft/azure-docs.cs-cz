---
title: 'Kurz: Získávají se výsledky pro automatické návrhy pomocí rozhraní API pro automatické návrhy Bingu'
titlesuffix: Azure Cognitive Services
description: Ukazuje, jak používat Automatické návrhy Bingu.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: tutorial
ms.date: 09/28/2017
ms.author: v-jaswel
ms.openlocfilehash: a9a0dab47a13c349f11181f25b086b70031e08af
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190135"
---
# <a name="tutorial-build-a-web-page-autosuggest-client"></a>Kurz: Sestavení klientské webové stránky pro automatické návrhy

V tomto kurzu vytvoříme webovou stránku, která umožňuje uživatelům zadávat dotazy do rozhraní API pro automatické návrhy Bingu.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> - Vytvoření jednoduchého dotazu na rozhraní API pro automatické návrhy Bingu
> - zobrazení výsledků dotazu.

## <a name="prerequisites"></a>Požadavky

Abyste mohli postupovat podle tohoto kurzu, potřebujete klíč předplatného pro rozhraní API pro automatické návrhy Bingu. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=autosuggest-api).

## <a name="create-a-new-web-page"></a>Vytvoření nové webové stránky

Otevřete textový editor. Vytvořte nový soubor s názvem například autosuggest.html.

## <a name="html-header"></a>Hlavička HTML

Přidejte informace hlavičky HTML a začněte oddíl script tak, jak vidíte na následujícím obrázku.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Bing Autosuggest</title>

<style type="text/css">
    html, body, div, p, h1, h2 {font-family: Verdana, "Lucida Sans", sans-serif;}

    html, body, div, p  {font-weight: normal;}
    h1, h2 {font-weight: bold;}
    sup {font-weight: normal;}

    html, body, div, p  {font-size: 12px;}
    h1 {font-size: 20px;}
    h2 {font-size: 16px;}
    h1, h2 {clear: left;}

    img#logo {float: right;
</style>

<script type="text/javascript">
```

## <a name="getsubscriptionkey-function"></a>Funkce getSubscriptionKey

Funkce getSubscriptionKey vrací klíč rozhraní API pro automatické návrhy Bingu. Buď ho získá z místního úložiště (t.j. souboru cookie), nebo v případě potřeby vyzve uživatele k jeho zadání.

Začněte s funkcí getSubscriptionKey a deklarujte název souboru cookie následujícím způsobem.

```html
getSubscriptionKey = function() {

    var COOKIE = "bing-autosuggest-api-key";   // name used to store API key in key/value storage
```

Podpůrná funkce findCookie vrátí hodnotu zadaného souboru cookie. Pokud soubor cookie není nalezen, vrátí prázdný řetězec.

```html
    function findCookie(name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) {
                return keyvalue[1];
            }
        }
        return "";
        }
```

Podpůrná funkce getSubscriptionKeyCookie zobrazí uživateli výzvu k zadání hodnoty klíče rozhraní API pro automatické návrhy Bingu a vrátí hodnotu tohoto klíče.

```html
    function getSubscriptionKeyCookie() {
        var key = findCookie(COOKIE);
        while (key.length !== 32) {
            key = prompt("Enter Bing Autosuggest API subscription key:", "").trim();
            var expiry = new Date();
            expiry.setFullYear(expiry.getFullYear() + 2);
            document.cookie = COOKIE + "=" + key.trim() + "; expires=" + expiry.toUTCString();
        }
        return key;
    }
```

Podpůrná funkce getSubscriptionKeyLocalStorage se nejprve pokusí načíst klíč rozhraní API pro automatické návrhy Bingu vyhledáním příslušného souboru cookie. Pokud soubor cookie není nalezen, vyzve uživatele k zadání hodnoty klíče. Potom vrátí hodnotu klíče.

```html
    function getSubscriptionKeyLocalStorage() {
        var key = localStorage.getItem(COOKIE) || "";
        while (key.length !== 32)
            key = prompt("Enter Bing Autosuggest API subscription key:", "").trim();
        localStorage.setItem(COOKIE, key)
        return key;
    }
```

Pomocná funkce GetSubscriptionKey přijímá jeden parametr, **invalidate**. Pokud má **invalidate** hodnotu **true**, funkce getSubscriptionKey odstraní soubor cookie, který obsahuje klíč rozhraní API pro automatické návrhy Bingu. Pokud má **invalidate** hodnotu **false**, funkce getSubscriptionKey vrátí hodnotu klíče rozhraní API pro automatické návrhy Bingu.

```html
    function getSubscriptionKey(invalidate) {
        if (invalidate) {
            try {
                localStorage.removeItem(COOKIE);
            } catch (e) {
                document.cookie = COOKIE + "=";
            }
        } else {
            try {
                return getSubscriptionKeyLocalStorage();
            } catch (e) {
                return getSubscriptionKeyCookie();
            }
        }
    }
```

Vraťte pomocnou funkci getSubscriptionKey jako výsledek vnější funkce getSubscriptionKey. Zavřete definici vnější funkce getSubscriptionKey.

```html
    return getSubscriptionKey;

}();
```

## <a name="helper-functions"></a>Pomocné funkce

Pomocná funkce pre vrátí zadaný text předformátovaný se značkou HTML [pre](https://www.w3schools.com/tags/tag_pre.asp).

```html
function pre(text) {
    return "<pre>" + text.replace(/&/g, "&amp;").replace(/</g, "&lt;") + "</pre>"
}
```

Funkce renderSearchResults zobrazí zadané výsledky z rozhraní API pro automatické návrhy Bingu s použitím přehledného výpisu JSON.

```html
function renderSearchResults(results) {
    document.getElementById("results").innerHTML = pre(JSON.stringify(results, null, 2));
}
```

Funkce renderErrorMessage zobrazí zadanou chybovou zprávou a kód chyby.

```html
function renderErrorMessage(message, code) {
    if (code)
        document.getElementById("results").innerHTML = "<pre>Status " + code + ": " + message + "</pre>";
    else
        document.getElementById("results").innerHTML = "<pre>" + message + "</pre>";
}
```

## <a name="bingautosuggest-function"></a>Funke bingAutosuggest

Funkce BingAutosuggest je volána pokaždé, když uživatel zadá text do pole formuláře HTML.
Přijímá dva parametry: obsah pole ve formuláři HTML a klíč rozhraní API pro automatické návrhy Bingu.

```html
function bingAutosuggest(query, key) {
```

Zadejte koncový bod rozhraní API pro automatické návrhy Bingu a deklarujte objekt XMLHttpRequest, který budeme používat k odesílání požadavků na koncový bod.

```html
    var endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/Suggestions";

    var request = new XMLHttpRequest();

    try {
        request.open("GET", endpoint + "?q=" + encodeURIComponent(query));
    }
    catch (e) {
        renderErrorMessage("Bad request");
        return false;
    }
```

Nastavte hlavičku **Ocp-Apim-Subscription-Key** na hodnotu klíče rozhraní API pro automatické návrhy Bingu.

```html
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
```

Zpracujte odpověď z koncového bodu. Pokud je stav 200 (OK), zobrazte výsledky. Jinak zobrazte informace o chybě.

```html
    request.addEventListener("load", function() {
        if (this.status === 200) {
            renderSearchResults(JSON.parse(this.responseText));
        }
        else {
            if (this.status === 401) getSubscriptionKey(true);
            renderErrorMessage(this.statusText, this.status);
        }
    });
```

Také ošetřete možné chybové události v objektu XMLHttpRequest.

```html
    request.addEventListener("error", function() {
        renderErrorMessage("Network error");
    });

    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });
```

Odešlete požadavek. Zavřete funkci bingAutosuggest, značku **script** a značku **head**.

```html
    request.send();
    return false;
}
// --></script>

</head>
```

## <a name="html-body"></a>Tělo HTML

Při načtení webové stránky zkontrolujte, že máme klíč rozhraní API pro automatické návrhy Bingu a že je v případě potřeby uživatel vyzván k jeho zadání.

```html
<body onload="document.forms.bing.query.focus(); getSubscriptionKey();">
```

Zobrazte logo Bingu.

```html
<img id="logo" align=base src="data:image/png;base64,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">
```

Vytvořte formulář HTML s textovým polem. Zpracujte událost **oninput** a zavolejte funkci bingAutosuggest, přičemž jí předejte obsah textového pole a klíč rozhraní API pro automatické návrhy Bingu.

```html
<form name="bing" oninput="return bingAutosuggest(this.query.value, getSubscriptionKey())">
    <h2>Autosuggest</h2>
    <input type="text" name="query" size="80" placeholder="Autosuggest" autocomplete=off>
</form>
```

Přidejte značku HTML **div**, kterou používáme pro zobrazení výsledků. Kód JavaScript, který jsme definovali dříve, odkazuje právě na tuto značku **div**.

```html
<h2>Results</h2>
<div id="results">
<p>None yet.</p>

</div>

</body>
</html>
```

Uložte soubor.

## <a name="display-results"></a>Zobrazení výsledků

Otevřete webovou stránku ve svém prohlížeči. Až budete vyzváni, zadejte klíč předplatného rozhraní API pro automatické návrhy Bingu. Potom zadejte dotaz (zde v příkladu „sail“) do textového pole **Automatické návrhy**. Jak začnete psát, webová stránka se automaticky aktualizuje a zobrazuje výsledky pro automatické návrhy.

```json
{
  "_type": "Suggestions",
  "queryContext": {
    "originalQuery": "sail"
  },
  "suggestionGroups": [
    {
      "name": "Web",
      "searchSuggestions": [
        {
          "url": "https://www.bing.com/cr?IG=30C49D910FAE478288D54A8DBC5D66F1&CID=122B759B00966D02199C7E9001906C30&rd=1&h=vheQSvKZylM3dlX_B9bQ8-hQEsEJo8zDD2y7H1nsBjE&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailor%2bbrinkley%2bcook%26FORM%3dUSBAPI&p=DevEx,5003.1",
          "displayText": "sailor brinkley cook",
          "query": "sailor brinkley cook",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG=30C49D910FAE478288D54A8DBC5D66F1&CID=122B759B00966D02199C7E9001906C30&rd=1&h=EStLqAfxGCa44Ur3jEMXBv-Qp-lXUSFJbkBfnUdKKDg&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailor%2bbrinkley%26FORM%3dUSBAPI&p=DevEx,5004.1",
          "displayText": "sailor brinkley",
          "query": "sailor brinkley",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG=30C49D910FAE478288D54A8DBC5D66F1&CID=122B759B00966D02199C7E9001906C30&rd=1&h=gvtP9TS9NwhajSapY2Se6y1eCbP2fq_GiP2n-cxi6OY&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailrite%26FORM%3dUSBAPI&p=DevEx,5005.1",
          "displayText": "sailrite",
          "query": "sailrite",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG=30C49D910FAE478288D54A8DBC5D66F1&CID=122B759B00966D02199C7E9001906C30&rd=1&h=c0QOA_j6swCZJy9FxqOwke2KslJE7ZRmMooGClAuCpY&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboats%2bfor%2bsale%26FORM%3dUSBAPI&p=DevEx,5006.1",
          "displayText": "sailboats for sale",
          "query": "sailboats for sale",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG=30C49D910FAE478288D54A8DBC5D66F1&CID=122B759B00966D02199C7E9001906C30&rd=1&h=mnMdREUH20SepmHQH1zlh9Hy_w7jpOlZFm3KG2R_BoA&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailing%2banarchy%26FORM%3dUSBAPI&p=DevEx,5007.1",
          "displayText": "sailing anarchy",
          "query": "sailing anarchy",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG=30C49D910FAE478288D54A8DBC5D66F1&CID=122B759B00966D02199C7E9001906C30&rd=1&h=0udadFl0gCTKCp0QmzQTXS3_y08iO8FpwsoKPHPS6kw&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboatdata%26FORM%3dUSBAPI&p=DevEx,5008.1",
          "displayText": "sailboatdata",
          "query": "sailboatdata",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG=30C49D910FAE478288D54A8DBC5D66F1&CID=122B759B00966D02199C7E9001906C30&rd=1&h=BTS0G6AakxntIl9rmbDXtk1n6rQpsZZ99aQ7ClE7dTY&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsail%2bsand%2bpoint%26FORM%3dUSBAPI&p=DevEx,5009.1",
          "displayText": "sail sand point",
          "query": "sail sand point",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG=30C49D910FAE478288D54A8DBC5D66F1&CID=122B759B00966D02199C7E9001906C30&rd=1&h=quBMwmKlGwqC5wAU0K7n416plhWcR8zQCi7r-Fw9Y0w&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailflow%26FORM%3dUSBAPI&p=DevEx,5010.1",
          "displayText": "sailflow",
          "query": "sailflow",
          "searchKind": "WebSearch"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Požadavky na použití a zobrazení](../UseAndDisplayRequirements.md)
