---
title: Konfigurace upozornění metriky pro službu Azure Database for PostgreSQL na webu Azure portal
description: Tento článek popisuje postup konfigurace a upozornění na metriku přístupu pro službu Azure Database for PostgreSQL na webu Azure Portal.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: b57827cb4e8c1105c88b6fab217e17d6cf4db056
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271216"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql"></a>Nastavte si upozornění na metriky pro službu Azure Database for PostgreSQL pomocí webu Azure portal 

V tomto článku se dozvíte, jak nastavit – Azure Database for postgresql – výstrah pomocí webu Azure portal. Obdržíte výstrahu na základě monitorování metrik pro služby Azure.

Upozornění aktivační události, když hodnota zadaného metrika překročí mezní hodnotu, kterou přiřadíte. Upozornění triggery obě, pokud je první podmínka splněna, a pak později při podmínka, která už probíhá není splněná. 

Můžete nakonfigurovat výstrahu při aktivaci provést následující akce:
* Odeslání e-mailová oznámení správce služeb a spolupracujících správců.
* odeslání e-mailu další e-maily, které zadáte.
* Volání webhooku.

Můžete nakonfigurovat a získat informace o použití pravidel upozornění:
* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Rozhraní příkazového řádku (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Rozhraní REST API služby Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Vytvoření pravidla upozornění na metriky na webu Azure Portal
1. V [webu Azure portal](https://portal.azure.com/), vyberte serveru Azure Database for PostgreSQL chcete monitorovat.

2. V části **monitorování** části bočního panelu, vyberte **pravidla upozornění** uvedeno:

   ![Vybrat pravidla upozornění](./media/howto-alert-on-metric/1-alert-rules.png)

3. Vyberte **přidat upozornění metriky** (ikony +). 

4. **Přidat pravidlo** otevře se stránka, jak je znázorněno níže.  Vyplňte požadované informace:

   ![Přidat upozornění metriky formulář](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Nastavení | Popis  |
   |---------|---------|
   | Název | Zadejte název pravidla upozornění. Tato hodnota posílá oznámení e-mailu. |
   | Popis | Zadejte krátký popis pravidla výstrahy. Tato hodnota posílá oznámení e-mailu. |
   | Výstraha při | Zvolte **metriky** pro tento typ výstrahy. |
   | Předplatné | Toto pole je předem vyplněná odběr, který je hostitelem vaší službě Azure Database for PostgreSQL. |
   | Skupina prostředků | Toto pole je předem s vybranou skupinou prostředků služby Azure Database for PostgreSQL. |
   | Prostředek | Toto pole je předem vyplněná název služby Azure Database for PostgreSQL. |
   | Metrika | Vyberte, kterou chcete vydat výstrahu pro metriku. Například **procento úložiště**. |
   | Podmínka | Vyberte podmínku pro metriku, která se má porovnat s. Například **větší než**. |
   | Prahová hodnota | Prahová hodnota pro metriky, například 85 (procenta). |
   | Období | Časový úsek, která pravidlo metriky musí být splněna před výstrah aktivační události. Například **za posledních 30 minut**. |

   Na základě příkladu, výstrahu hledá procento úložiště vyšší než 85 % po dobu 30 minut. Toto upozornění se aktivuje, když průměrné procento úložiště byla vyšší než 85 % po dobu 30 minut. Jakmile dojde k první aktivační událost, aktivuje akci při průměrnou procentuální hodnotu úložiště je nižší než 85 % více než 30 minut.

5. Zvolte jako metodu oznamování, kterou chcete použít pro pravidlo upozornění. 

   Zkontrolujte **e-mailu vlastníci, přispěvatelé a čtenáři** možnost, pokud chcete, aby správci předplatného a spolusprávci e-mailem odešle, když se aktivuje upozornění.

   Pokud chcete další e-maily přijímat oznámení, když se aktivuje upozornění, přidejte je **další email(s) správce** pole. Více e-mailů oddělujte středníkem -  *email@contoso.com;email2@contoso.com*

   Volitelně zadejte platný identifikátor URI, **Webhooku** pole, pokud chcete, volá se, když se aktivuje upozornění.

6. Vyberte **OK** k vytvoření upozornění.

   Během několika minut upozornění je aktivní a aktivuje jak bylo popsáno dříve.

## <a name="manage-your-alerts"></a>Správa výstrah
Po vytvoření výstrahy, můžete ho vyberte a proveďte následující akce:

* Zobrazte graf zobrazující mezní hodnota metriky a skutečnými hodnotami z předchozího dne odpovídající této výstrahy.
* **Upravit** nebo **odstranit** pravidlo upozornění.
* **Zakázat** nebo **povolit** výstrahu, pokud chcete dočasně zastavit nebo obnovit příjem oznámení.

## <a name="next-steps"></a>Další postup
* Další informace o [konfiguraci webhooků ve výstrahách](../azure-monitor/platform/alerts-webhooks.md).
* Získat [přehled shromažďování metrik](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) k Ujistěte se, že vaše služba není k dispozici a reagují.
