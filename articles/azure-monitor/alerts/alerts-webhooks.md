---
title: Volání Webhooku s klasickou výstrahou metriky v Azure Monitor
description: Naučte se, jak přesměrovat výstrahy metrik Azure do jiných systémů mimo Azure.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 7ab719560320882a3a6569bff5df7d2e2cf0b747
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037553"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Volání Webhooku s klasickou výstrahou metriky v Azure Monitor

> [!WARNING]
> Tento článek popisuje, jak používat starší klasické výstrahy metrik. Azure Monitor teď podporuje [novější výstrahy na metriky téměř v reálném čase a nové prostředí výstrah](./alerts-overview.md). Klasické výstrahy jsou [vyřazené](./monitoring-classic-retirement.md) pro uživatele veřejného cloudu, i když jsou omezeného využití do **31. května 2021**. Klasické výstrahy pro Azure Government Cloud a Azure Čína 21Vianet vybudou od **29. února 2024**.
>

Webhooky můžete použít ke směrování oznámení o výstrahách Azure do jiných systémů pro následné zpracování nebo vlastní akce. Webhook na výstraze můžete použít ke směrování do služeb, které odesílají zprávy SMS, k protokolování chyb, k oznamování týmu prostřednictvím chatu nebo služby zasílání zpráv nebo pro různé další akce. 

Tento článek popisuje, jak nastavit Webhook na upozornění metriky Azure. Zobrazuje také informace o tom, jak bude datová část příspěvku HTTP na Webhook vypadat. Informace o nastavení a schématu pro upozornění protokolu aktivit Azure (výstrahy na události) najdete v tématu [volání Webhooku v upozornění protokolu aktivit Azure](../alerts/alerts-log-webhook.md).

Výstrahy Azure používají HTTP POST k odeslání obsahu upozornění ve formátu JSON k identifikátoru URI Webhooku, který zadáte při vytváření výstrahy. Schéma je definováno dále v tomto článku. Identifikátor URI musí být platný koncový bod HTTP nebo HTTPS. Azure při aktivaci výstrahy účtuje jednu položku na žádost.

