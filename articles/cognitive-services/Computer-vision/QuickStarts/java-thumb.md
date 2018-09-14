---
title: Rychlý start k rozhraní API pro počítačové zpracování obrazu v Javě | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: V tomto rychlém startu vygenerujete ve službě Cognitive Services pomocí počítačového zpracování obrazu v jazyce Java miniaturu obrázku.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 8907b104cacfbcce53f6e490e348f108580d84a3
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43769370"
---
# <a name="quickstart-generate-a-thumbnail---rest-java"></a>Rychlý start: Vygenerování miniatury – REST, Java

V tomto rychlém startu vygenerujete pomocí počítačového zpracování obrazu miniaturu obrázku.

## <a name="prerequisites"></a>Požadavky

Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného – [jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-thumbnail-request"></a>Žádost Get Thumbnail

Miniaturu obrázku můžete vygenerovat pomocí [metody Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb). Zadáte výšku a šířku, které se mohou od poměru stran vstupního obrázku lišit. Počítačové zpracování obrazu použije chytré oříznutí, které inteligentně identifikuje oblast zájmu a na základě této oblasti vygeneruje souřadnice oříznutí.

Pokud chcete spustit ukázku, postupujte následovně:

1. Vytvořte novou aplikaci příkazového řádku.
1. Nahraďte třídu Main následujícím kódem (zachovejte všechny příkazy `package`).
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte hodnotu `uriBase` na umístění, kde jste klíče předplatného získali.
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

import java.awt.*;
import javax.swing.*;
import java.net.URI;
import java.io.InputStream;
import javax.imageio.ImageIO;
import java.awt.image.BufferedImage;

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
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder uriBuilder = new URIBuilder(uriBase);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
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

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200) {
                // Display the thumbnail.
                System.out.println("\nDisplaying thumbnail.\n");
                displayImage(entity.getContent());
            } else {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }

    // Displays the given input stream as an image.
    private static void displayImage(InputStream inputStream) {
        try {
            BufferedImage bufferedImage = ImageIO.read(inputStream);

            ImageIcon imageIcon = new ImageIcon(bufferedImage);

            JLabel jLabel = new JLabel();
            jLabel.setIcon(imageIcon);

            JFrame jFrame = new JFrame();
            jFrame.setLayout(new FlowLayout());
            jFrame.setSize(100, 150);

            jFrame.add(jLabel);
            jFrame.setVisible(true);
            jFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="get-thumbnail-response"></a>Odpověď metody Get Thumbnail

Úspěšná odpověď obsahuje binární kód miniatury. Pokud požadavek selže, bude odpověď obsahovat chybový kód a zprávu, která vám pomůže určit, co se nepovedlo. Následující text je příkladem úspěšné odpovědi.

```text
Response:

StatusCode: 200, ReasonPhrase: 'OK', Version: 1.1, Content: System.Net.Http.StreamContent, Headers:
{
  Pragma: no-cache
  apim-request-id: 131eb5b4-5807-466d-9656-4c1ef0a64c9b
  Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
  x-content-type-options: nosniff
  Cache-Control: no-cache
  Date: Tue, 06 Jun 2017 20:54:07 GMT
  X-AspNet-Version: 4.0.30319
  X-Powered-By: ASP.NET
  Content-Length: 5800
  Content-Type: image/jpeg
  Expires: -1
}
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte aplikaci v Java Swing, která používá počítačové zpracování obrazu k optickému rozpoznávání znaků (OCR); vytváření chytře ořezaných miniatur; a rozpoznávání, kategorizování, označování a popisování vizuálních funkcí, včetně obličejů v obrázcích. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Kurz rozhraní API pro počítačové zpracování obrazu v Javě](../Tutorials/java-tutorial.md)
