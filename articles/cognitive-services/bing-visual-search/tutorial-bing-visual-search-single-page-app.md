---
title: " Vytvoření webové aplikace jednostránkové – vizuální vyhledávání Bingu"
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak integrovat Visual API Bingu pro vyhledávání do jednostránkovou webovou aplikaci.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 10/04/2017
ms.author: aahi
ms.openlocfilehash: cf8525d4cc829805532210bf09e9ea9da240405d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857745"
---
# <a name="create-a-visual-search-single-page-web-app"></a>Vytvořit pro vizuální vyhledávání jednostránkovou webovou aplikaci 

Rozhraní API pro vizuální vyhledávání Bingu poskytuje prostředí podobné podrobnostem o obrázcích zobrazeným na Bing.com/images. Pomocí vizuálního vyhledávání můžete zadat obrázek a získat zpět přehledy o obrázku, například vizuálně podobné obrázky, nákupní zdroje, webové stránky, na kterých se obrázek nachází, a další. 

Tento článek vysvětluje, jak rozšířit jednostránkovou webovou aplikaci pro rozhraní API Bingu pro vyhledávání obrázků. K zobrazení tohoto kurzu nebo získat zdrojový kód se tady použít, najdete v článku [kurzu: Vytvoření jednostránkovou aplikaci pro rozhraní API Bingu pro vyhledávání obrázků](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md). 

Úplný zdrojový kód pro tuto aplikaci (po rozšíření ho na použití Bing API pro vizuální vyhledávání), je k dispozici na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Volání rozhraní API vizuální vyhledávání Bingu a zpracovat odpověď

Upravit kurzu Bingu pro vyhledávání obrázků a přidejte následující kód do konce `<script>` – element (a před ukončením `</script>` značky). Následující kód zpracovává odpověď z rozhraní API pro vizuální vyhledávání, Iteruje přes výsledky a zobrazí je.

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

Následující kód odešle požadavek hledání rozhraní API pomocí naslouchací proces událostí volání `handleVisualSearchResponse()`.


```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;       
    
    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Zachycení tokenu přehledů

Přidejte následující kód do `searchItemsRenderer` objektu. Tento kód přidá odkaz **find similar** (najít podobné), který při kliknutí volá funkci `bingVisualSearch`. Funkce jako argument obdrží token imageInsightsToken.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Zobrazení podobných obrázků

Přidejte následující kód HTML na řádek 601. Tento kód využívající značky přidá prvek sloužící k zobrazení výsledků volání rozhraní API pro vizuální vyhledávání Bingu.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Když jsou všechny prvky kódu JavaScriptu a HTML na místě, zobrazují se výsledky hledání s odkazem **find similar** (najít podobné). Kliknutím na odkaz vyplníte sekci **Similar** (Podobné) obrázky, které se podobají vybranému obrázku. Možná budete muset sekci **Similar** rozbalit, aby se obrázky zobrazily.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Oříznutí a změna nahrání image](tutorial-visual-search-crop-area-results.md)
