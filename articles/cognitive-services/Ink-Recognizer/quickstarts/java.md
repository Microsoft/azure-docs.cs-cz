---
title: 'Rychlý start: Rozpoznávání digitálního inkoustu pomocí nástroje pro rozpoznávání rukopisu REST API a Java'
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
ms.openlocfilehash: 36ff0fe4550b140a722ed25f4e372f7c88581211
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212685"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Rychlý start: Rozpoznávání digitálního inkoustu pomocí nástroje pro rozpoznávání rukopisu REST API a Java

Pomocí tohoto rychlého startu můžete začít používat rozhraní API pro rozpoznávání rukopisu na tahy digitálního pera. Tato aplikace Java pošle požadavek rozhraní API obsahující data tahu ve formátu JSON a získá odpověď.

I když je tato aplikace napsaná v jazyce Java, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Obvykle byste volali rozhraní API z digitální aplikace pro psaní rukou. V tomto rychlém startu se v souboru JSON pošle data tahy perem pro následující psaný vzorek.

![Obrázek rukopisného textu](../media/handwriting-sample.jpg)

Zdrojový kód pro tento rychlý Start najdete na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Požadavky

- [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) nebo novější.

- Importovat tyto knihovny z úložiště Maven
    - [JSON v balíčku Java](https://mvnrepository.com/artifact/org.json/json)
    - Balíček [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Ukázková data tahu perem pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Java a naimportujte následující knihovny.

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Vytvořte proměnné pro svůj klíč předplatného a koncový bod. Nahraďte níže uvedený koncový bod následujícím vygenerovaným prostředkem pro rozpoznávání rukopisu. Připojte ho k identifikátoru URI pro rozpoznávání rukopisu pro připojení k rozhraní API.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://<your-custom-subdomain>.cognitiveservices.azure.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou funkci s názvem `sendRequest()` , která přijímá proměnné vytvořené výše. Pak proveďte následující kroky.

2. `CloseableHttpClient` Vytvořte objekt, který může odesílat požadavky do rozhraní API. Odešlete požadavek do `HttpPut` objektu žádosti kombinací koncového bodu a adresy URL nástroje pro rozpoznávání rukopisu.

3. Pomocí `setHeader()` funkce Request `Content-Type` nastavte hlavičku na `application/json`a přidejte do `Ocp-Apim-Subscription-Key` hlavičky klíč předplatného.

4. Použijte `setEntity()` funkci žádosti na data, která chcete odeslat.   

5. Použijte `execute()` funkci klienta k odeslání žádosti a uložte ji `CloseableHttpResponse` do objektu. 

6. `HttpEntity` Vytvořte objekt pro uložení obsahu odpovědi. Získejte obsah pomocí `getEntity()`. Pokud odpověď není prázdná, vraťte ji.
    
    ```java
    static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPut request = new HttpPut(endpoint + apiAddress);
            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.setEntity(new StringEntity(requestData));
            try (CloseableHttpResponse response = client.execute(request)) {
                HttpEntity respEntity = response.getEntity();
                if (respEntity != null) {
                    return EntityUtils.toString(respEntity, "utf-8");
                }
            } catch (Exception respEx) {
                respEx.printStackTrace();
            }
        } catch (IOException ex) {
            System.err.println("Exception recognizing ink: " + ex.getMessage());
            ex.printStackTrace();
        }
        return null;
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Poslat požadavek na rozpoznávání rukopisu

Vytvořte metodu volanou `recognizeInk()` pro rozpoznání dat tahu perem. `sendRequest()` Zavolejte metodu vytvořenou výše pomocí vašeho koncového bodu, adresy URL, klíče předplatného a dat JSON. Získejte výsledek a vytiskněte ho do konzoly.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Načtení dat digitálního inkoustu a odeslání žádosti

1. V metodě Main aplikace si přečtěte v souboru JSON obsahujícím data, která se přidají do požadavků.

2. Zavolejte funkci rozpoznávání rukopisu vytvořenou výše.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Spuštění aplikace a zobrazení odpovědi

Spusťte aplikaci. Ve formátu JSON se vrátí úspěšná odpověď. Můžete také najít odpověď JSON na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://go.microsoft.com/fwlink/?linkid=2089907)


Pokud chcete zjistit, jak funguje rozhraní API pro rozpoznávání rukopisu v digitální aplikaci pro rukopis, podívejte se na následující ukázkové aplikace na GitHubu:
* [C# a Univerzální platforma Windows (UPW)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# a Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikace webového prohlížeče v Javascriptu](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace v Javě a Androidu](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobilní aplikace ve Swiftu a iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
