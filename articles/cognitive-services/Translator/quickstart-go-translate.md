---
title: Translator Text – překlad textu s Go | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: V tomto rychlém startu přeložíte text z jednoho jazyka do jiného pomocí služby Translator Text API s Go ve službách Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: 72d4a3bc7563d6427335f1536f787eab6d7a0de4
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "43769114"
---
# <a name="quickstart-translate-text-with-go"></a>Rychlý start: Překlad textu s Go

V tomto rychlém startu přeložíte text z jednoho jazyka do jiného pomocí služby Translator Text API.

## <a name="prerequisites"></a>Požadavky

Abyste mohli tento kód spustit, musíte nainstalovat [distribuci Go](https://golang.org/doc/install). Vzorový kód používá pouze **základní** knihovny, takže neexistují žádné externí závislosti.

Abyste mohli použít službu Translator Text API, budete potřebovat klíč předplatného. Přečtěte si [jak se zaregistrovat ve službě Translator Text API](translator-text-how-to-signup.md).

## <a name="translate-request"></a>Žádost Translate

Následující kód překládá zdrojový text z jednoho jazyka do jiného pomocí metody [Translate](./reference/v3-0-translate.md).

1. Ve svém oblíbeném editoru kódu vytvořte nový projekt Go.
2. Přidejte níže uvedený kód.
3. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Uložte soubor s příponou .go.
5. Na počítači s nainstalovaným jazykem Go otevřete příkazové okno.
6. Vytvořte soubor, například: go build quickstart-translate.go.
7. Spusťte soubor, například: quickstart-translate.

```golang
package translate

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
    const uriPath = "/translate?api-version=3.0"

    // Translate to German and Italian
    const params = "&to=de&to=it"

    const uri = uriBase + uriPath + params

    const text = "Hello, world!"

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

## <a name="translate-response"></a>Odpověď metody Translate

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

Prozkoumejte balíčky Go pro rozhraní API služeb Cognitive Services v tématu [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte balíčky Go na GitHubu](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
