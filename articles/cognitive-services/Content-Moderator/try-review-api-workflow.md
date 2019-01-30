---
title: Pracovní postupy pro moderování obsahu z rozhraní API konzoly – Content Moderator
titlesuffix: Azure Cognitive Services
description: Operace pracovního postupu můžete využít v Azure Content Moderator k vytvoření nebo aktualizaci pracovního postupu nebo získat podrobnosti pracovního postupu pomocí rozhraní API pro kontrolu.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: c71358828be1c5a2a83092f8079eca37c9e757dd
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207203"
---
# <a name="workflows-from-the-api-console"></a>Pracovní postupy z konzoly pro rozhraní API

Použití [operace pracovního postupu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) v Azure vytvořit nebo aktualizovat pracovní postup nebo získat podrobnosti pracovního postupu pomocí rozhraní API pro kontrolu Content Moderatoru. Pomocí tohoto rozhraní API můžete definovat jednoduchý, komplexní a vnořené výrazy pro pracovní postupy. Pracovní postupy se zobrazí v nástroj pro recenze pro použití týmem. Pracovní postupy také používají operace úlohy revizi rozhraní API.

## <a name="prerequisites"></a>Požadavky

1. Přejděte [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com/). Pokud jste tak ještě neučinili, přihlaste. 
2. V nástroji pro kontrolu v rámci **nastavení**, vyberte **pracovních postupů** kartu, jak je znázorněno v nástroj pro recenze [kurzu pracovního postupu](Review-Tool-User-Guide/Workflows.md).

### <a name="browse-to-the-workflows-screen"></a>Přejděte do okna pracovních postupů

Na řídicím panelu Content Moderator vyberte **revize** > **nastavení** > **pracovních postupů**. Zobrazí se výchozí pracovní postup.

  ![Výchozí pracovní postup](images/default-workflow-listed.PNG)

### <a name="get-the-json-definition-of-the-default-workflow"></a>Získat definice JSON pracovního výchozí

Vyberte **upravit** a potom vyberte možnost pro pracovní postup **JSON** kartu. Zobrazí se následující výraz JSON:

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isAdult",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": []
        }
        ],
        "Type": "Actions"
        }
    }

## <a name="get-workflow-details"></a>Získat podrobnosti pracovního postupu

Použití **pracovního postupu - Get** operace zobrazíte podrobnosti o existujícím pracovním postupem výchozí.

