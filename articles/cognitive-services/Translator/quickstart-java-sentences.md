---
title: 'Rychlý start: Získání délky vět, Java – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu zjistíte jak určit délka větu pomocí Javy a rozhraní Translator Text API.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 12/03/2018
ms.author: erhopf
ms.openlocfilehash: 941467e7756faa4fd06220bafbf733f42b43e8d9
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888575"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-java"></a>Rychlý start: Použití rozhraní Translator Text API k určení délky větu pomocí Javy

V tomto rychlém startu zjistíte určení délky větu pomocí Javy a rozhraní Translator Text API.

K tomuto rychlému startu potřebujete [účet služby Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby Translator Text. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

* [JDK 7 nebo novější](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Klíč předplatného Azure pro službu Translator Text

## <a name="initialize-a-project-with-gradle"></a>Inicializovat projekt s Gradle

Začněme vytvořením pracovní adresář pro tento projekt. Z příkazového řádku (nebo terminál) spusťte tento příkaz:

```console
mkdir break-sentence-sample
cd break-sentence-sample
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
    mainClassName = "BreakSentence"
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

Dále v této složce vytvořte soubor s názvem `BreakSentence.java`.

## <a name="import-required-libraries"></a>Importujte požadované knihovny

Otevřít `BreakSentence.java` a přidat tyto příkazy import:

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
public class BreakSentence {
  // All project code goes here...
}
```

Přidejte tyto řádky do `BreakSentence` třídy. Uvidíte, že spolu s `api-version`, můžete definovat jazyk. V této ukázce je angličtina.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0&language=en";
```

## <a name="create-a-client-and-build-a-request"></a>Vytvoření klienta a žádost o sestavení

Přidejte tento řádek, který `BreakSentence` třída pro vytvoření instance `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

V dalším kroku Vytvořme požadavek POST. Nebojte se změní celý text. Text musí být uvozeny řídicími znaky.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"How are you? I am fine. What did you do today?\"\n}]");
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
        BreakSentence breakSentenceRequest = new BreakSentence();
        String response = breakSentenceRequest.Post();
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

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>Další postup

Prozkoumejte vzorový kód pro tento rychlý start a další, včetně překladu a transkripce, a také další vzorové projekty Translator Text na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte příklady v Javě na GitHubu](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>Další informace najdete v tématech

* [Překlad textu](quickstart-java-translate.md)
* [Transliterace textu](quickstart-java-transliterate.md)
* [Identifikace jazyka podle vstupu](quickstart-java-detect.md)
* [Získání alternativních překladů](quickstart-java-dictionary.md)
* [Získání seznamu podporovaných jazyků](quickstart-java-languages.md)
* [Určení délky věty ze vstupu](quickstart-java-sentences.md)
