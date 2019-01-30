---
title: 'Rychlý start: Převést text skriptu Java – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu zjistíte jak transkripce text (převod) z jednoho skriptu na jinou pomocí Javy a rozhraní REST Translator Text API. V této ukázce se provádí transkripce z japonštiny do latinky.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 12/03/2018
ms.author: erhopf
ms.openlocfilehash: e42aa7ec796b3be912614ced5f3a4db47f67c124
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210050"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-java"></a>Rychlý start: Transkripce textu s použitím jazyka Java pomocí rozhraní Translator Text API

V tomto rychlém startu zjistíte jak transkripce text (převod) z jednoho skriptu na jinou pomocí Javy a rozhraní REST Translator Text API. V uvedené ukázce se provádí transkripce z japonštiny do latinky.

K tomuto rychlému startu potřebujete [účet služby Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby Translator Text. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

* [JDK 7 nebo novější](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Klíč předplatného Azure pro službu Translator Text

## <a name="initialize-a-project-with-gradle"></a>Inicializovat projekt s Gradle

Začněme vytvořením pracovní adresář pro tento projekt. Z příkazového řádku (nebo terminál) spusťte tento příkaz:

```console
mkdir transliterate-sample
cd transliterate-sample
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

Povšimněte si, že tato ukázka obsahuje závislosti na OkHttp pro požadavky HTTP a Gson ke zpracování a parsování formátu JSON. Pokud chcete získat další informace o konfiguracích sestavení, naleznete v tématu [vytváření nového sestavení Gradle](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Vytvořte soubor Java

Pojďme vytvořit složku pro ukázkovou aplikaci. Z pracovního adresáře spusťte:

```console
mkdir -p src/main/java
```

Dále v této složce vytvořte soubor s názvem `Transliterate.java`.

## <a name="import-required-libraries"></a>Importujte požadované knihovny

Otevřít `Transliterate.java` a přidat tyto příkazy import:

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
public class Transliterate {
  // All project code goes here...
}
```

Přidejte tyto řádky do `Transliterate` třídy. Uvidíte, že spolu s `api-version`, dva další parametry se připojili k `url`. Tyto parametry slouží k nastavení jazyk a skriptů pro přepis. V tomto příkladu je nastavena na japonština (`jpan`) a latinky (`latn`). Nezapomeňte že aktualizovat hodnotu klíče předplatného.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
```

## <a name="create-a-client-and-build-a-request"></a>Vytvoření klienta a žádost o sestavení

Přidejte tento řádek, který `Transliterate` třída pro vytvoření instance `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

V dalším kroku Vytvořme požadavek POST. Nebojte se změní celý text pro přepis.

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
        Transliterate transliterateRequest = new Transliterate();
        String response = transliterateRequest.Post();
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

## <a name="sample-response"></a>Ukázková odpověď

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Další postup

Prozkoumejte vzorový kód pro tento a další rychlé starty, včetně překladu a identifikace jazyka a také dalších vzorových projektů služby Translator Text na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte příklady v Javě na GitHubu](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>Další informace najdete v tématech

* [Překlad textu](quickstart-java-translate.md)
* [Identifikace jazyka podle vstupu](quickstart-java-detect.md)
* [Získání alternativních překladů](quickstart-java-dictionary.md)
* [Získání seznamu podporovaných jazyků](quickstart-java-languages.md)
* [Určení délky věty ze vstupu](quickstart-java-sentences.md)
