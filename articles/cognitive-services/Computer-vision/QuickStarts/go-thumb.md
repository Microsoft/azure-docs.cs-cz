---
title: 'Rychlý start: Vygenerování miniatury – REST, Go'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí rozhraní API pro počítačové zpracování obrazu a Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ed79686a8c2c649852c4652a3e334b1731491de9
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177237"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-with-go"></a>Rychlý Start: vygenerování miniatury pomocí Počítačové zpracování obrazu REST API s příkazem přejít

V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí REST API Počítačové zpracování obrazu. Určíte výšku a šířku, což se může lišit v poměru stran od vstupní bitové kopie. Počítačové zpracování obrazu používá inteligentní ořezávání k inteligentně identifikaci oblasti zájmu a generování souřadnic oříznutí na základě této oblasti.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

- Musíte mít nainstalovaný [Go](https://golang.org/dl/).
- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Bezplatný zkušební klíč si můžete [vyzkoušet Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Případně postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přihlášení k odběru počítačové zpracování obrazu a získání klíče. Pak [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro řetězec klíčového a koncového bodu služby s názvem `COMPUTER_VISION_SUBSCRIPTION_KEY` a `COMPUTER_VISION_ENDPOINT` v uvedeném pořadí.

## <a name="create-and-run-the-sample"></a>Vytvoření a spuštění ukázky

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Zkopírujte do textového editoru následující kód.
1. Volitelně můžete hodnotu `imageUrl` nahradit adresou URL jiného obrázku, ze kterého chcete vygenerovat miniaturu.
1. Uložte kód jako soubor s příponou `.go`. Například, `get-thumbnail.go`.
1. Otevřete okno příkazového řádku.
1. Na příkazovém řádku spusťte příkaz `go build`, aby se ze souboru zkompiloval balíček. Například, `go build get-thumbnail.go`.
1. Na příkazovém řádku spusťte zkompilovaný balíček. Například, `get-thumbnail`.

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
    // Add your Computer Vision subscription key and endpoint to your environment variables.
    subscriptionKey := os.Getenv("COMPUTER_VISION_SUBSCRIPTION_KEY")
    if (subscriptionKey == "") {
        log.Fatal("\n\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n" +
            "**Restart your shell or IDE for changes to take effect.**\n")

    endpoint := os.Getenv("COMPUTER_VISION_ENDPOINT")
    if ("" == endpoint) {
        log.Fatal("\n\nSet the COMPUTER_VISION_ENDPOINT environment variable.\n" +
            "**Restart your shell or IDE for changes to take effect.**")
    }
    const uriBase = endpoint + "vision/v2.1/generateThumbnail"
    const imageUrl =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

    const params = "?width=100&height=100&smartCropping=true"
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

    // Parse the JSON data
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the JSON result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď obsahuje binární data miniatury obrázku. Pokud požadavek selže, bude odpověď obsahovat chybový kód a zprávu, která vám pomůže určit, co se nepovedlo.

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu k analýze obrázku, detekci celebrit a orientačních bodů, vytvoření miniatury a extrakci vytištěného a rukopisného textu. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
