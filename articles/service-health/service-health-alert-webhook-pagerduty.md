---
title: Konfigurace výstrah stavu služby Azure s použitím PagerDuty | Dokumentace Microsoftu
description: Získáte přizpůsobená oznámení o události služby service health k vaší instanci PagerDuty.
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
ms.openlocfilehash: 5f6f3f61b5f7a06ac4056499edfb811780838cdc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441873"
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>Konfigurace výstrah stavu služby s použitím PagerDuty

Tento článek ukazuje, jak nastavit oznámení o stavu služby Azure prostřednictvím PagerDuty pomocí webhooku. S použitím [PagerDuty](https://www.pagerduty.com/)pro vlastní typ integrace Microsoft Azure, si můžete přidat upozornění na stav služby k vašim službám PagerDuty nové nebo existující.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Vytvoření adresy URL služby health integrace v PagerDuty
1.  Ujistěte se, že nemáte registrovanou službu a přihlášení do vaší [PagerDuty](https://www.pagerduty.com/) účtu.

1.  Přejděte **služby** části v PagerDuty.

    ![V části "Služby" v PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Vyberte **přidat novou službu** nebo otevřete existující službu jste vytvořili.

1.  V **nastavení integrace**, vyberte následující:

    a. **Typ integrace**: Microsoft Azure

    b. **Název integrace**: \<název\>

    !["Nastavení integrace" v PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Vyplňte všechna povinná pole a vyberte **přidat**.

1.  Otevřete tento nově zavedené integraci a zkopírujte a uložte **adresu URL integrace**.

    !["URL integrace" v PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Vytvořit upozornění PagerDuty pomocí webu Azure Portal
### <a name="for-a-new-action-group"></a>Pro nová skupina akcí:
1. Postupujte podle kroků 1 až 8 v [vytvořit upozornění na nová skupina akcí oznámení služby stavu s využitím webu Azure portal](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

1. Definování v seznamu **akce**:

    a. **Typ akce:** *Webhooku*

    b. **Podrobnosti:** PagerDuty **adresu URL integrace** jste uložili dřív.

    c. **Název:** Webhooku pro název, alias nebo identifikátor.

1. Vyberte **Uložit** po dokončení vytvoření výstrahy.

### <a name="for-an-existing-action-group"></a>Pro existující skupiny akcí:
1. V [webu Azure portal](https://portal.azure.com/)vyberte **monitorování**.

1. V **nastavení** vyberte **skupiny akcí**.

1. Vyhledejte a vyberte skupinu akcí, které chcete upravit.

1. Přidat do seznamu **akce**:

    a. **Typ akce:** *Webhooku*

    b. **Podrobnosti:** PagerDuty **adresu URL integrace** jste uložili dřív.

    c. **Název:** Webhooku pro název, alias nebo identifikátor.

1. Vyberte **Uložit** po dokončení aktualizovat skupinu akcí.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testování vaší integraci webhooků prostřednictvím požadavku HTTP POST
1. Vytvořte datová část služby stavu, který chcete odeslat. Můžete najít příkladu služby health datová část webhooku v [upozornění protokolů Webhooky Azure aktivity](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

1. Vytvoření požadavku HTTP POST následujícím způsobem:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Měli byste obdržet `202 Accepted` zprávou obsahující vaše "ID události.

1. Přejděte na [PagerDuty](https://www.pagerduty.com/) potvrďte, že vaše integrace byl nastaven úspěšně.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [nakonfigurovat oznámení webhooku pro existující systémy pro správu problémů](service-health-alert-webhook-guide.md).
- Zkontrolujte [schéma webhooku v upozornění protokolu aktivit](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Další informace o [služby oznámení o stavu](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Další informace o [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md).