---
title: Nastavení výstrah a oznámení v Azure Portal
description: Pomocí Azure Portal můžete vytvářet výstrahy, které můžou aktivovat oznámení nebo automatizaci při splnění podmínek, které zadáte.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: aamalvea
ms.author: aamalvea
ms.reviewer: wiassaf, sstein
ms.date: 05/04/2020
ms.openlocfilehash: 86ac0f64a6a797b1bc702597ed30e6417f2efe14
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500967"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-using-the-azure-portal"></a>Vytváření upozornění pro Azure SQL Database a Azure synapse Analytics pomocí Azure Portal
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


## <a name="overview"></a>Přehled

V tomto článku se dozvíte, jak nastavit výstrahy pro databáze v Azure SQL Database a Azure synapse Analytics pomocí Azure Portal. Když některá metrika (například velikost databáze nebo využití procesoru) dosáhne prahové hodnoty, můžou vám výstrahy poslat e-mail nebo zavolat webový Hook.

> [!NOTE]
> Pokyny pro konkrétní instrukce spravované instance Azure SQL najdete v tématu [Vytvoření upozornění pro spravovanou instanci Azure SQL](../managed-instance/alerts-create.md).

Můžete obdržet upozornění na základě metrik monitorování pro události nebo služby Azure.

* **Hodnoty metrik** – výstraha se aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, kterou přiřadíte v obou směrech. To znamená, že se aktivuje při prvním splnění podmínky a následně v případě, že se už podmínka nesplní.
* **Události protokolu aktivit** – výstraha se může aktivovat *každou* událost, nebo jenom v případě, že dojde k určitému počtu událostí.

Můžete nakonfigurovat výstrahu, která při triggeru provede následující akce:

* Odesílání e-mailových oznámení správci služeb a spolusprávcům
* Odešlete e-mail na další e-maily, které zadáte.
* Volání webhooku

Můžete nakonfigurovat a získat informace o pravidlech upozornění pomocí

* [Azure Portal](../../azure-monitor/platform/alerts-classic-portal.md)
* [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md)
* [Rozhraní příkazového řádku (CLI)](../../azure-monitor/platform/alerts-classic-portal.md)
* [Rozhraní REST API služby Azure Monitor](/rest/api/monitor/alertrules)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Vytvoření pravidla výstrahy na metrikě s Azure Portal

1. Na [portálu](https://portal.azure.com/)vyhledejte prostředek, který chcete monitorovat, a vyberte ho.
2. V části monitorování vyberte **výstrahy** . Text a ikona se mohou mírně lišit pro různé prostředky.  

   ![Sledování](./media/alerts-insights-configure-portal/Alerts.png)
  
3. Kliknutím na tlačítko **nové pravidlo výstrahy** otevřete stránku **vytvořit pravidlo** .
  ![Vytvořit pravidlo](./media/alerts-insights-configure-portal/create-rule.png)

4. V části **Podmínka** klikněte na **Přidat**.
  ![Definovat podmínku](./media/alerts-insights-configure-portal/create-rule.png)
5. Na stránce **Konfigurovat logiku signálu** vyberte signál.
  ![Vybrat signál](./media/alerts-insights-configure-portal/select-signal.png)
6. Po výběru signálu, jako je **Procento procesoru**, se zobrazí stránka **Konfigurovat logiku signálu** .
  ![Konfigurace logiky signálů](./media/alerts-insights-configure-portal/configure-signal-logic.png)
7. Na této stránce nakonfigurujte tento typ prahové hodnoty, operátor, typ agregace, prahovou hodnotu, členitost agregace a frekvenci vyhodnocení. Pak klikněte na **Hotovo**.
8. V poli **vytvořit pravidlo** vyberte existující **skupinu akcí** nebo vytvořte novou skupinu. Skupina akcí umožňuje definovat akci, která má být provedena při výskytu výstrahy.
  ![Definovat skupinu akcí](./media/alerts-insights-configure-portal/action-group.png)

9. Definujte název pravidla, zadejte volitelný popis, zvolte úroveň závažnosti pro pravidlo, zvolte, jestli se má pravidlo Povolit při vytváření pravidla, a pak klikněte na **vytvořit výstrahu pravidla** a vytvořte výstrahu pravidla metriky.

Během 10 minut je výstraha aktivní a triggery, jak je popsáno výše.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [konfiguraci webhooků v upozorněních](../../azure-monitor/platform/alerts-webhooks.md).