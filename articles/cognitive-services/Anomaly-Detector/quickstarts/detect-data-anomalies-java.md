---
title: 'Rychlý start: Detekovat anomálie ve vašich datech časových řad pomocí rozhraní REST API služby detekce anomálií a Java | Dokumentace Microsoftu'
description: Zjištění anomálií v datových řad v dávce nebo na streamovaných datech pomocí rozhraní API detekce anomálií.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 1c8ce91a0fd8805b307e1e21bc08f9050b8a47d4
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547035"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Rychlý start: Detekovat anomálie ve vašich datech časových řad pomocí rozhraní REST API služby detekce anomálií a Java

V tomto rychlém startu můžete začít používat rozhraní API detekce anomálií dva režimy zjišťování zjišťovat anomálie ve vašich datech časových řad. Tato aplikace v Javě odešle dva požadavky rozhraní API, obsahující data ve formátu JSON časových řad a získá odpovědi.

| Žádosti na rozhraní API                                        | Výstup aplikace                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Zjišťovat anomálie v dávce                        | Odpověď JSON, který obsahuje stav anomálií (a další data) pro každý datový bod v datech časových řad a pozice jakékoli detekované anomálie. |
| Zjištění anomálií stavu nejnovější datového bodu | Odpověď JSON, který obsahuje stav anomálií (a další data) pro poslední datový bod v datech časové řady.                                                                                                                                         |

 Zatímco tato aplikace je napsána v jazyce Java, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

- [Java&trade; vývoj Kit(JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) nebo novější.

- Tyto knihovny importu z úložiště Maven
    - [JSON v jazyce Java](https://mvnrepository.com/artifact/org.json/json) balíčku
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) balíčku

- Odkazuje JSON soubor obsahující data časových řad. Ukázková data pro účely tohoto rychlého startu můžete najít na [Githubu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

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
    import org.json.JSONArray;
    import org.json.JSONObject;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Vytváření proměnných pro váš klíč předplatného a váš koncový bod. V následující tabulce jsou identifikátory URI, můžete použít k detekci anomálií. Ty se připojí k vašeho koncového bodu služby později k vytvoření rozhraní API pro adresy URL požadavků.

    |Metoda detekce  |Identifikátor URI  |
    |---------|---------|
    |Zjišťování služby batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Zjišťování na nejnovější datového bodu     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    //replace the endpoint URL with the correct one for your subscription. Your endpoint can be found in the Azure portal. 
    //For example: https://westus2.api.cognitive.microsoft.com
    static final String endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    static final String dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    static final String latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    static final String batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

3. Přečtěte si v datovém souboru JSON

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce k odesílání požadavků

1. Vytvořit novou funkci s názvem `sendRequest()` , která přebírá proměnných vytvořené výše. Pak proveďte následující kroky.

2. Vytvoření `CloseableHttpClient` objekt, který může odesílat požadavky na rozhraní API. Odeslat žádost o `HttpPost` objekt žádosti kombinací koncový bod služby a adresu URL detekce anomálií.

3. Použijte žádosti `setHeader()` funkce nastavíte `Content-Type` záhlaví `application/json`a přidat váš klíč předplatného na `Ocp-Apim-Subscription-Key` záhlaví.

4. Použijte žádosti `setEntity()` funkci pro data, která mají být odeslány.

5. Použití klienta `execute()` funkce Odeslat požadavek a uložit ho. tím `CloseableHttpResponse` objektu.

6. Vytvoření `HttpEntity` objekt pro uložení obsahu odpovědi. Získat obsah s `getEntity()`. Pokud odpovědi není prázdný, vrátí jej.

```java
static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
    try (CloseableHttpClient client = HttpClients.createDefault()) {
        HttpPost request = new HttpPost(endpoint + apiAddress);
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
        System.err.println("Exception on Anomaly Detector: " + ex.getMessage());
        ex.printStackTrace();
    }
    return null;
}
```

## <a name="detect-anomalies-as-a-batch"></a>Zjišťovat anomálie v dávce

1. Vytvořit metodu nazvanou `detectAnomaliesBatch()` zjišťovat anomálie v celém data v dávce. Volání `sendRequest()` metoda vytvořené výše s koncovým bodem, adresa url, klíč předplatného a dat json. Získat výsledek a vypíše do konzoly.

2. Pokud odpověď obsahuje `code` pole, kód chyby a chybová zpráva.

3. V opačném případě vyhledání pozice anomálie v datové sadě. Odpověď na `isAnomaly` pole obsahuje hodnotu typu boolean týkající se určuje, zda je daný datový bod anomálie. Získat pole JSON a iteraci v rámci, tisk index žádné `true` hodnoty. Tyto hodnoty odpovídat indexu neobvyklé datových bodů, pokud některá.

```java
static void detectAnomaliesBatch(String requestData) {
    System.out.println("Detecting anomalies as a batch");
    String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
    if (result != null) {
        System.out.println(result);
        JSONObject jsonObj = new JSONObject(result);
        if (jsonObj.has("code")) {
            System.out.println(String.format("Detection failed. ErrorCode:%s, ErrorMessage:%s", jsonObj.getString("code"), jsonObj.getString("message")));
        } else {
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjištění anomálií stavu nejnovější datového bodu

* Vytvořit metodu nazvanou `detectAnomaliesLatest()` pro detekci anomálií stav poslední datový bod v datové sadě. Volání `sendRequest()` metoda vytvořené výše s koncovým bodem, adresa url, klíč předplatného a dat json. Získat výsledek a vypíše do konzoly.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Načtení dat časových řad a odeslat žádost

1. V hlavní metodě vaší aplikace přečtěte si v souboru JSON, který obsahuje data, která se přidá na požadavky.

2. Volání funkce detekce anomálií dvě vytvořili výše.

```java
public static void main(String[] args) throws Exception {
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);
}
```

### <a name="example-response"></a>Příklad odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Kliknutím na odkazy níže zobrazíte odpověď JSON na Githubu:
* [Příklad odpovědi zjišťování služby batch](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Příklad nejnovější bod zjišťování odpovědi](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)