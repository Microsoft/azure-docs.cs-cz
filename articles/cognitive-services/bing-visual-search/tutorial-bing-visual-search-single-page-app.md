---
title: " Vytvoření jednostránkové webové aplikace – Vizuální vyhledávání Bingu"
titleSuffix: Azure Cognitive Services
description: Naučte se integrovat rozhraní API pro vizuální vyhledávání Bingu do jednostránkové webové aplikace.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b9458f6080759f0948d103f7fe3d131f6a4c4e37
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092717"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>Kurz: Vytvoření webové aplikace Vizuální vyhledávání jednostránkového stránkování

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Rozhraní API pro vizuální vyhledávání Bingu vrátí přehledy pro obrázek. Můžete buď nahrát obrázek, nebo zadat adresu URL k jednomu. Přehledy jsou vizuálně podobné obrázky, nákupní zdroje, webové stránky, které obsahují obrázek, a další. Přehledy vrácené rozhraní API pro vizuální vyhledávání Bingu jsou podobné těm, které jsou zobrazené na Bing.com/images.

V tomto kurzu se dozvíte, jak můžete na rozhraní API Bingu pro vyhledávání obrázků zvětšit jednostránkové webovou aplikaci. Pokud chcete zobrazit tento kurz nebo získat zde používaný zdrojový kód, přečtěte si téma [kurz: vytvoření jednostránkové aplikace pro rozhraní API Bingu pro vyhledávání obrázků](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md).

Úplný zdrojový kód pro tuto aplikaci (po rozšíření IT na použití rozhraní API pro vizuální vyhledávání Bingu) je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Volání rozhraní API pro vizuální vyhledávání Bingu a zpracování odpovědi

Upravte kurz Vyhledávání obrázků Bingu a přidejte následující kód na konec `<script>` elementu (a před uzavírací `</script>` značku). Následující kód zpracovává odezvu vizuálního hledání z rozhraní API, prochází výsledky a zobrazí je:

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

Následující kód odešle požadavek na hledání do rozhraní API pomocí naslouchacího procesu události pro volání `handleVisualSearchResponse()` :

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

Do objektu přidejte následující kód `searchItemsRenderer` . Tento kód přidá odkaz **find similar** (najít podobné), který při kliknutí volá funkci `bingVisualSearch`. Funkce přijímá `imageInsightsToken` jako argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Zobrazení podobných obrázků

Přidejte následující kód HTML na řádek 601. Tento kód přidá element pro zobrazení výsledků volání rozhraní API pro vizuální vyhledávání Bingu:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Když jsou všechny prvky kódu JavaScriptu a HTML na místě, zobrazují se výsledky hledání s odkazem **find similar** (najít podobné). Kliknutím na odkaz vyplníte sekci **Similar** (Podobné) obrázky, které se podobají vybranému obrázku. Možná budete muset sekci **Similar** rozbalit, aby se obrázky zobrazily.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: oříznutí obrázku v sadě Vizuální vyhledávání Bingu SDK pro jazyk C #](tutorial-visual-search-crop-area-results.md)
