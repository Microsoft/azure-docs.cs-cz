---
title: Odesílání upozornění na Azure Service Health pomocí PagerDuty
description: Získejte přizpůsobená oznámení o událostech služby Service Health do vaší instance PagerDuty.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 7ae5141f33ebf4ec6e32029625af3a199a04d67b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594534"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Odesílání upozornění na Azure Service Health s PagerDuty pomocí webhooků

V tomto článku se dozvíte, jak nastavit oznámení o stavu služby Azure prostřednictvím PagerDuty pomocí Webhooku. Pomocí vlastního Microsoft Azureho typu integrace [PagerDuty](https://www.pagerduty.com/)můžete snadno přidávat Service Health výstrahy na nové nebo existující služby PagerDuty.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Vytvoření adresy URL pro integraci služby Health Service v PagerDuty
1.  Ujistěte se, že jste si zaregistrovali a jste přihlášeni ke svému účtu [PagerDuty](https://www.pagerduty.com/) .

1.  Přejděte do části **služby** v PagerDuty.

    ![Část "Services" v PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Vyberte **Přidat novou službu** nebo otevřete existující službu, kterou jste nastavili.

1.  V **Nastavení integrace** vyberte následující:

    a. **Typ integrace**: Microsoft Azure

    b. **Název integrace**: \<Name\>

    ![Nastavení integrace v PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Vyplňte všechna další povinná pole a vyberte **Přidat**.

1.  Otevřete tuto novou integraci a zkopírujte a uložte **integrační URL**.

    ![Adresa URL integrace v PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Vytvoření upozornění pomocí PagerDuty v Azure Portal
### <a name="for-a-new-action-group"></a>Pro novou skupinu akcí:
1. Postupujte podle kroků 1 až 8 v [části Vytvoření výstrahy na oznámení o stavu služby pro novou skupinu akcí pomocí Azure Portal](./alerts-activity-log-service-notifications-portal.md).

1. V seznamu akcí definujte tyto **Akce**:

    a. **Typ akce:** *Webhook*

    b. **Podrobnosti:** **Adresa URL integrace** PagerDuty, kterou jste předtím uložili.

    c. **Název:** Název, alias nebo identifikátor Webhooku.

1. Po dokončení vyberte **Uložit** , aby se výstraha vytvořila.

### <a name="for-an-existing-action-group"></a>Pro existující skupinu akcí:
1. V [Azure Portal](https://portal.azure.com/)vyberte **monitorovat**.

1. V části **Nastavení** vyberte **skupiny akcí**.

1. Vyhledejte a vyberte skupinu akcí, kterou chcete upravit.

1. Přidat do seznamu **akcí**:

    a. **Typ akce:** *Webhook*

    b. **Podrobnosti:** **Adresa URL integrace** PagerDuty, kterou jste předtím uložili.

    c. **Název:** Název, alias nebo identifikátor Webhooku.

1. Po dokončení aktualizace skupiny akcí vyberte **Uložit** .

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testování integrace Webhooku prostřednictvím požadavku HTTP POST
1. Vytvořte datovou část stavu služby, kterou chcete odeslat. Ukázkovou datovou část Webhooku pro stav služby najdete na [webhookech pro výstrahy protokolu aktivit Azure](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Požadavek HTTP POST vytvoříte takto:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Měla by se zobrazit `202 Accepted` Zpráva s informacemi o "ID události".

1. Pokud chcete ověřit, že se integrace úspěšně nastavila, navštivte [PagerDuty](https://www.pagerduty.com/) .

## <a name="next-steps"></a>Další kroky
- Naučte se [Konfigurovat oznámení Webhooku pro stávající systémy správy problémů](service-health-alert-webhook-guide.md).
- Zkontrolujte [schéma Webhooku upozornění protokolu aktivit](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Přečtěte si o [oznámeních o stavu služby](./service-notifications.md).
- Přečtěte si další informace o [skupinách akcí](../azure-monitor/alerts/action-groups.md).
