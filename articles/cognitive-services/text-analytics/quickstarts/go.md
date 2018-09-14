---
title: Rychlý start pro Go a Azure Cognitive Services, rozhraní API pro analýzu textu | Microsoft Docs
description: Získejte informace a vzorové kódy, které vám usnadní začátky práce s rozhraním API pro analýzu textu ve službách Microsoft Cognitive Services v Azure.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: nolachar
ms.openlocfilehash: 0969700434e3f848aebfa833f0816c6f9f019560
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "43769149"
---
# <a name="quickstart-for-text-analytics-api-with-go"></a>Rychlý start pro rozhraní API pro analýzu textu s Go 
<a name="HOLTop"></a>

Tento článek vás seznámí s tím, jak [rozpoznat jazyk](#Detect), [analyzovat mínění](#SentimentAnalysis), [extrahovat klíčové fráze](#KeyPhraseExtraction) a [identifikovat propojené entity](#Entities) pomocí [rozhraní API pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759711) s využitím Go.

Technickou dokumentaci pro tato rozhraní API najdete v [definicích rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Požadavky

Musíte mít [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraním API analýzu textu**. Pro provedení tohoto rychlého startu můžete použít **bezplatnou úroveň 5 000 transakcí za měsíc**.

Dále musíte mít [koncový bod a přístupový klíč](../How-tos/text-analytics-how-to-access-key.md) vygenerovaný během registrace.

<a name="Detect"></a>

## <a name="detect-language-request"></a>Žádost Detect Language

Rozhraní API pro rozpoznávání jazyka rozpozná jazyk textového dokumentu pomocí [metody Detect Language](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

1. Ve svém oblíbeném editoru kódu vytvořte nový projekt Go.
1. Přidejte níže uvedený kód.
1. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
1. Nahraďte umístění v `uriBase` (momentálně `westcentralus`) oblastí, kterou jste si zaregistrovali.
1. Uložte soubor s příponou .go.
1. Na počítači s nainstalovaným jazykem Go otevřete příkazové okno.
1. Vytvořte soubor, například: go build quickstart.go.
1. Spusťte soubor, například: quickstart.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    /*
    Replace or verify the region.

    You must use the same region in your REST API call as you used to obtain your access keys.
    For example, if you obtained your access keys from the westus region, replace 
    "westcentralus" in the URI below with "westus".

    NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
    a free trial access key, you should not need to change this region.
    */
    const uriBase =    "https://westcentralus.api.cognitive.microsoft.com"
    const uriPath = "/text/analytics/v2.0/languages"

    const uri = uriBase + uriPath

    data := []map[string]string{
        {"id": "1", "text": "This is a document written in English."},
        {"id": "2", "text": "Este es un document escrito en Español."},
        {"id": "3", "text": "这是一个用中文写的文件"},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
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

## <a name="detect-language-response"></a>Odpověď metody Detect Language

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment-request"></a>Žádost Analyze Sentiment

Rozhraní API pro analýzu mínění rozpozná mínění sady textových záznamů pomocí [metody Sentiment](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). Následující příklad vyhodnotí dva dokumenty, jeden v angličtině a druhý ve španělštině.

1. Ve svém oblíbeném editoru kódu vytvořte nový projekt Go.
1. Přidejte níže uvedený kód.
1. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
1. Nahraďte umístění v `uriBase` (momentálně `westcentralus`) oblastí, kterou jste si zaregistrovali.
1. Uložte soubor s příponou .go.
1. Na počítači s nainstalovaným jazykem Go otevřete příkazové okno.
1. Vytvořte soubor, například: go build quickstart.go.
1. Spusťte soubor, například: quickstart.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    /*
    Replace or verify the region.

    You must use the same region in your REST API call as you used to obtain your access keys.
    For example, if you obtained your access keys from the westus region, replace 
    "westcentralus" in the URI below with "westus".

    NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
    a free trial access key, you should not need to change this region.
    */
    const uriBase =    "https://westcentralus.api.cognitive.microsoft.com"
    const uriPath = "/text/analytics/v2.0/sentiment"

    const uri = uriBase + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
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

## <a name="analyze-sentiment-response"></a>Odpověď metody Analyze Sentiment

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases-request"></a>Žádost Extract Key Phrases

Rozhraní API pro extrakci klíčových frází extrahuje klíčové fráze z textového dokumentu pomocí [metody Key Phrases](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Následující příklad extrahuje klíčové fráze z anglického i španělského dokumentu.

1. Ve svém oblíbeném editoru kódu vytvořte nový projekt Go.
1. Přidejte níže uvedený kód.
1. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
1. Nahraďte umístění v `uriBase` (momentálně `westcentralus`) oblastí, kterou jste si zaregistrovali.
1. Uložte soubor s příponou .go.
1. Na počítači s nainstalovaným jazykem Go otevřete příkazové okno.
1. Vytvořte soubor, například: go build quickstart.go.
1. Spusťte soubor, například: quickstart.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    /*
    Replace or verify the region.

    You must use the same region in your REST API call as you used to obtain your access keys.
    For example, if you obtained your access keys from the westus region, replace 
    "westcentralus" in the URI below with "westus".

    NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
    a free trial access key, you should not need to change this region.
    */
    const uriBase =    "https://westcentralus.api.cognitive.microsoft.com"
    const uriPath = "/text/analytics/v2.0/keyPhrases"

    const uri = uriBase + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
        {"id": "3", "language": "en", "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
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

## <a name="extract-key-phrases-response"></a>Odpověď metody Extract Key Phrases

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-linked-entities-request"></a>Žádost Identify Linked Entities

Rozhraní API pro propojení entit identifikuje dobře známé entity v textovém dokumentu pomocí [metody Entity Linking](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634). Následující příklad identifikuje entity pro anglické dokumenty.

1. Ve svém oblíbeném editoru kódu vytvořte nový projekt Go.
1. Přidejte níže uvedený kód.
1. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
1. Nahraďte umístění v `uriBase` (momentálně `westcentralus`) oblastí, kterou jste si zaregistrovali.
1. Uložte soubor s příponou .go.
1. Na počítači s nainstalovaným jazykem Go otevřete příkazové okno.
1. Vytvořte soubor, například: go build quickstart.go.
1. Spusťte soubor, například: quickstart.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    /*
    Replace or verify the region.

    You must use the same region in your REST API call as you used to obtain your access keys.
    For example, if you obtained your access keys from the westus region, replace 
    "westcentralus" in the URI below with "westus".

    NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
    a free trial access key, you should not need to change this region.
    */
    const uriBase =    "https://westcentralus.api.cognitive.microsoft.com"
    const uriPath = "/text/analytics/v2.0/entities"

    const uri = uriBase + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "en", "text": "The Seattle Seahawks won the Super Bowl in 2014."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
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

## <a name="identify-linked-entities-response"></a>Odpověď žádosti Identify Linked Entities

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "4K resolution",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "4K resolution",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/4K_resolution",
                    "bingId": "1d4d689e-9cbf-b9eb-6ecf-f3296aaa96d8"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "Seattle Seahawks",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Seattle Seahawks",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle_Seahawks",
                    "bingId": "1bf844db-8225-f90c-a78e-1787fb8af0ce"
                },
                {
                    "name": "Super Bowl",
                    "matches": [
                        {
                            "text": "Super Bowl",
                            "offset": 29,
                            "length": 10
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Super Bowl",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Super_Bowl",
                    "bingId": "ce1fece8-34c4-6249-a1aa-2e779294760e"
                }
            ]
        }
    ],
    "errors": []
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza textu s využitím Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Viz také

 [Přehled rozhraní API pro analýzu textu](../overview.md)  
 [Nejčastější dotazy](../text-analytics-resource-faq.md)
