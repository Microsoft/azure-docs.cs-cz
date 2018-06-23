---
title: Hledání bitové kopie Bingu jednostránkovou webovou aplikaci | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Ukazuje, jak používat rozhraní API služby Bing Image Search v jednostránkovou webovou aplikaci.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-brapel
ms.openlocfilehash: 303d7745167d2ea25fda083ed99881ac4e0a7ec7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343283"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>Kurz: Visual vyhledávání jednostránkové webové aplikace

Rozhraní API vyhledávání Visual Bing poskytuje podobné zobrazený na Bing.com/images podrobnosti bitové kopie. S Visual Search můžete zadat bitovou kopii a získat zpět přehledy o bitovou kopii jako vizuálně podobné bitové kopie, nákupní zdroje, webové stránky, které zahrnují bitovou kopii a další. 

V tomto kurzu rozšiřuje jednostránkové webové aplikace z tohoto kurzu vyhledávání bitové kopie Bingu (v tématu [jednostránkovou webovou aplikaci](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)). Úplný zdrojový kód do začátku tohoto kurzu, najdete v části [jednostránkovou webovou aplikaci (zdrojový kód)](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md). Poslední zdrojový kód v tomto kurzu, najdete v části [Visual vyhledávání jednostránkovou webovou aplikaci](tutorial-bing-visual-search-single-page-app-source.md).

Úlohy popsané jsou:

> [!div class="checklist"]
> * Volání rozhraní API vyhledávání Visual Bing s token Statistika bitové kopie
> * Podobně jako zobrazení obrázků

## <a name="call-bing-visual-search"></a>Volání hledání Visual Bing
Upravit kurz Search bitové kopie Bingu a přidejte následující kód do konce element script na řádku 409. Tento kód zavolá rozhraní API služby Bing Visual vyhledávání a zobrazí výsledky.

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

## <a name="capture-insights-token"></a>Zaznamenat Statistika tokenu
Přidejte následující kód do `searchItemsRenderer` objektu na řádku 151. Tento kód přidá **vyhledejte podobné** odkaz, který volá `bingVisualSearch` funkce při kliknutí na. Funkce imageInsightsToken přijímá jako argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Podobně jako zobrazení obrázků
Na řádku 601 přidejte následující kód HTML. Tento kód značek přidá element slouží k zobrazení výsledků volání rozhraní API vyhledávání Visual Bing.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Se všemi nový kód JavaScript a prvků HTML na místě, se zobrazí výsledky hledání s **vyhledejte podobné** odkaz. Klikněte na odkaz k naplnění **podobá** část s obrázky, které jsou podobné jste vybrali. Možná budete muset Rozbalit **podobá** části zobrazíte bitové kopie.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Visual zdroj vyhledávání jednostránkovou webovou aplikaci](tutorial-bing-visual-search-single-page-app-source.md)
> [Bing Visual vyhledávání API – referenční informace](https://aka.ms/bingvisualsearchreferencedoc)