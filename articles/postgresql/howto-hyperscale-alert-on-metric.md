---
title: Konfigurace výstrah – Citus (-Scale) – Azure Database for PostgreSQL
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k výstrahám metrik pro Azure Database for PostgreSQL-Citus (škálování na více procesorů).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a830f344023967f07ab809d67833f99e4e2958
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977603"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Použití Azure Portal k nastavení výstrah pro metriky pro Azure Database for PostgreSQL – Citus (škálování)

V tomto článku se dozvíte, jak nastavit výstrahy Azure Database for PostgreSQL pomocí Azure Portal. Můžete obdržet upozornění na základě metrik monitorování vašich služeb Azure.

Nastavíme výstrahu, která se aktivuje, když hodnota zadané metriky přechází z prahové hodnoty. Výstraha se aktivuje při prvním splnění podmínky a dále se aktivuje.

Můžete nakonfigurovat výstrahu, která provede následující akce při triggerech:
* Odesílat e-mailová oznámení správcům služeb a spolusprávcům.
* Odešlete e-mail na další e-maily, které zadáte.
* Zavolejte Webhook.

Můžete nakonfigurovat a získat informace o pravidlech výstrah pomocí:
* [Azure Portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Rozhraní REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Vytvoření pravidla výstrahy na metrikě z Azure Portal
1. V [Azure Portal](https://portal.azure.com/)vyberte server Azure Database for PostgreSQL, který chcete monitorovat.

2. V části **monitorování** na bočním panelu vyberte **výstrahy** , jak je znázorněno níže:

   ![Vybrat pravidla výstrah](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Vyberte **nové pravidlo výstrahy** (+ ikona).

4. Otevře se stránka **vytvořit pravidlo** , jak je znázorněno níže. Vyplňte požadované informace:

   ![Přidat formulář upozornění metriky](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. V části **Podmínka** vyberte **Přidat**.

6. Vyberte metriku ze seznamu signálů, na kterých se má upozornit. V tomto příkladu vyberte "úložiště v procentech".
   
   ![Vybrat metriku](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. Konfigurace logiky výstrah:

    * – **Operátor** (ex. "Větší než")
    * **Prahová hodnota** (např. 85 procent)
    * Časový rozsah **členitosti agregace** , po kterou musí být splněno pravidlo metriky před triggery výstrahy (např. "Za posledních 30 minut")
    * a **frekvence vyhodnocení** (např. 1 minuta)
   
   Po dokončení vyberte **Hotovo** .

   ![Vybrat metriku](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. V části **skupiny akcí** vyberte **vytvořit novou** a vytvořte novou skupinu pro příjem oznámení o výstraze.

9. Vyplňte formulář přidat skupinu akcí s názvem, krátkým názvem, předplatným a skupinou prostředků.

    ![Skupina akcí](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. Nakonfigurujte typ akce **e-mail/SMS/Push/Voice** .
    
    Pokud chcete odesílat oznámení vlastníkům, přispěvatelům a čtenářům předplatného, vyberte Azure Resource Manager role e-mailu.
   
    Po dokončení vyberte **OK** .

    ![Skupina akcí](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Zadejte název, popis a závažnost pravidla výstrahy.

    ![Skupina akcí](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. Vyberte **vytvořit pravidlo výstrahy** a vytvořte výstrahu.

    Během několika minut je výstraha aktivní a triggery, jak je popsáno výše.

## <a name="manage-your-alerts"></a>Správa výstrah

Po vytvoření výstrahy ji můžete vybrat a provést následující akce:

* Zobrazení grafu znázorňujícího prahovou hodnotu metriky a skutečné hodnoty z předchozího dne, které se týkají této výstrahy.
* **Upravte** nebo **odstraňte** pravidlo výstrahy.
* Pokud chcete dočasně zastavit nebo obnovit přijímání oznámení, **zakažte** nebo **Povolte** výstrahu.

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [konfiguraci webhooků v upozorněních](../azure-monitor/platform/alerts-webhooks.md).
* Získejte [Přehled o kolekci metrik](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) , abyste měli jistotu, že je vaše služba dostupná a reaguje.
