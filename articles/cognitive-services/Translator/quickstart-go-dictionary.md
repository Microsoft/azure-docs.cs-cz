---
title: Translator Text – vyhledání alternativních překladů s Go | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: V tomto rychlém startu vyhledáte alternativní překlady a příklady termínů v kontextu pomocí služby Translator Text API s Go ve službách Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: a076418dbf969a61107c28f191457fc336a8b907
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "43769109"
---
# <a name="quickstart-find-alternate-translations-and-usage-with-go"></a>Rychlý start: Vyhledání alternativních překladů a použití s Go

V tomto rychlém startu vyhledáte podrobnosti možných alternativních překladů termínu a také příklady použití těchto alternativních překladů pomocí služby Translator Text API.

## <a name="prerequisites"></a>Požadavky

Abyste mohli tento kód spustit, musíte nainstalovat [distribuci Go](https://golang.org/doc/install). Vzorový kód používá pouze **základní** knihovny, takže neexistují žádné externí závislosti.

Abyste mohli použít službu Translator Text API, budete potřebovat klíč předplatného. Přečtěte si [jak se zaregistrovat ve službě Translator Text API](translator-text-how-to-signup.md).

## <a name="dictionary-lookup-request"></a>Žádost Dictionary Lookup

Následující kód získá alternativní překlady slova pomocí metody [Dictionary Lookup](./reference/v3-0-dictionary-lookup.md).

1. Ve svém oblíbeném editoru kódu vytvořte nový projekt Go.
2. Přidejte níže uvedený kód.
3. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Uložte soubor s příponou .go.
5. Na počítači s nainstalovaným jazykem Go otevřete příkazové okno.
6. Vytvořte soubor, například: go build quickstart-lookup.go.
7. Spusťte soubor, například: quickstart-lookup.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    const uriBase = "https://api.cognitive.microsofttranslator.com"
    const uriPath = "/dictionary/lookup?api-version=3.0"

    // From English to French
    const params = "&from=en&to=fr"

    const uri = uriBase + uriPath + params

    const text = "great"

    r := strings.NewReader("[{\"Text\" : \"" + text + "\"}]")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.FormatInt(req.ContentLength, 10))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="dictionary-lookup-response"></a>Odpověď metody Dictionary Lookup

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
[
  {
    "normalizedSource": "great",
    "displaySource": "great",
    "translations": [
      {
        "normalizedTarget": "grand",
        "displayTarget": "grand",
        "posTag": "ADJ",
        "confidence": 0.2783,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 34358
          },
          {
            "normalizedText": "big",
            "displayText": "big",
            "numExamples": 15,
            "frequencyCount": 21770
          },
...
        ]
      },
      {
        "normalizedTarget": "super",
        "displayTarget": "super",
        "posTag": "ADJ",
        "confidence": 0.1514,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "super",
            "displayText": "super",
            "numExamples": 15,
            "frequencyCount": 12023
          },
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 10931
          },
...
        ]
      },
...
    ]
  }
]
```

## <a name="dictionary-examples-request"></a>Žádost Dictionary Examples

Následující kód získá kontextové příklady použití termínu ve slovníku pomocí metody [Dictionary Examples](./reference/v3-0-dictionary-examples.md).

1. Ve svém oblíbeném editoru kódu vytvořte nový projekt Go.
2. Přidejte níže uvedený kód.
3. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Uložte soubor s příponou .go.
5. Na počítači s nainstalovaným jazykem Go otevřete příkazové okno.
6. Vytvořte soubor, například: go build quickstart-examples.go.
7. Spusťte soubor, například: quickstart-examples.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    const uriBase = "https://api.cognitive.microsofttranslator.com"
    const uriPath = "/dictionary/examples?api-version=3.0"

    // From English to French
    const params = "&from=en&to=fr"

    const uri = uriBase + uriPath + params

    const text = "great"
    const translation = "formidable"

    r := strings.NewReader("[{\"Text\" : \"" + text + "\", \"Translation\" : \"" + translation + "\"}]")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.FormatInt(req.ContentLength, 10))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="dictionary-examples-response"></a>Odpověď metody Dictionary Examples

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
[
  {
    "normalizedSource": "great",
    "normalizedTarget": "formidable",
    "examples": [
      {
        "sourcePrefix": "You have a ",
        "sourceTerm": "great",
        "sourceSuffix": " expression there.",
        "targetPrefix": "Vous avez une expression ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
      {
        "sourcePrefix": "You played a ",
        "sourceTerm": "great",
        "sourceSuffix": " game today.",
        "targetPrefix": "Vous avez été ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
...
    ]
  }
]
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte balíčky Go pro rozhraní API služeb Cognitive Services v tématu [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte balíčky Go na GitHubu](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
