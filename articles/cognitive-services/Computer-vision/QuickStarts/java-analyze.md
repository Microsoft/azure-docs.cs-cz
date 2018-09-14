---
title: Rychlý start k rozhraní API pro počítačové zpracování obrazu v Javě | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: V tomto rychlém startu analyzujete obrázek pomocí počítačového zpracování obrazu s Javou ve službách Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 9faa8d05a747855ceb0a468845c3d3a82aa71337
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43769293"
---
# <a name="quickstart-analyze-a-remote-image---rest-java"></a>Rychlý start: Analýza vzdáleného obrázku – REST, Java

V tomto rychlém startu analyzujete obrázek za účelem extrakce vizuálních prvků pomocí počítačového zpracování obrazu.

## <a name="prerequisites"></a>Požadavky

Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-image-request"></a>Žádost Analyze Image

Pomocí [metody Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) můžete extrahovat vizuální funkce na základě obsahu obrázku. Obrázek můžete nahrát nebo můžete určit jeho adresu URL a vybrat funkce, které se mají vrátit, včetně:

* Podrobného seznamu značek souvisejících s obsahem obrázku
* Popisu obsahu obrázku v celé větě
* Souřadnic, pohlaví a věku veškerých obličejů na obrázku
* Typu obrázku (klipart nebo perokresba)
* Převládající barvy, doplňkové barvy a toho, jestli je obrázek černobílý
* Kategorie definované v této [taxonomii](../Category-Taxonomy.md)
* Je obsah obrázku určený pro dospělé nebo se jedná o sexuálně sugestivní obsah?

Pokud chcete spustit ukázku, postupujte takto:

1. Vytvořte novou aplikaci příkazového řádku.
1. Nahraďte třídu Main následujícím kódem (zachovejte všechny příkazy `package`).
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte hodnotu `uriBase` na umístění, kde jste získali klíče předplatného.
1. Volitelně můžete hodnotu `imageToAnalyze` změnit na jiný obrázek.
1. Následující knihovny stáhněte z úložiště Maven do projektového adresáře `lib`:
   * `org.apache.httpcomponents:httpclient:4.5.5`
   * `org.apache.httpcomponents:httpcore:4.4.9`
   * `org.json:json:20180130`
1. Spusťte třídu „Main“.

```java
// This sample uses the following libraries:
//  - Apache HTTP client (org.apache.httpcomponents:httpclient:4.5.5)
//  - Apache HTTP core (org.apache.httpcomponents:httpccore:4.4.9)
//  - JSON library (org.json:json:20180130).

import java.net.URI;

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.json.JSONObject;

public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same region in your REST call as you used to get your
    // subscription keys. For example, if you got your subscription keys from
    // westus, replace "westcentralus" in the URI below with "westus".
    //
    // Free trial subscription keys are generated in the westcentralus region. If you
    // use a free trial subscription key, you shouldn't need to change this region.
    private static final String uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze";

    private static final String imageToAnalyze =
            "https://upload.wikimedia.org/wikipedia/commons/" +
                    "1/12/Broadway_and_Times_Square_by_night.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Make the REST API call and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="analyze-image-response"></a>Odpověď metody Analyze Image

Úspěšná odpověď se vrátí ve formátu JSON, například:

```json
REST Response:

{
  "metadata": {
    "width": 1826,
    "format": "Jpeg",
    "height": 2436
  },
  "color": {
    "dominantColorForeground": "Brown",
    "isBWImg": false,
    "accentColor": "B74314",
    "dominantColorBackground": "Brown",
    "dominantColors": ["Brown"]
  },
  "requestId": "bbffe1a1-4fa3-4a6b-a4d5-a4964c58a811",
  "description": {
    "captions": [{
      "confidence": 0.8241405091548035,
      "text": "a group of people on a city street filled with traffic at night"
    }],
    "tags": [
      "outdoor",
      "building",
      "street",
      "city",
      "busy",
      "people",
      "filled",
      "traffic",
      "many",
      "table",
      "car",
      "group",
      "walking",
      "bunch",
      "crowded",
      "large",
      "night",
      "light",
      "standing",
      "man",
      "tall",
      "umbrella",
      "riding",
      "sign",
      "crowd"
    ]
  },
  "categories": [{
    "score": 0.625,
    "name": "outdoor_street"
  }]
}
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte aplikaci v Java Swingu, která používá počítačové zpracování obrazu k optickému rozpoznávání znaků (OCR), vytvořte chytře ořezané miniatury a rozpoznávejte, kategorizujte, označujte a popisujte vizuální vlastnosti na obrázku včetně obličejů. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Kurz rozhraní API pro počítačové zpracování obrazu v Javě](../Tutorials/java-tutorial.md)
