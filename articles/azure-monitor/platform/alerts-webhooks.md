---
title: Volání webhooku s klasickou metrikou ve službě Azure Monitor
description: Zjistěte, jak přesměrovat upozornění na metriky Azure do jiných systémů, které nejsou azure.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: alerts
ms.openlocfilehash: 27510871f9a022cb27c6b03b812ce1d37b47312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248980"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Volání webhooku s klasickou metrikou ve službě Azure Monitor

Pomocí webhooků můžete směrovat upozornění Azure do jiných systémů pro následné zpracování nebo vlastní akce. Můžete použít webhook na upozornění směrovat do služeb, které posílají SMS zprávy, protokolovat chyby, upozornit tým prostřednictvím chatu nebo zasílání zpráv služby, nebo pro různé jiné akce. 

Tento článek popisuje, jak nastavit webhooku na upozornění metriky Azure. Také ukazuje, jak vypadá datová část pro HTTP POST na webhooku. Informace o nastavení a schématu pro výstrahu protokolu aktivit Azure (výstraha na události) najdete v [tématu Volání webhooku na upozornění protokolu aktivit Azure](alerts-log-webhook.md).

Výstrahy Azure používají HTTP POST k odeslání obsahu výstrah ve formátu JSON do identifikátoru URI webhooku, který zadáte při vytváření výstrahy. Schéma je definováno dále v tomto článku. Identifikátor URI musí být platný koncový bod PROTOKOLU HTTP nebo HTTPS. Azure zaúčtuje jednu položku na žádost, když je aktivována výstraha.

## <a name="configure-webhooks-via-the-azure-portal"></a>Konfigurace webových háků přes portál Azure
Pokud chcete přidat nebo aktualizovat identifikátor URI webhooku, přejděte na [portál Azure](https://portal.azure.com/)na **stránku Vytvoření nebo aktualizace výstrah**.

![Přidání podokna pravidel výstrahy](./media/alerts-webhooks/Alertwebhook.png)

Výstrahu můžete také nakonfigurovat pro odeslání do identifikátoru URI pomocí [rutin Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts), rozhraní API pro různé [platformy](../../azure-monitor/platform/cli-samples.md#work-with-alerts)nebo [rozhraní REST monitoru Azure](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Ověření webového háku
Webhook lze ověřit pomocí autorizace založené na tokenech. Identifikátor URI webhooku je uložen s ID tokenu. Příklad: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Schéma datové části
Operace POST obsahuje následující datové části JSON a schéma pro všechny výstrahy založené na metrikách:

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


| Pole | Povinné | Pevná sada hodnot | Poznámky |
|:--- |:--- |:--- |:--- |
| status |Ano |Aktivováno, vyřešeno |Stav výstrahy na základě nastavených podmínek. |
| kontext |Ano | |Kontext výstrahy. |
| časové razítko |Ano | |Čas, kdy byla výstraha spuštěna. |
| id |Ano | |Každé pravidlo výstrahy má jedinečné ID. |
| jméno |Ano | |Název výstrahy. |
| description |Ano | |Popis výstrahy. |
| conditionType |Ano |Metrika, Událost |Podporovány jsou dva typy výstrah: metrika a událost. Upozornění metriky jsou založeny na metrická podmínka. Upozornění na události jsou založena na události v protokolu aktivit. Pomocí této hodnoty můžete zkontrolovat, zda je výstraha založena na metrice nebo události. |
| Podmínka |Ano | |Konkrétní pole, která chcete zkontrolovat na základě hodnoty **conditionType.** |
| metricName |Pro upozornění na metriky | |Název metriky, která definuje, co pravidlo monitoruje. |
| metricUnit |Pro upozornění na metriky |Bajty, BajtyPerSecond, Počet, PočetPerSekunda, Procento, Sekundy |Jednotka povolena v metrice. Viz [povolené hodnoty](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |Pro upozornění na metriky | |Skutečná hodnota metriky, která způsobila výstrahu. |
| threshold |Pro upozornění na metriky | |Prahová hodnota, při které je výstraha aktivována. |
| windowSize |Pro upozornění na metriky | |Doba, která se používá ke sledování aktivity výstrahy na základě prahové hodnoty. Hodnota musí být mezi 5 minut a 1 den. Hodnota musí být ve formátu trvání ISO 8601. |
| timeAggregation |Pro upozornění na metriky |Průměr, poslední, Maximální, Minimální, Žádný, Součet |Jak by měla být shromážděná data kombinována v průběhu času. Výchozí hodnota je Průměr. Viz [povolené hodnoty](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
|  – operátor |Pro upozornění na metriky | |Operátor, který se používá k porovnání aktuální data metriky na nastavenou prahovou hodnotu. |
| subscriptionId |Ano | |ID předplatného Azure. |
| resourceGroupName |Ano | |Název skupiny prostředků pro ohrožený prostředek. |
| resourceName |Ano | |Název prostředku ohroženého prostředku. |
| resourceType |Ano | |Typ prostředku ovlivněného prostředku. |
| resourceId |Ano | |ID prostředku ohroženého prostředku. |
| resourceRegion |Ano | |Oblast nebo umístění ohroženého prostředku. |
| portalLink |Ano | |Přímý odkaz na stránku souhrnu prostředků portálu. |
| properties |Ne |Nepovinné |Sada párů klíč/hodnota, která má podrobnosti o události. Například, `Dictionary<String, String>`. Pole vlastností je volitelné. Ve vlastním uživatelském postupu nebo pracovním postupu založeném na aplikaci logiky mohou uživatelé zadat dvojice klíč/hodnota, které lze předat prostřednictvím datové části. Alternativní způsob, jak předat vlastní vlastnosti zpět do webhooku je přes webhooku URI sám (jako parametry dotazu). |

> [!NOTE]
> Pole **vlastností** můžete nastavit jenom pomocí [rozhraní API AZURE Monitor REST](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Další kroky
* Další informace o výstrahách Azure a webhookech najdete ve videu [Integrace výstrah Azure s PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Zjistěte, jak [spouštět skripty Azure Automation (runbooky) ve výstrahách Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* Naučte se, jak [pomocí aplikace logiky odeslat SMS zprávu prostřednictvím Twilio z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Přečtěte si, jak [pomocí aplikace logiky odeslat zprávu Slack z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Zjistěte, jak [pomocí aplikace logiky odeslat zprávu do fronty Azure z výstrahy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

