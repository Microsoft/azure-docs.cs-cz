---
title: 'Rychlý start: Analýza vzdáleného obrázku – REST, Go – počítačové zpracování obrazu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu budete analyzovat obrázek pomocí rozhraní API pro počítačové zpracování obrazu a Go.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: 57c8309af47e71226f41df8cce255e73f33b27c5
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341034"
---
# <a name="quickstart-analyze-a-remote-image-using-the-rest-api-and-go-in-computer-vision"></a>Rychlý start: Analýza vzdáleného obrázku pomocí rozhraní REST API a Go v počítačovém zpracování obrazu

V tomto rychlém startu analyzujete obrázek uložený vzdáleně za účelem extrakce vizuálních prvků pomocí rozhraní REST API počítačového zpracování obrazu. Pomocí metody [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) můžete extrahovat vizuální prvky na základě obsahu obrázku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít nainstalovaný [Go](https://golang.org/dl/).
- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Abyste získali klíč předplatného, přejděte k tématu [Jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Vytvoření a spuštění ukázky

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Zkopírujte do textového editoru následující kód.
1. Proveďte v kódu na příslušných místech následující změny:
    1. Hodnotu `subscriptionKey` nahraďte klíčem předplatného.
    1. Hodnotu `uriBase` nahraďte adresou URL koncového bodu metody [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) z oblasti Azure, kde jste získali klíče předplatného, pokud je to potřeba.
    1. Volitelně můžete hodnotu `imageUrl` nahradit adresou URL jiného obrázku, který chcete analyzovat.
1. Uložte kód jako soubor s příponou `.go`. Například, `analyze-image.go`.
1. Otevřete okno příkazového řádku.
1. Na příkazovém řádku spusťte příkaz `go build`, aby se ze souboru zkompiloval balíček. Například, `go build analyze-image.go`.
1. Na příkazovém řádku spusťte zkompilovaný balíček. Například, `analyze-image`.

```go
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
    // Replace <Subscription Key> with your valid subscription key.
    const subscriptionKey = "<Subscription Key>"

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the West Central US region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    const uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze"
    const imageUrl =
        "http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg"

    const params = "?visualFeatures=Description&details=Landmarks&language=en"
    const uri = uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the HTTP client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the POST request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add request headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Parse the JSON data from the byte array
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the JSON result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Ukázková aplikace provede analýzu a zobrazí úspěšnou odpověď v okně příkazového řádku, podobně jako v následujícím příkladu:

```json
{
  "categories": [
    {
      "detail": {
        "landmarks": []
      },
      "name": "outdoor_water",
      "score": 0.9921875
    }
  ],
  "description": {
    "captions": [
      {
        "confidence": 0.916458423253597,
        "text": "a large waterfall over a rocky cliff"
      }
    ],
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ]
  },
  "metadata": {
    "format": "Jpeg",
    "height": 959,
    "width": 1280
  },
  "requestId": "a92f89ab-51f8-4735-a58d-507da2213fc2"
}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už tuto ukázku nepotřebujete, odstraňte zkompilovaný balíček a soubor, ze kterého se tento balíček zkompiloval, a pak zavřete okno příkazového řádku a textový editor.

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu, které se používá pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
