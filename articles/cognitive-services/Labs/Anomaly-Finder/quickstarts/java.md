---
title: Jak používat rozhraní API pro vyhledávač anomálií s využitím Javy – Microsoft Cognitive Services | Dokumentace Microsoftu
description: Získat informace a ukázky kódu můžete rychle začít pracovat pomocí Javy a detekci anomálií ve službě Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: kefre
ms.openlocfilehash: ef876e19353e8765b679a9aae6282ce3ade600c2
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456537"
---
# <a name="use-the-anomaly-finder-api-with-java"></a>Vyhledávač anomálií rozhraní API pomocí Javy

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat rozhraní API pro detekci anomálií v Javě k provedení úlohy získat výsledek detekce anomálií dat časové řady.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-detection-api-using-java"></a>Zjišťování anomálií body rozhraní API pro detekci anomálií pomocí Javy

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Příklad dat časových řad

Následuje příklad času datových bodů řady.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-java-example"></a>Analýza dat a získat body anomálií příkladu Java

Ke spuštění ukázky, proveďte následující kroky:
1. Vytvoření nové aplikace příkazového řádku.
2. Nahraďte třídu Main následujícím kódem (zachovejte všechny příkazy `package`).
3. Hodnotu `[YOUR_SUBSCRIPTION_KEY]` nahraďte platným klíčem předplatného.
4. Nahradit `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` pomocí příkladu nebo vlastních datových bodů.
5. Následující globální knihovny stáhněte z úložiště Maven do projektového adresáře `lib`:
   * `org.apache.httpcomponents:httpclient:4.5.2`
6. Spusťte třídu „Main“.

```java

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace the subscriptionKey string value with your valid subscription key.
    public static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";

    public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

    public static void main(String[] args) {
        final String content = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]";

        CloseableHttpClient client = HttpClients.createDefault();
        HttpPost request = new HttpPost(uriBase);

        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

        try {
            StringEntity params = new StringEntity(content);
            request.setEntity(params);

            CloseableHttpResponse response = client.execute(request);
            try {
                HttpEntity respEntity = response.getEntity();
                if (respEntity != null) {
                    System.out.println("----------");
                    System.out.println(response.getStatusLine());
                    System.out.println("Response content is :\n");
                    System.out.println(EntityUtils.toString(respEntity, "utf-8"));
                    System.out.println("----------");
                }
            } catch (Exception respEx) {
                respEx.printStackTrace();
            } finally {
                response.close();
            }

        } catch (Exception ex) {
            System.err.println("Exception on Anomaly Detection: " + ex.getMessage());
            ex.printStackTrace();
        } finally {
            try {
                client.close();
            } catch (Exception e) {
                System.err.println("Exception on closing HttpClient: " + e.getMessage());
                e.printStackTrace();
            }
        }
    }
}

```

### <a name="example-response"></a>Příklad odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Následuje příklad odpovědi.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Aplikace v Javě](../tutorials/java-tutorial.md)
