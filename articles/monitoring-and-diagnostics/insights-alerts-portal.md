---
title: Vytvoření klasického upozornění pro služby Azure pomocí portálu Azure | Microsoft Docs
description: Aktivaci e-mailů nebo oznámení, nebo se telefonicky adresy URL webu (webhooky) nebo automatizace při splnění podmínek, které zadáte.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/23/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: d0e5512eb3f963898ded6d7155f8b75cfb6ef911
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287425"
---
# <a name="use-the-azure-portal-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Vytvoření klasického metriky výstrah v monitorování Azure pro služby Azure pomocí portálu Azure 

> [!div class="op_single_selector"]
> * [Azure Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Rozhraní příkazového řádku](insights-alerts-command-line-interface.md)
>

> [!NOTE]
> Tento článek popisuje postup vytvoření starší classic metriky výstrahy. Azure nyní podporuje monitorování [novější metriky výstrahy](monitoring-near-real-time-metric-alerts.md). 


Tento článek ukazuje, jak nastavení pomocí portálu Azure classic Azure metriky výstrahy. 

Obdržíte výstrahu na základě metriky pro služeb Azure, nebo můžete přijímat výstrahy pro události, které nastaly v Azure.

* **Metriky hodnoty**: výstraha aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, která přiřadíte v obou směrech. To znamená, aktivuje, i když nejprve splnění podmínky a pak když už se splnění podmínky.    

* **Události protokolu aktivit**: výstrahu můžete aktivovat pro *každých* události nebo když dojde k určitým událostem. Další informace o [aktivity protokolu výstrahy](monitoring-activity-log-alerts.md).

Můžete nakonfigurovat classic metriky výstrahu při aktivaci, proveďte následující:

* Správce služeb a spolusprávci odešlete e-mailová oznámení.
* Odeslat e-mail na další e-mailové adresy, které zadáte.
* Volejte webhook, jehož.
* Spusťte provádění runbook služby Azure (pouze z portálu Azure).

Můžete nakonfigurovat a získat informace o classic metriky pravidla výstrah v následujících umístěních: 

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI](insights-alerts-command-line-interface.md)
* [Rozhraní API REST Azure monitorování](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Vytvořit pravidlo výstrahy na metrika pomocí portálu Azure
1. V [portál](https://portal.azure.com/), vyhledejte prostředek, který chcete monitorovat a pak ho vyberte.

2. V **monitorování** vyberte **výstrahy (klasické)**. Text a ikona se mohou mírně lišit pro různé zdroje. Pokud nenajdete **výstrahy (klasické)** zde, je možné v **výstrahy** nebo **pravidla výstrah**.

    ![Monitorování](./media/insights-alerts-portal/AlertRulesButton.png)

3. Vyberte **přidat metriky upozornění (klasické)** příkaz a potom vyplňte příslušná pole.

    ![Přidat výstrahu](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Název** pravidla výstrah. Zvolte **popis**, která také se zobrazí v oznámení e-mailů.

5. Vyberte **metrika** , kterou chcete monitorovat. Zvolte **podmínku** a **prahová hodnota** hodnoty pro metriku. Taky **období** času, který metriky pravidlo je nutné splnit před výstrahy aktivačních událostí. Například pokud použijete období "za posledních 5 minut" a upozornění se hledá procesoru vyšší než 80 %, spustí výstrahu, když procesor byl konzistentně výše 80 % 5 minut. Po první aktivační události dojde, spustí znovu, když se procesoru zůstává nižší než 80 % 5 minut. Metriky měření využití procesoru se stane každou minutu.

6. Vyberte **e-mailu vlastníky...**  Pokud chcete, aby správci a spolusprávci příjmu e-mailová oznámení, když se aktivuje výstrahu.

7. Pokud chcete odesílat oznámení do další e-mailové adresy, když se aktivuje výstraha, přidejte je do **email(s) další správce** pole. Oddělte více e-mailů oddělte středníkem, v následujícím formátu:  *email@contoso.com; email2@contoso.com*

8. Umístit do platný identifikátor URI, **Webhooku** pole, pokud se má volat, když se aktivuje výstrahu.

9. Pokud používáte Azure Automation, můžete vybrat runbook a spustit, když se aktivuje výstrahu.

10. Vyberte **OK** vytvořit výstrahu.   

Během několika minut výstraha je aktivní a jak se popisuje výš se aktivuje.

## <a name="manage-your-alerts"></a>Spravovat oznámení
Po vytvoření výstrahy, můžete ho vyberte a proveďte jednu z následujících úloh:

* Zobrazí graf s prahovou hodnotu metriky a skutečnými hodnotami z předchozí den.
* Upravit nebo odstranit.
* **Zakázat** nebo **povolit** ji, pokud chcete dočasně zastavit nebo obnovit příjem oznámení pro výstrahy.

## <a name="next-steps"></a>Další postup
* [Získat přehled o Azure monitorování](monitoring-overview.md), včetně typy informací, můžete sledovat a shromažďovat.
* Další informace o [novější metriky výstrahy](monitoring-near-real-time-metric-alerts.md).
* Další informace o [konfigurace webhooky ve výstrahách](insights-webhooks-alerts.md).
* Další informace o [konfigurace výstrah pro aktivitu protokolu události](monitoring-activity-log-alerts.md).
* Další informace o [Azure Automation runbook](../automation/automation-starting-a-runbook.md).
* Získat [přehled diagnostické protokoly](monitoring-overview-of-diagnostic-logs.md)a shromažďovat podrobné metriky vysoká frekvence vaší služby.
* Získat [přehled metriky kolekce](insights-how-to-customize-monitoring.md) a ujistěte se, že vaše služba je k dispozici a reaguje.
