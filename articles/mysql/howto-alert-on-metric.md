---
title: Konfigurace upozornění na metriky – portál Azure – Databáze Azure pro MySQL
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k upozorněním metrik pro Azure Database for MySQL z webu Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c917804b42bc987228bbb2542682fe9fb308a467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067881"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Nastavení upozornění na metriky pro Azure Database for MySQL pomocí portálu Azure Portal 

Tento článek ukazuje, jak nastavit Azure Database for MySQL výstrahy pomocí portálu Azure. Můžete obdržet upozornění na základě metrik monitorování pro vaše služby Azure.

Výstraha se aktivuje, když hodnota zadané metriky překročí prahovou hodnotu, kterou přiřadíte. Výstraha se aktivuje při prvním splnění podmínky a poté, když tato podmínka již není splněna. 

Výstrahu můžete nakonfigurovat tak, aby při aktivaci spustila následující akce:
* Odeslání e-mailových oznámení správci služby a spolusprávcům
* Odešlete e-mail na další e-maily, které zadáte.
* Volání webhooku

Informace o pravidlech výstrah můžete konfigurovat a získat pomocí:
* [Portál Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Rozhraní REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Vytvoření pravidla výstrahy pro metriku z webu Azure Portal
1. Na [webu Azure Portal](https://portal.azure.com/)vyberte Azure Database for MySQL server, který chcete monitorovat.

2. V části **Monitorování** na postranním panelu vyberte **Výstrahy,** jak je znázorněno:

   ![Vybrat pravidla výstrahy](./media/howto-alert-on-metric/2-alert-rules.png)

3. Vyberte **Přidat upozornění na metriku** (+ ikona).

4. Otevře se stránka **Vytvořit pravidlo,** jak je znázorněno níže. Vyplňte požadované informace:

   ![Přidat formulář upozornění na metriku](./media/howto-alert-on-metric/4-add-rule-form.png)

5. V části **Podmínka** vyberte **Přidat podmínku**.

6. Vyberte metriku ze seznamu signálů, na které chcete být upozorněni. V tomto příkladu vyberte "Procento úložiště".
   
   ![Vybrat metriku](./media/howto-alert-on-metric/6-configure-signal-logic.png)

7. Nakonfigurujte logiku výstrah včetně **podmínky** (např. "Větší než"), **Práh** (např. 85 procent), **Agregace času**, **Časové období,** které musí být pravidlo metriky splněno, než se aktivuje výstraha (např. "Za posledních 30 minut"), a **frekvence**.
   
   Po dokončení vyberte **Hotovo.**

   ![Vybrat metriku](./media/howto-alert-on-metric/7-set-threshold-time.png)

8. V části **Skupiny akcí** vyberte **Vytvořit nový** a vytvořte novou skupinu pro příjem oznámení o výstraze.

9. Vyplňte formulář "Přidat skupinu akcí" s názvem, krátkým názvem, předplatným a skupinou prostředků.

10. Konfigurace typu akce **E-mail/SMS/Push/Hlas.**
    
    Zvolte "E-mail role Správce prostředků Azure" chcete-li vybrat vlastníky předplatného, přispěvatelé a čtenáři dostávat oznámení.
   
    Volitelně zadejte platný identifikátor URI v poli **Webhook,** pokud chcete, aby byl volán při požáru výstrahy.

    Po dokončení vyberte **ok.**

    ![Skupina akcí](./media/howto-alert-on-metric/10-action-group-type.png)

11. Zadejte název pravidla výstrahy, popis a závažnost.

    ![Skupina akcí](./media/howto-alert-on-metric/11-name-description-severity.png) 

12. Chcete-li **výstrahu** vytvořit, vyberte vytvořit pravidlo výstrahy.

    Během několika minut je výstraha aktivní a aktivuje se, jak bylo popsáno dříve.

## <a name="manage-your-alerts"></a>Správa výstrah
Jakmile vytvoříte výstrahu, můžete ji vybrat a provést následující akce:

* Zobrazení grafu znázorňujícího prahovou hodnotu metriky a skutečné hodnoty z předchozího dne relevantní pro tuto výstrahu.
* **Upravit** nebo **odstranit** pravidlo výstrahy.
* Pokud chcete výstrahu dočasně zastavit nebo obnovit příjem oznámení, **zakažte** nebo **povolte** výstrahu.


## <a name="next-steps"></a>Další kroky
* Další informace o [konfiguraci webových háků v výstrahách](../azure-monitor/platform/alerts-webhooks.md).
* Získejte [přehled o kolekci metrik,](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) abyste se ujistili, že vaše služba je dostupná a citlivá.
