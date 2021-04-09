---
title: Odesílání upozornění na Azure Service Health s OpsGenie pomocí webhooků
description: Získejte přizpůsobená oznámení o událostech služby Service Health do vaší instance OpsGenie.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: d8867d442d0c7fe563f6429fc1ff4edb212737c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594598"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Odesílání upozornění na Azure Service Health s OpsGenie pomocí webhooků

V tomto článku se dozvíte, jak nastavit upozornění na stav služby Azure pomocí Webhooku pomocí OpsGenie. Pomocí Azure Service Health integrace [OpsGenie](https://www.opsgenie.com/)můžete přeslat výstrahy Azure Service Health na OpsGenie. OpsGenie může určit správné uživatele na základě plánů na vyžádání, pomocí e-mailu, textových zpráv (SMS), telefonních hovorů, & iOS nabízených oznámení Androidu a předávat upozornění, dokud výstraha nebude potvrzená nebo uzavřená.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Vytvoření adresy URL pro integraci služby Health Service v OpsGenie
1.  Ujistěte se, že jste si zaregistrovali a jste přihlášeni ke svému účtu [OpsGenie](https://www.opsgenie.com/) .

1.  Přejděte do části **integrace** v OpsGenie.

    ![Část "integrace" v OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Vyberte tlačítko **Azure Service Health** Integration.

    ![Azure Service Health tlačítko v OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Pojmenujte** upozornění a zadejte pole **přiřazeno týmu** .

1.  Vyplňte další pole, jako jsou **příjemci**, **Povolení** a **potlačení oznámení**.

1.  Zkopírujte a uložte **adresu URL integrace**, která by již měla obsahovat vaše `apiKey` připojení ke konci.

    ![Adresa URL integrace v OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Vybrat **Uložit integraci**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Vytvoření upozornění pomocí OpsGenie v Azure Portal
### <a name="for-a-new-action-group"></a>Pro novou skupinu akcí:
1. Postupujte podle kroků 1 až 8 v [části Vytvoření výstrahy na oznámení o stavu služby pro novou skupinu akcí pomocí Azure Portal](./alerts-activity-log-service-notifications-portal.md).

1. V seznamu akcí definujte tyto **Akce**:

    a. **Typ akce:** *Webhook*

    b. **Podrobnosti:** **Adresa URL integrace** OpsGenie, kterou jste předtím uložili.

    c. **Název:** Název, alias nebo identifikátor Webhooku.

1. Po dokončení vyberte **Uložit** , aby se výstraha vytvořila.

### <a name="for-an-existing-action-group"></a>Pro existující skupinu akcí:
1. V [Azure Portal](https://portal.azure.com/)vyberte **monitorovat**.

1. V části **Nastavení** vyberte **skupiny akcí**.

1. Vyhledejte a vyberte skupinu akcí, kterou chcete upravit.

1. Přidat do seznamu **akcí**:

    a. **Typ akce:** *Webhook*

    b. **Podrobnosti:** **Adresa URL integrace** OpsGenie, kterou jste předtím uložili.

    c. **Název:** Název, alias nebo identifikátor Webhooku.

1. Po dokončení aktualizace skupiny akcí vyberte **Uložit** .

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testování integrace Webhooku prostřednictvím požadavku HTTP POST
1. Vytvořte datovou část stavu služby, kterou chcete odeslat. Ukázkovou datovou část Webhooku pro stav služby najdete na [webhookech pro výstrahy protokolu aktivit Azure](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Požadavek HTTP POST vytvoříte takto:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Měli byste obdržet `200 OK` odpověď se zprávou o stavu úspěšné.

1. Pokud chcete ověřit, že se integrace úspěšně nastavila, navštivte [OpsGenie](https://www.opsgenie.com/) .

## <a name="next-steps"></a>Další kroky
- Naučte se [Konfigurovat oznámení Webhooku pro stávající systémy správy problémů](service-health-alert-webhook-guide.md).
- Zkontrolujte [schéma Webhooku upozornění protokolu aktivit](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Přečtěte si o [oznámeních o stavu služby](./service-notifications.md).
- Přečtěte si další informace o [skupinách akcí](../azure-monitor/alerts/action-groups.md).
