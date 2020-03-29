---
title: Použití úloh moderování s konzolou rozhraní REST API – Moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Operace úloh rozhraní Review API slouží k zahájení úloh moderování obsahu od konce na konec pro obrazový nebo textový obsah v Moderátoru obsahu Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72935939"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definování a používání úloh moderování (REST)

Moderování úloha slouží jako druh obálky pro funkce moderování obsahu, pracovnípostupy a recenze. Tato příručka ukazuje, jak pomocí úlohy REST API zahájit a zkontrolovat úlohy moderování obsahu. Jakmile porozumíte struktuře rozhraní API, můžete tato volání snadno přenést na libovolnou platformu kompatibilní s rest.

## <a name="prerequisites"></a>Požadavky

- Přihlaste se nebo si vytvořte účet na webu [nástroje Kontrola](https://contentmoderator.cognitive.microsoft.com/) moderátora obsahu.
- (Nepovinné) [Definujte vlastní pracovní postup,](./Review-Tool-User-Guide/Workflows.md) který se použije s úlohou. můžete také použít výchozí pracovní postup.

## <a name="create-a-job"></a>Vytvoření úlohy

Chcete-li vytvořit úlohu moderování, přejděte na referenční stránku [úlohy – vytvoření](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) referenční stránky rozhraní API a vyberte tlačítko pro oblast předplatného (najdete ji v adrese URL koncového bodu na stránce **Přihlašovací údaje** [nástroje Revize).](https://contentmoderator.cognitive.microsoft.com/) Tím se spustí konzola rozhraní API, kde můžete snadno vytvořit a spustit volání rozhraní REST API.

![Úloha – vytvoření výběru oblasti stránky](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Zadat parametry volání REST

Zadejte následující hodnoty pro vytvoření volání REST:

- **TeamName**: ID týmu, které jste vytvořili při nastavování účtu [nástroje revize](https://contentmoderator.cognitive.microsoft.com/) (najdete v poli **Id** na obrazovce Pověření nástroje pro kontrolu).
- **ContentType**: To může být "Obrázek", "Text", nebo "Video".
- **ContentId**: Řetězec vlastního identifikátoru. Tento řetězec je předán do rozhraní API a vrácena prostřednictvím zpětného volání. Je užitečné pro připojování vnitřníidentifikátory nebo metadata s výsledky úlohy moderování.
- **Název pracovního postupu**: Název pracovního postupu, který jste dříve vytvořili (nebo "výchozí" pro výchozí pracovní postup).
- **CallbackEndpoint**: (Volitelné) Adresa URL pro příjem informací o zpětném volání po dokončení recenze.
- **Ocp-Apim-Subscription-Key**: Klíč moderátora obsahu. Najdete ji na kartě **Nastavení** [nástroje Revize](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Vyplnit tělo požadavku

Tělo volání REST obsahuje jedno pole **ContentValue**. Pokud moderujete text, vložte do nezpracovaného textového obsahu nebo zadejte adresu URL obrázku nebo videa, pokud zmírňujete obraz/video. Můžete použít následující adresu URL ukázkového obrázku:[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Úloha – vytvoření parametrů dotazu konzoly, záhlaví a pole Text požadavku](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Odešlete svůj požadavek

Vyberte **Poslat**. Pokud je operace úspěšná, `200 OK` **je stav Odpověď** a pole Obsah **odpovědi** zobrazí ID úlohy. Zkopírujte toto ID, které se použije v následujících krocích.

![Recenze – pole Vytvořit obsah odpovědi konzoly zobrazuje ID recenze.](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Získat stav úlohy

Chcete-li získat stav a podrobnosti o spuštěné nebo dokončené úloze, přejděte na referenční stránku [Úloha – získání](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) rozhraní API a vyberte tlačítko pro vaši oblast (oblast, ve které je váš klíč spravován).

![Úloha – výběr oblasti](images/test-drive-region.png)

Zadejte parametry volání REST jako ve výše uvedené části. Pro tento krok **JobId** je jedinečný řetězec ID, který jste obdrželi při vytváření úlohy. Vyberte **Poslat**. Pokud je operace úspěšná, `200 OK` **je stav Odpověď** a pole Obsah **odpovědi** zobrazí úlohu ve formátu JSON, například následující:

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

![Úloha – odpověď na volání REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Prozkoumat nové recenze (y)

Pokud vaše úloha obsahu vedla k vytvoření recenze, můžete ji zobrazit v [nástroji Revize](https://contentmoderator.cognitive.microsoft.com). Vyberte **Zkontrolovat** > **textové**/**video** **obrázku**/(v závislosti na použitém obsahu). Obsah by se měl objevit, připravený k lidské kontrole. Poté, co lidský moderátor zkontroluje automaticky přiřazené značky a data předpovědi a odešle konečné rozhodnutí o moderování, rozhraní API úloh odešle všechny tyto informace do určeného koncového bodu zpětného volání.

## <a name="next-steps"></a>Další kroky

V této příručce jste se naučili, jak vytvořit a dotazovat úlohy moderování obsahu pomocí rozhraní REST API. Dále integrujte úlohy do scénáře moderování od konce, jako je například kurz [moderování elektronického obchodování.](./ecommerce-retail-catalog-moderation.md)