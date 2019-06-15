---
title: Definování pracovních postupů pro moderování s konzolou rozhraní REST API – Content Moderator
titlesuffix: Azure Cognitive Services
description: Obsah moderátor revizi rozhraní API služby Azure můžete použít k definování vlastních pracovních postupů a prahových hodnot na základě obsahu zásad.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: e150b1321f2fbd348e737222c752203281503643
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60605820"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definice a používání moderování pracovních postupů (REST)

Pracovní postupy jsou založené na cloudu vlastní filtry, které vám umožní efektivněji zpracovat obsah. Pracovní postupy můžete připojit k různým službám v různých způsobů filtrování obsahu a poté přijmout vhodná opatření. Tato příručka ukazuje, jak používat pracovní postup rozhraní REST API, prostřednictvím rozhraní API konzoly, vytváření a používání pracovních postupů. Jakmile porozumíte strukturu rozhraní API, můžete snadno port těchto volání na jakoukoli platformu REST kompatibilní.

## <a name="prerequisites"></a>Požadavky

- Přihlaste se nebo si vytvořte účet v Content Moderatoru [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com/) lokality.

## <a name="create-a-workflow"></a>Vytvoření pracovního postupu

Pokud chcete vytvořit nebo aktualizovat pracovní postup, přejděte na **[pracovního postupu - vytvořit nebo aktualizovat](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** rozhraní API odkazovat na stránku a klikněte na tlačítko pro vaše klíčové oblasti (zjistíte ji v adresu URL koncového bodu na **přihlašovacích údajů**  stránku [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com/)). Spustí se rozhraní API konzoly, kde můžete snadno vytvořit a spustit volání rozhraní REST API.

![Pracovní postup - vytvořit nebo aktualizovat stránku oblasti výběr](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Zadejte parametry volání REST

Zadejte hodnoty pro **týmu**, **workflowname**, a **Ocp-Apim-Subscription-Key**:

- **Tým**: ID týmu, který jste vytvořili při nastavování vašeho [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com/) účtu (součástí **Id** pole na obrazovce nástroje zkontrolujte přihlašovací údaje).
- **workflowname**: Název nového pracovního postupu pro přidání (nebo existující název, pokud chcete aktualizovat existující pracovní postup).
- **Ocp-Apim-Subscription-Key**: Content Moderator klíč. Tento nástroj naleznete na **nastavení** karty [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com).

![Pracovní postup - vytvořit parametry dotazu nebo aktualizace konzoly a hlavičky](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Zadejte definici pracovního postupu

1. Upravit **text žádosti** pole k zadání požadavku JSON s podrobnostmi o **popis** a **typ** (buď `Image` nebo `Text`).
2. Pro **výraz**, zkopírujte výchozí pracovní postup výraz JSON. Vaše poslední řetězec JSON by měl vypadat nějak takto:

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
> Jednoduché, komplexní a vnořené výrazy můžete definovat pro své pracovní postupy pomocí tohoto rozhraní API. [Pracovního postupu - vytvořit nebo aktualizovat](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) dokumentace je příkladem složitější logiku.

### <a name="submit-your-request"></a>Odeslání žádosti
  
Vyberte **Poslat**. Pokud je operace úspěšná, **stav odpovědi** je `200 OK`a **obsah odpovědi** zobrazí `true`.

### <a name="examine-the-new-workflow"></a>Prozkoumejte nový pracovní postup

V [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com/)vyberte **nastavení** > **pracovních postupů**. Nový pracovní postup by se zobrazit v seznamu.

![Nástroj pro revize seznam pracovních postupů](images/workflow-console-new-workflow.PNG)

Vyberte **upravit** možnost pro pracovní postup a přejděte **návrháře** kartu. Zde můžete zobrazit reprezentaci intuitivní logiky JSON.

![Kartu návrháře pro vybraný pracovní postup](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Získat podrobnosti pracovního postupu

K načtení podrobností o stávajícím pracovním postupu, přejděte na **[pracovního postupu - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** rozhraní API odkazovat na stránku a klikněte na tlačítko pro vaši oblast (oblasti, ve kterém je spravována klíč).

![Pracovní postup - výběr oblasti Get](images/test-drive-region.png)

Zadejte parametry volání REST stejně jako v předchozím oddílu. Ujistěte se, že současné době **workflowname** je název existujícího pracovního postupu.

![Parametry dotazu a hlavičky](images/workflow-get-default.PNG)

Vyberte **Poslat**. Pokud je operace úspěšná, **stav odpovědi** je `200 OK`a **obsah odpovědi** pole zobrazuje pracovní postup ve formátu JSON, jako je následující:

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

## <a name="next-steps"></a>Další postup

- Další informace o použití pracovních postupů pomocí [úlohy moderování obsahu](try-review-api-job.md).