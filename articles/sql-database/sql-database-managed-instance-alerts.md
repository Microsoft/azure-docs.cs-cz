---
title: Nastavení výstrah a oznámení pro spravovanou instanci (portál Azure)
description: Na webu Azure Portal můžete vytvářet výstrahy spravované instance SQL, které můžou aktivovat oznámení nebo automatizaci, když jsou splněny zadané podmínky.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/02/2020
ms.openlocfilehash: a332627d149a36ba5d5beb2626023e58a221f0d6
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639195"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Vytváření výstrah pro azure sql spravovanou instanci pomocí portálu Azure

## <a name="overview"></a>Přehled

Tento článek ukazuje, jak nastavit výstrahy pro databáze v azure SQL spravované instance databáze pomocí portálu Azure. Upozornění vám mohou poslat e-mail nebo zavolat webový háček, když některé metriky (například velikost úložiště instance nebo využití procesoru) dosáhnou prahové hodnoty. Tento článek také obsahuje osvědčené postupy pro nastavení období výstrah.

Můžete obdržet upozornění na základě metrik monitorování nebo událostí na vašich službách Azure.

* **Hodnoty metriky** – výstraha se aktivuje, když hodnota zadané metriky překročí prahovou hodnotu, kterou přiřadíte v obou směrech. To znamená, že aktivuje jak při prvním splnění podmínky, tak poté, když tato podmínka již není splněna.
* **Události protokolu aktivit** – výstraha může aktivovat na *každé* události, nebo pouze v případě, že dojde k určitému počtu událostí.

Výstrahu můžete nakonfigurovat tak, aby při aktivaci spustila následující akce:

* Odeslání e-mailových oznámení správci služby a spolusprávcům
* Odešlete e-mail na další e-maily, které zadáte.
* Volání webhooku

Můžete konfigurovat a získat informace o pravidlech výstrah pomocí

* [portál Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [rozhraní příkazového řádku (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Rozhraní REST API služby Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Vytvoření pravidla výstrahy pro metriku pomocí portálu Azure

1. Na [portálu](https://portal.azure.com/)vyhledejte zdroj, který vás zajímá monitorování, a vyberte jej.
2. V části Monitorování vyberte **Výstrahy.** Text a ikona se mohou u různých zdrojů mírně lišit.  

   ![Monitorování](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Výběrem tlačítka **Nové pravidlo výstrahy** otevřete stránku **Vytvořit pravidlo.**
   ![Vytvořit pravidlo](media/sql-database-insights-alerts-portal/create-rule.png)

4. V části **Podmínka** klikněte na **Přidat**.
   ![Definovat podmínku](media/sql-database-insights-alerts-portal/create-rule.png)
5. Na stránce **Konfigurovat logiku signálu** vyberte signál.
   ![Vybrat signál](media/sql-database-insights-alerts-portal/select-signal.png)
6. Po výběru signálu, například **procenta procesoru**, se zobrazí stránka **Logika signálu Konfigurace.**
   ![Konfigurace logiky signálů](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. Na této stránce nakonfigurujte tento typ prahové hodnoty, operátor, typ agregace, prahovou hodnotu, rozlišovací schopnost agregace a četnost hodnocení. Potom klepněte na tlačítko **Hotovo**.
8. V **pravidle Vytvořit**vyberte existující **skupinu akcí** nebo vytvořte novou skupinu. Skupina akcí umožňuje definovat akci, která má být provedena, když dojde k výstražné podmínce.
  ![Definovat skupinu akcí](media/sql-database-insights-alerts-portal/action-group.png)

9. Definujte název pravidla, zadejte volitelný popis, zvolte úroveň závažnosti pravidla, zvolte, zda chcete povolit pravidlo při vytváření pravidla, a pak klikněte na **Vytvořit výstrahu pravidla** a vytvořte výstrahu pravidla metriky.

Během 10 minut je výstraha aktivní a aktivuje se, jak bylo popsáno dříve.

## <a name="next-steps"></a>Další kroky

* Další informace o [konfiguraci webových háků v výstrahách](../azure-monitor/platform/alerts-webhooks.md).
