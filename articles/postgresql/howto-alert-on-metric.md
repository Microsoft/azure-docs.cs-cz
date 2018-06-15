---
title: Konfigurace výstrah metriky pro databázi Azure pro PostgreSQL na portálu Azure
description: Tento článek popisuje postup konfigurace a výstrahy metriky přístup pro databázi Azure pro PostgreSQL z portálu Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: b4b15998276dd6c32e9c15622aa0251c6c066085
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
ms.locfileid: "29690249"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql"></a>Nastavit výstrahy na metriky pro databázi Azure pro PostgreSQL pomocí portálu Azure 

Tento článek ukazuje, jak nastavit Azure databáze PostgreSQL výstrah pomocí portálu Azure. Obdržíte výstrahu založenou na monitorování metriky pro služeb Azure.

Výstrahy aktivačních událostí, když hodnota zadané metriky překračuje prahovou hodnotu, kterou přiřadíte. Splněna výstrahy aktivačních událostí obě, pokud je podmínka vyhodnocena jako první, a pak později, pokud podmínky už probíhá není splněná. 

Můžete nakonfigurovat výstrahu při aktivaci provést následující akce:
* Správce služeb a spolusprávci odešlete e-mailová oznámení.
* Odeslat e-mail na další e-mailů, které zadáte.
* Volejte webhook, jehož.

Můžete nakonfigurovat a získat informace o pravidla výstrah pomocí:
* [portál Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [Rozhraní příkazového řádku (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [Rozhraní API REST Azure monitorování](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Vytvořit pravidlo výstrahy na metriky na portálu Azure
1. V [portál Azure](https://portal.azure.com/), vyberte databázi Azure, pro kterou chcete sledovat server PostgreSQL.

2. V části **monitorování** části na bočním panelu, vyberte **výstrah pravidla** znázorněné:

   ![Vybrat pravidla výstrah](./media/howto-alert-on-metric/1-alert-rules.png)

3. Vyberte **přidat metriky upozornění** (+ ikonu). 

4. **Přidat pravidlo** otevře se stránka jak je uvedeno níže.  Zadejte požadované informace:

   ![Přidat metriky výstrahy formuláře](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Nastavení | Popis  |
   |---------|---------|
   | Název | Zadejte název pro pravidlo výstrahy. Tato hodnota se odesílají e-mailem oznámení výstrah. |
   | Popis | Zadejte krátký popis pravidla výstrahy. Tato hodnota se odesílají e-mailem oznámení výstrah. |
   | Výstraha při | Zvolte **metriky** pro tento typ výstrahy. |
   | Předplatné | Toto pole je naplněna odběr, který je hostitelem databáze Azure pro PostgreSQL. |
   | Skupina prostředků | Toto pole je naplněna skupiny prostředků Azure Database pro PostgreSQL. |
   | Prostředek | Toto pole je naplněna název vaší databáze Azure pro PostgreSQL. |
   | Metrika | Vyberte metriku, kterou chcete vydat výstrahu pro. Například **procento úložiště**. |
   | Podmínka | Vyberte podmínku pro metriku, který se má porovnat s. Například **větší než**. |
   | Mezní hodnota | Prahová hodnota pro metriku, například 85 (v procentech). |
   | Období | Časový interval, který metriky pravidlo je nutné splnit před výstrahy aktivačních událostí. Například **za posledních 30 minut**. |

   Založena na příkladu, výstraha vyhledává procento úložiště než 85 % za období 30 minut. Výstrahy se aktivuje při průměrnou procentuální hodnotu úložiště jsou vyšší než 85 % po dobu 30 minut. Jakmile dojde k první aktivační událost, aktivuje znovu při průměrnou procentuální hodnotu úložiště je nižší než 85 % více než 30 minut.

5. Vyberte způsob oznámení, které chcete použít pro pravidlo výstrahy. 

   Zkontrolujte **e-mailu vlastníci, přispěvatelé a čtenáři** možnost, pokud chcete, aby správci předplatného a spolusprávci, který se má při aktivuje výstrahu.

   Pokud chcete další e-mailů příjmu oznámení, když se aktivuje výstraha, přidejte je do **email(s) další správce** pole. Oddělte více e-mailů středníky -  *email@contoso.com;email2@contoso.com*

   Volitelně zadejte platný identifikátor URI, **Webhooku** pole, pokud chcete, aby je volána, když se aktivuje výstrahu.

6. Vyberte **OK** vytvořit výstrahu.

   Během několika minut výstraha je aktivní a jak se popisuje výš se aktivuje.

## <a name="manage-your-alerts"></a>Spravovat oznámení
Po vytvoření výstrahy, můžete jej vybrat a proveďte následující akce:

* Zobrazte graf zobrazující prahovou hodnotu metriky a skutečnými hodnotami z předchozí den relevantní pro tuto výstrahu.
* **Upravit** nebo **odstranit** pravidlo výstrahy.
* **Zakázat** nebo **povolit** výstrahu, pokud chcete dočasně zastavit nebo obnovit příjem oznámení.

## <a name="next-steps"></a>Další postup
* Další informace o [konfigurace webhooky ve výstrahách](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Získat [přehled metriky kolekce](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) zkontrolovat služby je k dispozici a dobře reagovaly.
