---
title: Vytvoření klasického upozornění pro služby Azure – portál Azure
description: Aktivační událost e-mailů, oznámení, weby adresy URL (webhooky), nebo volat automatizace při splnění zadané podmínky.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/23/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: d2ae4d9e1d0ef89fad4098a926fc689b84d1111b
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262177"
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---azure-portal"></a>Vytvoření klasického metriky výstrahy v Azure monitorování pro služby Azure - portálu Azure
> [!div class="op_single_selector"]
> * [Azure Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Rozhraní příkazového řádku](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Přehled

> [!NOTE]
> Tento článek popisuje postup vytvoření starší classic metriky výstrahy. Azure nyní podporuje monitorování [novější metriky výstrahy](monitoring-near-real-time-metric-alerts.md). 
>
>

Tento článek ukazuje, jak nastavit classic Azure metriky výstrah pomocí portálu Azure. 

Můžete zobrazit upozornění na základě monitorování metriky pro nebo událostí na služeb Azure.

* **Metriky hodnoty** -výstrahy aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu přiřadíte v obou směrech. To znamená, aktivuje obě při nejprve je splněna podmínka, a pak později, pokud podmínka je už plněny.    
* **Aktivity protokolu události** -výstrahu můžete aktivovat pro *každých* události nebo pouze tehdy, když dojde k určité události. Další informace o [aktivity protokolu výstrahy](monitoring-activity-log-alerts.md).

Můžete nakonfigurovat classic metriky výstrahu při aktivaci, proveďte následující:

* odesílat oznámení e-mailu Správce služeb a spolusprávci
* Odeslat e-mail na další e-mailů, které zadáte.
* Volat webhook, jehož
* Spusťte provádění runbook služby Azure (pouze z portálu Azure)

Můžete nakonfigurovat a získat informace o classic metriky pravidla výstrah pomocí

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Rozhraní příkazového řádku (CLI)](insights-alerts-command-line-interface.md)
* [Rozhraní API REST Azure monitorování](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Vytvořit pravidlo výstrahy na metrika pomocí portálu Azure
1. V [portál](https://portal.azure.com/), vyhledejte prostředků, které vás zajímají monitorování a vyberte ho.

2. Vyberte **výstrahy (klasické)** části monitorování. Text a ikona se mohou mírně lišit pro různé prostředky. Pokud nenajdete **výstrahy (klasické)**, můžete zjistit, je v části **výstrahy** nebo **pravidla výstrah**

    ![Monitorování](./media/insights-alerts-portal/AlertRulesButton.png)

3. Vyberte **přidat metriky upozornění (klasické)** příkazů a vyplňte příslušná pole.

    ![Přidat výstrahu](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Název** upozornění pravidlo a vyberte **popis**, který také zobrazuje v oznámení e-mailů.

5. Vyberte **metrika** chcete monitorovat, a potom vyberte **podmínku** a **prahová hodnota** hodnoty pro metriku. Taky **období** času, který metriky pravidlo je nutné splnit před výstrahy aktivačních událostí. Tak například pokud používáte období "za posledních 5 minut" a upozornění hledá procesoru vyšší než 80 %, výstrahy se aktivuje při procesoru bylo konzistentně výše 80 % 5 minut. Jakmile dojde k první aktivační událost, se znovu spustí, když procesoru zůstává nižší než 80 % 5 minut. Metriky měření využití procesoru nastane každou minutu.

6. Zkontrolujte **e-mailu vlastníky...**  Pokud chcete, aby správci a spolusprávci se má při aktivuje výstrahu.

7. Pokud chcete další e-mailů příjmu oznámení, když se aktivuje výstraha, přidejte je do **email(s) další správce** pole. Oddělte více e-mailů středníky -  *email@contoso.com;email2@contoso.com*

8. Umístit do platný identifikátor URI, **Webhooku** pole, pokud chcete, aby je volána, když se aktivuje výstrahu.

9. Pokud používáte Azure Automation, můžete vybrat Runbook a spustit, když se aktivuje výstrahu.

10. Vyberte **OK** po dokončení vytvoření výstrahy.   

Během několika minut výstraha je aktivní a jak se popisuje výš se aktivuje.

## <a name="managing-your-alerts"></a>Správa upozornění
Po vytvoření výstrahy, můžete ji vybrat a:

* Zobrazte graf zobrazující prahovou hodnotu metriky a skutečnými hodnotami z předchozí den.
* Upravit nebo odstranit.
* **Zakázat** nebo **povolit** ji, pokud chcete dočasně zastavit nebo obnovit příjem oznámení pro výstrahy.

## <a name="next-steps"></a>Další postup
* [Získat přehled o Azure monitorování](monitoring-overview.md) včetně typy informací, můžete sledovat a shromažďovat.
* Další informace o [novější metriky výstrahy](monitoring-near-real-time-metric-alerts.md)
* Další informace o [konfigurace webhooky ve výstrahách](insights-webhooks-alerts.md).
* Další informace o [konfigurace výstrah pro aktivitu protokolu události](monitoring-activity-log-alerts.md).
* Další informace o [sad Azure Automation Runbook](../automation/automation-starting-a-runbook.md).
* Získat [přehled diagnostické protokoly](monitoring-overview-of-diagnostic-logs.md) a shromažďovat podrobné metriky vysoká frekvence vaší služby.
* Získat [přehled metriky kolekce](insights-how-to-customize-monitoring.md) zkontrolovat služby je k dispozici a dobře reagovaly.
