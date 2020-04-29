---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 2ead85da805bb33247ca54bea51cccc57b0e4e94
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "69906746"
---
[!INCLUDE [Prerequisites](prerequisites-go.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt pro procházení pomocí oblíbených rozhraní IDE nebo editoru. Pak do svého projektu, do souboru s názvem `translate-text.go`, zkopírujte tento fragment kódu.

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

Tato ukázka se pokusí přečíst klíč předplatného Translator Text a koncový bod z těchto proměnných prostředí `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` : `TRANSLATOR_TEXT_ENDPOINT`a. Pokud nejste obeznámeni s proměnnými prostředí, můžete nastavit `subscriptionKey` a `endpoint` jako řetězce a komentovat podmíněné příkazy.

Zkopírujte do svého projektu tento kód:

```go
func main() {
    /*
    * Read your subscription key from an env variable.
    * Please note: You can replace this code block with
    * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
    * want to use env variables. If so, be sure to delete the "os" import.
    */
    if "" == os.Getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_SUBSCRIPTION_KEY.")
    }
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY")
    if "" == os.Getenv("TRANSLATOR_TEXT_ENDPOINT") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_ENDPOINT.")
    }
    endpoint := os.Getenv("TRANSLATOR_TEXT_ENDPOINT")
    uri := endpoint + "/translate?api-version=3.0"
    /*
     * This calls our breakSentence function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    translate(subscriptionKey, uri)
}
```

## <a name="create-a-function-to-translate-text"></a>Vytvoření funkce pro překlad textu

Pojďme vytvořit funkci pro překlad textu. Tato funkce bude mít jeden argument, Translator Text klíč předplatného.

```go
func translate(subscriptionKey string, uri string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Nyní vytvoříme adresu URL. Adresa URL je sestavena pomocí `Parse()` metod `Query()` a. Všimněte si, že parametry jsou přidány s `Add()` metodou. V této ukázce budete překládáte z angličtiny do němčiny a italštiny `de` : `it`a.

Zkopírujte tento kód do `translate` funkce.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse(uri)
q := u.Query()
q.Add("to", "de")
q.Add("to", "it")
u.RawQuery = q.Encode()
```

>[!NOTE]
> Další informace o koncových bodech, trasách a parametrech požadavků najdete v tématu [Translator Text API 3.0: Překlad](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate).

## <a name="create-a-struct-for-your-request-body"></a>Vytvoření struktury pro tělo žádosti

Dále vytvořte anonymní strukturu pro text žádosti a zakódovat ji jako JSON pomocí `json.Marshal()`. Přidejte tento kód do `translate` funkce.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "Hello, world!"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>Sestavení požadavku

Teď, když jste zakódovi tělo požadavku jako JSON, můžete sestavit požadavek POST a zavolat Translator Text API.

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

Pokud používáte Cognitive Services předplatné s více službami, musíte taky zahrnout `Ocp-Apim-Subscription-Region` do parametrů žádosti. [Přečtěte si další informace o ověřování pomocí předplatného s více službami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="handle-and-print-the-response"></a>Zpracuje a vytiskne odpověď.

Přidejte tento kód do `translate` funkce k dekódování odpovědi JSON a pak formátujte a tiskněte výsledek.

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

## <a name="next-steps"></a>Další kroky

Podívejte se na reference k rozhraní API, abyste porozuměli všem, co můžete s Translator Text API dělat.

> [!div class="nextstepaction"]
> [referenční dokumentace k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
