---
title: Definování pracovních postupů moderování pomocí konzoly rozhraní REST API – Moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Pomocí řešení API pro kontrolu obsahu Azure můžete definovat vlastní pracovní postupy a prahové hodnoty na základě zásad obsahu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754187"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definování a používání pracovních postupů moderování (REST)

Pracovní postupy jsou cloudové přizpůsobené filtry, které můžete použít k efektivnějšímu zpracování obsahu. Pracovní postupy se mohou připojit k různým službám, aby filtrovaly obsah různými způsoby a pak podnikají příslušnou akci. Tato příručka ukazuje, jak používat rozhraní REST API pracovního postupu prostřednictvím konzoly rozhraní API k vytváření a používání pracovních postupů. Jakmile porozumíte struktuře rozhraní API, můžete tato volání snadno přenést na libovolnou platformu kompatibilní s rest.

## <a name="prerequisites"></a>Požadavky

- Přihlaste se nebo si vytvořte účet na webu [nástroje Kontrola](https://contentmoderator.cognitive.microsoft.com/) moderátora obsahu.

## <a name="create-a-workflow"></a>Vytvoření pracovního postupu

Chcete-li vytvořit nebo aktualizovat pracovní postup, přejděte na referenční stránku **[Pracovního postupu – vytvořit nebo aktualizovat](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** rozhraní API a vyberte tlačítko pro oblast klíče (najdete ji v adrese URL koncového bodu na stránce Přihlašovací **údaje** [nástroje Revize).](https://contentmoderator.cognitive.microsoft.com/) Tím se spustí konzola rozhraní API, kde můžete snadno vytvořit a spustit volání rozhraní REST API.

![Pracovní postup – výběr oblasti stránky Vytvořit nebo aktualizovat](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Zadat parametry volání REST

Zadejte hodnoty pro **tým**, **název pracovního postupu**a klíč **Ocp-Apim-Subscription-Key**:

- **Tým:** ID týmu, které jste vytvořili při nastavování účtu [nástroje revize](https://contentmoderator.cognitive.microsoft.com/) (najdete v poli **ID** na obrazovce Pověření nástroje pro kontrolu).
- **Workflowname**: Název nového pracovního postupu, který chcete přidat (nebo existující název, pokud chcete aktualizovat existující pracovní postup).
- **Ocp-Apim-Subscription-Key**: Klíč moderátora obsahu. Najdete ji na kartě **Nastavení** [nástroje Revize](https://contentmoderator.cognitive.microsoft.com).

![Pracovní postup – vytvoření nebo aktualizace parametrů a záhlaví konzoly](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Zadání definice pracovního postupu

1. Chcete-li zadat požadavek JSON s podrobnostmi pro `Image` **popis** a **typ** (buď nebo). **Request body** `Text`
2. V **aplikaci Expression**zkopírujte výchozí výraz JSON pracovního postupu. Váš konečný řetězec JSON by měl vypadat takto:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Pomocí tohoto rozhraní API můžete definovat jednoduché, složité a dokonce i vnořené výrazy pro vaše pracovní postupy. [Pracovní postup - vytvořit nebo aktualizovat](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) dokumentaci obsahuje příklady složitější logiky.

### <a name="submit-your-request"></a>Odešlete svůj požadavek
  
Vyberte **Poslat**. Pokud je operace úspěšná, `200 OK`je **stav Odpověď** a `true`zobrazí se pole **Obsah odpovědi** .

### <a name="examine-the-new-workflow"></a>Zkontrolujte nový pracovní postup

V [nástroji revize](https://contentmoderator.cognitive.microsoft.com/)vyberte **Nastavení** > **pracovních postupů**. Nový pracovní postup by se měl zobrazit v seznamu.

![Zkontrolovat seznam pracovních postupů nástroje](images/workflow-console-new-workflow.PNG)

Vyberte možnost **Upravit** pro pracovní postup a přejděte na kartu **Návrhář.** Zde můžete vidět intuitivní reprezentaci logiky JSON.

![Karta Návrhář pro vybraný pracovní postup](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Získat podrobnosti pracovního postupu

Chcete-li načíst podrobnosti o existujícím pracovním postupu, přejděte na referenční stránku **[Pracovního postupu – získání](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** rozhraní API a vyberte tlačítko pro vaši oblast (oblast, ve které je klíč spravován).

![Pracovní postup – výběr oblasti](images/test-drive-region.png)

Zadejte parametry volání REST jako ve výše uvedené části. Ujistěte se, že tentokrát název **pracovního postupu** je název existujícího pracovního postupu.

![Získání parametrů dotazu a záhlaví](images/workflow-get-default.PNG)

Vyberte **Poslat**. Pokud je operace úspěšná, `200 OK` **je stav Odpověď** a pole Obsah **odpovědi** zobrazuje pracovní postup ve formátu JSON, například následující:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak používat pracovní postupy s [úlohami moderování obsahu](try-review-api-job.md).