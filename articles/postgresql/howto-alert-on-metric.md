---
title: Konfigurace upozornění metriky pro službu Azure Database for PostgreSQL – jeden Server na webu Azure portal
description: Tento článek popisuje postup konfigurace a upozornění na metriku přístupu pro službu Azure Database for PostgreSQL – jeden Server z webu Azure portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 000dfe2d3e594c71f9c7ebbff7bce7141243668a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067300"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---single-server"></a>Nastavte si upozornění na metriky pro službu Azure Database for PostgreSQL – jeden Server pomocí webu Azure portal

V tomto článku se dozvíte, jak nastavit – Azure Database for postgresql – výstrah pomocí webu Azure portal. Obdržíte výstrahu na základě monitorování metrik pro služby Azure.

Upozornění aktivační události, když hodnota zadaného metrika překročí mezní hodnotu, kterou přiřadíte. Upozornění triggery obě, pokud je první podmínka splněna, a pak později při podmínka, která už probíhá není splněná. 

Můžete nakonfigurovat výstrahu při aktivaci provést následující akce:
* Odeslání e-mailová oznámení správce služeb a spolupracujících správců.
* odeslání e-mailu další e-maily, které zadáte.
* Volání webhooku.

Můžete nakonfigurovat a získat informace o použití pravidel upozornění:
* [Azure Portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Rozhraní REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Vytvoření pravidla upozornění na metriky na webu Azure Portal
1. V [webu Azure portal](https://portal.azure.com/), vyberte serveru Azure Database for PostgreSQL chcete monitorovat.

2. V části **monitorování** části bočního panelu, vyberte **výstrahy** uvedeno:

   ![Vybrat pravidla upozornění](./media/howto-alert-on-metric/2-alert-rules.png)

3. Vyberte **přidat upozornění metriky** (ikony +).

4. **Vytvořit pravidlo** otevře se stránka, jak je znázorněno níže. Vyplňte požadované informace:

   ![Přidat upozornění metriky formulář](./media/howto-alert-on-metric/4-add-rule-form.png)

5. V rámci **podmínku** vyberte **přidat podmínku**.

6. Vyberte ze seznamu signálů, které se mají generovat výstrahy na metriku. V tomto příkladu vyberte "Procenta úložiště".
   
   ![Vybrat metriku](./media/howto-alert-on-metric/6-configure-signal-logic.png)

7. Konfigurace, včetně logika upozornění **podmínku** (např.) "Větší než"), **prahová hodnota** (např.) 85 procent), **Časová agregace**, **období** času musí být splněny pravidlo metriky před výstrah aktivačních událostí (např.) "Za posledních 30 minut") a **frekvence**.
   
   Vyberte **provádí** po dokončení.

   ![Vybrat metriku](./media/howto-alert-on-metric/7-set-threshold-time.png)

8. V rámci **skupiny akcí** vyberte **vytvořit nový** k vytvoření nové skupiny pro příjem oznámení o výstraze.

9. Vyplňte formulář "Přidat skupinu akcí" s názvem, krátký název, předplatné a skupinu prostředků.

10. Konfigurace **e-mailu/SMS nebo nabízená/hlasové** typ akce.
    
    Zvolte "E-mailu Azure Resource Manager roli" k výběru předplatného vlastníci, přispěvatelé a čtenáři dostávat oznámení.
   
    Volitelně zadejte platný identifikátor URI, **Webhooku** pole, pokud chcete, volá se, když se aktivuje upozornění.

    Vyberte **OK** po dokončení.

    ![Skupina akcí](./media/howto-alert-on-metric/10-action-group-type.png)

11. Zadejte název pravidla výstrahy, popis a závažnosti.

    ![Skupina akcí](./media/howto-alert-on-metric/11-name-description-severity.png) 

12. Vyberte **vytvořit pravidlo upozornění** k vytvoření upozornění.

    Během několika minut upozornění je aktivní a aktivuje jak bylo popsáno dříve.

## <a name="manage-your-alerts"></a>Správa výstrah
Po vytvoření výstrahy, můžete ho vyberte a proveďte následující akce:

* Zobrazte graf zobrazující mezní hodnota metriky a skutečnými hodnotami z předchozího dne odpovídající této výstrahy.
* **Upravit** nebo **odstranit** pravidlo upozornění.
* **Zakázat** nebo **povolit** výstrahu, pokud chcete dočasně zastavit nebo obnovit příjem oznámení.

## <a name="next-steps"></a>Další postup
* Další informace o [konfiguraci webhooků ve výstrahách](../azure-monitor/platform/alerts-webhooks.md).
* Získat [přehled shromažďování metrik](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) k Ujistěte se, že vaše služba není k dispozici a reagují.
