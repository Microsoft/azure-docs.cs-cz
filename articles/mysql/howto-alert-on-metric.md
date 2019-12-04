---
title: Konfigurace upozornění na metriku – Azure Portal-Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k výstrahám metrik pro Azure Database for MySQL z Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 487ca76115b93020a762cfa2afb5fd5a3be40a66
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770658"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Pomocí Azure Portal můžete nastavit výstrahy pro metriky pro Azure Database for MySQL 

V tomto článku se dozvíte, jak nastavit výstrahy Azure Database for MySQL pomocí Azure Portal. Můžete obdržet upozornění na základě metrik monitorování vašich služeb Azure.

Výstraha se aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, kterou přiřadíte. Tato výstraha se aktivuje při prvním splnění podmínky a následně v případě, že se už podmínka nesplní. 

Můžete nakonfigurovat výstrahu, která provede následující akce při triggerech:
* odesílání e-mailových oznámení správci služeb a spolusprávcům
* Odešlete e-mail na další e-maily, které zadáte.
* Volání webhooku

Můžete nakonfigurovat a získat informace o pravidlech výstrah pomocí:
* [Azure Portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Vytvoření pravidla výstrahy na metrikě z Azure Portal
1. V [Azure Portal](https://portal.azure.com/)vyberte server Azure Database for MySQL, který chcete monitorovat.

2. V části **monitorování** na bočním panelu vyberte **výstrahy** , jak je znázorněno níže:

   ![Vybrat pravidla výstrah](./media/howto-alert-on-metric/2-alert-rules.png)

3. Vyberte **Přidat upozornění metriky** (+ ikona).

4. Otevře se stránka **vytvořit pravidlo** , jak je znázorněno níže. Vyplňte požadované informace:

   ![Přidat formulář upozornění metriky](./media/howto-alert-on-metric/4-add-rule-form.png)

5. V části **Podmínka** vyberte **Přidat podmínku**.

6. Vyberte metriku ze seznamu signálů, na kterých se má upozornit. V tomto příkladu vyberte "úložiště v procentech".
   
   ![Vybrat metriku](./media/howto-alert-on-metric/6-configure-signal-logic.png)

7. Nakonfigurujte logiku výstrahy včetně **podmínky** (např. "Větší než"), **prahová hodnota** (ex. 85 procent), **Časová agregace**, časový **interval** , po který musí být pravidlo metriky splněno před triggery výstrahy (např. Za posledních 30 minut a **frekvence**.
   
   Po dokončení vyberte **Hotovo** .

   ![Vybrat metriku](./media/howto-alert-on-metric/7-set-threshold-time.png)

8. V části **skupiny akcí** vyberte **vytvořit novou** a vytvořte novou skupinu pro příjem oznámení o výstraze.

9. Vyplňte formulář přidat skupinu akcí s názvem, krátkým názvem, předplatným a skupinou prostředků.

10. Nakonfigurujte typ akce **e-mail/SMS/Push/Voice** .
    
    Zvolte možnost Azure Resource Manager role e-mailu a vyberte vlastníky, přispěvatele a čtenáři předplatného, kteří budou dostávat oznámení.
   
    V případě potřeby zadejte do pole **Webhooku** platný identifikátor URI, pokud chcete, aby byla vyvolána, když se výstraha aktivuje.

    Po dokončení vyberte **OK** .

    ![Skupina akcí](./media/howto-alert-on-metric/10-action-group-type.png)

11. Zadejte název, popis a závažnost pravidla výstrahy.

    ![Skupina akcí](./media/howto-alert-on-metric/11-name-description-severity.png) 

12. Vyberte **vytvořit pravidlo výstrahy** a vytvořte výstrahu.

    Během několika minut je výstraha aktivní a triggery, jak je popsáno výše.

## <a name="manage-your-alerts"></a>Správa výstrah
Jakmile vytvoříte výstrahu, můžete ji vybrat a provést následující akce:

* Zobrazení grafu znázorňujícího prahovou hodnotu metriky a skutečné hodnoty z předchozího dne, které se týkají této výstrahy.
* **Upravte** nebo **odstraňte** pravidlo výstrahy.
* Pokud chcete dočasně zastavit nebo obnovit přijímání oznámení, **zakažte** nebo **Povolte** výstrahu.


## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [konfiguraci webhooků v upozorněních](../azure-monitor/platform/alerts-webhooks.md).
* Získejte [Přehled o kolekci metrik](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) , abyste měli jistotu, že je vaše služba dostupná a reaguje.
