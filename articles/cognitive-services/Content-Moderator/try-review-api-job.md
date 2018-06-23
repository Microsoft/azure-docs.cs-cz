---
title: Spuštění úloh obsahu přerušování v Azure obsah moderátora | Microsoft Docs
description: Zjistěte, jak spouštět úlohy obsahu přerušování v konzole rozhraní API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/03/2017
ms.author: sajagtap
ms.openlocfilehash: 6f741be1001ae70d5fdbf6f374204aaad1601abe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342434"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>Spustit úlohu přerušování z konzoly pro rozhraní API

Použít rozhraní API zkontrolujte [úlohy operations](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) zahájíte obsahu přerušování začátku do konce úlohy pro obrázek nebo textový obsah v Azure moderátora obsah. 

Úloha přerušování kontroluje svůj obsah pomocí rozhraní API přerušování obsah moderátora bitovou kopii nebo Text přerušování API. Potom úlohu přerušování používá pracovní postupy (definovanou v nástroji zkontrolujte) ke generování recenze v nástroji zkontrolujte. 

Jakmile lidského moderátor zkontroluje automaticky přiřazená značky a předpovědi data a odešle přerušování konečné rozhodnutí, odešle rozhraní API zkontrolujte všechny informace, které váš koncový bod rozhraní API.

## <a name="prerequisites"></a>Požadavky

Přejděte na [Zkontrolujte nástroj](https://contentmoderator.cognitive.microsoft.com/). Pokud jste tak ještě neučinili, zaregistrujte si. V rámci nástroje zkontrolujte [definovat vlastní pracovní postup](Review-Tool-User-Guide/Workflows.md) používat v tomto `Job` operaci.

## <a name="use-the-api-console"></a>Pomocí rozhraní API konzoly
Chcete-li test-drive rozhraní API pomocí konzoly služby online, je třeba několik hodnot k zadání do konzoly:
    
- `teamName`: Pomocí `Id` pole z obrazovky vaše nástroje zkontrolujte přihlašovací údaje. 
- `ContentId`: Tento řetězec je předán do rozhraní API a vráceny prostřednictvím zpětné volání. **ContentId** je užitečné pro přidružení k výsledky úlohy přerušování. interní identifikátory nebo metadata- `Workflowname`: název [pracovního postupu, který jste vytvořili](Review-Tool-User-Guide/Workflows.md) v předchozí části.
- `Ocp-Apim-Subscription-Key`: Nachází na **nastavení** kartě. Další informace najdete v tématu [přehled](overview.md).

Přístup k rozhraní API konzoly je z **pověření** okno.

### <a name="navigate-to-the-api-reference"></a>Přejděte na referenční dokumentace rozhraní API
V **pověření** vyberte [referenční dokumentace rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5).

  `Job.Create` Otevře se stránka.

### <a name="select-your-region"></a>Vyberte oblast
Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu.
  ![Úloha – vytvoření výběr oblast stránky](images/test-drive-job-1.png)

  `Job.Create` Otevře se konzola rozhraní API. 

### <a name="enter-parameters"></a>Zadání parametrů

Zadejte hodnoty pro požadované parametry dotazu a svůj klíč předplatného. V **text žádosti** zadejte umístění informace, které chcete kontrolovat. V tomto příkladu použijeme to [Ukázkový obrázek](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png).

  ![Úloha – vytvoření parametry dotazu konzoly, hlavičky a pole textu požadavku](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Odešlete žádost
Vyberte **Poslat**. ID úlohy je vytvořen. Zkopírujte tento používat v dalších krocích.

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>Otevřete stránku Podrobnosti o získání úlohy
Vyberte **získat**a pak otevřete rozhraní API výběrem tlačítka, který odpovídá vaší oblasti.

  ![Úloha - vytvořit konzoly Get výsledky](images/test-drive-job-4.png)

### <a name="review-the-response"></a>Přečtěte si odpovědi

Zadejte hodnoty pro **teamName** a **JobID**. Zadejte svůj klíč předplatného a potom vyberte **odeslat**. Následující odpověď se zobrazuje stav ukázkové úlohy a podrobnosti.

```
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": 
        [
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
```

## <a name="navigate-to-the-review-tool"></a>Přejděte k nástroji Kontrola
Na řídicím panelu obsahu moderátora vyberte **zkontrolujte** > **Image**. Zobrazí se bitovou kopii, která můžete zkontrolovat, připraveni k lidské zkontrolujte.

  ![Zkontrolujte nástroj obrázek tři cyklisty](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>Další postup

Použít rozhraní API REST v kódu ani začínat [rychlé spuštění úlohy .NET](moderation-jobs-quickstart-dotnet.md) integrovat s vaší aplikací.
