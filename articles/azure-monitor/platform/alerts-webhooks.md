---
title: Volání Webhooku s klasickou výstrahou metriky v Azure Monitor
description: Naučte se, jak přesměrovat výstrahy metrik Azure do jiných systémů mimo Azure.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: alerts
ms.openlocfilehash: 0677c7a0521fe1f63c9c2c9fce65d8dbd8e6d5c4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826906"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Volání Webhooku s klasickou výstrahou metriky v Azure Monitor

Webhooky můžete použít ke směrování oznámení o výstrahách Azure do jiných systémů pro následné zpracování nebo vlastní akce. Webhook na výstraze můžete použít ke směrování do služeb, které odesílají zprávy SMS, k protokolování chyb, k oznamování týmu prostřednictvím chatu nebo služby zasílání zpráv nebo pro různé další akce. 

Tento článek popisuje, jak nastavit Webhook na upozornění metriky Azure. Zobrazuje také informace o tom, jak bude datová část příspěvku HTTP na Webhook vypadat. Informace o nastavení a schématu pro upozornění protokolu aktivit Azure (výstrahy na události) najdete v tématu [volání Webhooku v upozornění protokolu aktivit Azure](alerts-log-webhook.md).

Výstrahy Azure používají HTTP POST k odeslání obsahu upozornění ve formátu JSON k identifikátoru URI Webhooku, který zadáte při vytváření výstrahy. Schéma je definováno dále v tomto článku. Identifikátor URI musí být platný koncový bod HTTP nebo HTTPS. Azure při aktivaci výstrahy účtuje jednu položku na žádost.

## <a name="configure-webhooks-via-the-azure-portal"></a>Konfigurace webhooků prostřednictvím Azure Portal
Pokud chcete přidat nebo aktualizovat identifikátor URI Webhooku, klikněte na [Azure Portal](https://portal.azure.com/)na stránce **vytvořit nebo aktualizovat výstrahy**.

![Přidat podokno pravidla výstrahy](./media/alerts-webhooks/Alertwebhook.png)

Můžete také nakonfigurovat upozornění pro odeslání na identifikátor URI Webhooku pomocí [rutin Azure PowerShell](../samples/powershell-samples.md#create-metric-alerts), rozhraní příkazového [řádku pro více platforem](../samples/cli-samples.md#work-with-alerts)nebo [rozhraní API REST Azure monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

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
| status |Ano |Aktivované, vyřešené |Stav výstrahy na základě podmínek, které jste nastavili. |
| kontext |Ano | |Kontext výstrahy. |
| časové razítko |Ano | |Čas, kdy byla výstraha aktivována. |
| id |Ano | |Každé pravidlo výstrahy má jedinečné ID. |
| name |Ano | |Název výstrahy |
| description |Ano | |Popis výstrahy |
| conditionType |Ano |Metrika, událost |Podporují se dva typy výstrah: metrika a událost. Výstrahy metriky jsou založené na podmínce metriky. Výstrahy událostí jsou založené na události v protokolu aktivit. Tuto hodnotu použijte, chcete-li ověřit, zda je výstraha založena na metrikě nebo události. |
| pomocné |Ano | |Konkrétní pole, která se mají kontrolovat v závislosti na hodnotě **conditionType** |
| metricName |Pro výstrahy metriky | |Název metriky, která definuje, co pravidlo monitoruje. |
| metricUnit |Pro výstrahy metriky |Bajty, BytesPerSecond, Count, CountPerSecond, procenta, sekundy |Jednotka povolená v metrikě Viz [Povolené hodnoty](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |Pro výstrahy metriky | |Skutečná hodnota metriky, která způsobila výstrahu. |
| threshold |Pro výstrahy metriky | |Prahová hodnota, při které je výstraha aktivována. |
| windowSize |Pro výstrahy metriky | |Časové období, které se používá k monitorování aktivity výstrahy na základě prahové hodnoty. Hodnota musí být mezi 5 minutami a 1 dnem. Hodnota musí být ve formátu ISO 8601 Duration. |
| timeAggregation |Pro výstrahy metriky |Průměr, poslední, maximum, minimum, žádné, celkem |Způsob, jakým budou shromážděná data v průběhu času kombinována. Výchozí hodnota je Average. Viz [Povolené hodnoty](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
|  – operátor |Pro výstrahy metriky | |Operátor, který se používá k porovnání aktuálních dat metriky s prahovou hodnotou sady. |
| subscriptionId |Ano | |ID předplatného Azure. |
| resourceGroupName |Ano | |Název skupiny prostředků pro ovlivněný prostředek. |
| resourceName |Ano | |Název prostředku ovlivněného prostředku. |
| resourceType |Ano | |Typ prostředku ovlivněného prostředku. |
| resourceId |Ano | |ID prostředku ovlivněného prostředku. |
| resourceRegion |Ano | |Oblast nebo umístění ovlivněného prostředku. |
| portalLink |Ano | |Přímý odkaz na stránku souhrnu prostředků na portálu. |
| properties |Ne |Volitelné |Sada párů klíč/hodnota, která obsahuje podrobnosti o události. Například, `Dictionary<String, String>`. Pole vlastností je volitelné. Ve vlastním uživatelském rozhraní nebo pracovním postupu založeném na aplikaci logiky mohou uživatelé zadat páry klíč/hodnota, které lze předat prostřednictvím datové části. Alternativní způsob, jak předat vlastní vlastnosti zpátky Webhooku, je prostřednictvím samotného identifikátoru URI Webhooku (jako parametr dotazů). |

> [!NOTE]
> Pole **vlastnosti** můžete nastavit jenom pomocí [Azure monitor rozhraní REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Další kroky
* Další informace o výstrahách a webhookech Azure v videu [integrace výstrah Azure s PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Naučte se [spouštět skripty Azure Automation (runbooky) na výstrahách Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* Naučte se [používat aplikaci logiky k odeslání zprávy SMS přes Twilio z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Naučte se [používat aplikaci logiky k odeslání zprávy s časovou rezervou z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Naučte se [používat aplikaci logiky k odeslání zprávy do fronty Azure z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

