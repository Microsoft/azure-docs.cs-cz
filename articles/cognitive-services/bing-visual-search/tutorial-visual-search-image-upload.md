---
title: 'Kurz: Jak nahrát obrázek – vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Rozděluje proces nahrávání obrázku do Bingu, aby o něm získal přehledy, a potom parsuje a zobrazí odpověď.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 07/10/2018
ms.author: scottwhi
ms.openlocfilehash: aa221569cec6df298db4a90560ac7ea0992dd120
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54318724"
---
# <a name="tutorial-uploading-images-to-the-bing-visual-search-api"></a>Kurz: Nahrávání imagí do rozhraní API vizuální vyhledávání Bingu

Rozhraní API vizuální vyhledávání Bingu umožňuje hledat na webu pro podobný těch, které nahrajete Image. Pomocí tohoto kurzu vytvořte webovou aplikaci, která můžete odeslat image do rozhraní API a zobrazují poznatky, které vrátí na webové stránce. Všimněte si, že tuto aplikaci všem nedrží [požadavky na zobrazení a použití Bingu](./use-and-display-requirements.md) používání rozhraní API.

Úplný zdrojový kód pro tuto ukázku můžete najít další chyba zpracování a poznámky na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html).

Ukázková aplikace předvádí, jak:

> [!div class="checklist"]
> * Nahrání obrázku do rozhraní API vizuální vyhledávání Bingu
> * Zobrazit výsledky hledání obrázků ve webové aplikaci
> * Prozkoumejte různé přehledy poskytovaný rozhraním API

## <a name="prerequisites"></a>Požadavky 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>Vytvořit a uspořádat webovou stránku

Vytvořte stránku HTML, která odesílá Bingu bitovou kopii a získá zpět insights a zobrazí je. V oblíbeném editoru nebo prostředí IDE, vytvořte soubor s názvem, `uploaddemo.html`. Přidejte do souboru následující základní strukturu HTML.

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

Rozdělte oddíl žádost, ve kterém uživatel zadá všechny informace potřebné k odeslání požadavku, a odpověď části, kde se zobrazují informace na stránce. Přidejte následující `<div>` klíčová slova `<body>`. `<hr>` Značky vizuálně rozděluje části žádosti z části odpovědi.

```html
<div id="requestSection"></div>
<hr />      
<div id="responseSection"></div>
```

Přidat `<script>` značku na `<head>` značka, které obsahují JavaScript pro aplikace.

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>Získat soubor k nahrání

Umožňuje uživateli vybrat bitovou kopii pro nahrání aplikace používá `<input>` značku s atributem typu nastavena na `file`. Uživatelské rozhraní je potřeba usnadňují vymazat, kterou aplikace používá k získání požadovaných výsledků vyhledávání Bingu. 

Přidejte následující `<div>` k requestSection div. Vstup souboru přijímá jeden soubor libovolného typu obrázku (například .jpg, .gif, .png). Událost `onchange` určuje obslužnou rutinu, která je volána, když uživatel vybere soubor.

`<output>` Značka se používá k zobrazení miniatury vybrané bitové kopie.


