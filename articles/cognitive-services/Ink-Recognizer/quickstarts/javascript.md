---
title: 'Rychlý start: Rozpoznat digitálního inkoustu pomocí rozhraní REST API pro rozpoznávání rukopisu a Node.js'
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro rozpoznávání rukopisu spusťte uznání digitální inkoustových tahů.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 7e158b0ae27780eeecb1ee7948087bf59b1502e1
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721269"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Rychlý start: Rozpoznat digitálního inkoustu pomocí rozhraní REST API pro rozpoznávání rukopisu a JavaScript

Pokud chcete začít používat rozhraní API pro rozpoznávání rukopisu na digitální inkoustových tahů použijte v tomto rychlém startu. Tato aplikace JavaScript odešle požadavek rozhraní API, obsahující data ve formátu JSON inkoustu stroke a zobrazí odpovědi.

Zatímco tato aplikace je napsána v jazyce Javascript a běží ve webovém prohlížeči, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků.

Obvykle by volání rozhraní API z aplikace pro digitální rukopisu. V tomto rychlém startu odesílá data inkoustu stroke pro následující ukázka rukou psaný ze souboru JSON.

![Obrázek rukou psaný text](../media/handwriting-sample.jpg)

Zdrojový kód v tomto rychlém startu najdete na [Githubu](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Požadavky

- Webový prohlížeč
- Příklad inkoustu stroke data pro účely tohoto rychlého najdete na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V oblíbeném IDE nebo editoru, vytvořte nový `.html` souboru. Pak přidejte základní HTML do ní pro kód, který přidáte později.
    
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

2. V rámci `<body>` značky, přidejte následující kód html:
    1. Dvě oblasti text pro zobrazení JSON žádostí a odpovědí.
    2. Tlačítka pro volání `recognizeInk()` funkce, která bude možné vytvořit později.
    
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

## <a name="load-the-example-json-data"></a>Načíst příklad JSON data

1. V rámci `<script>` značku, vytvořte proměnnou pro sampleJson. Potom vytvořit funkci JavaScriptu s názvem `openFile()` , která otevře Průzkumníka souborů, takže můžete vybrat svůj soubor JSON. Když `Recognize ink` po kliknutí na tlačítko, bude voláním této funkce a začít číst soubor.
2. Použití `FileReader` objektu `onload()` funkce k asynchronnímu zpracování souboru. 
    1. Nahradit libovolný `\n` nebo `\r` znaky v souboru s prázdným řetězcem. 
    2. Použití `JSON.parse()` pro převod textu na platný kód JSON
    3. Aktualizace `request` textového pole v aplikaci. Použití `JSON.stringify()` se naformátovat řetězec JSON. 
    
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

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Odeslat požadavek na rozhraní API pro rozpoznávání rukopisu

1. V rámci `<script>` značku, vytvořte funkci s názvem `recognizeInk()`. Tato funkce bude později volání rozhraní API a aktualizujte stránku s odpovědí. Přidejte kód z následujících kroků v rámci této funkce. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Vytváření proměnných pro adresu URL koncového bodu, klíč předplatného a ukázkovému kódu JSON. Vytvořte `XMLHttpRequest` objektu k odeslání žádosti o rozhraní API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. Vytvoření funkce pro vrácení `XMLHttpRequest` objektu. Tato funkce bude parsovat odpovědi rozhraní API z úspěšného požadavku a zobrazí v aplikaci. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Vytvořte chybovou funkci pro objekt žádosti. Tato funkce zaznamená chybu do konzoly. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Vytvoření funkce pro objekt žádosti `onreadystatechange` vlastnost. Při změně stavu připravenosti objekt žádosti, použije se výše uvedené funkce vrácených hodnot a chyba.
            
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
    
    5. Odeslání požadavku rozhraní API. Přidat klíč předplatného. Chcete `Ocp-Apim-Subscription-Key` záhlaví a nastavte `content-type` do `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Spusťte aplikaci a zobrazit odpovědi

Tuto aplikaci můžete spustit ve webovém prohlížeči. Úspěšná odpověď se vrátí ve formátu JSON. Můžete také najít odpověď JSON na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://go.microsoft.com/fwlink/?linkid=2089907)

Pokud chcete zobrazit, jak rozhraní API pro rozpoznávání rukopisu funguje v digitální rukopisu aplikaci, podívejte se na následující ukázkové aplikace na Githubu:
* [C# a Univerzální platforma Windows (UPW)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# a Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikace webového prohlížeče v Javascriptu](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace v Javě a Androidu](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobilní aplikace ve Swiftu a iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
