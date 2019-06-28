---
title: 'Rychlý start: Přeložení textu, přejděte – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu budete přeložení textu z jednoho jazyka do druhého rozhraní Translator Text API s využitím Go za méně než 10 minut.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e23ff7c38d5f2410c3fd0569cd0fda0ba92cf607
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445192"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-using-go"></a>Rychlý start: Použití rozhraní Translator Text API pro převod řetězce pomocí jazyka Go

V tomto rychlém startu zjistíte jak Převede textový řetězec z angličtiny italština a němčina pomocí Go a rozhraní REST Translator Text API.

K tomuto rychlému startu potřebujete [účet služby Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby Translator Text. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Go](https://golang.org/doc/install)
* Klíč předplatného Azure pro službu Translator Text

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvoření nového projektu přejít pomocí Oblíbené prostředí IDE nebo editoru. Pak do svého projektu, do souboru s názvem `translate-text.go`, zkopírujte tento fragment kódu.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>Vytvoření funkce main

Tato ukázka se pokusí přečíst klíč předplatného služby Translator Text z proměnné prostředí `TRANSLATOR_TEXT_KEY`. Pokud proměnné prostředí neznáte, můžete hodnotu `subscriptionKey` nastavit jako řetězec a okomentovat podmíněný příkaz.

Zkopírujte do svého projektu tento kód:

```go
func main() {
    /*
     * Read your subscription key from an env variable.
     * Please note: You can replace this code block with
     * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
     * want to use env variables. If so, be sure to delete the "os" import.
     */
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_KEY")
    if subscriptionKey == "" {
       log.Fatal("Environment variable TRANSLATOR_TEXT_KEY is not set.")
    }
    /*
     * This calls our translate function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    translate(subscriptionKey)
}
```

## <a name="create-a-function-to-translate-text"></a>Vytvoření funkce pro převod textu

Pojďme vytvořit funkci pro převod textu. Tato funkce se přijímají jediný argument, váš klíč předplatného Translator Text.

```go
func translate(subscriptionKey string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Dále můžeme se vytvoří adresa URL. Adresa URL se vytvořil pomocí `Parse()` a `Query()` metody. Všimněte si, že se přidají parametry `Add()` metody. V této ukázce jste už převod angličtina a němčina, italština: `de` a `it`.

Zkopírujte tento kód do `translate` funkce.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/translate?api-version=3.0")
q := u.Query()
q.Add("to", "de")
q.Add("to", "it")
u.RawQuery = q.Encode()
```

>[!NOTE]
> Další informace o koncových bodech, cesty a parametry požadavku najdete v tématu [Translator Text API 3.0: Přeložit](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate).

## <a name="create-a-struct-for-your-request-body"></a>Vytvoření struktury vaší tělo žádosti

V dalším kroku vytvoření anonymní struktury tělo žádosti a kódovat jako dokumenty JSON pomocí `json.Marshal()`. Přidejte tento kód `translate` funkce.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "Hello, world!"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>Žádost o sestavení

Teď, když jste kódování textu žádosti jako dokumenty JSON, můžete vytvořit váš požadavek POST a volání rozhraní Translator Text API.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

Pokud používáte víc služeb předplatného služeb Cognitive Services, musíte taky zahrnout `Ocp-Apim-Subscription-Region` v parametry požadavku. [Další informace o dvojúrovňovém víc služeb předplatného](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication). 

## <a name="handle-and-print-the-response"></a>Zpracování a tisku odpověď

Přidejte tento kód `translate` funkce, která se dekódovat odpověď JSON a poté ho naformátujte a vytiskne výsledek.

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

To je vše, sestavili jste jednoduchý program, který zavolá službu Translator Text API a vrátí odpověď JSON. Teď je čas program spustit:

```console
go run translate-text.go
```

Pokud chcete porovnat svůj kód s naším, kompletní ukázka je k dispozici na [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Ukázková odpověď

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Další postup

Prozkoumejte příklady Go pro rozhraní API služeb Cognitive Services z [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) na Githubu.

> [!div class="nextstepaction"]
> [Prozkoumejte příklady přejít na Githubu](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)

## <a name="see-also"></a>Další informace najdete v tématech

Další informace o použití rozhraní Translator Text API na:

* [Transliterace textu](quickstart-go-transliterate.md)
* [Identifikace jazyka podle vstupu](quickstart-go-detect.md)
* [Získání alternativních překladů](quickstart-go-dictionary.md)
* [Získání seznamu podporovaných jazyků](quickstart-go-languages.md)
* [Určení délky věty ze vstupu](quickstart-go-sentences.md)
