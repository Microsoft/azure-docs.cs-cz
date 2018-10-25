---
title: 'Rychlý start: Project URL Preview, JavaScript'
titlesuffix: Azure Cognitive Services
description: Ukázkový skript, který vám pomůže rychle začít používat rozhraní URL Preview API Bingu pomocí JavaScriptu.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: f36609448819ed197cb92c0bc4d9cc0237fe6df8
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466932"
---
# <a name="quickstart-url-preview-in-javascript"></a>Rychlý start: URL Preview s JavaScriptem 

Následující jednostránková aplikace používá JavaScript k vytvoření služby URL Preview pro web SwiftKey: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Požadavky

Získání přístupového klíče k bezplatné zkušební verzi služby [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-url-preview)

## <a name="code-scenario"></a>Scénář kódu
Následující příklad JavaScriptu vloží objekt vstupního textového pole, do kterého uživatel zadá adresu URL k náhledu.  Když uživatel klikne na tlačítko **Preview** (Náhled), metoda onclick provede přesměrování do `getPreview`, kde kód vygeneruje webový požadavek na koncový bod **UrlPreview**.

Kód vytvoří objekt *XMLHttpRequest*, přidá hlavičku *Ocp-Apim-Subscription-Key* a klíč a odešle požadavek.  Přidá obslužnou rutinu asynchronní události pro zpracování odpovědi.

Pokud se úspěšně vrátí odpověď, obslužná rutina přiřadí text JSON odpovědi k odstavci `demo` na stránce. Ostatní elementy odpovědi se pro účely zobrazení nastaví na následující odstavce.

**Nezpracovaná odpověď JSON**

````
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https://swiftkey.com/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

````

**Zkuste ukázku spustit**.

![Příklad náhledu adresy URL v JavaScriptu](./media/java-script-demo.png)

## <a name="running-the-application"></a>Spouštění aplikace.

Spusťte aplikaci:

1. Hodnotu `YOUR-SUBSCRIPTION-KEY` nahraďte platným přístupovým klíčem pro vaše předplatné.
2. Uložte kód HTML a skript do souboru s příponou .html.
3. Spusťte webovou stránku v prohlížeči.
4. Použijte stávající adresu URL nebo do textového pole zadejte jinou.
5. Klikněte na tlačítko **Preview** (Náhled).

**Zdrojový kód:**

```
<!DOCTYPE html>

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>urlPreview Demo</title>
</head>
<body>
    <h3>URL Preview Demo</h3>

    Page to preview: <input type="url" id="myURL" value="https://swiftkey.com/en">
    <button onclick="getPreview()">Preview</button>

    <p id="demo"></p>
    <br />
    <p id="jsonDesc"></p>
    <p><a id="familyFriendly"></a></p>
    <a id="contentUrl"></a>
    <p />
    <img id="jsonImage" />

    <script>

        var BING_ENDPOINT = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search"; 
        var key = "YOUR-SUBSCRIPTION-KEY";
        var xhr;

        function getPreview() {
            xhr = new XMLHttpRequest();

            var queryUrl = BING_ENDPOINT + "?q=" +
                encodeURIComponent(document.getElementById("myURL").value);
            xhr.open('GET', queryUrl, true);
            xhr.setRequestHeader("Ocp-Apim-Subscription-Key", key);

            xhr.send();

            xhr.addEventListener("readystatechange", processRequest, false);
        }

        function processRequest(e) {

            if (xhr.readyState == 4 && xhr.status == 200) {
                document.getElementById("demo").innerHTML = xhr.responseText;
                var obj = JSON.parse(xhr.responseText);
                document.getElementById("jsonDesc").innerHTML = obj.description;
                document.getElementById("familyFriendly").innerHTML = "Family Friendly: " + obj.isFamilyFriendly;
                document.getElementById("contentUrl").innerHTML = obj.url;
                document.getElementById("contentUrl").href = obj.url;
                document.getElementById("jsonImage").width = 350;
                document.getElementById("jsonImage").src = obj.primaryImageOfPage.contentUrl;

            }

        }

    </script>

</body>
</html>

```

## <a name="next-steps"></a>Další kroky
- [Rychlý start pro jazyk C#](csharp.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start pro Node](node-quickstart.md)
- [Rychlý start pro Python](python-quickstart.md)
