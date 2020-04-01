---
title: 'Kurz: Jak nahrát obrázek pomocí rozhraní API pro vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak nahrát obrázek do Bingu, získat přehledy o něm, zobrazit odpověď.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: scottwhi
ms.openlocfilehash: ecd1ab5e613bb326b65f6aa50f3f85172bc334ac
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477930"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Kurz: Nahrávání obrázků do rozhraní API pro vizuální vyhledávání Bingu

Rozhraní API pro vizuální vyhledávání Bingu umožňuje vyhledávat na webu obrázky podobné těm, které nahrajete. Tento kurz slouží k vytvoření webové aplikace, která může odeslat bitovou kopii do rozhraní API a zobrazit přehledy, které vrátí v rámci webové stránky. Všimněte si, že tato aplikace nedodržuje všechny [požadavky na použití bingu a zobrazení](../bing-web-search/use-display-requirements.md) pro používání rozhraní API.

Úplný zdrojový kód pro tuto ukázku najdete s dalším zpracováním chyb a anotacemi na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html).

Ukázková aplikace předvádí, jak:

> [!div class="checklist"]
> * Nahrání obrázku do rozhraní API pro vizuální vyhledávání Bingu
> * Zobrazení výsledků hledání obrázků ve webové aplikaci
> * Prozkoumejte různé poznatky poskytované rozhraním API

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>Vytvoření a struktura webové stránky

Vytvořte stránku HTML, která odešle obrázek do rozhraní API vizuálního vyhledávání Bingu, obdrží přehledy a zobrazí je. Ve svém oblíbeném editoru nebo IDE vytvořte soubor s názvem "uploaddemo.html". Přidejte do souboru následující základní strukturu HTML:

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>
```

Rozdělte stránku do oddílu požadavku, kde uživatel poskytuje všechny informace požadované pro požadavek a oddíl odpovědi, kde jsou zobrazeny přehledy. Do souboru načíst doplňky přidáte následující `<div>` značky. `<body>` Značka `<hr>` vizuálně odděluje oddíl požadavku od části odpovědi:

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Přidejte `<script>` do `<head>` značky značku, která bude obsahovat JavaScript pro aplikaci:

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>Získání souboru pro nahrávání

Aby uživatel mohl vybrat obrázek, který `<input>` má nahrát, použije `file`aplikace značku s atributem typu nastaveným na . UI musí být jasné, že aplikace používá Bing získat výsledky hledání.

Do souboru `requestSection` `<div>`načtete následující. `<div>` Vstup souboru přijímá jeden soubor libovolného typu obrázku (například .jpg, .gif, .png). Událost `onchange` určuje obslužnou rutinu, která je volána, když uživatel vybere soubor.

Značka `<output>` se používá k zobrazení miniatury vybraného obrázku:

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Vytvoření obslužné rutiny souboru

Vytvořte funkci obslužné rutiny, která může číst v obrázku, který chcete nahrát. Při iterace přes soubory `FileList` v objektu, obslužná rutina by měla ujistěte se, že vybraný soubor je soubor obrázku a že jeho velikost je 1 MB nebo méně. Pokud je obrázek větší, musíte před nahráním zmenšit jeho velikost. Nakonec obslužná rutina zobrazí miniaturu obrázku:

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>Přidání a uložení klíče předplatného

Aplikace vyžaduje klíč předplatného pro volání rozhraní API vizuálního vyhledávání Bingu. V tomto kurzu jej poskytnete v ui. Přidejte `<input>` následující značku (s atributem typu `<body>` nastaveným na text) těsně pod `<output>` značkou souboru:

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

Pomocí obrázku a klíče předplatného můžete volat do vizuálního vyhledávání Bingu a získat přehled o obrázku. V tomto kurzu volání používá výchozí`en-us`trh ( )`moderate`a hodnotu bezpečného vyhledávání ( ).

Tato aplikace má možnost tyto hodnoty změnit. Pod klíč `<div>` předplatného přidejte následující text `<div>`. Aplikace používá `<select>` značku k poskytnutí rozevíracího seznamu pro tržní a bezpečné vyhledávací hodnoty. V obou seznamech se zobrazí výchozí hodnota.

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong SAR(Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>Přidání možností hledání na webovou stránku

Aplikace skryje seznamy v `<div>` sbalitelné, který je řízen odkaz možnosti dotazu. Po klepnutí na odkaz `<div>` Možnosti dotazu se rozbalí, takže můžete zobrazit a upravit možnosti dotazu. Pokud znovu klepnete na `<div>` odkaz Možnosti dotazu, sbalí se a bude skrytý. Následující úryvek zobrazuje obslužnou `onclick` rutinu odkazu možnosti dotazu. Obslužná `<div>` rutina určuje, zda je rozbalený nebo sbalený. Přidejte tuto `<script>` obslužnou rutinu do oddílu. Obslužná rutina `<div>` je používána všemi sbalitelnými oddíly v ukázce.

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>Volání `onclick` obslužné rutiny

Pod možnosti `"Get insights"` `<div>` v těle přidejte následující tlačítko. Tlačítko umožňuje zahájit volání. Po klepnutí na tlačítko se kurzor změní na kurzor čekání na točení a je volána obslužná rutina. `onclick`

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Přidejte obslužnou `onclick` rutinu `handleQuery()` tlačítka do značky. `<script>`

## <a name="handle-the-query"></a>Zpracování dotazu

Obslužná rutina `handleQuery()` zajišťuje, že klíč předplatného je přítomen a je dlouhý 32 znaků a že je vybrán obrázek. Vymaže také všechny přehledy z předchozího dotazu. Poté volá `sendRequest()` funkci, která má volat.

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>Odeslat požadavek na vyhledávání

Funkce `sendRequest()` zformátuje adresu URL koncového bodu, nastaví `Ocp-Apim-Subscription-Key` záhlaví na klíč odběru, připojí binární část obrázku k nahrání, určí obslužnou rutinu odpovědi a provede volání:

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>Získání a zpracování odpovědi rozhraní API

Funkce `handleResponse()` zpracovává odpověď z volání vizuálního vyhledávání Bing. Pokud je volání úspěšné, naparsuje odpověď JSON do jednotlivých značek, které obsahují přehledy. Dále přidá výsledky hledání na stránku. Aplikace pak vytvoří sbalitelné `<div>` pro každou značku spravovat, kolik dat se zobrazí. Přidejte obslužnou rutinu do oddílu. `<script>`

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait cursor set by query insights button
}
```

