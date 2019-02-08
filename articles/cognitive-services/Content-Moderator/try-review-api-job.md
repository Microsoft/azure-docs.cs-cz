---
title: Spouštění úloh moderování obsahu s konzolou rozhraní API – Content Moderator
titlesuffix: Azure Cognitive Services
description: Ke spuštění úlohy začátku do konce pro moderování obsahu pro obsah image nebo text v Azure Content Moderator použijte operace úlohy revizi rozhraní API.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 9fac80ff152b0f7b9c36c182759d41245364fff9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862424"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>Spustit úlohu moderování z konzoly pro rozhraní API

Použití rozhraní API revize [úlohy operace](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) iniciovat úlohy začátku do konce pro moderování obsahu pro obsah image nebo text v Azure Content Moderator. 

Moderování úlohy kontroluje váš obsah s použitím rozhraní API pro moderování obsahu moderátor obrázků nebo rozhraní API pro moderování textu. Potom úlohu moderování použije pracovní postupy (definované v nástroj pro recenze) ke generování kontroly v nástroj pro recenze. 

Poté, co lidské moderátory kontroly automaticky přiřazená značky a data předpovědí a odešle moderování konečné rozhodnutí, odešle rozhraní API pro kontrolu všechny informace do vašeho koncového bodu rozhraní API.

## <a name="prerequisites"></a>Požadavky

Přejděte [nástroji pro kontrolu](https://contentmoderator.cognitive.microsoft.com/). Pokud jste tak ještě neučinili, přihlaste. V rámci nástroje pro recenze [definovat vlastní pracovní postup](Review-Tool-User-Guide/Workflows.md) používat v tomto `Job` operace.

## <a name="use-the-api-console"></a>Pomocí rozhraní API konzoly
Mohli vyzkoušet rozhraní API pomocí konzole online, budete potřebovat několik hodnot, zadejte do konzoly:
    
- `teamName`: Použití `Id` pole z obrazovky nástroj zkontrolujte přihlašovací údaje. 
- `ContentId`: Tento řetězec je předán rozhraní API a vrátí přes zpětného volání. **ContentId** je užitečné pro přidružení k výsledky úlohy moderování interní identifikátory nebo metadata.- `Workflowname`: Název [pracovního postupu, který jste vytvořili](Review-Tool-User-Guide/Workflows.md) v předchozí části.
- `Ocp-Apim-Subscription-Key`: Na **nastavení** kartu. Další informace najdete v tématu [Přehled](overview.md).

Přístup k rozhraní API konzoly je z **pověření** okna.

### <a name="navigate-to-the-api-reference"></a>Přejděte na reference k rozhraní API
V **pověření** okně [reference k rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5).

  `Job.Create` Otevře se stránka.

### <a name="select-your-region"></a>Vyberte oblast
Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu.
  ![Úloha: vytvoření stránky oblast výběru](images/test-drive-job-1.png)

  `Job.Create` Otevře se konzola rozhraní API. 

### <a name="enter-parameters"></a>Zadání parametrů

Zadejte hodnoty pro požadované parametry dotazu a váš klíč předplatného. V **text žádosti** zadejte umístění informace, které chcete zkontrolovat. V tomto příkladu použijeme to [Ukázkový obrázek](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png).

  ![Úlohy – vytvoření parametry dotazu konzoly, záhlaví a pole textu požadavku](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Odeslání žádosti
Vyberte **Poslat**. ID úlohy se vytvoří. Zkopírujte tuto hodnotu na použití v dalších krocích.

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>Otevřete stránku podrobností získat úlohu
Vyberte **získat**a pak otevřete rozhraní API tak, že vyberete tlačítko, které odpovídá vaší oblasti.

  ![Vytvoření úlohy – konzoly získat výsledky](images/test-drive-job-4.png)

### <a name="review-the-response"></a>Zkontrolovat odpovědi

Zadejte hodnoty pro **teamName** a **JobID**. Zadejte klíč předplatného a pak vyberte **odeslat**. Následující odpověď ukazuje stav ukázkové úlohy a podrobnosti.

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

## <a name="navigate-to-the-review-tool"></a>Přejděte na nástroje pro recenze
Na řídicím panelu Content Moderator, vyberte **revize** > **Image**. Obrázek, který jste se zobrazí, připravena pro recenze prováděné lidmi.

  ![Zkontrolujte nástroj obrázek tři cyklisty](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>Další postup

Použití rozhraní REST API ve vašem kódu nebo začínat [rychlý úvod k .NET úlohy](moderation-jobs-quickstart-dotnet.md) integrovat s vaší aplikací.
