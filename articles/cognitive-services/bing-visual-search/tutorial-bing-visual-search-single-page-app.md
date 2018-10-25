---
title: 'Kurz: Sestavení jednostránkové webové aplikace – Vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Ukazuje, jak používat rozhraní API Bingu pro vizuální vyhledávání Bingu v jednostránkové webové aplikaci.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 10/04/2017
ms.author: bking
ms.openlocfilehash: a63107b86e82bf0bcd89523588414b45e6e21d5a
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465193"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>Kurz: Vizuální vyhledávání v jednostránkové webové aplikaci

API pro vizuální vyhledávání Bingu poskytuje prostředí podobné podrobnostem obrázků zobrazeným na Bing.com/images. Pomocí vizuálního vyhledávání můžete zadat obrázek a získat zpět přehledy o obrázku, například vizuálně podobné obrázky, nákupní zdroje, webové stránky, na kterých se obrázek nachází, a další. 

Tento kurz rozšiřuje jednostránkovou webovou aplikaci z kurzu pro vyhledávání obrázků Bingu (viz [Jednostránková webová aplikace](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)). Úplný zdrojový kód pro zahájení tohoto kurzu najdete ve [zdrojovém kódu jednostránkové webové aplikace](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md). Poslední zdrojový kód v tomto kurzu najdete v kurzu pro [jednostránkovou webovou aplikaci pro vizuální vyhledávání](tutorial-bing-visual-search-single-page-app-source.md).

Probírají se tyto úlohy:

> [!div class="checklist"]
> * Volání rozhraní API pro vizuální vyhledávání Bingu s tokenem přehledů o obrázku
> * Zobrazení podobných obrázků

## <a name="call-bing-visual-search"></a>Volání vizuálního vyhledávání Bingu
Upravte kurz pro vyhledávání obrázků Bingu a přidejte následující kód na konec prvku skriptu na řádku 409. Tento kód volá rozhraní API pro vizuální vyhledávání Bingu a zobrazí výsledky.

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
Přidejte následující kód do objektu `searchItemsRenderer` na řádku 151. Tento kód přidá odkaz **find similar** (najít podobné), který při kliknutí volá funkci `bingVisualSearch`. Funkce jako argument obdrží token imageInsightsToken.

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zdroj jednostránkové webové aplikace pro vizuální vyhledávání](tutorial-bing-visual-search-single-page-app-source.md)
> [Informace o rozhraní API pro vizuální vyhledávání Bingu](https://aka.ms/bingvisualsearchreferencedoc)