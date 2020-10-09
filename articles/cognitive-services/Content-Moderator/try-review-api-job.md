---
title: Použití úloh moderování s konzolou REST API – Content Moderator
titleSuffix: Azure Cognitive Services
description: Pomocí operací prověřit rozhraní API můžete zahájit kompletní úlohy Moderování obsahu pro obrázek nebo textový obsah v Azure Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "72935939"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definování a použití úloh moderování (REST)

Úloha moderování slouží jako typ obálky pro funkčnost Moderování obsahu, pracovních postupů a revizí. V této příručce se dozvíte, jak pomocí rozhraní REST API úlohy iniciovat a kontrolovat úlohy Moderování obsahu. Jakmile pochopíte strukturu rozhraní API, můžete tato volání snadno přenést na libovolnou platformu kompatibilní s REST.

## <a name="prerequisites"></a>Požadavky

- Přihlaste se nebo vytvořte účet na webu [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) Content moderator.
- Volitelné [Definujte vlastní pracovní postup](./Review-Tool-User-Guide/Workflows.md) , který se má používat s vaší úlohou. můžete použít také výchozí pracovní postup.

## <a name="create-a-job"></a>Vytvoření úlohy

Pokud chcete vytvořit úlohu moderování, klikněte na referenční stránku pro [úlohu vytvořit](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) rozhraní API a vyberte tlačítko pro vaši oblast předplatného (najdete ho na stránce s **přihlašovacími údaji** v [nástroji pro kontrolu](https://contentmoderator.cognitive.microsoft.com/)na adrese URL koncového bodu). Spustí se konzola rozhraní API, kde můžete snadno sestavit a spustit REST API volání.

![Úloha – vytvoření výběru oblasti stránky](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Zadejte parametry volání REST

Zadejte následující hodnoty pro sestavení volání REST:

- ID **týmu**: ID týmu, které jste vytvořili při nastavování účtu [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) (najdete ho v poli **ID** na obrazovce s přihlašovacími údaji nástroje pro kontrolu).
- **ContentType**: může to být "image", "text" nebo "video".
- ID **obsahu: vlastní**řetězec identifikátoru. Tento řetězec je předán rozhraní API a vrácen prostřednictvím zpětného volání. Je vhodný pro přidružení interních identifikátorů nebo metadat k výsledkům úlohy moderování.
- **Workflow**: název pracovního postupu, který jste dříve vytvořili (nebo výchozí) pro výchozí pracovní postup.
- **CallbackEndpoint**: (volitelné) adresa URL pro příjem informací o zpětném volání po dokončení revize.
- **OCP-APIM-Subscription-Key**: váš Content moderator klíč. Najdete ho na kartě **Nastavení** [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Vyplnit text žádosti

Tělo volání REST obsahuje jedno pole **ContentValue**. Pokud připravujete text, vložte obsah nezpracovaného textu, pokud jste přihlásili obrázek nebo video, pokud jste přihlásili obrázek nebo video. Můžete použít následující adresu URL ukázkového obrázku: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Úloha – vytvoření parametrů dotazu konzoly, záhlaví a textu požadavku na konzolu](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Odeslání žádosti

Vyberte **Poslat**. Pokud je operace úspěšná, **stav odpovědi** je `200 OK` a v poli **obsah odpovědi** se zobrazí ID úlohy. Zkopírujte toto ID, které chcete použít v následujících krocích.

![Revize – pole vytvořit obsah odpovědi konzoly zobrazí ID revize.](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Získat stav úlohy

Chcete-li získat stav a podrobnosti o spuštěné nebo dokončené úloze, klikněte na stránku Reference k rozhraní API pro [úlohu](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) a vyberte tlačítko pro vaši oblast (oblast, ve které je klíč spravovaný).

![Úloha – získat výběr oblasti](images/test-drive-region.png)

Zadejte parametry volání REST jako v předchozí části. Pro tento krok je identifikátor **JobId** jedinečným řetězcem ID, který jste dostali při vytváření úlohy. Vyberte **Poslat**. Pokud je operace úspěšná, **stav odpovědi** je `200 OK` a pole **obsah odpovědi** zobrazí úlohu ve formátu JSON, třeba takto:

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

![Úloha – získat odpověď volání REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Prozkoumejte nové revize (y)

Pokud vaše úloha s obsahem skončila vytvořením recenze, můžete ji zobrazit v [nástroji pro revize](https://contentmoderator.cognitive.microsoft.com). Vyberte **zkontrolovat**  >  **Image** / **Text** / **video** textu obrazu (v závislosti na obsahu, který jste použili). Měl by se zobrazit obsah, který je připravený pro lidskou kontrolu. Po revizi lidského moderátora se automaticky přiřadí značky a předpovědi a odešle konečné rozhodnutí o moderování. rozhraní API úlohy odešle všechny tyto informace do určeného koncového bodu koncového bodu zpětného volání.

## <a name="next-steps"></a>Další kroky

V této příručce jste zjistili, jak vytvořit a dotazovat úlohy Moderování obsahu pomocí REST API. Dále Integrujte úlohy do kompletního scénáře moderování, jako je například kurz pro [moderování elektronického obchodování](./ecommerce-retail-catalog-moderation.md) .