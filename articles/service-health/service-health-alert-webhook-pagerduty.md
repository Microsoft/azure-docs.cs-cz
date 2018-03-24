---
title: Konfigurace výstrah stavu služeb Azure s PagerDuty | Microsoft Docs
description: Získáte přizpůsobený oznámení o události stavu služby k vaší instanci PagerDuty.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 6e9fcf20d368e270f9af4551c539acd873335498
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>Konfigurace výstrah stavu služeb s PagerDuty

Tento článek ukazuje, jak nastavit oznámení o stavu služby Azure prostřednictvím PagerDuty pomocí webhook, jehož. Pomocí [PagerDuty](https://www.pagerduty.com/)pro vlastní typ integrace Microsoft Azure, můžete pak moci bez obtíží přidat stav služby oznámení k vašim službám PagerDuty nový nebo existující.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Vytváření v PagerDuty adresou URL integrační služby stavu
1.  Zajistěte, aby si zaregistrovali a přihlášení do vaší [PagerDuty](https://www.pagerduty.com/) účtu.

2.  Přejděte na **služby** v PagerDuty části.

    ![V části "Služby" v PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

3.  Vyberte **přidat novou službu** nebo otevřete stávající službu jste nastavili.

4.  V **nastavení integrace**, vyberte následující:

    a. **Typ integrace**: Microsoft Azure

    b. **Název integrace**: \<název\>

    !["Nastavení integrace" v PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

5.  Vyplňte všechna povinná pole a vyberte **přidat**.

6.  Otevřete tento nový integrace a zkopírujte a uložte **URL integraci**.

    !["URL integrace" v PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Vytvořit výstrahu PagerDuty pomocí portálu Azure
### <a name="for-a-new-action-group"></a>Pro novou skupinu akce:
1. Postupujte podle kroků 1 až 8 v [pomocí portálu Azure na oznámení stavu služby pro novou skupinu akce vytvořit výstrahu](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Definování v seznamu **akce**:

    a. **Typ akce:** *Webhooku*

    b. **Podrobnosti:** PagerDuty **URL integraci** jste uložili dřív.

    c. **Název:** Webhooku pro název, alias nebo identifikátor.

3. Vyberte **Uložit** po dokončení vytvoření výstrahy.

### <a name="for-an-existing-action-group"></a>Pro stávající skupinu akce:
1. V [portál Azure](https://portal.azure.com/), vyberte **monitorování**.

2. V **nastavení** vyberte **skupiny akcí**.

3. Najděte a vyberte skupinu akce, kterou chcete upravit.

4. Přidejte do seznamu **akce**:

    a. **Typ akce:** *Webhooku*

    b. **Podrobnosti:** PagerDuty **URL integraci** jste uložili dřív.

    c. **Název:** Webhooku pro název, alias nebo identifikátor.

5. Vyberte **Uložit** po dokončení aktualizace skupiny akce.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testování svoji integraci webhooku prostřednictvím požadavku HTTP POST
1. Vytvoření datové služby stavu, který chcete odeslat. Můžete najít datovou příklad služby stavu webhooku v [Webhooky Azure aktivity protokolu výstrahy](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Vytvořte požadavek HTTP POST takto:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Měli byste obdržet `202 Accepted` zprávou obsahující vaší "ID události."

4. Přejděte na [PagerDuty](https://www.pagerduty.com/) potvrďte, že byl úspěšně nastaven svoji integraci.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [nakonfigurovat oznámení webhooku pro existující systémy správy problém](service-health-alert-webhook-guide.md).
- Zkontrolujte [schéma výstrahy webhooku protokolu činnosti](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Další informace o [oznámení o stavu služby](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Další informace o [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md).