### <a name="parse-the-response"></a>Parsování odpovědi

Funkce `parseResponse` převede odpověď JSON na objekt slovníku iteracem prostřednictvím `json.tags`.

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];

        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>Vytvoření oddílu značky

Funkce `buildTagSections()` iterace prostřednictvím analyzované Značky JSON a volá `buildDiv()` funkci k sestavení `<div>` pro každou značku. Každá značka je zobrazena jako odkaz. Po kliknutí na odkaz se značka rozbalí a zobrazí přehledy přidružené ke značce. Opětovným kliknutím na odkaz se oddíl zhroutí.

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>Zobrazení výsledků hledání na webové stránce

Funkce `buildDiv()` volá `addDivContent` funkci k vytvoření obsahu každé značky `<div>`sbalitelné .

Obsah značky zahrnuje kód JSON z odpovědi pro značku. Zpočátku jsou zobrazeny pouze prvních 100 znaků JSON, ale můžete kliknout na řetězec JSON zobrazíte všechny JSON. Pokud na něj kliknete znovu, řetězec JSON se sbalí zpět na 100 znaků.

V dalším kroku přidejte typy akcí nalezené ve značce. Pro každý typ akce volejte příslušné funkce a přidejte jeho přehledy:

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>Zobrazení přehledů pro různé akce

Následující funkce zobrazují přehledy pro různé akce. Funkce poskytují obrázek, na který lze kliknout, nebo odkaz, na který lze kliknout a který vás odešle na webovou stránku s dalšími informacemi o obrázku. Tato stránka je hostována buď Bing.com nebo původní web obrázku. V této aplikaci nejsou zobrazena všechna data přehledů. Chcete-li zobrazit všechna pole, která jsou k dispozici pro přehled, podívejte se na odkaz [Obrázky – vizuální vyhledávání.](https://aka.ms/bingvisualsearchreferencedoc)

> [!NOTE]
> Na stránce je nutné zobrazit minimální množství informací o přehledech. Další informace najdete v [tématu Používání rozhraní API pro vyhledávání bingů a jejich zobrazení.](../bing-web-search/use-display-requirements.md)

### <a name="relatedimages-insights"></a>Přehledy relatedimages

Funkce `addRelatedImages()` vytvoří název pro každý z webových stránek, které hostují související obrázek iteraceprostřed seznamu `RelatedImages` akcí a připojení matné `<img>` značky na vnější straně `<div>` pro každý:

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays
        // when the user hovers over the image.

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>Stránkyvčetně přehledů

Funkce `addPagesIncluding()` vytvoří odkaz pro každý z webových stránek, které hostují `PagesIncluding` nahraný obrázek iteraceprostřed seznamu akcí a připojením `<img>` značky na vnější straně `<div>` pro každý:

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>Přehledy souvisejícívyhledávání

Funkce `addRelatedSearches()` vytvoří odkaz pro webové stránky, které hostí obrázek, `RelatedSearches` tím, že `<img>` iterace `<div>` prostřednictvím seznamu akcí a připojení značky na vnější straně pro každého:

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Recepty postřehy

Funkce `addRecipes()` vytvoří odkaz pro každý z receptů vrácených iterace prostřednictvím seznamu `Recipes` akcí a připojení značky `<img>` na vnější straně `<div>` pro každý:

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Nákupní přehledy

Funkce `addShopping()` vytvoří odkaz pro všechny vrácené nákupní výsledky iteraceprostřed seznamu `RelatedImages` `<img>` akcí a `<div>` připojením značky na vnější straně pro každou z nich:

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>Přehledy produktů

Funkce `addProducts()` vytvoří odkaz pro všechny vrácené produkty výsledky iterace prostřednictvím seznamu `Products` akcí a připojení značky `<img>` na vnější `<div>` straně pro každý:

```javascript

    // Display the first 10 related products. Display a clickable image of the
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>TextVýsledky přehledů

Funkce `addTextResult()` zobrazí libovolný text, který byl rozpoznán v obraze:

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

Funkce `addEntity()` zobrazí odkaz, který uživatele přenese na Bing.com kde může získat podrobnosti o typu entity v bitové kopii, pokud byla zjištěna:

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

Funkce `addImageWithWebSearchUrl()` zobrazí obrázek, na `<div>` který lze kliknout, který uživatele přiměje k vyhledávání výsledků v Bing.com:

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>Přidání stylu CSS

Přidejte `<style>` do značky následující oddíl pro `<head>` uspořádání rozložení webové stránky:

```html
        <style>

            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>Další kroky

>[!div class="nextstepaction"]
> [Kurz: Najít podobné obrázky z předchozích vyhledávání pomocí ImageInsightsToken](./tutorial-visual-search-insights-token.md)