Nástroj pro recenze, přejděte na [pověření](Review-Tool-User-Guide/credentials.md#the-review-tool) oddílu.

### <a name="browse-to-the-api-reference"></a>Přejděte na reference k rozhraní API

1. V **pověření** zobrazit, vyberte možnost [reference k rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). 
2. Když **pracovního postupu - vytvořit nebo aktualizovat** otevře se stránka, přejděte na [pracovního postupu - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58) odkaz.

### <a name="select-your-region"></a>Vyberte oblast

Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu.

  ![Pracovní postup - výběr oblasti Get](images/test-drive-region.png)

  **Pracovního postupu - Get** otevře se konzola rozhraní API.

### <a name="enter-parameters"></a>Zadání parametrů

Zadejte hodnoty pro **týmu**, **workflowname**, a **Ocp-Apim-Subscription-Key** (váš klíč předplatného):

- **Tým**: ID týmu, který jste vytvořili při nastavování vašeho [Zkontrolujte nástroj účet](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Název pracovního postupu. Použití `default`.
- **Ocp-Apim-Subscription-Key**: Na **nastavení** kartu. Další informace najdete v tématu [Přehled](overview.md).

  ![Parametry dotazu a hlavičky](images/workflow-get-default.PNG)

### <a name="submit-your-request"></a>Odeslání žádosti
  
Vyberte **Poslat**. Pokud je operace úspěšná, **stav odpovědi** je `200 OK`a **obsah odpovědi** pole zobrazuje následující pracovní postup JSON:

    {
        "Name": "default",
        "Description": "Default",
        "Type": "Image",
        "Expression": {
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isadult",
            "Operator": "eq",
            "Value": "true",
            "AlternateInput": null,
            "Type": "Condition"
            },
        "Then": {
            "Perform": [{
                "Name": "createreview",
                "Subteam": null,
                "CallbackEndpoint": null,
                "Tags": []
            }],
            "Type": "Actions"
            },
            "Else": null,
            "Type": "Logic"
            }
    }


## <a name="create-a-workflow"></a>Vytvoření pracovního postupu

Nástroj pro recenze, přejděte na [pověření](Review-Tool-User-Guide/credentials.md#the-review-tool) oddílu.

### <a name="browse-to-the-api-reference"></a>Přejděte na reference k rozhraní API

V **pověření** zobrazit, vyberte možnost [reference k rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). **Pracovního postupu - vytvořit nebo aktualizovat** otevře se stránka.

### <a name="select-your-region"></a>Vyberte oblast

Pro **testovací konzoly Open API**, vyberte oblast, která nejlépe popisuje vaši polohu.

  ![Pracovní postup - vytvořit nebo aktualizovat stránku oblasti výběr](images/test-drive-region.png)

  **Pracovního postupu - vytvořit nebo aktualizovat** otevře se konzola rozhraní API.

### <a name="enter-parameters"></a>Zadání parametrů

Zadejte hodnoty pro **týmu**, **workflowname**, a **Ocp-Apim-Subscription-Key** (váš klíč předplatného):

- **Tým**: ID týmu, který jste vytvořili při nastavování vašeho [Zkontrolujte nástroj účet](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: Název nového pracovního postupu.
- **Ocp-Apim-Subscription-Key**: Na **nastavení** kartu. Další informace najdete v tématu [Přehled](overview.md).

  ![Pracovní postup - vytvořit parametry dotazu nebo aktualizace konzoly a hlavičky](images/workflow-console-parameters.PNG)

### <a name="enter-the-workflow-definition"></a>Zadejte definici pracovního postupu

1. Upravit **text žádosti** pole k zadání požadavku JSON s podrobnostmi o **popis** a **typ** (Image nebo Text). 
2. Pro **výraz**, zkopírujte výchozí výraz pracovního postupu v předchozí části, jak je znázorněno zde:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": 
                // Copy the default workflow expression from the preceding section
        }

    Vaše tělo požadavku bude vypadat jako následující žádosti JSON:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": {
                "Type": "Logic",
                "If": {
                    "ConnectorName": "moderator",
                    "OutputName": "isAdult",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                    },
                "Then": {
                "Perform": [
                {
                    "Name": "createreview",
                    "CallbackEndpoint": null,
                    "Tags": [ ]
                }
                ],
                "Type": "Actions"
                }
            }
        }
 
### <a name="submit-your-request"></a>Odeslání žádosti
  
Vyberte **Poslat**. Pokud je operace úspěšná, **stav odpovědi** je `200 OK`a **obsah odpovědi** zobrazí `true`.

### <a name="check-out-the-new-workflow"></a>Podívejte se na nový pracovní postup

Nástroj pro recenze, vyberte **revize** > **nastavení** > **pracovních postupů**. Nový pracovní postup se zobrazí a je připravený k použití.

  ![Nástroj pro revize seznam pracovních postupů](images/workflow-console-new-workflow.PNG)
  
### <a name="review-your-new-workflow-details"></a>Projděte si podrobnosti o vašem novém pracovního postupu

1. Vyberte **upravit** a potom vyberte možnost pro pracovní postup **návrháře** a **JSON** karty.

   ![Kartu návrháře pro vybraný pracovní postup](images/workflow-console-new-workflow-designer.PNG)

2. Chcete-li zobrazit JSON pracovního postupu, vyberte **JSON** kartu.

## <a name="next-steps"></a>Další postup

* Složitější příklady pracovního postupu najdete v článku [přehledu pracovních postupů](workflow-api.md).
* Další informace o použití pracovních postupů pomocí [úlohy moderování obsahu](try-review-api-job.md).
