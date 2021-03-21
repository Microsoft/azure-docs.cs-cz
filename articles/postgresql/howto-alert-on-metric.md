---
title: Konfigurace výstrah – Azure Portal-Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k výstrahám metrik pro Azure Database for PostgreSQL-Single server z Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 1b4c10d152afba708eb176e89355ed31ec809168
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586737"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---single-server"></a>K nastavení výstrah pro metriky pro Azure Database for PostgreSQL jeden server použijte Azure Portal.

V tomto článku se dozvíte, jak nastavit výstrahy Azure Database for PostgreSQL pomocí Azure Portal. Můžete obdržet upozornění na základě metrik monitorování vašich služeb Azure.

Výstraha se aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, kterou přiřadíte. Tato výstraha se aktivuje při prvním splnění podmínky a následně v případě, že se už podmínka nesplní. 

Můžete nakonfigurovat výstrahu, která provede následující akce při triggerech:
* Odesílat e-mailová oznámení správcům služby a spolusprávcům.
* Odešlete e-mail na další e-maily, které zadáte.
* Zavolejte Webhook.

Můžete nakonfigurovat a získat informace o pravidlech výstrah pomocí:
* [Azure Portal](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Rozhraní REST API služby Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Vytvoření pravidla upozornění na metriku na webu Azure Portal
1. V [Azure Portal](https://portal.azure.com/)vyberte server Azure Database for PostgreSQL, který chcete monitorovat.

2. V části **monitorování** na bočním panelu vyberte **výstrahy** , jak je znázorněno níže:

   :::image type="content" source="./media/howto-alert-on-metric/2-alert-rules.png" alt-text="Vybrat pravidla výstrah":::

3. Vyberte **Přidat upozornění metriky** (+ ikona).

4. Otevře se stránka **vytvořit pravidlo** , jak je znázorněno níže. Vyplňte požadované informace:

   :::image type="content" source="./media/howto-alert-on-metric/4-add-rule-form.png" alt-text="Přidat formulář upozornění metriky":::

5. V části **Podmínka** vyberte **Přidat podmínku**.

6. Vyberte metriku ze seznamu signálů, na kterých se má upozornit. V tomto příkladu vyberte "úložiště v procentech".
   
   :::image type="content" source="./media/howto-alert-on-metric/6-configure-signal-logic.png" alt-text="Vybrat metriku":::

7. Nakonfigurujte logiku výstrahy včetně **podmínky** (např. "Větší než"), **prahová hodnota** (ex. 85 procent), **Časová agregace**, časový **interval** , po který musí být pravidlo metriky splněno před triggery výstrahy (např. Za posledních 30 minut a **frekvence**.
   
   Po dokončení vyberte **Hotovo** .

   :::image type="content" source="./media/howto-alert-on-metric/7-set-threshold-time.png" alt-text="Snímek obrazovky, který zvýrazní část logiky výstrah a tlačítko Hotovo.":::

8. V části **skupiny akcí** vyberte **vytvořit novou** a vytvořte novou skupinu pro příjem oznámení o výstraze.

9. Vyplňte formulář přidat skupinu akcí s názvem, krátkým názvem, předplatným a skupinou prostředků.

10. Nakonfigurujte typ akce **e-mail/SMS/Push/Voice** .
    
    Zvolte možnost Azure Resource Manager role e-mailu a vyberte vlastníky, přispěvatele a čtenáři předplatného, kteří budou dostávat oznámení.
   
    V případě potřeby zadejte do pole **Webhooku** platný identifikátor URI, pokud chcete, aby byla vyvolána, když se výstraha aktivuje.

    Po dokončení vyberte **OK** .

    :::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Snímek obrazovky, který ukazuje, jak přidat novou skupinu akcí.":::

11. Zadejte název, popis a závažnost pravidla výstrahy.

    :::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Skupina akcí"::: 

12. Vyberte **vytvořit pravidlo výstrahy** a vytvořte výstrahu.

    Během několika minut je výstraha aktivní a triggery, jak je popsáno výše.

## <a name="manage-your-alerts"></a>Správa výstrah
Jakmile vytvoříte výstrahu, můžete ji vybrat a provést následující akce:

* Zobrazení grafu znázorňujícího prahovou hodnotu metriky a skutečné hodnoty z předchozího dne, které se týkají této výstrahy.
* **Upravte** nebo **odstraňte** pravidlo výstrahy.
* Pokud chcete dočasně zastavit nebo obnovit přijímání oznámení, **zakažte** nebo **Povolte** výstrahu.

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [konfiguraci webhooků v upozorněních](../azure-monitor/alerts/alerts-webhooks.md).
* Získejte [Přehled o kolekci metrik](../azure-monitor/data-platform.md) , abyste měli jistotu, že je vaše služba dostupná a reaguje.