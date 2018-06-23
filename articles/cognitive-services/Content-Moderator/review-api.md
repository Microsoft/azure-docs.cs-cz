---
title: Azure obsahu moderátora - přerušování úlohy a lidské v the smyčky recenze | Microsoft Docs
description: Lidské dohledu se vztahují na počítače asistované přerušování pro dosažení co nejlepších výsledků.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/21/2018
ms.author: sajagtap
ms.openlocfilehash: 35b3cdaa410712c3fd08d3df4ebe4c83e3955d50
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342493"
---
# <a name="moderation-jobs-and-reviews"></a>Přerušování úlohy a recenze

Kombinace s asistencí počítač přerušování s možnostmi lidské v the smyčky pomocí obsahu moderátora Azure [zkontrolujte API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) k dosažení nejlepších výsledků pro vaši organizaci.

Kontrola rozhraní API nabízí tyto způsoby do procesu obsahu přerušování zahrnout lidského dohledu:

* `Job` operace se používá ke spuštění přerušování s asistencí počítače a vytváření lidského kontrolní jako jeden krok.
* `Review` operace se používají pro vytvoření lidského zkontrolujte mimo přerušování krok.
* `Workflow` operace se používají ke správě pracovních postupů, které automatizují kontrolu s prahovými hodnotami pro vytvoření zkontrolujte.

`Job` a `Review` operations přijmout zpětného volání koncových bodů pro příjem stavu a výsledky.

Tento článek se zabývá `Job` a `Review` operace. Pro čtení [přehled pracovních](workflow-api.md) informace o tom, jak vytvořit, upravit a získat definice pracovního postupu.

## <a name="job-operations"></a>Operace úlohy

### <a name="start-a-job"></a>Spustit úlohu
Použití `Job.Create` operaci pro spuštění přerušování a lidské zkontrolujte vytvoření úlohy. Obsahu moderátora kontroly obsahu a vyhodnotí určené pracovního postupu. Na základě výsledků pracovního postupu je buď vytvoří recenze nebo přeskočí krok. Odesílá také po přerušování a po zkontrolujte značek k váš koncový bod zpětného volání.

Vstupní hodnoty zahrnují následující informace:

- ID revize týmu.
- Chcete-li být moderovaná obsah.
- Název pracovního postupu. (Výchozí hodnota je "Výchozí" pracovní postup.)
- Vaše zpětné volání rozhraní API bod pro oznámení.
 
Následující odpověď zobrazuje identifikátor úlohu, která byla spuštěna. Identifikátor úlohy slouží k získání stavu úlohy a získat podrobné informace.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

### <a name="get-job-status"></a>Získat stav úlohy

Použití `Job.Get` operace a identifikátor úlohy pro získání podrobností o spuštěné nebo dokončené úlohy. Operaci vrátí okamžitě, zatímco úloha přerušování běží asynchronně. Výsledky jsou vráceny prostřednictvím koncového bodu zpětného volání.

Vstupy zahrňte následující informace:

- ID revize týmu: identifikátor úlohy vrácený předchozí operace

Odpověď obsahuje následující informace:

- Identifikátor zkontrolujte vytvořili. (Toto ID použít k získání výsledků poslední kontrolní.)
- Stav úlohy (dokončené nebo probíhající): přiřazené přerušování značky (páry klíč hodnota).
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
 
![Zkontrolujte bitové kopie pro lidské moderátorů](images/ocr-sample-image.PNG)

## <a name="review-operations"></a>Zkontrolujte operací

### <a name="create-reviews"></a>Vytvoření recenze

Použití `Review.Create` operaci vytvoření lidského recenze. Střední je jinam nebo po přiřazení značek přerušování pomocí vlastní logiky.

Vstupy pro tuto operaci patří:

- Obsah, který mají být zkontrolovány.
- Přiřazené značky (párů klíčových hodnot) ke kontrole podle lidského moderátorů.

Následující odpověď zobrazuje identifikátor zkontrolujte:

    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]


### <a name="get-review-status"></a>Získat stav kontroly
Použití `Review.Get` operace k získání výsledků po dokončení kontrolu lidského moderované bitové kopie. Zobrazí se upozornění přes koncový bod zadané zpětné volání. 

Operaci vrátí dvě sady značek: 

* Značky přiřazené službou přerušování
* Značky po lidského kontrola byla dokončena.

Vstupy patří minimálně:

- Zkontrolujte název týmu
- Identifikátor zkontrolujte vrácený předchozí operace

Odpověď obsahuje následující informace:

- Zkontrolujte stav
- Značky (páry klíč hodnota) potvrdit lidského kontrolora
- Značky (páry klíč hodnota) přiřazené službou přerušování

Zobrazí obě značky přiřazené kontrolor (**reviewerResultTags**) a počáteční značky (**metadata**) v následující ukázková odpověď:

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

* Vyzkoušejte [rozhraní API pro úlohu konzoly](try-review-api-job.md)a použijte ukázky kódu rozhraní REST API. Pokud jste obeznámeni s Visual Studio a C#, taky podívejte se na [rychlé spuštění úlohy .NET](moderation-jobs-quickstart-dotnet.md). 
* Pro recenze, začít pracovat s [zkontrolujte API konzoly](try-review-api-review.md)a použijte ukázky kódu rozhraní REST API. Pak najdete v článku [rychlý start recenze .NET](moderation-reviews-quickstart-dotnet.md).
* Video recenze, použijte [rychlý start zkontrolujte Video](video-reviews-quickstart-dotnet.md)a zjistěte, jak [přidat přepisy ke kontrole video](video-transcript-reviews-quickstart-dotnet.md).
