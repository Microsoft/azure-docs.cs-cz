---
title: " Vytvoření jednostránkové webové aplikace – vizuální vyhledávání Bingu"
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak integrovat rozhraní API vizuálního vyhledávání Bingu do jednostránkové webové aplikace.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.openlocfilehash: 83cdaecfb819fb1f4677b051f87e23e0e03daef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370104"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>Kurz: Vytvoření jednostránkové webové aplikace vizuálního vyhledávání

Rozhraní API vizuálního vyhledávání Bingu vrací přehledy pro bitovou kopii. Můžete nahrát obrázek nebo zadat adresu URL. Přehledy jsou vizuálně podobné obrázky, nákupní zdroje, webové stránky, které obrázek obsahují, a další. Přehledy vrácené rozhraním API pro vizuální vyhledávání Bingu jsou podobné těm, které jsou zobrazeny na Bing.com/images.

Tento kurz vysvětluje, jak rozšířit jednostránkovou webovou aplikaci pro rozhraní API pro vyhledávání obrázků Bingu. Chcete-li zobrazit tento kurz nebo získat zdrojový kód použitý zde, [přečtěte si informace o tom, že můžete vytvořit jednostránkovou aplikaci pro rozhraní API pro vyhledávání obrázků Bingu](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md).

Úplný zdrojový kód pro tuto aplikaci (po rozšíření na použití rozhraní API pro vizuální vyhledávání Bing) je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Volání rozhraní API vizuálního vyhledávání Bingu a zpracování odpovědi

Upravte kurz hledání obrázků Bing a přidejte `<script>` následující kód na `</script>` konec prvku (a před uzavírací značku). Následující kód zpracovává vizuální odpověď vyhledávání z rozhraní API, iterates prostřednictvím výsledků a zobrazí je:

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

Následující kód odešle požadavek na vyhledávání do rozhraní `handleVisualSearchResponse()`API pomocí naslouchací proces události k volání :

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

Přidejte do objektu `searchItemsRenderer` následující kód. Tento kód přidá odkaz **find similar** (najít podobné), který při kliknutí volá funkci `bingVisualSearch`. Funkce obdrží `imageInsightsToken` jako argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Zobrazení podobných obrázků

Přidejte následující kód HTML na řádek 601. Tento kód značek přidá prvek pro zobrazení výsledků volání rozhraní API vizuálního vyhledávání Bingu:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Když jsou všechny prvky kódu JavaScriptu a HTML na místě, zobrazují se výsledky hledání s odkazem **find similar** (najít podobné). Kliknutím na odkaz vyplníte sekci **Similar** (Podobné) obrázky, které se podobají vybranému obrázku. Možná budete muset sekci **Similar** rozbalit, aby se obrázky zobrazily.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Oříznutí obrázku pomocí sady SDK vizuálního vyhledávání Bingu pro C #](tutorial-visual-search-crop-area-results.md)
