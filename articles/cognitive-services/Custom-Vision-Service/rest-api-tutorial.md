---
title: Použití Custom Vision Service rozhraní REST API – Azure Cognitive Services | Dokumentace Microsoftu
description: Pomocí rozhraní REST API pro vytváření, trénování, testování a exportovat vlastního modelu zpracování obrazu.
services: cognitive-services
author: blackmist
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 08/07/2018
ms.author: larryfr
ms.openlocfilehash: 44fa4d45c33f3064c089724ee761a70d0a8421ab
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250259"
---
# <a name="tutorial-use-the-custom-vision-rest-api"></a>Kurz: Použití rozhraní REST API Custom Vision

Další informace o použití rozhraní REST API Custom Vision pro vytváření, trénování, testování a exportovat do modelu.

Informace v tomto dokumentu ukazuje, jak použít klienta REST pro práci s rozhraním REST API pro trénování Custom Vision service. Příklady ukazují, jak použít rozhraní API pomocí `curl` nástroj z prostředí bash a `Invoke-WebRequest` z prostředí Windows PowerShell.

> [!div class="checklist"]
> * Získat klíče
> * Vytvoření projektu
> * Vytvoření značky
> * Přidání obrázků
> * Trénování a testování modelu
> * Exportovat modelu

## <a name="prerequisites"></a>Požadavky

* Základní znalosti s REST Representational State Transfer (). Tento dokument nepřešel do podrobností na věci, jako je příkazy HTTP, JSON nebo jiné se běžně používá s využitím REST.

