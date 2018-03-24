---
title: Konfigurace výstrah stavu služeb Azure s OpsGenie | Microsoft Docs
description: Získáte přizpůsobený oznámení o události stavu služby k vaší instanci OpsGenie.
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
ms.openlocfilehash: 6b8017f62dd895219f1d2cdac40f0efdf2db6c93
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="configure-service-health-alerts-with-opsgenie"></a>Konfigurace výstrah stavu služeb s OpsGenie

Tento článek ukazuje, jak nastavit výstrahy stavu služby Azure se OpsGenie pomocí webhook, jehož. Pomocí [OpsGenie](https://www.opsgenie.com/)na integrace se službou stavu služby Azure, může předat výstrahy stavu služby Azure OpsGenie. OpsGenie můžete určit správné lidí, kteří budou založené na na základě výzvy plány pomocí e-mailu, textové zprávy (SMS), telefonních hovorů, iOS a Android nabízená oznámení a narůstajícím výstrahy, dokud se výstrahy je potvrzena nebo uzavřený.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Vytváření v OpsGenie adresou URL integrační služby stavu
1.  Zajistěte, aby si zaregistrovali a přihlášení do vaší [OpsGenie](https://www.opsgenie.com/) účtu.

2.  Přejděte na **integrace** v OpsGenie části.

    ![V části "Integrace" v OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

3.  Vyberte **stavu služby Azure** integrace tlačítko.

    !["Tlačítko Azure stavu služby" v OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

4.  **Název** vaše výstrahy a zadejte **přiřazené týmu** pole.

5.  Vyplňte další pole, jako je **příjemce**, **povoleno**, a **potlačit oznámení**.

6.  Zkopírujte a uložte **URL integraci**, který by měl obsahovat vaše `apiKey` připojen na konec.

    !["URL integrace" v OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

7.  Vyberte **uložit integrace**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Vytvořit výstrahu OpsGenie pomocí portálu Azure
### <a name="for-a-new-action-group"></a>Pro novou skupinu akce:
1. Postupujte podle kroků 1 až 8 v [pomocí portálu Azure na oznámení stavu služby pro novou skupinu akce vytvořit výstrahu](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Definování v seznamu **akce**:

    a. **Typ akce:** *Webhooku*

    b. **Podrobnosti:** OpsGenie **URL integraci** jste uložili dřív.

    c. **Název:** Webhooku pro název, alias nebo identifikátor.

3. Vyberte **Uložit** po dokončení vytvoření výstrahy.

### <a name="for-an-existing-action-group"></a>Pro stávající skupinu akce:
1. V [portál Azure](https://portal.azure.com/), vyberte **monitorování**.

2. V **nastavení** vyberte **skupiny akcí**.

3. Najděte a vyberte skupinu akce, kterou chcete upravit.

4. Přidejte do seznamu **akce**:

    a. **Typ akce:** *Webhooku*

    b. **Podrobnosti:** OpsGenie **URL integraci** jste uložili dřív.

    c. **Název:** Webhooku pro název, alias nebo identifikátor.

5. Vyberte **Uložit** po dokončení aktualizace skupiny akce.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testování svoji integraci webhooku prostřednictvím požadavku HTTP POST
1. Vytvoření datové služby stavu, který chcete odeslat. Můžete najít datovou příklad služby stavu webhooku v [Webhooky Azure aktivity protokolu výstrahy](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Vytvořte požadavek HTTP POST takto:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Měli byste obdržet `200 OK` odpověď se zpráva stav "úspěšný".

4. Přejděte na [OpsGenie](https://www.opsgenie.com/) potvrďte, že byl úspěšně nastaven svoji integraci.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [nakonfigurovat oznámení webhooku pro existující systémy správy problém](service-health-alert-webhook-guide.md).
- Zkontrolujte [schéma výstrahy webhooku protokolu činnosti](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Další informace o [oznámení o stavu služby](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Další informace o [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md).