```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Vytvořte obslužnou rutinu souboru 

Vytvoření funkce obslužná rutina, kterou si můžete přečíst v image, kterou chcete nahrát. Při procházení souborů `FileList` objektu obslužnou rutinu by měl Ujistěte se, že vybraný soubor je soubor obrázku a že jeho velikost je 1 MB nebo méně. Pokud na obrázku je větší, musíte před jejich nahráním snížit jeho velikost. Obslužná rutina se nakonec zobrazí miniaturu obrázku.

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

## <a name="add-and-store-a-subscription-key"></a>Přidejte a uložte klíč předplatného

Aplikace musí volat rozhraní API vizuální vyhledávání Bingu klíč předplatného. Pro účely tohoto kurzu budete ho zadat v uživatelském rozhraní. Přidejte následující `<input>` značku (s atributem typu nastaven na text) na `<body>` pod souboru `<output>` značky.

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

Na obrázku a klíč předplatného můžete provést volání pro vizuální vyhledávání Bingu k získání přehledů o imagi. V tomto kurzu se použije volání na trhu výchozí (`en-us`) a hodnota bezpečné vyhledávání (`moderate`).

Tato aplikace má možnost tyto hodnoty změnit. Přidejte následující `<div>` níže div. klíčů předplatného Aplikace používá `<select>` značky, které poskytují rozevíracího seznamu pro vstup na trh a bezpečné vyhledávání hodnoty. Oba seznamy zobrazit výchozí hodnotu.

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
                <option value="zh-HK">Hong Kong (Traditional Chinese)</option>
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

## <a name="add-search-options-to-the-webpage"></a>Přidat možnosti vyhledávání do webové stránky 

Aplikace se skryje možnost seznamů do sbalitelné div, kterým je řízeno odkaz Možnosti dotazu. Po kliknutí na odkaz na další možnosti dotazu se div rozbalí, abyste viděli a mohli upravit možnosti dotazu. Pokud kliknete na odkaz znovu, div se sbalí a zůstane skrytý. Následující příklad ukazuje obslužnou rutinu onclick odkazu na možnosti dotazu. Obslužná rutina řídí, jestli je div rozbalený nebo sbalený. Přidání této obslužné rutiny na `<script>` oddílu. Obslužnou rutinu používají všechny sbalitelné divy v ukázce.

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

## <a name="call-the-onclick-handler"></a>Volání popisovač události

Přidejte následující `"Get insights"` pod možností div v textu. Tlačítko umožňuje iniciujte hovor. Při kliknutí na tlačítko kurzoru se změní na kurzor pro čekání pokryjte a se nazývá popisovač události.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Přidání rutiny při klepnutí na tlačítko, `handleQuery()` k `<script>` značky. 

## <a name="handle-the-query"></a>Zpracování dotazu

Obslužná rutina `handleQuery()` zajišťuje, že je k dispozici a je 32 znaků. klíč předplatného, a že image byla vybrána. Vymaže také všechny přehledy z předchozího dotazu. Potom se volá `sendRequest()` funkci volání.

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

## <a name="send-the-search-request"></a>Poslat žádost o vyhledávání

`sendRequest()` Funkce formáty adresu URL koncového bodu, nastaví záhlaví Ocp-Apim-Subscription-Key klíč předplatného, připojí binární soubor k nahrání obrázku, určuje obslužné rutiny a provádí volání. 

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

`handleResponse()` Funkce zpracuje odpověď z volání pro vizuální vyhledávání Bingu. Pokud je volání úspěšné, naparsuje odpověď JSON do jednotlivých značek, které obsahují přehledy. V dalším kroku na stránku přidá výsledky hledání. Aplikace pak vytvoří sbalitelnou div pro jednotlivé značky ke správě, kolik dat se zobrazí. Přidání obslužné rutiny `<script>` oddílu.

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

    document.body.style.cursor = 'default'; // reset the wait curor set by query insights button
}
```

### <a name="parse-the-response"></a>Analyzovat odpověď

`parseResponse` Funkce převede odpověď JSON na objekt slovníku iterací `json.tags`.

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

### <a name="build-a-tag-section"></a>Sekce značek sestavení

`buildTagSections()` Prochází analyzovaný značkami JSON a volání funkce `buildDiv()` funkce k vytvoření div pro jednotlivé značky. Každá značka se zobrazí jako odkaz. Po kliknutí na odkaz rozšíří značka zobrazuje přehledy související se značkou. Kliknutím na odkaz způsobí, že v části sbalte.

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

## <a name="display-the-search-results-in-the-webpage"></a>Zobrazit výsledky vyhledávání do webové stránky

`buildDiv()` Funkce volá funkci addDivContent k sestavení obsahu sbalitelné jednotlivé značky div.