* Buď bash (Bourne znovu prostředí) se [curl](https://curl.haxx.se) nástroj nebo prostředí Windows PowerShell 3.0 (nebo novější).

* Pro zpracování obrazu vlastní účet. Další informace najdete v tématu [sestavení klasifikátoru](getting-started-build-a-classifier.md) dokumentu.

## <a name="get-keys"></a>Získat klíče

Při použití rozhraní REST API, je třeba ověřit pomocí klíče. Při provádění správy nebo operace trénování, můžete použít __školení klíč__. Při použití model k predikci, je použít __předpovědi klíč__.

Při požadavku, pošle se jako hlavičku požadavku.

Získat klíče pro váš účet, přejděte [Custom Vision webové stránky](https://customvision.ai) a vyberte __ikonu ozubeného kolečka__ v pravém horním rohu. V __účty__ tématu, zkopírujte hodnoty z __školení klíč__ a __předpovědi klíč__ pole.

![Obrázek klíče uživatelského rozhraní](./media/rest-api-tutorial/training-prediction-keys.png)

> [!IMPORTANT]
> Od klíče se používají k ověření každého požadavku, příklady v tomto dokumentu předpokládají, že hodnoty klíče jsou obsaženy v proměnné prostředí. Použijte následující příkazy pro ukládání klíčů do proměnných prostředí před použitím další fragmenty kódu v tomto dokumentu:
>
> ```bash
> read -p "Enter the training key: " TRAININGKEY
> read -p "Enter the prediction key: " PREDICTIONKEY
> ```
>
> ```powershell
> $trainingKey = Read-Host 'Enter the training key'
> $predictionKey = Read-Host 'Enter the prediction key'
> ```

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Následující příklady vytvoření nového projektu s názvem `myproject` ve vaší instanci Custom Vision service. Výchozí hodnota této služby `General` domény:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

Odpověď na žádost se podobá následující dokument JSON:

```json
{
  "id":"45d1b19b-69b6-4a22-8e7e-d1ca37504686",
  "name":"myproject",
  "description":"",
  "settings":{
    "domainId":"ee85a72c-405e-4adc-bb47-ffa8ca0c9f31",
    "useNegativeSet":true,
    "classificationType":"Multilabel",
    "detectionParameters":null
  },
  "created":"2018-08-10T17:39:02.5633333",
  "lastModified":"2018-08-10T17:39:02.5633333",
  "thumbnailUri":null
}
```

> [!TIP]
> `id` Položka odpovědi je ID nový projekt. Používá se v příkladech dále v tomto dokumentu.

Další informace o tomto požadavku najdete v tématu [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8290).

### <a name="specific-domains"></a>Konkrétní domény

Vytvoření projektu pro konkrétní doménu, můžete zadat __ID domény__ jako volitelný parametr. Následující příklady znázorňují postup načtení seznamu dostupných domén:

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

Odpověď na žádost se podobá následující dokument JSON:

```json
[
    {
        "id": "ee85a74c-405e-4adc-bb47-ffa8ca0c9f31",
        "name": "General",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "c151d5b5-dd07-472a-acc8-15d29dea8518",
        "name": "Food",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "ca455789-012d-4b50-9fec-5bb63841c793",
        "name": "Landmarks",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    ...
]
```

Další informace o tomto požadavku najdete v tématu [GetDomains](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a827d).

Následující příklad ukazuje, vytvořte nový projekt, který používá __památek__ domény:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

## <a name="create-tags"></a>Vytvoření značky

K označení obrázků je nutné použít identifikátor značky Následující příklad ukazuje, jak vytvořit novou značku s názvem `cat` a získejte identifikátor značky Nahraďte `{projectId}` s ID projektu. Použití `name=` parametr k určení názvu značky:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

Odpověď na žádost je podobně jako dokument JSON: 

```json
{"id":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43","name":"cat","description":null,"imageCount":0}
```

Uložit `id` hodnotu, protože se používá při označování imagí.

Další informace o tomto požadavku najdete v tématu [CreateTag](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829d).

## <a name="add-images"></a>Přidání obrázků

Následující příklady ukazují, přidání souboru z adresy URL. Nahraďte `{projectId}` s ID projektu. Nahraďte `{tagId}` s ID značky obrázku:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
$resp.Content
```

Odpověď na žádost se podobá následující dokument JSON:

```json
{
    "isBatchSuccessful": true,
    "images": [
        {
            "sourceUrl": "http://myimages/cat.jpg",
            "status": "OK",
            "image": {
                "id": "081adaee-a76b-4d94-a70e-e4fd0935a28f",
                "created": "2018-08-13T13:24:22.0815638",
                "width": 640,
                "height": 480,
                "imageUri": "https://linktoimage",
                "thumbnailUri": "https://linktothumbnail",
                "tags": [
                    {
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936"
                    }
                ],
                "regions": [
                    {
                        "regionId": "40f206a1-3f8a-4de7-a6c3-c7b4643117df",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936",
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "left": 119,
                        "top": 94,
                        "width": 240,
                        "height": 140
                    }
                ]
            }
        }
    ]
}
```

Další informace o tomto požadavku najdete v tématu [CreateImagesFromUrls](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8287).

## <a name="train-the-model"></a>Trénování modelu

Následující příklady ukazují, jak pro trénování modelu. Nahraďte `{projectId}` s ID projektu:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } 
$resp.Content
```

Odpověď na žádost se podobá následující dokument JSON:

```json
{
    "id": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "name": "Iteration 1",
    "isDefault": false,
    "status": "Training",
    "created": "2018-08-10T17:39:02.5766667",
    "lastModified": "2018-08-16T17:15:07.5250661",
    "projectId": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "exportable": false,
    "domainId": null
}
```

Uložit `id` hodnotu, protože se používá pro testování a exportovat modelu.

Další informace najdete v tématu [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8294).

## <a name="test-the-model"></a>Otestování modelu

Následující příklady ukazují, jak provést test modelu. Nahraďte `{projectId}` s ID projektu. Nahraďte `{iterationId}` s ID vrácená z trénování modelu. Nahraďte `https://linktotestimage` cestou k obrázku testu.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"url":"https://linktotestimage"}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"url":"https://linktotestimage"}'
$resp.Content
```

Odpověď na žádost se podobá následující dokument JSON:

```json
{
    "id": "369b010b-2a92-4f48-a918-4c1a0af91888",
    "project": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "iteration": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "created": "2018-08-16T17:39:20.7944508Z",
    "predictions": [
        {
            "probability": 0.8390652,
            "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
            "tagName": "cat"
        }
    ]
}
```

Další informace najdete v tématu [QuickTestImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a828d).

## <a name="export-the-model"></a>Exportovat modelu

Export modelu je dvoustupňový proces. Nejprve musíte zadat formátu modelu a poté požádat o adresu URL pro exportované model.

### <a name="request-a-model-export"></a>Žádost o export modelu

Následující příklady ukazují, jak exportovat `coreml` modelu. Nahraďte `{projectId}` s ID projektu. Nahraďte `{iterationId}` s ID vrácená z trénování modelu.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

Odpověď na žádost se podobá následující dokument JSON:

```json
{
    "platform": "CoreML",
    "status": "Exporting",
    "downloadUri": null,
    "flavor": null
}
```

Další informace najdete v tématu [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829b).

### <a name="download-the-exported-model"></a>Stáhnout exportovaná modelu

Následující příklady ukazují, jak načíst adresu URL exportované modelu. Nahraďte `{projectId}` s ID projektu. Nahraďte `{iterationId}` s ID vrácená z trénování modelu.

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

Odpověď na žádost se podobá následující dokument JSON:

```json
[
    {
        "platform": "CoreML",
        "status": "Done",
        "downloadUri": "https://linktoexportedmodel",
        "flavor": null
    }
]
```

Další informace najdete v tématu [GetExports](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829a).