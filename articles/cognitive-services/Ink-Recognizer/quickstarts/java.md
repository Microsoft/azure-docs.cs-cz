---
title: 'Rychlý start: Rozpoznat digitálního inkoustu pomocí rozhraní REST API pro rozpoznávání rukopisu a Javy.'
description: Pomocí rozhraní API pro rozpoznávání rukopisu spusťte uznání digitální inkoustových tahů.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 43dc388c9f24e3975d6efddbeec6e096bf062548
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026270"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Rychlý start: Rozpoznat digitálního inkoustu pomocí rozhraní REST API pro rozpoznávání rukopisu a Javy.

Pokud chcete začít používat rozhraní API pro rozpoznávání rukopisu na digitální inkoustových tahů použijte v tomto rychlém startu. Tato aplikace v Javě odešle požadavek rozhraní API, obsahující data ve formátu JSON inkoustu stroke a získá odpověď.

Zatímco tato aplikace je napsána v jazyce Java, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků.

Obvykle by volání rozhraní API z aplikace pro digitální rukopisu. V tomto rychlém startu odesílá data inkoustu stroke pro následující ukázka rukou psaný ze souboru JSON.

![Obrázek rukou psaný text](../media/handwriting-sample.jpg)

Zdrojový kód v tomto rychlém startu najdete na [Githubu](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Požadavky

- [Java&trade; vývoj Kit(JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) nebo novější.

- Tyto knihovny importu z úložiště Maven
    - [JSON v jazyce Java](https://mvnrepository.com/artifact/org.json/json) balíčku
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) balíčku

- Příklad inkoustu stroke data pro účely tohoto rychlého najdete na [Githubu](https://go.microsoft.com/fwlink/?linkid=2089904).

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

2. Vytváření proměnných pro váš klíč předplatného a váš koncový bod. Níže je identifikátor URI, můžete použít pro rozpoznávání rukopisu. Připojí se k vašeho koncového bodu služby později k vytvoření adresy URL požadavku rozhraní API.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce k odesílání požadavků

1. Vytvořit novou funkci s názvem `sendRequest()` , která přebírá proměnných vytvořené výše. Pak proveďte následující kroky.

2. Vytvoření `CloseableHttpClient` objekt, který může odesílat požadavky na rozhraní API. Odeslat žádost o `HttpPut` objekt žádosti kombinací koncový bod služby a adresu URL pro rozpoznávání rukopisu.

3. Použijte žádosti `setHeader()` funkce nastavíte `Content-Type` záhlaví `application/json`a přidat váš klíč předplatného na `Ocp-Apim-Subscription-Key` záhlaví.

4. Použijte žádosti `setEntity()` funkci pro data, která mají být odeslány.   

5. Použití klienta `execute()` funkce Odeslat požadavek a uložit ho. tím `CloseableHttpResponse` objektu. 

6. Vytvoření `HttpEntity` objekt pro uložení obsahu odpovědi. Získat obsah s `getEntity()`. Pokud odpovědi není prázdný, vrátí jej.
    
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

## <a name="send-an-ink-recognition-request"></a>Poslat žádost o rozpoznávání rukopisu

Vytvořit metodu nazvanou `recognizeInk()` rozpoznávat data inkoustu stroke. Volání `sendRequest()` metoda vytvořené výše s koncovým bodem, adresa url, klíč předplatného a dat json. Získat výsledek a vypíše do konzoly.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Načíst data digitálního inkoustu a odeslat žádost

1. V hlavní metodě vaší aplikace přečtěte si v souboru JSON, který obsahuje data, která se přidá na požadavky.

2. Volání funkce rozpoznávání rukopisu vytvořili výše.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Spusťte aplikaci a zobrazit odpovědi

Spusťte aplikaci. Úspěšná odpověď se vrátí ve formátu JSON. Můžete také najít odpověď JSON na [Githubu](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://go.microsoft.com/fwlink/?linkid=2089907)


Pokud chcete zobrazit, jak rozhraní API pro rozpoznávání rukopisu funguje v digitální rukopisu aplikaci, podívejte se na následující ukázkové aplikace na Githubu:
* [C#a Platform(UWP) Universal Windows](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C#a Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Webový prohlížeč aplikace jazyka JavaScript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace v Javě a Androidu](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Kód SWIFT a mobilní aplikaci pro iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