Obsah značky zahrnuje kód JSON z odpovědi pro značku. Standardně se zobrazuje jenom prvních 100 znaků ve formátu JSON, můžete ale na řetězec JSON kliknout a zobrazit ho celý. Pokud na něj kliknete znovu, řetězec JSON se sbalí zpět na 100 znaků.

V dalším kroku přidejte typy akcí nalezené ve značce. Pro každý typ akce volejte odpovídající funkce přidejte jeho insights.

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

## <a name="display-insights-for-different-actions"></a>Zobrazit přehledy pro různé akce.

Následující funkce zobrazují poznatky pro provedení různých akcí. Funkce poskytnout kliknout, čímž obrázek nebo prokliknutelný odkaz, který odešle na webovou stránku s dalšími informacemi o imagi. Tato stránka je buď hostitelem Bing.com nebo na obrázku původním webu. Ne všechna data přehledy se zobrazí v této aplikaci. Všechna pole, které jsou k dispozici pro určitý přehled najdete v tématu [referenční příručka Visual pro vyhledávání Bingu](https://aka.ms/bingvisualsearchreferencedoc).

> [!NOTE]
> Je minimální množství přehled o informace, které je třeba zobrazit na stránce. Najdete v článku [požadavky na zobrazení a použití Bingu](./use-and-display-requirements.md) Další informace.

### <a name="relatedimages-insights"></a>RelatedImages insights

`addRelatedImages()` Funkce vytvoří název pro každý z webů pro hostování související image podle iterace v rámci seznamu `RelatedImages` akce a připojení `<img>` značku na vnější `<div>` pro každý.

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

### <a name="pagesincluding-insights"></a>PagesIncluding insights

`addPagesIncluding()` Funkce vytvoří odkaz pro každou z webů pro hostování této odeslané image podle iterace v rámci seznamu `PagesIncluding` akce a připojení `<img>` značku na vnější `<div>` pro každý.

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

### <a name="relatedsearches-insights"></a>RelatedSearches insights

`addRelatedSearches()` Funkce vytvoří odkaz pro hostování obrázků, iterací seznam webů `RelatedSearches` akce a připojení `<img>` značku na vnější `<div>` pro každý.

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

### <a name="recipes-insights"></a>Recepty insights

`addRecipes()` Funkce vytvoří odkaz pro každý vrácený iterace v rámci seznamu recepty `Recipes` akce a připojení `<img>` značku na vnější `<div>` pro každý.

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

### <a name="shopping-insights"></a>Nákupní insights

`addShopping()` Funkce vytvoří odkaz pro všechny vrácené nákupní výsledky iterace v rámci seznamu `RelatedImages` akce a připojení `<img>` značku na vnější `<div>` pro každý.

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

### <a name="products-insights"></a>Přehled produktů

`addProducts()` Funkce vytvoří odkaz pro všechny vrácené výsledky produktů podle iterace v rámci seznamu `Products` akce a připojení `<img>` značku na vnější `<div>` pro každý.

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

### <a name="textresult-insights"></a>TextResult insights

`addTextResult()` Funkce se zobrazí text, který byl rozpoznán v bitové kopii.

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

`addEntity()` Funkce se zobrazí odkaz, který uživatel přejde na Bing.com kde může získat informace o typu entity na obrázku, pokud některý byla zjištěna.

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

`addImageWithWebSearchUrl()` Funkce zobrazí kliknout, čímž obrázek div, kterým uživatel přejde na výsledky hledání v Bing.com. 

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

## <a name="add-a-css-style"></a>Přidat stylu CSS

Přidejte následující `<style>` části `<head>` značek k uspořádání rozložení webové stránky.

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

## <a name="next-steps"></a>Další postup

* [Kurz: Najít podobné obrázky z předchozí vyhledávání pomocí ImageInsightsToken](./tutorial-visual-search-insights-token.md).
