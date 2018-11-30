---
title: 'Kurz: Použití rozhraní REST API Custom Vision Service'
titlesuffix: Azure Cognitive Services
description: Pomocí rozhraní REST API vytvářejte, trénujte, testujte a exportujte vlastní model zpracování obrazu.
services: cognitive-services
author: blackmist
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/07/2018
ms.author: larryfr
ms.openlocfilehash: afdc306a3d5a3130ecb44f88910e76f23f7c0c0e
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334716"
---
# <a name="tutorial-use-the-custom-vision-rest-api"></a>Kurz: Použití rozhraní REST API Custom Vision

Zjistěte, jak používat rozhraní REST API Custom Vision k vytváření, trénování, testování a exportování modelu.

Informace v tomto dokumentu ukazují, jak používat klienta REST na trénování služby Custom Vision s rozhraním REST API. Příklady ukazují, jak použít rozhraní API pomocí nástroje `curl` z prostředí bash a `Invoke-WebRequest` z prostředí Windows PowerShell.

> [!div class="checklist"]
> * Získání klíčů
> * Vytvoření projektu
> * Vytvoření značek
> * Přidání obrázků
> * Natrénování a otestování modelu
> * Vyexportování modelu

## <a name="prerequisites"></a>Požadavky

* Základní znalost REST (Representational State Transfer). Tento dokument nezachází do podrobností, jako jsou příkazy HTTP, JSON nebo jiné věci běžně používané s REST.

* Buď bash (Bourne Again Shell) s nástrojem [curl](https://curl.haxx.se) nebo prostředí Windows PowerShell 3.0 (nebo novější)

* Účet Custom Vision. Další informace najdete v dokumentu věnovaném [sestavení klasifikátoru](getting-started-build-a-classifier.md).

## <a name="get-keys"></a>Získání klíčů

Při použití rozhraní REST API je potřeba ověřit se pomocí klíče. Při provádění operací správy nebo trénování použijete __tréninkový klíč__. Při použití modelu k předpovědi použijete __klíč předpovědi__.

Při posílání požadavku se klíč pošle jako hlavička požadavku.

Klíče svého účtu získáte tak, že přejdete na [webovou stránku služby Custom Vision](https://customvision.ai) a vyberete __ikonu ozubeného kola__ v pravém horním rohu. V části __Účty__ zkopírujte hodnoty z polí pro __tréninkový klíč__ a __klíč předpovědi__.

![Obrázek uživatelského rozhraní klíčů](./media/rest-api-tutorial/training-prediction-keys.png)

> [!IMPORTANT]
> Vzhledem k tomu, že se klíče používají k ověření každého požadavku, příklady v tomto dokumentu předpokládají, že jsou hodnoty klíče obsaženy v proměnných prostředí. Dříve, než použijete další fragmenty kódu v tomto dokumentu, uložte klíče do proměnných prostředí pomocí následujících příkazů:
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

Následující příklady vytvoří nový projekt s názvem `myproject` ve vaší instanci služby Custom Vision. Služba používá jako výchozí doménu `General`:

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

Odpověď na žádost se podobá následujícímu dokumentu JSON:

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
> Položka `id` v odpovědi je ID nového projektu. To se používá v jiných příkladech dále v tomto dokumentu.

Další informace o tomto požadavku najdete v tématu [CreateProject](https://go.microsoft.com/fwlink/?linkid=865446).

### <a name="specific-domains"></a>Konkrétní domény

Pokud chcete vytvořit projekt pro konkrétní doménu, můžete zadat __ID domény__ jako volitelný parametr. Následující příklady znázorňují postup načtení seznamu dostupných domén:

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

Odpověď na žádost se podobá následujícímu dokumentu JSON:

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

Další informace o tomto požadavku najdete v tématu [GetDomains](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a827d).

Následující příklad ukazuje vytvoření nového projektu, který používá doménu __Landmarks__:

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

## <a name="create-tags"></a>Vytvoření značek

K označení obrázků je nutné použít ID značky. Následující příklad ukazuje, jak vytvořit novou značku s názvem `cat` a získat ID značky. Nahraďte `{projectId}` identifikátorem (ID) projektu. Pomocí parametru `name=` určete název značky:

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

Odpověď na žádost se podobá následujícímu dokumentu JSON: 

```json
{"id":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43","name":"cat","description":null,"imageCount":0}
```

Uložte si hodnotu `id`, protože se používá při označování obrázků.

Další informace o tomto požadavku najdete v tématu [CreateTag](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829d).

## <a name="add-images"></a>Přidání obrázků

Následující příklady ukazují přidání souboru z adresy URL. Nahraďte `{projectId}` identifikátorem (ID) projektu. Nahraďte `{tagId}` identifikátorem značky obrázku:

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

Odpověď na žádost se podobá následujícímu dokumentu JSON:

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

Následující příklady ukazují, jak trénovat model. Nahraďte `{projectId}` identifikátorem projektu:

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

Odpověď na žádost se podobá následujícímu dokumentu JSON:

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

Uložte si hodnotu `id`, protože se používá pro testování a exportování modelu.

Další informace najdete v tématu [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8294).

## <a name="test-the-model"></a>Testování modelu

Následující příklady ukazují, jak provést test modelu. Nahraďte `{projectId}` identifikátorem (ID) projektu. Nahraďte `{iterationId}` identifikátorem vráceným z trénování modelu. Nahraďte `https://linktotestimage` cestou k testovacímu obrázku.

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

Odpověď na žádost se podobá následujícímu dokumentu JSON:

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

Další informace najdete v tématu [QuickTestImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a828d).

## <a name="export-the-model"></a>Vyexportování modelu

Exportování modelu je dvoustupňový proces. Nejprve musíte zadat formát modelu a poté požádat o adresu URL pro exportovaný model.

### <a name="request-a-model-export"></a>Žádost o export modelu

Následující příklady ukazují, jak exportovat model `coreml`. Nahraďte `{projectId}` identifikátorem (ID) projektu. Nahraďte `{iterationId}` identifikátorem vráceným z trénování modelu.

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

Odpověď na žádost se podobá následujícímu dokumentu JSON:

```json
{
    "platform": "CoreML",
    "status": "Exporting",
    "downloadUri": null,
    "flavor": null
}
```

Další informace najdete v tématu [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829b).

### <a name="download-the-exported-model"></a>Stažení vyexportovaného modelu

Následující příklady ukazují, jak načíst adresu URL exportovaného modelu. Nahraďte `{projectId}` identifikátorem (ID) projektu. Nahraďte `{iterationId}` identifikátorem vráceným z trénování modelu.

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

Odpověď na žádost se podobá následujícímu dokumentu JSON:

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