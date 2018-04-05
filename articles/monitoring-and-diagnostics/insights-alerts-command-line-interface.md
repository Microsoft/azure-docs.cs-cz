---
title: Vytvořte výstrahy pro služby Azure - napříč platformami rozhraní příkazového řádku | Microsoft Docs
description: Aktivační událost e-mailů, oznámení, weby adresy URL (webhooky), nebo volat automatizace při splnění zadané podmínky.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 5c6a2d27-7dcc-4f89-8752-9bb31b05ff35
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: robb
ms.openlocfilehash: 9ea7da35acefc139625e71904c8aa1b01b87e4df
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>Vytvoření classic metriky výstrah v monitorování Azure pro služby Azure - napříč platformami rozhraní příkazového řádku
> [!div class="op_single_selector"]
> * [Azure Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Rozhraní příkazového řádku](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Přehled
> [!NOTE]
> Tento článek popisuje postup vytvoření starší classic metriky výstrahy. Azure nyní podporuje monitorování [novější, lepší metriky výstrahy](monitoring-near-real-time-metric-alerts.md). Tyto výstrahy můžete sledovat více metriky a povolit pro výstrahy na dimenzí metriky. Podpora rozhraní příkazového řádku pro novější metriky výstrahy tu bude brzo dostupná.
>
>

Tento článek ukazuje, jak nastavit Azure classic metriky výstrah pomocí napříč platformami rozhraní příkazového řádku (CLI).

> [!NOTE]
> Azure monitorování je nový název pro co byla volána "Statistika Azure" až 25 září 2016. Však obory názvů a proto níže uvedených příkazů stále obsahovat "insights".
>
>

Můžete zobrazit upozornění na základě monitorování metriky pro nebo událostí na služeb Azure.

* **Metriky hodnoty** -výstrahy aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu přiřadíte v obou směrech. To znamená, aktivuje obě při nejprve je splněna podmínka, a pak později, pokud podmínka je už plněny.    
* **Aktivity protokolu události** -výstrahu můžete aktivovat pro *každých* události nebo pouze tehdy, když dojde k určité události. Další informace o výstrahách aktivity protokolu [, klikněte sem](monitoring-activity-log-alerts.md)

Můžete nakonfigurovat classic metriky výstrahu při aktivaci, proveďte následující:

* odesílat oznámení e-mailu Správce služeb a spolusprávci
* Odeslat e-mail na další e-mailů, které zadáte.
* Volat webhook, jehož
* Spusťte provádění runbook služby Azure (pouze z portálu Azure v tuto chvíli)

Můžete nakonfigurovat a získat informace o classic metriky pravidla výstrah pomocí

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Rozhraní příkazového řádku (CLI)](insights-alerts-command-line-interface.md)
* [Rozhraní API REST Azure monitorování](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Vždy dostanete nápovědy pro příkazy zadáním příkazu a - pomoci na konci. Příklad:

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Vytvořit pravidla výstrah pomocí rozhraní příkazového řádku
1. Proveďte požadované součásti a přihlášení k Azure. V tématu [ukázky rozhraní příkazového řádku Azure monitorování](insights-cli-samples.md). Stručně řečeno nainstalujte rozhraní příkazového řádku a spuštění těchto příkazů. Budou si přihlášení, zobrazit, jaké předplatné a připravit se ke spuštění příkazů Azure monitorování.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. K zobrazení seznamu existujících pravidel ve skupině prostředků, použijte následující formulář **statistiky azure výstrahy pravidlo seznamu** *[možnosti] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. K vytvoření pravidla, musíte nejprve mít několik důležitých informací.
  * **ID prostředku** pro prostředek, kterou chcete nastavit upozornění pro
  * **Definice metrik** k dispozici pro tento prostředek

     Jeden způsob, jak získat ID prostředku je použití portálu Azure. Za předpokladu, že byl již vytvořený, vyberte ho v portálu. V okně Další vyberte *vlastnosti* pod *nastavení* části. *ID prostředku* je pole v okně Další. Dalším způsobem je použít [Průzkumníka prostředků Azure](https://resources.azure.com/).

     Je třeba id prostředků pro webovou aplikaci

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Pokud chcete získat seznam dostupné metriky a jednotky pro tyto metriky pro předchozí příklad prostředků, použijte následující příkaz rozhraní příkazového řádku:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* je stejná i členitost dostupné měření (1-minutách). Pomocí různých členitostí nabízí různé možnosti metriky.
4. Pokud chcete vytvořit pravidlo výstrahy založené na metrika, použijte příkaz v následujícím formátu:

    **metriky sadu pravidel výstrahy statistiky Azure** *[možnosti] &lt;ruleName&gt; &lt;umístění&gt; &lt;resourceGroup&gt; &lt;velikost_okna&gt; &lt;operátor&gt; &lt;prahová hodnota&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    Následující příklad nastaví výstrahu na webový server prostředku. Výstrahy aktivačních událostí, kdykoliv konzistentně obdrží veškerou komunikaci pro 5 minut a opakujte při přijetí žádný provoz 5 minut.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. K vytvoření webhooku nebo odeslat e-mail, když se aktivuje upozornění na classic metriky, je nutné nejprve vytvořte e-mailu nebo webhooky. Pravidlo okamžitě vytvořit později. Nelze přidružit webhooku nebo e-mailů s již vytvořili pravidla pomocí rozhraní příkazového řádku.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Můžete ověřit, zda byly upozornění správně vytvořeny prohlížením jednotlivých pravidel.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Pokud chcete odstranit pravidla, použijte příkaz ve tvaru:

    **Odstranit pravidlo výstrahy statistiky** [možnosti] &lt;resourceGroup&gt; &lt;ruleName&gt;

    Tyto příkazy odstranit pravidla dříve vytvořená v tomto článku.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Další postup
* [Získat přehled o Azure monitorování](monitoring-overview.md) včetně typy informací, můžete sledovat a shromažďovat.
* Další informace o [konfigurace webhooky ve výstrahách](insights-webhooks-alerts.md).
* Další informace o [konfigurace výstrah pro aktivitu protokolu události](monitoring-activity-log-alerts.md).
* Další informace o [sad Azure Automation Runbook](../automation/automation-starting-a-runbook.md).
* Získat [přehled shromažďování diagnostických protokolů](monitoring-overview-of-diagnostic-logs.md) ke shromažďování metrik podrobné vysoká frekvence vaší služby.
* Získat [přehled metriky kolekce](insights-how-to-customize-monitoring.md) zkontrolovat služby je k dispozici a dobře reagovaly.
