---
title: Moderování úloh a kontrol lidských v the smyčky – Content Moderator
titlesuffix: Azure Cognitive Services
description: Kombinace moderování s podporou počítače s možnostmi lidských v the smyčky pomocí Azure Content Moderator revize API k získání nejlepších výsledků pro vaši firmu.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: a348b18d1ecc9c0e4405c54a8e554d932781ec92
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265341"
---
# <a name="content-moderation-jobs-and-reviews"></a>Moderování obsahu úloh a kontrol

Kombinace moderování s podporou počítače s možnostmi lidských v the smyčky pomocí Azure Content Moderator [revizi rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) k dosažení nejlepších výsledků pro vaši firmu.

Rozhraní API pro kontrolu nabízí tyto způsoby zahrnutí jistotu lidské do procesu moderování obsahu:

* `Job` operace se používá ke spuštění jako jeden krok moderování s podporou počítače a vytvoření recenze prováděné lidmi.
* `Review` operace se používají pro vytvoření recenze prováděné lidmi mimo krok moderování.
* `Workflow` operace se používají ke správě pracovní postupy automatizující prohledávání pomocí prahové hodnoty pro vytvoření revize.

`Job` a `Review` přijmout zpětného volání koncových bodů pro příjem stavu a výsledky operace.

Tento článek se týká `Job` a `Review` operace. Přečtěte si [přehledu pracovních postupů](workflow-api.md) informace o tom, jak vytvořit, upravit a získat definice pracovního postupu.

## <a name="job-operations"></a>Operace úlohy

### <a name="start-a-job"></a>Spuštění úlohy
Použití `Job.Create` operaci pro spuštění moderování a úloha vytvoření recenze prováděné lidmi. Content Moderator prohledává obsah a vyhodnotí určené pracovního postupu. Na základě výsledků pracovních postupů se vytvoří revize nebo přeskočí krok. Odesílá také značky po moderování a po přezkoumání do zpětného volání koncového bodu.

Vstupy uveďte následující informace:

- ID revize týmu.
- Možné Moderovat obsah.
- Název pracovního postupu. (Výchozí hodnota je "Výchozí" pracovní postup).
- Zpětné volání rozhraní API bod pro oznámení.
 
Následující odpověď zobrazuje identifikátor úlohy, která byla spuštěna. Identifikátor úlohy můžete získat stav úlohy a získat podrobné informace.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

### <a name="get-job-status"></a>Získat stav úlohy

Použití `Job.Get` operace a identifikátor úlohy pro získání podrobností o spuštěné nebo dokončené úlohy. Operace vrátí hned, zatímco moderování úloha spouští asynchronně. Výsledky jsou vráceny prostřednictvím koncového bodu zpětného volání.

Zadání uveďte následující informace:

- ID revize týmu: Identifikátor úlohy vrácené z předchozí operace

Odpověď obsahuje následující informace:

- Identifikátor revize vytvořili. (Toto ID použít k získání požadovaných výsledků konečnou kontrolu.)
- Stav úlohy (dokončení nebo probíhající): Moderování přiřazené značky (páry klíč hodnota).
- Sestava spuštění úlohy.
 
 
        {
            "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
            "TeamName": "some team name",
            "Status": "Complete",
            "WorkflowId": "OCR",
            "Type": "Image",
            "CallBackEndpoint": "",
            "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
            "ResultMetaData":[
            {
            "Key": "hasText",
            "Value": "True"
            },
            {
            "Key": "ocrText",
            "Value": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
            }
            ],
            "JobExecutionReport": [
            {
                "Ts": "2018-01-07T00:38:29.3238715",
                "Msg": "Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
                },
                {
                "Ts": "2018-01-07T00:38:29.2928416",
                "Msg": "Job marked completed and job content has been removed"
                },
                {
                "Ts": "2018-01-07T00:38:29.0856472",
                "Msg": "Execution Complete"
                },
            {
                "Ts": "2018-01-07T00:38:26.7714671",
                "Msg": "Successfully got hasText response from Moderator"
                },
                {
                "Ts": "2018-01-07T00:38:26.4181346",
                "Msg": "Getting hasText from Moderator"
                },
                {
                "Ts": "2018-01-07T00:38:25.5122828",
                "Msg": "Starting Execution - Try 1"
                }
            ]
        }
 
![Kontrola obrázku lidskými moderátory](images/ocr-sample-image.PNG)

## <a name="review-operations"></a>Operace kontroly

### <a name="create-reviews"></a>Vytvoření kontroly

Použití `Review.Create` operaci vytvoření recenze prováděné lidmi. Jinde je střední nebo po pomocí vlastní logiky můžete přiřadit značky moderování.

Zadání této operace patří:

- Obsah musí zkontrolovat.
- Přiřazené značky (páry klíč-hodnota) ke kontrole lidské moderátory.

Následující odpověď zobrazuje identifikátor revize:

    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]


### <a name="get-review-status"></a>Získat stav kontroly
Použití `Review.Get` operace k získání požadovaných výsledků po dokončení recenze moderování obrázků. Dostanete se přes váš koncový bod zpětné volání. 

Operace vrátí dvě sady značek: 

* Značky přiřazené službou moderování
* Značky po dokončení lidskou kontrolu

Vstupy zahrnují minimálně:

- Zkontrolujte název týmu
- Zkontrolujte identifikátor vrácený z předchozí operace

Odpověď obsahuje následující informace:

- Stav kontroly
- Potvrdit lidské kontrolor značky (páry klíč hodnota)
- Značky (páry klíč hodnota) přidělené službou moderování

Zobrazit obě značky přiřazeny revidujícího (**reviewerResultTags**) a počáteční značky (**metadat**) v odpovědi na následující ukázka:

    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="next-steps"></a>Další postup

* Testovací verze [rozhraní API pro úlohu konzoly](try-review-api-job.md)a používat rozhraní REST API ukázky kódu. Pokud jste obeznámeni s Visual Studio a C#, podívejte [rychlý úvod k .NET úlohy](moderation-jobs-quickstart-dotnet.md). 
* U revizí, vám umožní začít [revizi rozhraní API konzoly](try-review-api-review.md)a používat rozhraní REST API ukázky kódu. Potom se podívejte [rychlý úvod k .NET revize](moderation-reviews-quickstart-dotnet.md).
* Video revize používat [rychlého startu zkontrolujte Video](video-reviews-quickstart-dotnet.md)a zjistěte, jak [přidat záznamy o studiu videa kontrolou](video-transcript-reviews-quickstart-dotnet.md).
