---
title: Odesílání výstrah stavu služby Azure pomocí OpsGenie pomocí webhooků
description: Do instance OpsGenie můžete dostávat přizpůsobená oznámení o událostech stavu služby.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: def12d5e7b1b93b8370cd7be61538fca53531ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654133"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Odesílání výstrah stavu služby Azure pomocí OpsGenie pomocí webhooků

Tento článek ukazuje, jak nastavit výstrahy stavu služby Azure s OpsGenie pomocí webhooku. Pomocí [opsGenie](https://www.opsgenie.com/)'s Azure Service Health Integration, můžete předat výstrahy stavu služby Azure OpsGenie. OpsGenie může určit správné lidi, kteří budou informovat na základě plánů na volání, pomocí e-mailu, textových zpráv (SMS), telefonních hovorů, iOS & nabízených oznámení androida a eskalujících upozornění, dokud není výstraha potvrzena nebo uzavřena.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Vytvoření adresy URL integrace stavu služby v OpsGenie
1.  Ujistěte se, že jste se zaregistrovali a jste přihlášeni ke svému účtu [OpsGenie.](https://www.opsgenie.com/)

1.  Přejděte do části **Integrace** v OpsGenie.

    ![Sekce "Integrace" v OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Vyberte tlačítko integrace **služby Azure Service Health.**

    ![Tlačítko "Azure Service Health" v OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Pojmenujte** výstrahu a zadejte pole **Přiřazeno týmu.**

1.  Vyplňte ostatní pole, jako **jsou Příjemci**, **Povoleno**a **Potlačit oznámení**.

1.  Zkopírujte a uložte **adresu URL** `apiKey` integrace , která by již měla obsahovat připojenou adresu až do konce.

    !["Adresa URL integrace" v OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Vybrat **uložit integraci**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Vytvoření výstrahy pomocí OpsGenie na webu Azure Portal
### <a name="for-a-new-action-group"></a>Pro novou skupinu akcí:
1. Postupujte podle kroků 1 až 8 v [části Vytvoření výstrahy na oznámení o stavu služby pro novou skupinu akcí pomocí portálu Azure](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. Definovat v seznamu **akcí**:

    a. **Typ akce:** *Webhook*

    b. **Podrobnosti:** **Adresa URL integrace** OpsGenie, kterou jste dříve uložili.

    c. **Název:** Název, alias nebo identifikátor webhooku.

1. **Chcete-li** výstrahu vytvořit, vyberte uložit po dokončení možnost Uložit.

### <a name="for-an-existing-action-group"></a>Pro existující skupinu akcí:
1. Na [webu Azure Portal](https://portal.azure.com/)vyberte **Monitor**.

1. V části **Nastavení** vyberte **skupiny akcí**.

1. Najděte a vyberte skupinu akcí, kterou chcete upravit.

1. Přidat do seznamu **akcí**:

    a. **Typ akce:** *Webhook*

    b. **Podrobnosti:** **Adresa URL integrace** OpsGenie, kterou jste dříve uložili.

    c. **Název:** Název, alias nebo identifikátor webhooku.

1. Chcete-li aktualizovat skupinu **akcí,** vyberte možnost Uložit po dokončení.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testování integrace webhooku prostřednictvím požadavku HTTP POST
1. Vytvořte datovou část stavu služby, kterou chcete odeslat. Můžete najít příklad stavu služby webhooku datové části na [Webhooks pro výstrahy protokolu aktivit Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Vytvořte požadavek HTTP POST následujícím způsobem:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Měli byste `200 OK` obdržet odpověď se zprávou o stavu "úspěšné".

1. Přejděte na [OpsGenie](https://www.opsgenie.com/) a ověřte, že vaše integrace byla úspěšně nastavena.

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [konfigurovat oznámení webhooku pro stávající systémy pro správu problémů](service-health-alert-webhook-guide.md).
- Zkontrolujte [webhookové schéma výstrahy protokolu aktivit](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Další informace o [oznámeních o stavu služby](../azure-monitor/platform/service-notifications.md).
- Přečtěte si další informace o [skupinách akcí](../azure-monitor/platform/action-groups.md).