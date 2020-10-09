---
title: Definování pracovních postupů moderování pomocí konzoly REST API – Content Moderator
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API služby Azure Content Moderator recenze můžete definovat vlastní pracovní postupy a prahové hodnoty na základě zásad obsahu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "72754187"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definování a použití pracovních postupů moderování (REST)

Pracovní postupy jsou cloudově přizpůsobené filtry, které můžete použít ke efektivnějšímu zpracování obsahu. Pracovní postupy se můžou připojit k nejrůznějším službám a filtrovat obsah různými způsoby a pak provést příslušné akce. V této příručce se dozvíte, jak používat rozhraní REST API pracovního postupu prostřednictvím konzoly API k vytváření a používání pracovních postupů. Jakmile pochopíte strukturu rozhraní API, můžete tato volání snadno přenést na libovolnou platformu kompatibilní s REST.

## <a name="prerequisites"></a>Požadavky

- Přihlaste se nebo vytvořte účet na webu [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) Content moderator.

## <a name="create-a-workflow"></a>Vytvořit pracovní postup

Pokud chcete vytvořit nebo aktualizovat pracovní postup, klikněte na stránku **[pracovní postup – vytvořit nebo aktualizovat](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** odkaz na rozhraní API a vyberte tlačítko pro vaši oblast klíče (Toto můžete najít na stránce s **přihlašovacími údaji** [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/)na adrese URL koncového bodu). Spustí se konzola rozhraní API, kde můžete snadno sestavit a spustit REST API volání.

![Pracovní postup – výběr nebo aktualizace výběru oblasti stránky](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Zadejte parametry volání REST

Zadejte hodnoty pro **Team**, **Workflow**a **OCP-APIM-Subscription-Key**:

- **tým**: ID týmu, které jste vytvořili při nastavování účtu [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com/) (najdete ho v poli **ID** na obrazovce s přihlašovacími údaji nástroje pro kontrolu).
- **Workflow**: název nového pracovního postupu, který se má přidat (nebo existující název, pokud chcete aktualizovat existující pracovní postup).
- **OCP-APIM-Subscription-Key**: váš Content moderator klíč. Najdete ho na kartě **Nastavení** [Nástroje pro kontrolu](https://contentmoderator.cognitive.microsoft.com).

![Pracovní postup – parametry a hlavičky dotazů konzoly pro vytváření a aktualizaci](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Zadejte definici pracovního postupu.

1. Upravte pole **Text žádosti** a zadejte žádost JSON s podrobnostmi o **popisu** a **typu** ( `Image` nebo `Text` ).
2. Ve **výrazu pro výraz**zkopírujte výchozí výraz JSON pracovního postupu. Výsledný řetězec JSON by měl vypadat takto:

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
> Pomocí tohoto rozhraní API můžete definovat jednoduché, komplexní a dokonce vnořené výrazy pro vaše pracovní postupy. Dokumentace [pracovní postup – vytvořit nebo aktualizovat](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) obsahuje příklady složitější logiky.

### <a name="submit-your-request"></a>Odeslání žádosti
  
Vyberte **Poslat**. Pokud je operace úspěšná, **stav odpovědi** je `200 OK` a zobrazí se okno **obsah odpovědi** `true` .

### <a name="examine-the-new-workflow"></a>Kontrola nového pracovního postupu

V [nástroji pro revize](https://contentmoderator.cognitive.microsoft.com/)vyberte **Nastavení**  >  **pracovní postupy**. Nový pracovní postup by se měl zobrazit v seznamu.

![Seznam pracovních postupů nástroje pro kontrolu](images/workflow-console-new-workflow.PNG)

Vyberte pro svůj pracovní postup možnost **úprav** a pak na kartu **Návrhář** . Tady vidíte intuitivní znázornění logiky JSON.

![Karta Návrhář pro vybraný pracovní postup](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Získat podrobnosti pracovního postupu

Chcete-li načíst podrobnosti o existujícím pracovním postupu, klikněte na stránku **[pracovní postup – získat](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** rozhraní API a vyberte tlačítko pro vaši oblast (oblast, ve které je klíč spravovaný).

![Pracovní postup – získat výběr oblasti](images/test-drive-region.png)

Zadejte parametry volání REST jako v předchozí části. Ujistěte se, že tentokrát je název **pracovního postupu** názvem existujícího pracovního postupu.

![Získat parametry a záhlaví dotazů](images/workflow-get-default.PNG)

Vyberte **Poslat**. Pokud je operace úspěšná, **stav odpovědi** je a v `200 OK` poli **obsah odpovědi** se zobrazí pracovní postup ve formátu JSON, například takto:

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

- Naučte se používat pracovní postupy s [úlohami Moderování obsahu](try-review-api-job.md).