## <a name="configure-webhooks-via-the-azure-portal"></a>Konfigurace webhooků prostřednictvím Azure Portal
Pokud chcete přidat nebo aktualizovat identifikátor URI Webhooku, klikněte na [Azure Portal](https://portal.azure.com/)na stránce **vytvořit nebo aktualizovat výstrahy**.

![Přidat podokno pravidla výstrahy](./media/alerts-webhooks/Alertwebhook.png)

Můžete také nakonfigurovat upozornění pro odeslání na identifikátor URI Webhooku pomocí [rutin Azure PowerShell](../powershell-samples.md#create-metric-alerts), rozhraní příkazového [řádku pro více platforem](../cli-samples.md#work-with-alerts)nebo [rozhraní API REST Azure monitor](/rest/api/monitor/alertrules).

## <a name="authenticate-the-webhook"></a>Ověření Webhooku
Webhook se může ověřit pomocí ověřování na základě tokenu. Identifikátor URI Webhooku se uloží s ID tokenu. Příklad: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Schéma datové části
Operace POST obsahuje následující datovou část a schéma JSON pro všechny výstrahy založené na metrikách:

```JSON
{
    "status": "Activated",
    "context": {
        "timestamp": "2015-08-14T22:26:41.9975398Z",
        "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
        "name": "ruleName1",
        "description": "some description",
        "conditionType": "Metric",
        "condition": {
            "metricName": "Requests",
            "metricUnit": "Count",
            "metricValue": "10",
            "threshold": "10",
            "windowSize": "15",
            "timeAggregation": "Average",
            "operator": "GreaterThanOrEqual"
        },
        "subscriptionId": "s1",
        "resourceGroupName": "useast",
        "resourceName": "mysite1",
        "resourceType": "microsoft.foo/sites",
        "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
        "resourceRegion": "centralus",
        "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
    },
    "properties": {
        "key1": "value1",
        "key2": "value2"
    }
}
```


| Pole | Povinné | Opravená sada hodnot | Poznámky |
|:--- |:--- |:--- |:--- |
| status |Y |Aktivované, vyřešené |Stav výstrahy na základě podmínek, které jste nastavili. |
| kontext |Y | |Kontext výstrahy. |
| časové razítko |Y | |Čas, kdy byla výstraha aktivována. |
| id |Y | |Každé pravidlo výstrahy má jedinečné ID. |
| name |Y | |Název výstrahy |
| description |Y | |Popis výstrahy |
| conditionType |Y |Metrika, událost |Podporují se dva typy výstrah: metrika a událost. Výstrahy metriky jsou založené na podmínce metriky. Výstrahy událostí jsou založené na události v protokolu aktivit. Tuto hodnotu použijte, chcete-li ověřit, zda je výstraha založena na metrikě nebo události. |
| pomocné |Y | |Konkrétní pole, která se mají kontrolovat v závislosti na hodnotě **conditionType** |
| metricName |Pro výstrahy metriky | |Název metriky, která definuje, co pravidlo monitoruje. |
| metricUnit |Pro výstrahy metriky |Bajty, BytesPerSecond, Count, CountPerSecond, procenta, sekundy |Jednotka povolená v metrikě Viz [Povolené hodnoty](/previous-versions/azure/reference/dn802430(v=azure.100)). |
| metricValue |Pro výstrahy metriky | |Skutečná hodnota metriky, která způsobila výstrahu. |
| mezí |Pro výstrahy metriky | |Prahová hodnota, při které je výstraha aktivována. |
| windowSize |Pro výstrahy metriky | |Časové období, které se používá k monitorování aktivity výstrahy na základě prahové hodnoty. Hodnota musí být mezi 5 minutami a 1 dnem. Hodnota musí být ve formátu ISO 8601 Duration. |
| timeAggregation |Pro výstrahy metriky |Průměr, poslední, maximum, minimum, žádné, celkem |Způsob, jakým budou shromážděná data v průběhu času kombinována. Výchozí hodnota je Average. Viz [Povolené hodnoty](/previous-versions/azure/reference/dn802410(v=azure.100)). |
| operátor |Pro výstrahy metriky | |Operátor, který se používá k porovnání aktuálních dat metriky s prahovou hodnotou sady. |
| subscriptionId |Y | |ID předplatného Azure. |
| resourceGroupName |Y | |Název skupiny prostředků pro ovlivněný prostředek. |
| resourceName |Y | |Název prostředku ovlivněného prostředku. |
| resourceType |Y | |Typ prostředku ovlivněného prostředku. |
| resourceId |Y | |ID prostředku ovlivněného prostředku. |
| resourceRegion |Y | |Oblast nebo umístění ovlivněného prostředku. |
| portalLink |Y | |Přímý odkaz na stránku souhrnu prostředků na portálu. |
| properties |N |Volitelné |Sada párů klíč/hodnota, která obsahuje podrobnosti o události. Například, `Dictionary<String, String>`. Pole vlastností je volitelné. Ve vlastním uživatelském rozhraní nebo pracovním postupu založeném na aplikaci logiky mohou uživatelé zadat páry klíč/hodnota, které lze předat prostřednictvím datové části. Alternativní způsob, jak předat vlastní vlastnosti zpátky Webhooku, je prostřednictvím samotného identifikátoru URI Webhooku (jako parametr dotazů). |

> [!NOTE]
> Pole **vlastnosti** můžete nastavit jenom pomocí [Azure monitor rozhraní REST API](/rest/api/monitor/alertrules).
>
>

## <a name="next-steps"></a>Další kroky
* Další informace o výstrahách a webhookech Azure v videu [integrace výstrah Azure s PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Naučte se [spouštět skripty Azure Automation (runbooky) na výstrahách Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* Naučte se [používat aplikaci logiky k odeslání zprávy SMS přes Twilio z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Naučte se [používat aplikaci logiky k odeslání zprávy s časovou rezervou z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Naučte se [používat aplikaci logiky k odeslání zprávy do fronty Azure z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).