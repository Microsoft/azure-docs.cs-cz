---
title: Konfigurace výstrah stavu služby Azure s použitím OpsGenie | Dokumentace Microsoftu
description: Získáte přizpůsobená oznámení o události služby service health k vaší instanci použitím OpsGenie.
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
ms.openlocfilehash: 6e9d44c4b64e41b04c40d0c511a638e539752da4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435022"
---
# <a name="configure-service-health-alerts-with-opsgenie"></a>Konfigurace výstrah stavu služby s použitím OpsGenie

Tento článek ukazuje, jak nastavit upozornění na stav služby Azure s použitím OpsGenie pomocí webhooku. S použitím [použitím OpsGenie](https://www.opsgenie.com/)integrace služby Azure Service Health, můžete použitím OpsGenie předávání výstrah stavu služby Azure. Použitím OpsGenie můžete určit správné osoby pro oznámení podle plánů ve volání pomocí e-mailu, textové zprávy (SMS), telefonní hovory, iOS a Android nabízená oznámení a rostoucích výstrahy, dokud je potvrzení nebo zavření výstrahy.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Vytvoření adresy URL služby health integrace v použitím OpsGenie
1.  Ujistěte se, že nemáte registrovanou službu a přihlášení do vaší [použitím OpsGenie](https://www.opsgenie.com/) účtu.

1.  Přejděte **integrace** v použitím OpsGenie části.

    ![V části "Integrace" v použitím OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Vyberte **Azure Service Health** integrace tlačítko.

    !["Azure Service Health tlačítko" v použitím OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Název** vaše upozornění a určit **přiřazeny týmu** pole.

1.  Vyplňte další pole, jako jsou **příjemci**, **povoleno**, a **potlačit oznámení**.

1.  Zkopírujte a uložte **adresu URL integrace**, která by měla obsahovat vaše `apiKey` příponou.

    !["URL integrace" v použitím OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Vyberte **uložit integrace**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Vytvořit upozornění na webu Azure Portal pomocí použitím OpsGenie
### <a name="for-a-new-action-group"></a>Pro nová skupina akcí:
1. Postupujte podle kroků 1 až 8 v [vytvořit upozornění na nová skupina akcí oznámení služby stavu s využitím webu Azure portal](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

1. Definování v seznamu **akce**:

    a. **Typ akce:** *Webhooku*

    b. **Podrobnosti:** použitím OpsGenie **adresu URL integrace** jste uložili dřív.

    c. **Název:** Webhooku pro název, alias nebo identifikátor.

1. Vyberte **Uložit** po dokončení vytvoření výstrahy.

### <a name="for-an-existing-action-group"></a>Pro existující skupiny akcí:
1. V [webu Azure portal](https://portal.azure.com/)vyberte **monitorování**.

1. V **nastavení** vyberte **skupiny akcí**.

1. Vyhledejte a vyberte skupinu akcí, které chcete upravit.

1. Přidat do seznamu **akce**:

    a. **Typ akce:** *Webhooku*

    b. **Podrobnosti:** použitím OpsGenie **adresu URL integrace** jste uložili dřív.

    c. **Název:** Webhooku pro název, alias nebo identifikátor.

1. Vyberte **Uložit** po dokončení aktualizovat skupinu akcí.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testování vaší integraci webhooků prostřednictvím požadavku HTTP POST
1. Vytvořte datová část služby stavu, který chcete odeslat. Můžete najít příkladu služby health datová část webhooku v [upozornění protokolů Webhooky Azure aktivity](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

1. Vytvoření požadavku HTTP POST následujícím způsobem:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Měli byste obdržet `200 OK` odpověď se zpráva stav "úspěšné".

1. Přejděte na [použitím OpsGenie](https://www.opsgenie.com/) potvrďte, že vaše integrace byl nastaven úspěšně.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [nakonfigurovat oznámení webhooku pro existující systémy pro správu problémů](service-health-alert-webhook-guide.md).
- Zkontrolujte [schéma webhooku v upozornění protokolu aktivit](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Další informace o [služby oznámení o stavu](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Další informace o [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md).