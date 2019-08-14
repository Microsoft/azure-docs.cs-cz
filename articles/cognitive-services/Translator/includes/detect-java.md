---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c7da8781767401a79bfca5c70e0a5f6244ed8d8b
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968710"
---
## <a name="prerequisites"></a>Požadavky

* [JDK 7 nebo novější](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Klíč předplatného Azure pro službu Translator Text

## <a name="initialize-a-project-with-gradle"></a>Inicializovat projekt pomocí Gradle

Pojďme začít vytvořením pracovního adresáře pro tento projekt. Z příkazového řádku (nebo terminálu) spusťte tento příkaz:

```console
mkdir detect-sample
cd detect-sample
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
    mainClassName = "Detect"
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
mkdir -p src/main/java
```

Potom v této složce vytvořte soubor s názvem `Detect.java`.

## <a name="import-required-libraries"></a>Importovat požadované knihovny

Otevřete `Detect.java` a přidejte tyto příkazy pro import:

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
public class Detect {
  // All project code goes here...
}
```

Přidejte tyto řádky do `Detect` třídy:

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0";
```

Pokud používáte Cognitive Services předplatné s více službami, musíte taky zahrnout `Ocp-Apim-Subscription-Region` do parametrů žádosti. [Přečtěte si další informace o ověřování pomocí předplatného s více službami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Vytvoření klienta a sestavení žádosti

Přidejte tento řádek `Detect` do třídy pro vytvoření instance `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Nyní sestavíme požadavek POST. Můžete měnit text pro detekci jazyka.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Salve mondo!\"\n}]");
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
        Detect detectRequest = new Detect();
        String response = detectRequest.Post();
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

V tomto [seznamu jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)Najděte zkratku země/oblasti.

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="next-steps"></a>Další kroky

Podívejte se na reference k rozhraní API, abyste porozuměli všem, co můžete s Translator Text API dělat.

> [!div class="nextstepaction"]
> [Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
