---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 707e6c1fb063ca7c8580df4ace2685417fd7847d
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968696"
---
## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Go](https://golang.org/doc/install)

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt přejít pomocí oblíbeného integrovaného vývojového prostředí (IDE) nebo editoru nebo nové složky na ploše. Potom tento fragment kódu zkopírujte do svého projektu nebo složky v souboru s názvem `get-languages.go`.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)
```

## <a name="create-the-main-function"></a>Vytvoření funkce main

Pojďme vytvořit hlavní funkci pro naši aplikaci. Všimněte si, že se jedná o jediný řádek kódu. To je proto, že vytváříme jednu funkci pro získání a tisk seznamu podporovaných jazyků pro Translator Text.

Zkopírujte do svého projektu tento kód:

```go
func main() {
    /*
     * This calls our getLanguages function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    getLanguages()
}
```

## <a name="create-a-function-to-get-a-list-of-supported-languages"></a>Vytvoření funkce, která získá seznam podporovaných jazyků

Pojďme vytvořit funkci, která získá seznam podporovaných jazyků.

```go
func getLanguages() {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Nyní vytvoříme adresu URL. Adresa URL je sestavena pomocí `Parse()` metod `Query()` a.

Zkopírujte tento kód do `getLanguages` funkce.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/languages")
q := u.Query()
q.Add("api-version", "3.0")
u.RawQuery = q.Encode()
```

>[!NOTE]
> Další informace o koncových bodech, trasách a parametrech požadavků [najdete v článku Translator text API 3,0: Jazyky](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

## <a name="build-the-request"></a>Žádost o sestavení

Teď, když jste zakódovi tělo požadavku jako JSON, můžete sestavit požadavek POST a zavolat Translator Text API.

```go
// Build the HTTP GET request
req, err := http.NewRequest("GET", u.String(), nil)
if err != nil {
    log.Fatal(err)
}
// Add required headers
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

Pokud používáte Cognitive Services předplatné s více službami, musíte taky zahrnout `Ocp-Apim-Subscription-Region` do parametrů žádosti. [Přečtěte si další informace o ověřování pomocí předplatného s více službami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="handle-and-print-the-response"></a>Zpracuje a vytiskne odpověď.

Přidejte tento kód do `getLanguages` funkce k dekódování odpovědi JSON a pak formátujte a tiskněte výsledek.

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
go run get-languages.go
```

Pokud chcete porovnat svůj kód s naším, kompletní ukázka je k dispozici na [GitHubu](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Ukázková odpověď

V tomto [seznamu jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)Najděte zkratku země/oblasti.

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
{
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  },
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Další kroky

Podívejte se na reference k rozhraní API, abyste porozuměli všem, co můžete s Translator Text API dělat.

> [!div class="nextstepaction"]
> [Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
