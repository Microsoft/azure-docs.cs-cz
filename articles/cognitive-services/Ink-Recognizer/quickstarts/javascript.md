---
title: 'Rychlý Start: rozpoznávání digitálního inkoustu pomocí nástroje pro rozpoznávání rukopisu REST API a Node. js'
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro rozpoznávání rukopisu můžete začít rozpoznávat tahy digitálního inkoustu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: 19626bd68ad82108b2ebaa823d196d0f22008e29
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996911"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Rychlý Start: rozpoznávání digitálního inkoustu pomocí REST API a JavaScriptu pro rozpoznávání rukopisu

Pomocí tohoto rychlého startu můžete začít používat rozhraní API pro rozpoznávání rukopisu na tahy digitálního pera. Tato aplikace JavaScriptu pošle požadavek rozhraní API obsahující data tahu ve formátu JSON a zobrazí odpověď.

I když je tato aplikace napsaná v jazyce JavaScript a běží ve webovém prohlížeči, je rozhraní API webová služba RESTful, která je kompatibilní s většinou programovacích jazyků.

Obvykle byste volali rozhraní API z digitální aplikace pro psaní rukou. V tomto rychlém startu se v souboru JSON pošle data tahy perem pro následující psaný vzorek.

![Obrázek rukopisného textu](../media/handwriting-sample.jpg)

Zdrojový kód pro tento rychlý Start najdete na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Požadavky

- Webový prohlížeč
- Ukázková data tahu perem pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání rukopisu

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V oblíbených IDE nebo editoru vytvořte nový soubor `.html`. Pak do něj přidejte základní kód HTML pro kód, který přidáte později.
    
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

2. Do značky `<body>` přidejte následující kód HTML:
    1. Dvě textové oblasti pro zobrazení žádosti a odpovědi JSON
    2. Tlačítko pro volání funkce `recognizeInk()`, která bude vytvořena později.
    
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

1. V rámci značky `<script>` Vytvořte proměnnou pro sampleJson. Pak vytvořte funkci JavaScriptu s názvem `openFile()`, která otevře Průzkumníka souborů, abyste mohli vybrat soubor JSON. Po kliknutí na tlačítko `Recognize ink` bude tato funkce volána a začít číst soubor.
2. K asynchronnímu zpracování souboru použijte funkci `FileReader` objektu `onload()`. 
    1. Nahraďte všechny `\n` nebo `\r` znaky v souboru prázdným řetězcem. 
    2. Pomocí `JSON.parse()` převést text na platný formát JSON
    3. Aktualizujte textové pole `request` v aplikaci. K formátování řetězce JSON použijte `JSON.stringify()`. 
    
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

1. V rámci značky `<script>` vytvořte funkci s názvem `recognizeInk()`. Tato funkce později zavolá rozhraní API a aktualizuje stránku pomocí odpovědi. Do této funkce přidejte kód z následujících kroků. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Vytvořte proměnné pro adresu URL koncového bodu, klíč předplatného a vzorový kód JSON. Pak vytvořte objekt `XMLHttpRequest` pro odeslání požadavku rozhraní API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Vytvořte návratovou funkci pro objekt `XMLHttpRequest`. Tato funkce bude analyzovat odpověď rozhraní API z úspěšné žádosti a zobrazí ji v aplikaci. 
            
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

    4. Vytvořte funkci pro vlastnost `onreadystatechange` objektu žádosti. Když se změní stav připravenosti objektu žádosti, použijí se výše uvedené návratové a chybové funkce.
            
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
    
    5. Odešlete žádost o rozhraní API. Do hlavičky `Ocp-Apim-Subscription-Key` přidejte svůj klíč předplatného a nastavte `content-type` na `application/json`
    
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
