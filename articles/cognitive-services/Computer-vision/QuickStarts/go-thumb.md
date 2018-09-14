---
title: Rychlý start k vytvoření miniatury obrázku pomocí rozhraní API pro počítačové zpracování obrazu s Go | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: V tomto rychlém startu vygenerujete ve službách Cognitive Services pomocí počítačového zpracování obrazu s Go miniaturu obrázku.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 02ac30d295b0637b97187dbb7e1fbb1b10a2972b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43769321"
---
# <a name="quickstart-generate-a-thumbnail---rest-go"></a>Rychlý start: Vygenerování miniatury – REST, Go

V tomto rychlém startu vygenerujete pomocí počítačového zpracování obrazu miniaturu obrázku.

## <a name="prerequisites"></a>Požadavky

Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak klíče předplatného získat](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-thumbnail-request"></a>Žádost Get Thumbnail

Miniaturu obrázku můžete vygenerovat pomocí [metody Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb). Zadáte výšku a šířku, které se mohou od poměru stran vstupního obrázku lišit. Počítačové zpracování obrazu použije chytré oříznutí, které inteligentně identifikuje oblast zájmu a na základě této oblasti vygeneruje souřadnice oříznutí.

Pokud chcete spustit ukázku, postupujte takto:

1. Zkopírujte do editoru následující kód.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte hodnotu `uriBase` na umístění, kde jste získali klíče předplatného.
1. Volitelně můžete změnit hodnotu `imageUrl` na obrázek, který chcete analyzovat.
1. Uložte soubor s příponou `.go`.
1. Na počítači s nainstalovaným jazykem Go otevřete příkazové okno.
1. Vytvořte soubor, například: `go build get-thumbnail.go`.
1. Spusťte soubor, například: `get-thumbnail`.

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
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail"
    const imageUrl =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

    const params = "?width=100&height=100&smartCropping=true"
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

## <a name="get-thumbnail-response"></a>Odpověď metody Get Thumbnail

Úspěšná odpověď obsahuje binární kód miniatury obrázku. Pokud požadavek selže, bude odpověď obsahovat chybový kód a zprávu, která vám pomůže určit, co se nepovedlo.

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu používané pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
