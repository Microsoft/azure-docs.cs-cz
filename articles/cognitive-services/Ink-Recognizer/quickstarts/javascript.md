---
title: 'Rychlý Start: rozpoznávání digitálního inkoustu pomocí nástroje pro rozpoznávání rukopisu REST API a Node.js'
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro rozpoznávání rukopisu a JavaScriptu začněte rozpoznávat digitální inkoustové tahy v tomto rychlém startu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 13e094b0f5d59e070a96ab4b45dcd37315c46c60
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327344"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Rychlý Start: rozpoznávání digitálního inkoustu pomocí REST API a JavaScriptu pro rozpoznávání rukopisu

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Pomocí tohoto rychlého startu můžete začít používat rozhraní API pro rozpoznávání rukopisu na tahy digitálního pera. Tato aplikace JavaScriptu pošle požadavek rozhraní API obsahující data tahu ve formátu JSON a zobrazí odpověď.

I když je tato aplikace napsaná v jazyce JavaScript a běží ve webovém prohlížeči, je rozhraní API webová služba RESTful, která je kompatibilní s většinou programovacích jazyků.

Obvykle byste volali rozhraní API z digitální aplikace pro psaní rukou. V tomto rychlém startu se v souboru JSON pošle data tahy perem pro následující psaný vzorek.

![Obrázek rukopisného textu](../media/handwriting-sample.jpg)

Zdrojový kód pro tento rychlý Start najdete na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Předpoklady

- Webový prohlížeč
- Ukázková data tahu perem pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání rukopisu

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový `.html` soubor. Pak do něj přidejte základní kód HTML pro kód, který přidáte později.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. Do `<body>` značky přidejte následující kód HTML:
    1. Dvě textové oblasti pro zobrazení žádosti a odpovědi JSON
    2. Tlačítko pro volání `recognizeInk()` funkce, která bude vytvořena později.
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>Načtení ukázkových dat JSON

1. V rámci `<script>` značky Vytvořte proměnnou pro sampleJson. Pak vytvořte funkci JavaScriptu s názvem `openFile()` , která otevře Průzkumníka souborů, abyste mohli vybrat soubor JSON. Po `Recognize ink` kliknutí na tlačítko bude tato funkce volána a začít číst soubor.
2. `FileReader` `onload()` K asynchronnímu zpracování souboru použijte funkci objektu. 
    1. Nahraďte `\n` `\r` znaky v souboru prázdným řetězcem. 
    2. Použijte `JSON.parse()` k převedení textu na platný formát JSON.
    3. Aktualizujte `request` textové pole v aplikaci. Použijte `JSON.stringify()` k formátování řetězce JSON. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Poslat žádost na rozhraní API pro rozpoznávání rukopisu

1. V rámci `<script>` značky vytvořte funkci s názvem `recognizeInk()` . Tato funkce později zavolá rozhraní API a aktualizuje stránku pomocí odpovědi. Do této funkce přidejte kód z následujících kroků. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Vytvořte proměnné pro adresu URL koncového bodu, klíč předplatného a vzorový kód JSON. Pak vytvořte `XMLHttpRequest` objekt pro odeslání požadavku rozhraní API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Vytvořte pro objekt návratovou funkci `XMLHttpRequest` . Tato funkce bude analyzovat odpověď rozhraní API z úspěšné žádosti a zobrazí ji v aplikaci. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Vytvořte funkci Error objektu Request. Tato funkce zaznamená chybu do konzoly. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Vytvořte funkci pro vlastnost objektu Request `onreadystatechange` . Když se změní stav připravenosti objektu žádosti, použijí se výše uvedené návratové a chybové funkce.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. Odešlete žádost o rozhraní API. Přidejte do záhlaví klíč předplatného `Ocp-Apim-Subscription-Key` a nastavte `content-type` na `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Spuštění aplikace a zobrazení odpovědi

Tuto aplikaci lze spustit ve webovém prohlížeči. Ve formátu JSON se vrátí úspěšná odpověď. Odpověď JSON můžete také najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://go.microsoft.com/fwlink/?linkid=2089907)

Pokud chcete zjistit, jak funguje rozhraní API pro rozpoznávání rukopisu v digitální aplikaci pro rukopis, podívejte se na následující ukázkové aplikace na GitHubu:
* [C# a Univerzální platforma Windows (UPW)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# a Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikace webového prohlížeče v Javascriptu](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace v Javě a Androidu](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobilní aplikace ve Swiftu a iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
