---
title: Konfigurace upozornění na metriku – Azure Portal-Azure Database for MySQL-flexibilní Server
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k výstrahám metrik pro Azure Database for MySQL flexibilní Server z Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: be52bbe58f6c2ff6ee21703860e8d8e00ec30072
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110147"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql---flexible-server"></a>Použití Azure Portal k nastavení výstrah pro metriky pro Azure Database for MySQL-flexibilní Server 

> [!IMPORTANT] 
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

V tomto článku se dozvíte, jak nastavit výstrahy Azure Database for MySQL pomocí Azure Portal. Můžete obdržet upozornění na základě metrik monitorování vašich služeb Azure.

Výstraha se aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, kterou přiřadíte. Tato výstraha se aktivuje při prvním splnění podmínky a následně v případě, že se už podmínka nesplní. Upozornění na metriku jsou stavová, to znamená, že odesílají oznámení pouze při změně stavu.

Můžete nakonfigurovat výstrahu, která provede následující akce při triggerech:
* Odesílání e-mailových oznámení správci služeb a spolusprávcům
* Odešlete e-mail na další e-maily, které zadáte.
* Volání webhooku

Můžete nakonfigurovat a získat informace o pravidlech výstrah pomocí:
* [Azure Portal](../../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Rozhraní REST API služby Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Vytvoření pravidla upozornění na metriku na webu Azure Portal
1. V [Azure Portal](https://portal.azure.com/)vyberte Azure Database for MySQL flexibilní Server, který chcete monitorovat.
2. V části **monitorování** v postranním panelu vyberte **výstrahy**.
3. Vyberte **+ Nové pravidlo upozornění**.
4. Otevře se stránka **Vytvořit pravidlo**. Vyplňte požadované informace:
5. V části **Podmínka** zvolte **možnost vybrat podmínku**.
6. Zobrazí se seznam podporovaných signálů, vyberte metriku, na které chcete vytvořit výstrahu. Vyberte například "procento úložiště".
7. Zobrazí se graf metriky za posledních 6 hodin. Pomocí rozevíracího seznamu **období grafu** můžete vybrat, že se má zobrazit historie metriky.
8. Vyberte typ **prahové hodnoty** (např. "Static" nebo "Dynamic"), **operátor** (ex. "Větší než") a **typ agregace** (např. průměr). Tím se určí logika, kterou vyhodnotí pravidlo výstrahy metriky.
    - Pokud používáte **statickou** prahovou hodnotu, pokračujte v definování **prahové hodnoty** (např. 85 procent). Graf metriky vám pomůže určit, co může být vhodnou prahovou hodnotou.
    - Pokud používáte **dynamickou** prahovou hodnotu, pokračujte v definování **prahové hodnoty citlivosti**. V grafu metriky se zobrazí vypočtené prahové hodnoty na základě nedávných dat. [Přečtěte si další informace o typu dynamické prahové hodnoty a možnosti citlivosti](../../azure-monitor/alerts/alerts-dynamic-thresholds.md).
9. Upřesněte podmínku tak, že upravíte interval **členitosti agregace (tečka)** , přes který jsou datové body seskupeny pomocí funkce typ agregace (např. "30 minut") a **frekvence** (ex "každých 15 minut").
10. Klikněte na **Hotovo**.
11. Přidejte skupinu akcí. Skupina akcí je kolekce předvoleb oznámení definovaných vlastníkem předplatného Azure. V části **skupiny akcí** zvolte **možnost vybrat skupinu akcí** a vyberte již existující skupinu akcí, kterou chcete připojit k pravidlu výstrahy.
12. Můžete také vytvořit novou skupinu akcí pro příjem oznámení o výstraze. Další informace najdete v tématu [Vytvoření a Správa skupiny akcí](../../azure-monitor/alerts/action-groups.md) .
13. Chcete-li vytvořit novou skupinu akcí, vyberte možnost **+ vytvořit skupinu akcí**. Vyplňte formulář vytvořit skupinu akcí pomocí **předplatného**, **skupiny prostředků**, **názvu skupiny akcí** a **zobrazovaného názvu**.
14. Nakonfigurujte **oznámení** pro skupinu akcí.
    
    V části **Typ oznámení** zvolte Azure Resource Manager role e-mailu a vyberte vlastníky, přispěvatele a čtenáři předplatného, kteří budou dostávat oznámení. Vyberte **roli Azure Resource Manager** pro odeslání e-mailu.
    Můžete také zvolit **e-mail/zprávu SMS/nabízení/hlas** k odesílání oznámení konkrétním příjemcům.

    Zadejte **název** typu oznámení a po dokončení vyberte **zkontrolovat + vytvořit** .

    <!--:::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Action group":::-->
    
15. Vyplňte **Podrobnosti pravidla upozornění** , jako **je název pravidla výstrahy**, **Popis**, **uložení pravidla upozornění na skupinu prostředků** a **závažnost**.

    <!--:::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Action group":::-->

16. Vyberte **vytvořit pravidlo výstrahy** a vytvořte výstrahu.
    Během několika minut je výstraha aktivní a triggery, jak je popsáno výše.
## <a name="manage-your-alerts"></a>Správa výstrah
Jakmile vytvoříte výstrahu, můžete ji vybrat a provést následující akce:

* Zobrazení grafu znázorňujícího prahovou hodnotu metriky a skutečné hodnoty z předchozího dne, které se týkají této výstrahy.
* **Upravte** nebo **odstraňte** pravidlo výstrahy.
* Pokud chcete dočasně zastavit nebo obnovit přijímání oznámení, **zakažte** nebo **Povolte** výstrahu.


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [Nastavení výstrahy na metrikách](../../azure-monitor/alerts/alerts-metric.md).
- Přečtěte si další informace o dostupných [metrikách v Azure Database for MySQL flexibilním serveru](./concepts-monitoring.md).
- [Principy fungování upozornění na metriky ve službě Azure Monitor](../../azure-monitor/alerts/alerts-metric-overview.md)