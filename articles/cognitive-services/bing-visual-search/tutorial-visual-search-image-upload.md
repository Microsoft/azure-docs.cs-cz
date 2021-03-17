---
title: 'Kurz: Nahrání obrázku pomocí rozhraní API pro vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak nahrát obrázek do Bingu, získat o něm přehledy, zobrazit odpověď.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: scottwhi
ms.custom: devx-track-js
ms.openlocfilehash: 96a4b13d11e40e24e78d3aed8dfebcc88b41c525
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486874"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Kurz: nahrání obrázků do rozhraní API pro vizuální vyhledávání Bingu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Rozhraní API pro vizuální vyhledávání Bingu vám umožňuje hledat na webu obrázky podobné těm, které nahráváte. Pomocí tohoto kurzu můžete vytvořit webovou aplikaci, která může odeslat obrázek do rozhraní API a zobrazit přehledy, které vrátí na webové stránce. Všimněte si, že tato aplikace není v souladu se všemi [požadavky na použití a zobrazení Bingu](../bing-web-search/use-display-requirements.md) pro použití rozhraní API.

Úplný zdrojový kód pro tuto ukázku najdete v části Další zpracování chyb a poznámky na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html).

Ukázková aplikace předvádí, jak:

> [!div class="checklist"]
> * Nahrání obrázku do rozhraní API pro vizuální vyhledávání Bingu
> * Zobrazení výsledků hledání obrázků ve webové aplikaci
> * Prozkoumejte různé přehledy poskytované rozhraním API

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>Vytvoření a strukturování webové stránky

Vytvoří stránku HTML, která pošle obrázek do rozhraní API pro vizuální vyhledávání Bingu, přijme přehledy a zobrazí je. V oblíbených editorech nebo v integrovaném vývojovém prostředí vytvořte soubor s názvem "uploaddemo.html". Do souboru přidejte následující základní strukturu HTML:

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

Rozdělte stránku do oddílu žádosti, kde uživatel poskytne všechny informace požadované pro požadavek, a část odpovědi, kde jsou přehledy zobrazené. Přidejte následující `<div>` značky do `<body>` . `<hr>`Značka vizuálně odděluje oddíl žádosti z části odpověď:

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Přidejte `<script>` značku ke `<head>` značce, která bude obsahovat JavaScript pro aplikaci:

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>Získat soubor pro odeslání

Aby uživatel mohl vybrat obrázek k nahrání, aplikace použije `<input>` značku s atributem type nastaveným na `file` . Uživatelské rozhraní musí být jasné, že aplikace používá Bing k získání výsledků hledání.

Přidejte následující `<div>` do `requestSection` `<div>` . Vstup souboru přijímá jeden soubor libovolného typu obrázku (například .jpg, .gif, .png). Událost `onchange` určuje obslužnou rutinu, která je volána, když uživatel vybere soubor.

`<output>`Značka se používá k zobrazení Miniatury vybrané Image:

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Vytvoření obslužné rutiny souboru

Vytvořte funkci obslužné rutiny, která může být načtena v obrázku, který chcete nahrát. Při iteraci mezi soubory v `FileList` objektu by měla obslužná rutina ověřit, že vybraný soubor je soubor obrázku a že jeho velikost je 1 MB nebo méně. Pokud je obrázek větší, je před nahráním nutné zmenšit jeho velikost. Nakonec obslužná rutina zobrazí miniaturu obrázku:

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

Aplikace vyžaduje pro volání rozhraní API pro vizuální vyhledávání Bingu klíč předplatného. V tomto kurzu ho poskytnete v uživatelském rozhraní. Přidejte následující `<input>` značku (s atributem type nastaveným na text) na `<body>` hned pod `<output>` značku souboru:

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

Pomocí Image a klíče předplatného můžete uskutečnit volání Vizuální vyhledávání Bingu, abyste získali přehled o imagi. V tomto kurzu volání používá výchozí trh ( `en-us` ) a bezpečnou vyhledávací hodnotu ( `moderate` ).

Tato aplikace má možnost tyto hodnoty změnit. Níže přidejte následující `<div>` klíč předplatného `<div>` . Aplikace používá `<select>` značku k poskytnutí rozevíracího seznamu pro hodnoty na trhu a bezpečné vyhledávání. V obou seznamech se zobrazí výchozí hodnota.

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

## <a name="add-search-options-to-the-webpage"></a>Přidat možnosti hledání na webovou stránku

Aplikace skryje seznamy v sbalitelné `<div>` , které jsou ovládány odkazem možnosti dotazu. Když kliknete na odkaz Možnosti dotazu, rozšíří se, `<div>` aby bylo možné zobrazit a upravit možnosti dotazu. Pokud znovu kliknete na odkaz Možnosti dotazu, `<div>` sbalení a bude skryto. Následující fragment kódu ukazuje `onclick` obslužnou rutinu odkazu možnosti dotazu. Obslužná rutina řídí, zda `<div>` je rozbalen nebo sbalen. Přidejte tuto obslužnou rutinu do `<script>` oddílu. Obslužná rutina se používá všemi sbalitelnými `<div>` oddíly v ukázce.

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

Přidejte následující `"Get insights"` tlačítko pod možnostmi `<div>` v těle. Tlačítko umožňuje zahájit volání. Po kliknutí na tlačítko se kurzor změní na rotující kurzor a `onclick` obslužná rutina je volána.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Přidejte `onclick` do značky obslužnou rutinu tlačítka `handleQuery()` `<script>` .

## <a name="handle-the-query"></a>Zpracování dotazu

