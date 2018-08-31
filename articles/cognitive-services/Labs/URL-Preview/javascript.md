---
title: Rychlý start jazyka JavaScript pro náhled adresy URL projektu – Microsoft Cognitive Services | Dokumentace Microsoftu
description: Ukázkový skript rychle začít pomocí rozhraní API Bingu pro náhled adresy URL ve službě Microsoft Cognitive Services v Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: dda6f7c105dfbadc3c22f0c008aa8759fe12fa03
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301348"
---
# <a name="url-preview-in-javascript"></a>Náhled adresy URL v jazyce JavaScript 

Následující jednostránková aplikace používá JavaScript vytvořit náhled adresy URL pro web SwiftKey: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Požadavky

Získání přístupového klíče pro bezplatnou zkušební verzi [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-url-preview)

## <a name="code-scenario"></a>Scénář kódu
Následující příklad jazyka javascript obsahuje textové pole vstupního objektu, ve kterém uživatel zadá adresu URL ve verzi preview.  Pokud uživatel klikne **ve verzi Preview** trasy metoda onclick na tlačítko `getPreview` kde kód generuje webovou žádost k **UrlPreview** koncový bod.

Kód vytvoří *XMLHttpRequest*, přidá *Ocp-Apim-Subscription-Key* záhlaví a klíč a odešle žádost.  Přidá obslužnou rutinu události pro asynchronní zpracování odpovědi.

Pokud odpověď vrátí úspěšně, obslužná rutina přiřadí text JSON odpovědi `demo` odstavce na stránce. Další prvky odpovědi jsou nastaveny na následujících odstavcích pro zobrazení.

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

**Spuštění ukázky**

![Náhled adresy Url JavaScript příklad](./media/java-script-demo.png)

## <a name="running-the-application"></a>Spouštění aplikace.

Ke spuštění aplikace:

1. Nahraďte `YOUR-SUBSCRIPTION-KEY` hodnotu s platný přístupový klíč pro vaše předplatné.
2. Uložte do souboru s příponou .html HTML a skript.
3. Spusťte webovou stránku v prohlížeči.
4. Použijte stávající adresy URL nebo zadejte jiný do textového pole.
5. Klikněte na tlačítko **ve verzi Preview** tlačítko.

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
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start uzlu](node-quickstart.md)
- [Rychlý start Pythonu](python-quickstart.md)
