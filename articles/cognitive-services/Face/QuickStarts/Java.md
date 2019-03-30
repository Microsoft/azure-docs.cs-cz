---
title: 'Rychlý start: Rozpoznávání tváří v obrázku pomocí rozhraní Azure REST API a Javy.'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete rozhraní Azure REST API pro rozpoznávání tváře s Javou rozpoznávání tváří v obrázku.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: pafarley
ms.openlocfilehash: c0c1b9c1e9afc84e9702f6c1897d372a017be868
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629893"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Rychlý start: Rozpoznávání tváří v obrázku pomocí rozhraní REST API a Java

V tomto rychlém startu použijete rozhraní Azure REST API pro rozpoznávání tváře s Javou detekovat lidské tváře v obrázku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

- Klíč rozhraní API pro rozpoznávání tváře předplatného. Můžete získat bezplatné předplatné zkušební verze klíče z [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Nebo, postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) k odběru služby API pro rozpoznávání tváře a získejte klíč.
- Žádné prostředí Java IDE podle svého výběru.

## <a name="create-the-java-project"></a>Vytvoření projektu jazyka Java

1. Vytvoření nové aplikace v Javě příkazového řádku v prostředí IDE a přidejte **hlavní** třídy s **hlavní** metoda.
1. Následující knihovny naimportujte do projektu Java. Pokud používáte Maven, jsou k dispozici souřadnice Maven pro každou knihovnu.
   - [Klient Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.6)
   - [Jádro serveru Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.10)
   - [Knihovna JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Protokolování Apache Commons](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (commons-protokolování: commons-protokolování: 1.1.2)

## <a name="add-face-detection-code"></a>Přidejte kód pro rozpoznávání tváře detekce

Otevřete třídu hlavní vašeho projektu. Zde přidejte kód potřebný k načtení bitové kopie a rozpoznávání tváří.

### <a name="import-packages"></a>Import balíčků

Přidejte následující `import` příkazy do horní části souboru.

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;
```

### <a name="add-essential-fields"></a>Přidat nezbytné pole

Nahradit **hlavní** třídy následujícím kódem. Tato data Určuje, jak se připojit ke službě pro rozpoznávání tváře a kde se stáhnout vstupní data. Budete muset aktualizovat `subscriptionKey` pole s hodnotou váš klíč předplatného a může být nutné změnit `uriBase` řetězec tak, aby obsahoval oblasti správný identifikátor (najdete v článku [dokumenty k rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) seznam všech oblastí Koncové body). Možná budete také chtít nastavit `imageWithFaces` hodnotu na cestu, která odkazuje na jiný soubor obrázku.

`faceAttributes` Pole je jednoduše seznam určitých typů atributů. Určí které informace se mají načíst informace o zjištěných tváří.

```Java
public class Main {
    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // NOTE: You must use the same region in your REST call as you used to
    // obtain your subscription keys. For example, if you obtained your
    // subscription keys from westus, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westus" region. If you
    // use a free trial subscription key, you shouldn't need to change this region.
    private static final String uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";

    private static final String imageWithFaces =
        "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

    private static final String faceAttributes =
        "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

### <a name="call-the-face-detection-rest-api"></a>Volání rozhraní REST API pro rozpoznávání tváře

Přidat **hlavní** metodu s následujícím kódem. Sestaví volání REST API pro rozpoznávání tváře k detekci informací o rozpoznávání tváře ve bitovou vzdálenou ( `faceAttributes` řetězec Určuje, které pro rozpoznávání tváře atributy k načtení). Pak zapíše výstupní data na řetězec formátu JSON.

```Java
    public static void main(String[] args) {
        HttpClient httpclient = HttpClientBuilder.create().build();

        try
        {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("returnFaceId", "true");
            builder.setParameter("returnFaceLandmarks", "false");
            builder.setParameter("returnFaceAttributes", faceAttributes);

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity reqEntity = new StringEntity(imageWithFaces);
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();
```

### <a name="parse-the-json-response"></a>Analyzovat odpověď JSON

Přímo pod předchozím kódu přidejte následující blok, který převádí vrácená data JSON do více snadno čitelného formátu před tiskem do konzoly. A konečně, zavřete bloku try-catch **hlavní** metody a **hlavní** třídy.

```Java
            if (entity != null)
            {
                // Format and display the JSON response.
                System.out.println("REST Response:\n");

                String jsonString = EntityUtils.toString(entity).trim();
                if (jsonString.charAt(0) == '[') {
                    JSONArray jsonArray = new JSONArray(jsonString);
                    System.out.println(jsonArray.toString(2));
                }
                else if (jsonString.charAt(0) == '{') {
                    JSONObject jsonObject = new JSONObject(jsonString);
                    System.out.println(jsonObject.toString(2));
                } else {
                    System.out.println(jsonString);
                }
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="run-the-app"></a>Spuštění aplikace

Zkompilování kódu a spustíme ji. Úspěšná odpověď se zobrazí data pro rozpoznávání tváře ve snadno čitelném formátu JSON v okně konzoly. Příklad:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili jednoduchou konzolovou aplikaci Java, který používá volání REST pomocí rozhraní API pro rozpoznávání tváře Azure k rozpoznávání tváří v obrázku a vrátí jejich atributy. Dále se naučíte lepší pomocí této funkce v aplikaci pro Android.

> [!div class="nextstepaction"]
> [Kurz: Vytvoření aplikace pro Android ke zjišťování a snímků tváří](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