Obslužná rutina `handleQuery()` zajišťuje, že klíč předplatného je přítomen a je 32 znaků a že je vybraný obrázek. Vymaže také všechny přehledy z předchozího dotazu. Následně volá `sendRequest()` funkci, která provede volání.

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

## <a name="send-the-search-request"></a>Odeslat požadavek hledání

`sendRequest()`Funkce formátuje adresu URL koncového bodu, nastaví `Ocp-Apim-Subscription-Key` hlavičku na klíč předplatného, připojí binární soubor obrázku, který se má nahrát, určí obslužnou rutinu odpovědi a provede volání:

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

`handleResponse()`Funkce zpracovává odpověď od volání vizuální vyhledávání Bingu. Pokud je volání úspěšné, naparsuje odpověď JSON do jednotlivých značek, které obsahují přehledy. V dalším kroku přidá výsledky hledání na stránku. Aplikace potom vytvoří sbalitelnou `<div>` pro každou značku, která bude spravovat, kolik dat se zobrazí. Přidejte obslužnou rutinu do `<script>` oddílu.

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

`parseResponse`Funkce převede odpověď JSON na objekt Dictionary pomocí iterace `json.tags` .

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

### <a name="build-a-tag-section"></a>Sestavení oddílu značky

`buildTagSections()`Funkce provádí iteraci analyzovaných značek JSON a volá `buildDiv()` funkci pro sestavení `<div>` pro každou značku. Každá značka se zobrazí jako odkaz. Po kliknutí na odkaz se značka rozbalí a zobrazí se přehledy spojené se značkou. Opakovaným kliknutím na odkaz dojde k sbalení oddílu.

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

`buildDiv()`Funkce volá `addDivContent` funkci pro sestavení obsahu každé sbalitelné značky `<div>` .

Obsah značky zahrnuje kód JSON z odpovědi pro značku. Zpočátku jsou zobrazeny pouze první 100 znaky JSON, ale můžete kliknout na řetězec JSON a zobrazit tak všechny JSON. Pokud na něj kliknete znovu, řetězec JSON se sbalí zpět na 100 znaků.

V dalším kroku přidejte typy akcí nalezené ve značce. Pro každý typ akce zavolejte příslušné funkce a přidejte své přehledy:

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

## <a name="display-insights-for-different-actions"></a>Zobrazit přehledy pro různé akce

Následující funkce zobrazují přehledy o různých akcích. Funkce poskytují obrázek umožňující kliknutí nebo odkaz, který vás pošle na webovou stránku s dalšími informacemi o imagi. Tato stránka je buď hostovaná pomocí Bing.com, nebo původního webu image. V této aplikaci se nezobrazí všechna data Insights. Pokud chcete zobrazit všechna pole, která jsou k dispozici pro přehled, přečtěte si odkaz [obrázky – vizuální vyhledávání](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) .

> [!NOTE]
> Existuje minimální množství přehledných informací, které je třeba zobrazit na stránce. Další informace najdete v tématu věnovaném [použití rozhraní API vyhledávání Bingu a zobrazení](../bing-web-search/use-display-requirements.md) .

### <a name="relatedimages-insights"></a>RelatedImages přehledy

`addRelatedImages()`Funkce vytvoří název pro každý web, který hostuje související image, tak, že projde seznam `RelatedImages` akcí a připojí `<img>` značku k vnějšímu `<div>` pro každý z nich:

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

### <a name="pagesincluding-insights"></a>PagesIncluding přehledy

`addPagesIncluding()`Funkce vytvoří odkaz pro každý web hostující nahranou image tak, že projde seznam `PagesIncluding` akcí a připojí `<img>` značku k vnějšímu `<div>` pro každý z nich:

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

### <a name="relatedsearches-insights"></a>RelatedSearches přehledy

`addRelatedSearches()`Funkce vytvoří odkaz na web, který je hostitelem obrázku, tak, že projde seznam `RelatedSearches` akcí a připojí `<img>` značku k vnějšímu `<div>` pro každý z nich:

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

### <a name="recipes-insights"></a>Přehledy recepty

`addRecipes()`Funkce vytvoří odkaz pro každý recept vrácený při iteraci seznamem `Recipes` akcí a připojením `<img>` značky k vnějšímu `<div>` pro každý z nich:

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

### <a name="shopping-insights"></a>Přehledy nákupu

`addShopping()`Funkce vytvoří odkaz pro všechny vrácené výsledky nákupu tak, že projde seznam `RelatedImages` akcí a připojí `<img>` značku k vnějšímu `<div>` :

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

`addProducts()`Funkce vytvoří odkaz na výsledky vrácených produktů pomocí iterace v seznamu `Products` akcí a připojením `<img>` značky k vnějšímu `<div>` pro každý z nich:

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

### <a name="textresult-insights"></a>TextResult přehledy

`addTextResult()`Funkce zobrazí veškerý text, který byl rozpoznán v obrázku:

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

`addEntity()`Funkce zobrazí odkaz, který převezme uživatele, aby Bing.com, kde mohou získat podrobnosti o typu entity v imagi, pokud byl nalezen:

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

`addImageWithWebSearchUrl()`Funkce zobrazí obrázek s `<div>` možností kliknutí, který uživateli provede hledání výsledků na Bing.com:

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

## <a name="add-a-css-style"></a>Přidat styl CSS

Přidejte následující `<style>` oddíl do `<head>` značky pro uspořádání rozložení webové stránky:

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
> [Kurz: Vyhledání podobných imagí z předchozích hledání pomocí ImageInsightsToken](./tutorial-visual-search-insights-token.md)