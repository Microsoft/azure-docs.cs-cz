---
title: 'Rychlý start: Analýza vzdáleného obrázku – REST, Java – počítačové zpracování obrazu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu budete analyzovat obrázek pomocí rozhraní API pro počítačové zpracování obrazu a Javy.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: 2503d8cda05787885935ffab1be6b1efd85ad0cd
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852108"
---
# <a name="quickstart-analyze-a-remote-image-using-the-rest-api-and-java-in-computer-vision"></a>Rychlý start: Analýza vzdáleného obrázku pomocí rozhraní REST API a Javy v Počítačovém zpracování obrazu

V tomto rychlém startu analyzujete obrázek uložený vzdáleně za účelem extrakce vizuálních prvků pomocí rozhraní REST API počítačového zpracování obrazu. Pomocí metody [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) můžete extrahovat vizuální prvky na základě obsahu obrázku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít nainstalovanou platformu [Java&trade;, Standard Edition Development Kit 7 nebo 8](https://aka.ms/azure-jdks) (JDK 7 nebo 8).
- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Abyste získali klíč předplatného, přejděte k tématu [Jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample-application"></a>Vytvoření a spuštění ukázkové aplikace

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Ve svém oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Java. Vytvořte projekt Java ze šablony aplikace příkazového řádku, pokud je tato možnost k dispozici.
1. Následující knihovny naimportujte do projektu Java. Pokud používáte Maven, jsou k dispozici souřadnice Maven pro každou knihovnu.
   - [Klient Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Jádro Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [Knihovna JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Přidejte následující příkazy `import` do souboru, který obsahuje veřejnou třídu `Main` pro váš projekt.  

   ```java
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
   ```

1. Nahraďte veřejnou třídu `Main` kódem zobrazeným níže a tam, kde je to potřeba, proveďte následující změny v kódu:
   1. Hodnotu `subscriptionKey` nahraďte klíčem předplatného.
   1. Hodnotu `uriBase` nahraďte adresou URL koncového bodu metody [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) z oblasti Azure, kde jste získali klíče předplatného, pokud je to potřeba.
   1. Volitelně můžete hodnotu `imageToAnalyze` nahradit adresou URL jiného obrázku, který chcete analyzovat.
1. Uložte a sestavte projekt Java.
1. Pokud používáte integrované vývojové prostředí, spusťte `Main`. V opačném případě otevřete okno příkazového řádku a potom pomocí příkazu `java` spusťte zkompilovanou třídu. Například, `java Main`.

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westus" region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
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

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
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

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Ukázková aplikace provede analýzu a zobrazí úspěšnou odpověď v okně konzoly, podobně jako v následujícím příkladu:

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud projekt Java už nepotřebujete, odstraňte jej včetně zkompilované třídy a importovaných knihoven.

## <a name="next-steps"></a>Další postup

Prozkoumejte aplikaci v Java Swingu, která používá počítačové zpracování obrazu k optickému rozpoznávání znaků (OCR), vytvořte chytře ořezané miniatury a rozpoznávejte, kategorizujte, označujte a popisujte vizuální vlastnosti na obrázku včetně obličejů. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Kurz rozhraní API pro počítačové zpracování obrazu v Javě](../Tutorials/java-tutorial.md)
