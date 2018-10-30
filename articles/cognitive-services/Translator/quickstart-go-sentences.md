---
title: 'Rychlý start: Získání délky vět, Go – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu zjistíte délky vět v textu pomocí rozhraní Translator Text API a jazyka Go.
services: cognitive-services
author: noellelacharite
manager: erhopf
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: erhopf
ms.openlocfilehash: 92ea2291760edf16863dc58a00bac4389f818e72
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49644903"
---
# <a name="quickstart-get-sentence-lengths-with-the-translator-text-rest-api-go"></a>Rychlý start: Získání délky vět pomocí rozhraní REST API služby Translator Text (Go)

V tomto rychlém startu zjistíte délky vět v textu pomocí služby Translator Text API.

## <a name="prerequisites"></a>Požadavky

Abyste mohli tento kód spustit, musíte nainstalovat [distribuci Go](https://golang.org/doc/install). Vzorový kód používá pouze **základní** knihovny, takže neexistují žádné externí závislosti.

Abyste mohli použít službu Translator Text API, budete potřebovat klíč předplatného. Přečtěte si [jak se zaregistrovat ve službě Translator Text API](translator-text-how-to-signup.md).

## <a name="breaksentence-request"></a>Žádost BreakSentence

Následující kód rozdělí zdrojový text do vět pomocí metody [BreakSentence](./reference/v3-0-break-sentence.md).

1. Ve svém oblíbeném editoru kódu vytvořte nový projekt Go.
2. Přidejte níže uvedený kód.
3. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Uložte soubor s příponou .go.
5. Na počítači s nainstalovaným jazykem Go otevřete příkazové okno.
6. Vytvořte soubor, například: go build quickstart-sentences.go.
7. Spusťte soubor, například: quickstart-sentences.

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
    const uriPath = "/breaksentence?api-version=3.0"

    const uri = uriBase + uriPath

    const text = "How are you? I am fine. What did you do today?"

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

## <a name="breaksentence-response"></a>Odpověď metody BreakSentence

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

## <a name="next-steps"></a>Další kroky

Prozkoumejte balíčky Go pro rozhraní API služeb Cognitive Services v tématu [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte balíčky Go na GitHubu](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
