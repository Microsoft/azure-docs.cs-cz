---
title: Rychlý start JavaScript pro náhled adresy URL projektu - kognitivní služby Microsoft | Microsoft Docs
description: Ukázka skriptu rychle začít pomocí rozhraní API služby Bing adresu URL náhledu v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9041a88a292fb2dabead69195ebc3074e2ecf486
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343245"
---
# <a name="url-preview-in-javascript"></a>Náhled adresy URL v jazyce JavaScript 

Následující jednostránkové aplikace používá JavaScript pro vytvoření náhledu adresu URL pro web SwiftKey: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Požadavky

Získat přístupový klíč pro bezplatné zkušební verze [kognitivní Labs služby](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Scénář kódu
Následující příklad javascript obsahuje objekt vstupní textové pole, kde uživatel zadá adresu URL pro náhled.  Když uživatel klikne **Preview** tlačítko onclick metoda směrování k `getPreview` kde kód generuje webový požadavek na **UrlPreview** koncový bod.

Kód vytvoří *XMLHttpRequest*, přidá *Ocp-Apim-Subscription-Key* záhlaví a klíč a odešle žádost.  Přidá asynchronní obslužnou rutinu pro zpracování odpovědi.

Pokud odpověď se vrátí úspěšně, obslužná rutina přiřadí text JSON odpovědi na `demo` odstavce na stránce. Další prvky odpovědi jsou nastaveny v následujících odstavcích pro zobrazení.

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

**Spuštěné demo**

![Příklad JavaScript Url Preview](./media/java-script-demo.png)

## <a name="running-the-application"></a>Spouštění aplikace.

Ke spuštění aplikace:

1. Nahraďte `YOUR-SUBSCRIPTION-KEY` hodnotu s platnou přístupový klíč pro vaše předplatné.
2. Uložte do souboru s příponou .html HTML a skript.
3. Spusťte webovou stránku v prohlížeči.
4. Použít existující adresu URL nebo do textového pole zadejte jiné.
5. Klikněte **Preview** tlačítko.

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

## <a name="next-steps"></a>Další postup
- [Rychlý start C#](csharp.md)
- [Rychlý start Java](java-quickstart.md)
- [Rychlé spuštění uzlu](node-quickstart.md)
- [Rychlý start Python](python-quickstart.md)