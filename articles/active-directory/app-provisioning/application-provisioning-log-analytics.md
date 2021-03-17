---
title: Pochopte, jak zřizování integruje s protokoly Azure Monitor v Azure Active Directory.
description: Pochopte, jak zřizování integruje s protokoly Azure Monitor v Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f656f55b0c74103053f110b8c66645d353db9fbf
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561564"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>Informace o tom, jak se zřizování integruje s protokoly Azure Monitor

Zřizování se integruje s protokoly Azure Monitor a Log Analytics. S monitorováním Azure můžete provádět věci, jako je vytváření sešitů, označovaných také jako řídicí panely, ukládat protokoly zřizování po dobu 30 dnů a vytvářet vlastní dotazy a výstrahy. Tento článek popisuje, jak zajistit integraci protokolů s protokoly Azure Monitor. Další informace o tom, jak zřizování protokolů obecně funguje, najdete v tématu [zřizování protokolů](../reports-monitoring/concept-provisioning-logs.md).

## <a name="enabling-provisioning-logs"></a>Povolení protokolů zřizování

Službu Azure Monitoring a Log Analytics byste už měli znát. Pokud ne, přeskočte na Další informace a potom se vraťte k informací o protokolech zřizování aplikací. Další informace o monitorování Azure najdete v tématu [přehled Azure monitor](../../azure-monitor/overview.md). Další informace o protokolech a Log Analytics Azure Monitor najdete [v tématu Přehled dotazů protokolu v Azure monitor](../../azure-monitor/logs/log-query-overview.md).

Po nakonfigurování monitorování Azure můžete povolit protokoly pro zřizování aplikací. Možnost je umístěna na stránce **nastavení diagnostiky** .

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="Nastavení diagnostiky přístupu" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="Povolit protokoly zřizování aplikací" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> Pokud jste právě zřídili pracovní prostor, může trvat nějakou dobu, než můžete do něj Odeslat protokoly. Pokud se zobrazí chyba, že předplatné není zaregistrované pro používání *Microsoft. Insights* , vraťte se po několika minutách.
 
## <a name="understanding-the-data"></a>Porozumění datům
Podkladový datový proud, který zřizování odesílá, je téměř identický. Protokoly Azure Monitor jsou skoro stejný datový proud jako Azure Portal UI a rozhraní Azure API. V poli protokolu je jenom několik **rozdílů** , jak je uvedeno v následující tabulce. Další informace o těchto polích naleznete v [seznamu provisioningObjectSummary](/graph/api/provisioningobjectsummary-list?preserve-view=true&tabs=http&view=graph-rest-beta).

|Protokoly služby Azure Monitor   |Azure Portal UI   |Rozhraní API Azure |
|----------|-----------|------------|
|errorDescription |reason |resultDescription |
|status |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Azure Monitor sešity

Azure Monitor sešity poskytují flexibilní plátno pro analýzu dat. Také poskytují vytváření bohatých vizuálních sestav v rámci Azure Portal. Další informace najdete v tématu [přehled Azure Monitorch sešitů](../../azure-monitor/visualize/workbooks-overview.md).

Zřizování aplikací se dodává se sadou předem připravených sešitů. Můžete je najít na stránce sešity. Chcete-li zobrazit data, je nutné zajistit, aby všechny filtry (timeRange, jobID, appName) byly naplněny. Budete také muset zajistit, aby byla aplikace zajištěna, jinak nebudou v protokolech žádná data.

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="Sešity zřizování aplikací" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="Řídicí panel zřizování aplikace" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>Vlastní dotazy

Můžete vytvářet vlastní dotazy a zobrazovat data na řídicích panelech Azure. Další informace najdete v tématu [Vytvoření a sdílení řídicích panelů Log Analytics dat](../../azure-monitor/logs/get-started-queries.md). Nezapomeňte také zkontrolovat [Přehled dotazů protokolu v Azure monitor](../../azure-monitor/logs/log-query-overview.md).

Tady jsou některé ukázky, které vám pomohou začít s zřizováním aplikací.

Dotazování protokolů uživatele na základě jeho ID ve zdrojovém systému:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

Počet souhrnů na kód chyby:
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

Shrnout počet událostí za den podle akce:
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

Přijmout 100 událostí a vlastností klíčového klíče projektu:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>Vlastní výstrahy

Azure Monitor vám umožní nakonfigurovat vlastní výstrahy, abyste mohli dostávat oznámení o klíčových událostech týkajících se zřizování. Můžete například chtít dostávat upozornění na špičky při selháních. Nebo možná špičky zakáže nebo odstraní. Dalším příkladem, kde můžete chtít být upozornění, je chybějící jakékoli zřizování, což znamená, že je něco špatné.

Další informace o výstrahách najdete v tématu [reakce na události s výstrahami Azure monitor](../../azure-monitor/alerts/tutorial-response.md).

Upozorní, když dojde k selhání špičky. Nahraďte jobID řetězcem jobID pro vaši aplikaci.

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="Upozorní, když dojde k selhání špičky." lightbox="media/application-provisioning-log-analytics/alert1.png":::

Může se jednat o problém, který způsobil zastavení běhu služby zřizování. Pomocí následující výstrahy zjistíte, že během daného časového intervalu neexistují žádné události zřizování.

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="Může se jednat o problém, který způsobil zastavení běhu služby zřizování." lightbox="media/application-provisioning-log-analytics/alert2.png":::

Pošle upozornění, když se v nástroji zakáže nebo odstraní špička.

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="Pošle upozornění, když se v nástroji zakáže nebo odstraní špička." lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>Komunitní příspěvky

Přijímáme open source a přístup založený na komunitě pro dotazy a řídicí panely pro zřizování aplikací. Pokud jste vytvořili dotaz, výstrahu nebo sešit, který považujete za užitečné, nezapomeňte ho publikovat do [úložiště GitHub AzureMonitorCommunity](https://github.com/microsoft/AzureMonitorCommunity). Pak nám můžete poslat e-mail s odkazem. Podíváme se na službu a zveřejníme tak, aby ostatní mohli využívat výhod i dalších. Můžete nás kontaktovat na adrese provisioningfeedback@microsoft.com .

## <a name="next-steps"></a>Další kroky

- [Log Analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Začínáme s dotazy v protokolech Azure Monitor](../../azure-monitor/logs/get-started-queries.md)
- [Vytváření a Správa skupin výstrah v Azure Portal](../../azure-monitor/alerts/action-groups.md)
- [Instalace a použití zobrazení Log Analytics pro Azure Active Directory](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [Rozhraní API pro zřizování protokolů](/graph/api/resources/provisioningobjectsummary?preserve-view=true&view=graph-rest-beta)