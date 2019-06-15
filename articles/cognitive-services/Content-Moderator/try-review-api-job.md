---
title: Použití úloh moderování s konzolou rozhraní REST API – Content Moderator
titlesuffix: Azure Cognitive Services
description: Ke spuštění úlohy začátku do konce pro moderování obsahu pro obsah image nebo text v Azure Content Moderator použijte operace úlohy revizi rozhraní API.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 7827cee2af2dfc0c1fddc407c1d146dc9a66c514
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607595"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definice a používání moderování úlohy (REST)

Moderování úlohy slouží jako typ obálky pro funkce moderování obsahu, pracovní postupy a kontroly. Tato příručka ukazuje, jak používat tuto úlohu rozhraní REST API pro zahájení a úlohy pro moderování obsahu kontroly. Jakmile porozumíte strukturu rozhraní API, můžete snadno port těchto volání na jakoukoli platformu REST kompatibilní.

## <a name="prerequisites"></a>Požadavky

- Přihlaste se nebo si vytvořte účet v Content Moderatoru [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com/) lokality.
- (Volitelné) [Definovat vlastní pracovní postup](./Review-Tool-User-Guide/Workflows.md) použít s úlohou; můžete také použít výchozí pracovní postup.

## <a name="create-a-job"></a>Vytvoření úlohy

Chcete-li vytvořit úlohu moderování, přejděte na [úlohy – vytvoření](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) rozhraní API odkazovat na stránku a klikněte na tlačítko pro vaše klíčové oblasti (zjistíte ji v adresu URL koncového bodu na **přihlašovací údaje** stránku [revize Nástroj](https://contentmoderator.cognitive.microsoft.com/)). Spustí se rozhraní API konzoly, kde můžete snadno vytvořit a spustit volání rozhraní REST API.

![Úloha: vytvoření stránky oblast výběru](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Zadejte parametry volání REST

Zadejte následující hodnoty k vytvoření volání REST:

- **teamName**: ID týmu, který jste vytvořili při nastavování vašeho [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com/) účtu (součástí **Id** pole na obrazovce nástroje zkontrolujte přihlašovací údaje).
- **ContentType**: To může být "Image", "Text" nebo "Video".
- **ContentId**: Vlastní identifikátor řetězce. Tento řetězec je předán rozhraní API a vrátí přes zpětného volání. Je užitečné pro přidružení k výsledky úlohy moderování interní identifikátory nebo metadata.
- **workflowname**: Název pracovního postupu, který jste dříve vytvořili (nebo "Výchozí" pro výchozí pracovní postup).
- **CallbackEndpoint**: (Volitelné) Adresa URL získat informace zpětného volání při dokončení kontroly.
- **Ocp-Apim-Subscription-Key**: Content Moderator klíč. Tento nástroj naleznete na **nastavení** karty [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Zadejte text žádosti

Jedno pole obsahuje text volání REST **ContentValue**. Vložte obsah nezpracovaný text, pokud jsou moderování textu nebo zadejte obrázek nebo adresa URL videa, pokud jste moderování obrázků a videa. Můžete použít následující adresa URL obrázku vzorku: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Úlohy – vytvoření parametry dotazu konzoly, záhlaví a pole textu požadavku](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Odeslání žádosti

Vyberte **Poslat**. Pokud je operace úspěšná, **stav odpovědi** je `200 OK`a **obsah odpovědi** zobrazí ID pro úlohu. Zkopírujte toto ID se má použít v následujících krocích.

![Projděte si – vytvoření konzoly odpovědi obsahu pole se zobrazí ID revize](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Získat stav úlohy

Stav a podrobnosti o spuštěné nebo dokončené úlohy, najdete [úlohy – získání](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) rozhraní API odkazovat na stránku a klikněte na tlačítko pro vaši oblast (oblasti, ve kterém je spravována klíč).

![Úlohy – výběr oblasti Get](images/test-drive-region.png)

Zadejte parametry volání REST stejně jako v předchozím oddílu. V tomto kroku **JobId** je jedinečný řetězec ID jste získali při vytváření projektu. Vyberte **Poslat**. Pokud je operace úspěšná, **stav odpovědi** je `200 OK`a **obsah odpovědi** zobrazí úlohy ve formátu JSON, jako je následující:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Úlohy – získání odpovědi volání REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Prozkoumání nových review(s)

Pokud vaše úloha obsahu výsledkem vytvoření kontrolu, můžete zobrazit v [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com). Vyberte **revize** > **Image**/**Text**/**Video** (v závislosti na tom, co je obsah použít). Obsah by se měla objevit, připraveno pro recenze prováděné lidmi. Po lidské moderátory kontroly automaticky přiřazená značek a dat předpovědi a odesílá moderování konečné rozhodnutí, odešle všechny tyto informace do koncového bodu určeného zpětného volání koncového bodu rozhraní API pro úlohy.

## <a name="next-steps"></a>Další postup

V této příručce jste zjistili, jak vytvořit a dotazovat moderování obsahu úlohy pomocí rozhraní REST API. V dalším kroku můžete integrovat úlohy do scénáři moderování začátku do konce, jako [E-commerce moderování](./ecommerce-retail-catalog-moderation.md) kurzu.