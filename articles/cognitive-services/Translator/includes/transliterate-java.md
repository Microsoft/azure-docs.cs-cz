---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 82766e969f62f35f39f6545eb4c2c6bdda29c11b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586493"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Inicializovat projekt pomocí Gradle

Pojďme začít vytvořením pracovního adresáře pro tento projekt. Z příkazového řádku (nebo terminálu) spusťte tento příkaz:

```console
mkdir transliterate-sample
cd transliterate-sample
```

Teď budete chtít inicializovat projekt Gradle. Tento příkaz vytvoří základní soubory sestavení pro Gradle, co je nejdůležitější, a `build.gradle.kts` který se používá za běhu k vytvoření a konfiguraci vaší aplikace. Spusťte tento příkaz z pracovního adresáře:

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
    mainClassName = "Transliterate"
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

## <a name="create-a-java-file"></a>Vytvoření souboru Java

Pojďme vytvořit složku pro ukázkovou aplikaci. V pracovním adresáři spusťte:

```console
mkdir -p src\main\java
```

Potom v této složce vytvořte soubor s názvem `Transliterate.java` .

## <a name="import-required-libraries"></a>Importovat požadované knihovny

Otevřete `Transliterate.java` a přidejte tyto příkazy pro import:

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
public class Transliterate {
  // All project code goes here...
}
```

Přidejte tyto řádky do `Transliterate` třídy. Nejdřív se klíč předplatného a koncový bod čtou z proměnných prostředí. Pak si všimněte, že spolu s, byly `api-version` do nástroje připojeny dva další parametry `url` . Tyto parametry slouží k nastavení vstupního jazyka a ke skriptům pro předávání. V této ukázce je nastavená na japonštinu ( `jpan` ) a latinku ( `latn` ). 

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
```
Pokud používáte Cognitive Services předplatné s více službami, musíte taky zahrnout `Ocp-Apim-Subscription-Region` do parametrů žádosti. [Přečtěte si další informace o ověřování pomocí předplatného s více službami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Vytvoření klienta a sestavení žádosti

Přidejte tento řádek do `Transliterate` třídy pro vytvoření instance `OkHttpClient` :

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Nyní sestavíme požadavek POST. Můžete měnit text pro mezidobu.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"こんにちは\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Vytvoření funkce pro analýzu odpovědi

Tato jednoduchá funkce analyzuje a prettifies odpověď JSON ze služby Translator.

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
        Transliterate transliterateRequest = new Transliterate();
        String response = transliterateRequest.Post();
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
    "text": "konnichiwa",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Další kroky

Podívejte se na reference k rozhraní API, abyste porozuměli všem, co můžete s překladatelem dělat.

> [!div class="nextstepaction"]
> [referenční dokumentace k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
