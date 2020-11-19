---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: JSON
ms.openlocfilehash: f1f9e83a68539f6019169cb2faed16a19ef85a90
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918133"
---
<!--Create a basic transform REST-->

Následující příkaz Azure REST vytvoří základní prostředek zvuku. Nahraďte hodnoty `subscriptionID` , `resourceGroup` a `accountName` hodnotami, se kterými právě pracujete. Pojmenujte transformaci názvu nastavením `transformName` tady.

Změňte na `@odata.type` jedno z [dostupných přednastavení](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#definitions), například `#Microsoft.Media.AudioAnalyzerPreset` .

```

PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaServices/{{accountName}}/transforms/{{transformName}}?api-version=2020-05-01

```

## <a name="body"></a>Text

```json
{
    "properties": {
        "description": "Transform for Audio Analyzer Basic Mode",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.AudioAnalyzerPreset",
                    "audioLanguage": "en-US",
                    "mode": "Basic"
                }
            }
        ]
    }
}
```

## <a name="sample-response"></a>Ukázková odpověď
```json
{
    "name": "audioAnalyzerTransform",
    "id": "/subscriptions/35c2594a-xxxx-4fce-b59c-f6fb9513eeeb/resourceGroups/myresourcegroup/providers/Microsoft.Media/mediaservices/mymediaservicesaccount/transforms/audioAnalyzerTransform",
    "type": "Microsoft.Media/mediaservices/transforms",
    "properties": {
        "created": "2020-10-23T18:03:37.4793962Z",
        "description": "Transform for Audio Analyzer Basic Mode",
        "lastModified": "2020-10-23T18:03:37.4793962Z",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.AudioAnalyzerPreset",
                    "audioLanguage": "en-US",
                    "mode": "Basic",
                    "experimentalOptions": {
                        "basicAudioEnabled": "true"
                    }
                }
            }
        ]
    }
}

```
