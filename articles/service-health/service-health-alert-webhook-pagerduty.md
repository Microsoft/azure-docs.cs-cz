---
title: Odesílání výstrah stavu služby Azure pomocí služby PagerDuty
description: Do instance PagerDuty můžete dostávat přizpůsobená oznámení o událostech stavu služby.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: bb449a5279f3cea55e6aec2f72edfd11fb26227a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654065"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Odesílání výstrah stavu služby Azure pomocí služby PagerDuty pomocí webhooků

Tento článek ukazuje, jak nastavit oznámení o stavu služby Azure prostřednictvím PagerDuty pomocí webhooku. Pomocí vlastního typu integrace [Služby PagerDuty](https://www.pagerduty.com/)pro Microsoft Azure můžete snadno přidat výstrahy stavu služby do nových nebo stávajících služeb PagerDuty.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Vytvoření adresy URL integrace stavu služby v pagerduty
1.  Ujistěte se, že jste se zaregistrovali a jste přihlášeni ke svému účtu [PagerDuty.](https://www.pagerduty.com/)

1.  Přejděte do části **Služby** v PagerDuty.

    ![Oddíl "Služby" v PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Vyberte **Přidat novou službu** nebo otevřete existující službu, kterou jste nastavili.

1.  V **nastavení integrace**vyberte následující:

    a. **Typ integrace**: Microsoft Azure

    b. **Název**integrace \<: Název\>

    !["Nastavení integrace" v PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Vyplňte všechna další povinná pole a vyberte **Přidat**.

1.  Otevřete tuto novou integraci a zkopírujte a uložte **adresu URL integrace**.

    !["Adresa URL integrace" v PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Vytvoření výstrahy pomocí služby PagerDuty na webu Azure Portal
### <a name="for-a-new-action-group"></a>Pro novou skupinu akcí:
1. Postupujte podle kroků 1 až 8 v [části Vytvoření výstrahy na oznámení o stavu služby pro novou skupinu akcí pomocí portálu Azure](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. Definovat v seznamu **akcí**:

    a. **Typ akce:** *Webhook*

    b. **Podrobnosti:** Adresa URL **integrace** PagerDuty, kterou jste dříve uložili.

    c. **Název:** Název, alias nebo identifikátor webhooku.

1. **Chcete-li** výstrahu vytvořit, vyberte uložit po dokončení možnost Uložit.

### <a name="for-an-existing-action-group"></a>Pro existující skupinu akcí:
1. Na [webu Azure Portal](https://portal.azure.com/)vyberte **Monitor**.

1. V části **Nastavení** vyberte **skupiny akcí**.

1. Najděte a vyberte skupinu akcí, kterou chcete upravit.

1. Přidat do seznamu **akcí**:

    a. **Typ akce:** *Webhook*

    b. **Podrobnosti:** Adresa URL **integrace** PagerDuty, kterou jste dříve uložili.

    c. **Název:** Název, alias nebo identifikátor webhooku.

1. Chcete-li aktualizovat skupinu **akcí,** vyberte možnost Uložit po dokončení.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testování integrace webhooku prostřednictvím požadavku HTTP POST
1. Vytvořte datovou část stavu služby, kterou chcete odeslat. Můžete najít příklad stavu služby webhooku datové části na [Webhooks pro výstrahy protokolu aktivit Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Vytvořte požadavek HTTP POST následujícím způsobem:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Měli byste `202 Accepted` obdržet zprávu obsahující vaše "ID události".

1. Přejděte na [StránkrDuty](https://www.pagerduty.com/) a potvrďte, že vaše integrace byla úspěšně nastavena.

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [konfigurovat oznámení webhooku pro stávající systémy pro správu problémů](service-health-alert-webhook-guide.md).
- Zkontrolujte [webhookové schéma výstrahy protokolu aktivit](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Další informace o [oznámeních o stavu služby](../azure-monitor/platform/service-notifications.md).
- Přečtěte si další informace o [skupinách akcí](../azure-monitor/platform/action-groups.md).