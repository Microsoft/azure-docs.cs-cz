---
title: Nastavení výstrah a oznámení (Azure Portal)
description: Pomocí Azure Portal můžete vytvářet SQL Database výstrahy, které můžou aktivovat oznámení nebo automatizaci při splnění zadaných podmínek.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 67c47b35e84a93d7d9032ad55b425ae2bb6971fe
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79209509"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-databases-using-azure-portal"></a>Vytváření upozornění pro Azure SQL Database a databáze Azure synapse Analytics pomocí Azure Portal

## <a name="overview"></a>Přehled

V tomto článku se dozvíte, jak nastavit výstrahy pro databáze s jedním, sdruženým a datovým skladem v Azure SQL Database a Azure synapse Analytics (dřív Azure SQL Data Warehouse) pomocí Azure Portal. Když některá metrika (například velikost databáze nebo využití procesoru) dosáhne prahové hodnoty, můžou vám výstrahy poslat e-mail nebo zavolat webový Hook. Tento článek také poskytuje osvědčené postupy pro nastavení dob upozornění.

> [!IMPORTANT]
> Tato funkce není zatím k dispozici ve spravované instanci. Jako alternativu můžete použít agenta SQL k posílání e-mailových upozornění pro některé metriky na základě [zobrazení dynamické správy](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Můžete obdržet upozornění na základě metrik monitorování pro události nebo služby Azure.

* **Hodnoty metrik** – výstraha se aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, kterou přiřadíte v obou směrech. To znamená, že se aktivuje při prvním splnění podmínky a následně v případě, že se už podmínka nesplní.
* **Události protokolu aktivit** – výstraha se může aktivovat *každou* událost, nebo jenom v případě, že dojde k určitému počtu událostí.

Můžete nakonfigurovat výstrahu, která při triggeru provede následující akce:

* Odesílání e-mailových oznámení správci služeb a spolusprávcům
* Odešlete e-mail na další e-maily, které zadáte.
* Volání webhooku

Můžete nakonfigurovat a získat informace o pravidlech upozornění pomocí

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [rozhraní příkazového řádku (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Vytvoření pravidla výstrahy na metrikě s Azure Portal

1. Na [portálu](https://portal.azure.com/)vyhledejte prostředek, který chcete monitorovat, a vyberte ho.
2. V části monitorování vyberte **výstrahy** . Text a ikona se mohou mírně lišit pro různé prostředky.  

   ![Monitorování](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Kliknutím na tlačítko **nové pravidlo výstrahy** otevřete stránku **vytvořit pravidlo** .
  ![vytvořit pravidlo](media/sql-database-insights-alerts-portal/create-rule.png)

4. V části **Podmínka** klikněte na **Přidat**.
  ![definování podmínky](media/sql-database-insights-alerts-portal/create-rule.png)
5. Na stránce **Konfigurovat logiku signálu** vyberte signál.
  ![vybrat](media/sql-database-insights-alerts-portal/select-signal.png)signálu.
6. Po výběru signálu, jako je **Procento procesoru**, se zobrazí stránka **Konfigurovat logiku signálu** .
  ![nakonfigurovat logiku signálu](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. Na této stránce nakonfigurujte tento typ prahové hodnoty, operátor, typ agregace, prahovou hodnotu, členitost agregace a frekvenci vyhodnocení. Potom klikněte na **Done** (Hotovo).
8. V poli **vytvořit pravidlo**vyberte existující **skupinu akcí** nebo vytvořte novou skupinu. Skupina akcí umožňuje definovat akci, která má být provedena při výskytu výstrahy.
  ![definovat skupinu akcí](media/sql-database-insights-alerts-portal/action-group.png)

9. Definujte název pravidla, zadejte volitelný popis, zvolte úroveň závažnosti pro pravidlo, zvolte, jestli se má pravidlo Povolit při vytváření pravidla, a pak klikněte na **vytvořit výstrahu pravidla** a vytvořte výstrahu pravidla metriky.

Během 10 minut je výstraha aktivní a triggery, jak je popsáno výše.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [konfiguraci webhooků v upozorněních](../azure-monitor/platform/alerts-webhooks.md).
