---
title: 'Rychlý start: Přeložení textu, Javy – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu budete zjistěte, jak převést řetězec textu z angličtiny italština a němčina pomocí Javy a rozhraní REST Translator Text API.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 12/03/2018
ms.author: erhopf
ms.openlocfilehash: e2e7535476965258d02f3cd3db0126a0e8a486ab
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963620"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-using-java"></a>Rychlý start: Použití rozhraní Translator Text API pro převod řetězce pomocí Javy

V tomto rychlém startu budete zjistěte, jak převést řetězec textu z angličtiny italština a němčina pomocí Javy a rozhraní REST Translator Text API.

K tomuto rychlému startu potřebujete [účet služby Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby Translator Text. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

* [JDK 7 nebo novější](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Klíč předplatného Azure pro službu Translator Text

## <a name="initialize-a-project-with-gradle"></a>Inicializovat projekt s Gradle

Začněme vytvořením pracovní adresář pro tento projekt. Z příkazového řádku (nebo terminál) spusťte tento příkaz:

```console
mkdir translator-sample
cd translator-sample
```

V dalším kroku budete inicializovat projekt Gradle. Tento příkaz vytvoří soubory nezbytné sestavení pro Gradle, nejdůležitější ale je, `build.gradle.kts`, který se používá v době běhu k vytvoření a konfigurace aplikace. Spuštěním tohoto příkazu z pracovní adresář:

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **DSL**vyberte **Kotlin**.

## <a name="configure-the-build-file"></a>Konfigurace souboru sestavení

Vyhledejte `build.gradle.kts` a otevřete jej pomocí vašeho oblíbeného integrovaného vývojového prostředí textového editoru nebo editoru. Zkopírujte tuto konfiguraci sestavení:

```
plugins {
    java
    application
}
application {
    mainClassName = "Translate"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Povšimněte si, že tato ukázka obsahuje závislosti na OkHttp pro požadavky HTTP a Gson ke zpracování a parsování formátu JSON. Pokud chcete získat další informace o konfiguracích sestavení, naleznete v tématu [vytváření nového sestavení Gradle](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Vytvořte soubor Java

Pojďme vytvořit složku pro ukázkovou aplikaci. Z pracovního adresáře spusťte:

```console
mkdir -p src/main/java
```

Dále v této složce vytvořte soubor s názvem `Translate.java`.

## <a name="import-required-libraries"></a>Importujte požadované knihovny

Otevřít `Translate.java` a přidat tyto příkazy import:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```

## <a name="define-variables"></a>Definování proměnných

Nejprve budete muset vytvořit veřejnou třídu pro váš projekt:

```java
public class Translate {
  // All project code goes here...
}
```

Přidejte tyto řádky do `Translate` třídy. Uvidíte, že spolu s `api-version`, dva další parametry se připojili k `url`. Tyto parametry slouží k nastavení překladu výstupy. V této ukázce je nastaven na němčinu (`de`) a italština (`it`). Nezapomeňte že aktualizovat hodnotu klíče předplatného.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=de,it";
```

## <a name="create-a-client-and-build-a-request"></a>Vytvoření klienta a žádost o sestavení

Přidejte tento řádek, který `Translate` třída pro vytvoření instance `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

V dalším kroku Vytvořme požadavek POST. Nebojte se změní celý text pro překlad. Text musí být uvozeny řídicími znaky.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Welcome to Microsoft Translator. Guess how many languages I speak!\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Vytvoření funkce pro parsování odpovědi

Tato jednoduchá funkce analyzuje a prettifies odpověď JSON od službu Translator Text.

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

Posledním krokem je vytvořit žádost a získejte odpověď. Do projektu přidejte tyto řádky:

```java
public static void main(String[] args) {
    try {
        Translate translateRequest = new Translate();
        String response = translateRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Je to, jste připraveni spustit ukázkovou aplikaci. Z příkazového řádku (nebo relaci Terminálové služby) přejděte do kořenového adresáře pracovního adresáře a spusťte:

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
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Willkommen bei Microsoft Translator. Erraten Sie, wie viele Sprachen ich spreche!",
        "to": "de"
      },
      {
        "text": "Benvenuti a Microsoft Translator. Indovinate quante lingue parlo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Další postup

Prozkoumejte vzorový kód pro tento a další rychlé starty, včetně transkripce a identifikace jazyka a také dalších vzorových projektů služby Translator Text na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte příklady v Javě na GitHubu](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>Další informace najdete v tématech

* [Transliterace textu](quickstart-java-transliterate.md)
* [Identifikace jazyka podle vstupu](quickstart-java-detect.md)
* [Získání alternativních překladů](quickstart-java-dictionary.md)
* [Získání seznamu podporovaných jazyků](quickstart-java-languages.md)
* [Určení délky věty ze vstupu](quickstart-java-sentences.md)
