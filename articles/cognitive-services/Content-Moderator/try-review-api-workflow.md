---
title: Azure obsahu moderátora - obsahu přerušování pracovních z konzoly pro rozhraní API | Microsoft Docs
description: Další informace o použití pracovních obsahu přerušování z konzoly pro rozhraní API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/05/2018
ms.author: sajagtap
ms.openlocfilehash: 700b2bea5e902141659266a94d61ceb810c1b802
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342495"
---
# <a name="workflows-from-the-api-console"></a>Pracovní postupy z konzoly pro rozhraní API

Použití [operace pracovního postupu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) v obsahu moderátora Azure vytvořit nebo aktualizovat pracovního postupu nebo získat podrobnosti pracovního postupu pomocí rozhraní API revize. Můžete definovat jednoduchý, komplexní a i vnořené výrazy pro vaše pracovní postupy pomocí toto rozhraní API. Pracovní postupy se zobrazí v kontrolní nástroj pro váš tým používat. Pracovní postupy také používají operace rozhraní API zkontrolujte úlohy.

## <a name="prerequisites"></a>Požadavky

1. Přejděte na [Zkontrolujte nástroj](https://contentmoderator.cognitive.microsoft.com/). Pokud jste tak ještě neučinili, zaregistrujte si. 
2. V nástroji zkontrolujte v části **nastavení**, vyberte **pracovních** kartě, jak je znázorněno v nástroji zkontrolujte [kurzu pracovního postupu](Review-Tool-User-Guide/Workflows.md).

### <a name="browse-to-the-workflows-screen"></a>Přejděte k obrazovce pro pracovní postupy

Na řídicím panelu obsahu moderátora vyberte **zkontrolujte** > **nastavení** > **pracovních**. Zobrazí výchozí pracovní postup.

  ![Výchozí pracovní postup](images/default-workflow-listed.PNG)

### <a name="get-the-json-definition-of-the-default-workflow"></a>Získat definici JSON výchozí pracovního postupu

Vyberte **upravit** a pak vyberte možnost pro pracovní postup **JSON** kartě. Zobrazí následující výraz JSON:

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

## <a name="get-workflow-details"></a>Získat podrobnosti o pracovní postup

Použití **pracovní postup - Get** operaci získat podrobnosti o existující výchozí pracovního postupu.

V nástroji kontrolní, přejděte na [pověření](Review-Tool-User-Guide/credentials.md#the-review-tool) části.

### <a name="browse-to-the-api-reference"></a>Přejděte do referenční dokumentace rozhraní API

1. V **pověření** zobrazit, vyberte možnost [referenční dokumentace rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). 
2. Když **pracovní postup - vytvořit nebo aktualizovat** otevře se stránka, přejděte na [pracovní postup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58) odkaz.

### <a name="select-your-region"></a>Vyberte oblast

Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu.

  ![Pracovní postup - výběr oblasti Get](images/test-drive-region.png)

  **Pracovní postup - Get** otevře se konzola rozhraní API.

### <a name="enter-parameters"></a>Zadání parametrů

Zadejte hodnoty pro **team**, **workflowname**, a **Ocp-Apim-Subscription-Key** (svůj klíč předplatného):

- **tým**: ID týmu, který jste vytvořili při nastavování vaší [Zkontrolujte nástroj účet](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: název pracovního postupu. Použití `default`.
- **OCP-Apim-Subscription-Key**: nachází na **nastavení** kartě. Další informace najdete v tématu [přehled](overview.md).

  ![Parametry dotazu GET a hlavičky](images/workflow-get-default.PNG)

### <a name="submit-your-request"></a>Odešlete žádost
  
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

V nástroji kontrolní, přejděte na [pověření](Review-Tool-User-Guide/credentials.md#the-review-tool) části.

### <a name="browse-to-the-api-reference"></a>Přejděte do referenční dokumentace rozhraní API

V **pověření** zobrazit, vyberte možnost [referenční dokumentace rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59). **Pracovní postup - vytvořit nebo aktualizovat** otevře se stránka.

### <a name="select-your-region"></a>Vyberte oblast

Pro **testování konzoly otevřené rozhraní API**, vyberte oblast, která nejlépe popisuje vaši polohu.

  ![Pracovní postup - vytvořit nebo aktualizovat výběr oblast stránky](images/test-drive-region.png)

  **Pracovní postup - vytvořit nebo aktualizovat** otevře se konzola rozhraní API.

### <a name="enter-parameters"></a>Zadání parametrů

Zadejte hodnoty pro **team**, **workflowname**, a **Ocp-Apim-Subscription-Key** (svůj klíč předplatného):

- **tým**: ID týmu, který jste vytvořili při nastavování vaší [Zkontrolujte nástroj účet](https://contentmoderator.cognitive.microsoft.com/). 
- **workflowname**: název nového pracovního postupu.
- **OCP-Apim-Subscription-Key**: nachází na **nastavení** kartě. Další informace najdete v tématu [přehled](overview.md).

  ![Pracovní postup – vytvoření nebo aktualizace parametry dotazu konzoly a hlavičky](images/workflow-console-parameters.PNG)

### <a name="enter-the-workflow-definition"></a>Zadejte definici pracovního postupu

1. Upravit **text žádosti** pole k zadání požadavku JSON s podrobnostmi o **popis** a **typu** (obrázek nebo Text). 
2. Pro **výraz**, zkopírujte výraz výchozí pracovního postupu z předchozí části, jak je vidět tady:

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": 
                // Copy the default workflow expression from the preceding section
        }

    Vaše textu žádosti vypadá následující požadavku JSON:

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
 
### <a name="submit-your-request"></a>Odešlete žádost
  
Vyberte **Poslat**. Pokud je operace úspěšná, **stav odpovědi** je `200 OK`a **obsah odpovědi** pole zobrazí `true`.

### <a name="check-out-the-new-workflow"></a>Podívejte se na nový pracovní postup

V nástroji zkontrolujte vyberte **zkontrolujte** > **nastavení** > **pracovních**. Váš nový pracovní postup se zobrazí a je připravený k použití.

  ![Kontrolní seznam nástroj pracovních postupů](images/workflow-console-new-workflow.PNG)
  
### <a name="review-your-new-workflow-details"></a>Nový pracovní postup podrobnosti

1. Vyberte **upravit** a pak vyberte možnost pro pracovní postup **Návrhář** a **JSON** karty.

   ![Karta návrháře pro vybraný pracovní postup](images/workflow-console-new-workflow-designer.PNG)

2. Pokud chcete zobrazit zobrazení JSON pracovního postupu, vyberte **JSON** kartě.

## <a name="next-steps"></a>Další postup

* Složitější příklady pracovního postupu najdete v tématu [přehled pracovních](workflow-api.md).
* Další informace o použití pracovních s [obsahu přerušování úlohy](try-review-api-job.md).
