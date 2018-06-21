---
title: Pomocí rozhraní příkazového řádku a platformy Azure můžete vytvořit classic výstrahy pro služby Azure | Microsoft Docs
description: Aktivaci e-mailů nebo oznámení, nebo se telefonicky weby adresy URL (webhooky) nebo automatizace při splnění podmínek, které zadáte.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 8112b868bc8d2ca2a9478d38ee702d8b3350d48e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287094"
---
# <a name="use-the-cross-platform-azure-cli-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Pomocí rozhraní příkazového řádku a platformy Azure můžete vytvořit classic metriky výstrah v monitorování Azure pro služby Azure 

> [!div class="op_single_selector"]
> * [Azure Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Rozhraní příkazového řádku](insights-alerts-command-line-interface.md)
>
>
> [!NOTE]
> Tento článek popisuje postup vytvoření starší classic metriky výstrahy. Azure nyní podporuje monitorování [novější, lepší metriky výstrahy](monitoring-near-real-time-metric-alerts.md). Tyto výstrahy můžete sledovat více metriky a povolit pro výstrahy na dimenzí metriky. Podporu Azure CLI pro novější metriky výstrahy tu bude brzo dostupná.
>
>

Tento článek ukazuje, jak nastavit Azure classic metriky výstrah pomocí rozhraní příkazového řádku a platformy (Azure CLI).

> [!NOTE]
> Azure monitorování je nový název pro co byla volána "Statistika Azure" až 25 září 2016. Však obory názvů a proto příkazy, které jsou zde popsané dál zahrnovat slovo "přehledy."

Můžete zobrazit upozornění na základě metriky pro služeb Azure nebo na základě událostí, ke kterým došlo v Azure.

* **Metriky hodnoty**: výstraha aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, která přiřadíte v obou směrech. To znamená, aktivuje, i když nejprve splnění podmínky a pak když už se splnění podmínky.    

* **Události protokolu aktivit**: výstrahu můžete aktivovat pro *každých* události nebo když dojde k určitým událostem. Další informace o protokoly aktivity, najdete v části [vytvořit aktivitu protokolu výstrahy (klasické)](monitoring-activity-log-alerts.md). 

Můžete nakonfigurovat classic metriky výstrahu při aktivaci, proveďte následující:

* Správce služeb a spolusprávci odešlete e-mailová oznámení. 
* Odesílání e-mailu e-mailové adresy, které zadáte.
* Volejte webhook, jehož.
* Spusťte provádění runbook služby Azure (pouze z portálu Azure v tuto chvíli).

Můžete nakonfigurovat a získat informace o classic metriky pravidla výstrah pomocí následující: 

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI](insights-alerts-command-line-interface.md)
* [Rozhraní API REST Azure monitorování](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Také můžete získat nápovědu pro příkazy zadáním příkazu s **-pomoci** na konci. Tady je příklad: 

```console
 azure insights alerts -help
 azure insights alerts actions email create -help
 ```

## <a name="create-alert-rules-by-using-azure-cli"></a>Vytvořit pravidla výstrah pomocí rozhraní příkazového řádku Azure
1. Po instalaci požadavky, přihlaste se k Azure. V tématu [ukázky rozhraní příkazového řádku Azure monitorování](insights-cli-samples.md) pro příkazy, které potřebujete, abyste mohli začít. Tyto příkazy vám pomůže získat přihlášení, dozvíte, jaké předplatné, které používáte a připravit se ke spuštění příkazů Azure monitorování.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Seznam existujících pravidel ve skupině prostředků, použijte následující formát: 

   **pravidlo výstrahy seznamu statistiky Azure** *[možnosti] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. K vytvoření pravidla, musíte nejprve mít několik důležitých informací.
    * **ID prostředku** pro kterou chcete nastavit upozornění pro prostředek.
    * **Definice metrik** které jsou k dispozici pro tento prostředek.

     Jeden způsob, jak získat ID prostředku je použití portálu Azure. Za předpokladu, že je prostředek už vytvořili, vyberte ho v portálu. Potom v okně Další v **nastavení** vyberte **vlastnosti**. **ID prostředku** je pole v okně Další. 
     
     ID prostředku můžete získat také pomocí [Průzkumníka prostředků Azure](https://resources.azure.com/).

     Tady je příklad ID prostředků pro webovou aplikaci:

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Pokud chcete získat seznam dostupné metriky a jednotky pro metriku v předchozím příkladu prostředků, použijte následující příkaz rozhraní příkazového řádku Azure:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* je stejná i členitost dostupné měření (v 1 minutu intervalech). Pokud použijete jiný členitostí máte různé možnosti metriky.
     
4. Pokud chcete vytvořit pravidlo výstrahy založené na metrika, použijte příkaz v následujícím formátu:

    **metriky sadu pravidel výstrahy statistiky Azure** *[možnosti] &lt;ruleName&gt; &lt;umístění&gt; &lt;resourceGroup&gt; &lt;velikost_okna&gt; &lt;operátor&gt; &lt;prahová hodnota&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    Následující příklad nastaví výstrahy na prostředku webu. Výstrahy aktivačních událostí, kdykoliv konzistentně obdrží veškerou komunikaci pro 5 minut a opakujte při přijetí žádný provoz 5 minut.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. Vytvořit webhook, jehož nebo poslat e-mailem při aktivaci classic metriky výstrahy, je nutné nejprve vytvořte e-mailu nebo webhooku. Pravidlo okamžitě vytvořit později. Nelze přidružit webhooky nebo e-mailů s pravidla, které již byly vytvořeny.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Můžete ověřit, zda byly upozornění správně vytvořeny prohlížením jednotlivých pravidel.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. K odstranění pravidla, použijte příkaz v následujícím formátu:

    **Odstranit pravidlo výstrahy statistiky** [možnosti] &lt;resourceGroup&gt; &lt;ruleName&gt;

    Tyto příkazy odstranit pravidla, které byly dřív vytvořené v tomto článku.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Další postup
* [Získat přehled o Azure monitorování](monitoring-overview.md), včetně typy informací, můžete sledovat a shromažďovat.
* Další informace o [konfigurace webhooky ve výstrahách](insights-webhooks-alerts.md).
* Další informace o [konfigurace výstrah pro aktivitu protokolu události](monitoring-activity-log-alerts.md).
* Další informace o [Azure Automation runbook](../automation/automation-starting-a-runbook.md).
* Získat [přehled shromažďování diagnostických protokolů](monitoring-overview-of-diagnostic-logs.md) ke shromažďování metrik podrobné vysoká frekvence pro vaši službu.
* Získat [přehled metriky kolekce](insights-how-to-customize-monitoring.md) zkontrolovat služby je k dispozici a dobře reagovaly.
