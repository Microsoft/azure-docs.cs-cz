---
title: Rychlý start k analýze obrázku pomocí rozhraní API pro počítačové zpracování obrazu s Go | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: V tomto rychlém startu analyzujete obrázek pomocí počítačového zpracování obrazu s Go ve službách Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: ef7d6ac818f517615fc98f40ac073e6bfc9a65fd
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43769333"
---
# <a name="quickstart-analyze-a-remote-image---rest-go"></a>Rychlý start: Analýza vzdáleného obrázku – REST, Go

V tomto rychlém startu analyzujete obrázek za účelem extrakce vizuálních prvků pomocí počítačového zpracování obrazu.

## <a name="prerequisites"></a>Požadavky

Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak klíče předplatného získat](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-image-request"></a>Žádost Analyze Image

Pomocí [metody Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) můžete extrahovat vizuální funkce na základě obsahu obrázku. Obrázek můžete nahrát nebo můžete určit jeho adresu URL a vybrat funkce, které se mají vrátit, včetně:

* Podrobného seznamu značek souvisejících s obsahem obrázku
* Popisu obsahu obrázku v celé větě
* Souřadnic, pohlaví a věku veškerých obličejů na obrázku
* Typu obrázku (klipart nebo perokresba)
* Převládající barvy, doplňkové barvy a toho, jestli je obrázek černobílý
* Kategorie definované v této [taxonomii](../Category-Taxonomy.md)
* Je obsah obrázku určený pro dospělé nebo se jedná o sexuálně sugestivní obsah?

Pokud chcete spustit ukázku, postupujte takto:

1. Zkopírujte do editoru následující kód.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte hodnotu `uriBase` na umístění, kde jste získali klíče předplatného.
1. Volitelně můžete změnit hodnotu `imageUrl` na obrázek, který chcete analyzovat.
1. Uložte soubor s příponou `.go`.
1. Na počítači s nainstalovaným jazykem Go otevřete příkazové okno.
1. Vytvořte soubor, například: `go build analyze-image.go`.
1. Spusťte soubor, například: `analyze-image`.

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
    // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
    const subscriptionKey = "<Subscription Key>"

    // You must use the same location in your REST call as you used to get your
    // subscription keys. For example, if you got your subscription keys from
    // westus, replace "westcentralus" in the URL below with "westus".
    const uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze"
    const imageUrl =
        "http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg"

    const params = "?visualFeatures=Description&details=Landmarks&language=en"
    const uri = uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the Http client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the Post request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body.
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Parse the Json data
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the Json result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="analyze-image-response"></a>Odpověď metody Analyze Image

Úspěšná odpověď se vrátí ve formátu JSON, například:

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

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu používané pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
