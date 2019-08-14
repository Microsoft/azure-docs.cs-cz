---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 64bedef3cf52451d145a97385937ae2adc9b2b0c
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968619"
---
## <a name="prerequisites"></a>Požadavky

* [JDK 7 nebo novější](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Klíč předplatného Azure pro službu Translator Text

## <a name="initialize-a-project-with-gradle"></a>Inicializovat projekt pomocí Gradle

Pojďme začít vytvořením pracovního adresáře pro tento projekt. Z příkazového řádku (nebo terminálu) spusťte tento příkaz:

```console
mkdir alt-translation-sample
cd alt-translation-sample
```

Teď budete chtít inicializovat projekt Gradle. Tento příkaz vytvoří základní soubory sestavení pro Gradle, co je nejdůležitější, `build.gradle.kts`a který se používá za běhu k vytvoření a konfiguraci vaší aplikace. Spusťte tento příkaz z pracovního adresáře:

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **DSL**vyberte **Kotlin**.

## <a name="configure-the-build-file"></a>Konfigurovat soubor sestavení

Vyhledejte `build.gradle.kts` ho a otevřete ho pomocí svého oblíbeného integrovaného vývojového prostředí nebo textového editoru. Pak zkopírujte do této konfigurace sestavení:

```
plugins {
    java
    application
}
application {
    mainClassName = "AltTranslation"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Všimněte si, že tato ukázka obsahuje závislosti na OkHttp pro požadavky HTTP a gson pro zpracování a analýzu JSON. Pokud se chcete dozvědět více o konfiguracích sestavení, přečtěte si téma [vytváření nových Gradle sestavení](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Vytvořte soubor Java

Pojďme vytvořit složku pro ukázkovou aplikaci. V pracovním adresáři spusťte:

```console
mkdir -p src\main\java
```

Potom v této složce vytvořte soubor s názvem `AltTranslation.java`.

## <a name="import-required-libraries"></a>Importovat požadované knihovny

Otevřete `AltTranslation.java` a přidejte tyto příkazy pro import:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Definování proměnných

Nejprve budete muset vytvořit veřejnou třídu pro svůj projekt:

```java
public class AltTranslation {
  // All project code goes here...
}
```

Přidejte tyto řádky do `AltTranslation` třídy. Všimněte si, že spolu se `api-version`dvěma dalšími parametry byly připojeny `url`k. Tyto parametry slouží k nastavení vstupu a výstupu překladu. V této ukázce se jedná o angličtinu (`en`) a španělštinu`es`().

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es";
```

Pokud používáte Cognitive Services předplatné s více službami, musíte taky zahrnout `Ocp-Apim-Subscription-Region` do parametrů žádosti. [Přečtěte si další informace o ověřování pomocí předplatného s více službami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Vytvoření klienta a sestavení žádosti

Přidejte tento řádek `AltTranslation` do třídy pro vytvoření instance `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Nyní sestavíme požadavek POST. Můžete změnit text pro překlad.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Pineapples\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Vytvoření funkce pro analýzu odpovědi

Tato jednoduchá funkce analyzuje a prettifies odpověď JSON od služby Translator Text.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Posledním krokem je vytvoření žádosti a získání odpovědi. Přidejte tyto řádky do projektu:

```java
public static void main(String[] args) {
    try {
        AltTranslation altTranslationRequest = new AltTranslation();
        String response = altTranslationRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

To je to, že jste připraveni spustit ukázkovou aplikaci. Z příkazového řádku (nebo relace Terminálové služby) přejděte do kořenového adresáře svého pracovního adresáře a spusťte příkaz:

```console
gradle build
```

Po dokončení sestavení spusťte:

```console
gradle run
```

## <a name="sample-response"></a>Ukázková odpověď

```json
[
  {
    "normalizedSource": "pineapples",
    "displaySource": "pineapples",
    "translations": [
      {
        "normalizedTarget": "piñas",
        "displayTarget": "piñas",
        "posTag": "NOUN",
        "confidence": 0.7016,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "pineapples",
            "displayText": "pineapples",
            "numExamples": 5,
            "frequencyCount": 158
          },
          {
            "normalizedText": "cones",
            "displayText": "cones",
            "numExamples": 5,
            "frequencyCount": 13
          },
          {
            "normalizedText": "piña",
            "displayText": "piña",
            "numExamples": 3,
            "frequencyCount": 5
          },
          {
            "normalizedText": "ganks",
            "displayText": "ganks",
            "numExamples": 2,
            "frequencyCount": 3
          }
        ]
      },
      {
        "normalizedTarget": "ananás",
        "displayTarget": "ananás",
        "posTag": "NOUN",
        "confidence": 0.2984,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "pineapples",
            "displayText": "pineapples",
            "numExamples": 2,
            "frequencyCount": 16
          }
        ]
      }
    ]
  }
]
```

## <a name="next-steps"></a>Další postup

Podívejte se na reference k rozhraní API, abyste porozuměli všem, co můžete s Translator Text API dělat.

> [!div class="nextstepaction"]
> [Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
