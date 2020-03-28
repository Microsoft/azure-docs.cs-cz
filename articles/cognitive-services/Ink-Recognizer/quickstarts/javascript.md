---
title: 'Úvodní příručka: Rozpoznávání digitálních rukopisů pomocí rozhraní REST API a Node.js nástroje NOK Recognizer'
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro rozpoznávání rukopisu začněte rozpoznávat tahy digitálních inkoustů na tomto rychlém startu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: a37f2b7044fcba04ca18093aa73563961e9e35de
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448130"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Úvodní příručka: Rozpoznávání digitálního inkoustu pomocí rozhraní REST API pro rozpoznávání rukopisu a JavaScriptu

Pomocí tohoto rychlého startu můžete začít používat rozhraní API rozpoznávání rukopisu u digitálních tahů perem. Tato aplikace JavaScript odešle požadavek rozhraní API obsahující data tahu inkoustu ve formátu JSON a zobrazí odpověď.

Zatímco tato aplikace je napsána v Javascriptu a běží ve vašem webovém prohlížeči, API je RESTful webová služba kompatibilní s většinou programovacích jazyků.

Obvykle byste volat rozhraní API z digitální aplikace rukopisu. Tento rychlý start odesílá data tahu rukopisu pro následující ručně psaný vzorek ze souboru JSON.

![obrázek ručně psaného textu](../media/handwriting-sample.jpg)

Zdrojový kód pro tento rychlý start najdete na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Požadavky

- Webový prohlížeč
- Příklad dat tahů perem pro tento rychlý start najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Vytvoření prostředku nástroje pro rozpoznávání rukopisu

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. Ve svém oblíbeném rozhraní IDE `.html` nebo editoru vytvořte nový soubor. Pak k němu přidejte základní HTML pro kód, který přidáme později.
    
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

2. Do `<body>` značky přidejte následující html:
    1. Dvě textové oblasti pro zobrazení požadavku json a odpovědi.
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

## <a name="load-the-example-json-data"></a>Načíst příklad dat JSON

1. V `<script>` rámci značky vytvořte proměnnou pro sampleJson. Pak vytvořte funkci `openFile()` JavaScript s názvem, která otevře průzkumník souborů, abyste mohli vybrat soubor JSON. Po `Recognize ink` klepnutí na tlačítko bude tato funkce volat a začne soubor číst.
2. Pomocí `FileReader` `onload()` funkce objektu zpracujte soubor asynchronně. 
    1. Nahraďte všechny `\n` znaky `\r` v souboru prázdným řetězcem. 
    2. Slouží `JSON.parse()` k převodu textu na platný json.
    3. Aktualizujte `request` textové pole v aplikaci. Slouží `JSON.stringify()` k formátování řetězce JSON. 
    
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

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Odeslání požadavku do rozhraní API pro rozpoznávání rukopisu

1. V `<script>` rámci značky vytvořte funkci nazvanou `recognizeInk()`. Tato funkce bude později volat rozhraní API a aktualizovat stránku s odpovědí. Přidejte kód z následujících kroků v rámci této funkce. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Vytvořte proměnné pro adresu URL koncového bodu, klíč předplatného a ukázkový JSON. Pak vytvořte `XMLHttpRequest` objekt pro odeslání požadavku rozhraní API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Vytvořte návratovou `XMLHttpRequest` funkci objektu. Tato funkce bude analyzovat odpověď rozhraní API z úspěšného požadavku a zobrazí ji v aplikaci. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Vytvořte chybovou funkci pro objekt požadavku. Tato funkce zaznamená chybu do konzoly. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Vytvořte funkci pro `onreadystatechange` vlastnost objektu požadavku. Při změně stavu připravenosti objektu požadavku budou použity výše uvedené funkce vrácení a chyby.
            
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
    
    5. Odešlete požadavek rozhraní API. Přidejte klíč předplatného `Ocp-Apim-Subscription-Key` do záhlaví `content-type` a nastavte`application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Spuštění aplikace a zobrazení odpovědi

Tato aplikace může být spuštěna ve vašem webovém prohlížeči. Úspěšná odpověď je vrácena ve formátu JSON. Odpověď JSON najdete také na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Odkaz na rozhraní REST API](https://go.microsoft.com/fwlink/?linkid=2089907)

Pokud se chcete podívat, jak rozhraní API pro rozpoznávání rukopisu funguje v digitální aplikaci rukopisu, podívejte se na následující ukázkové aplikace na GitHubu:
* [C# a Univerzální platforma Windows (UPW)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# a Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikace webového prohlížeče v Javascriptu](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace v Javě a Androidu](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobilní aplikace ve Swiftu a iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
