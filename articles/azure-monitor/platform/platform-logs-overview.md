---
title: Přehled protokolů platformy Azure | Microsoft Docs
description: Přehled diagnostických protokolů v Azure, které poskytují bohatou a častou data o provozu prostředku Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c1602b9beb5e667caf1688901c7ae4e67e5f5839
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262567"
---
# <a name="overview-of-azure-platform-logs"></a>Přehled protokolů platformy Azure
Protokoly platforem poskytují podrobné informace o diagnostice a auditování pro prostředky Azure a platformu Azure, na které jsou závislé. Generují se automaticky, přestože potřebujete nakonfigurovat některé protokoly platformy, které se mají přemístit do jednoho nebo více cílů, aby je bylo možné zachovat. Tento článek obsahuje přehled protokolů platforem, včetně informací, které poskytují, a o tom, jak je můžete nakonfigurovat pro shromažďování a analýzu.

## <a name="types-of-platform-logs"></a>Typy protokolů platformy
V následující tabulce jsou uvedeny konkrétní protokoly platformy, které jsou k dispozici v různých vrstvách Azure.

| Vrstva | Logs | Popis |
|:---|:---|:---|
| Prostředky Azure | [Protokoly prostředků](resource-logs-overview.md) | Poskytněte přehled o operacích, které byly provedeny v rámci prostředku Azure ( *rovina dat*), například získání tajného kódu z Key Vault nebo vytvoření žádosti do databáze. Obsah protokolů prostředků se liší podle typu prostředku a služby Azure.<br>*Protokoly prostředků se dřív odkazovaly na diagnostické protokoly.*  |
| Předplatné Azure | [Protokol aktivit](activity-logs-overview.md) | Poskytuje přehled o operacích u každého prostředku Azure v předplatném mimo (*rovinu správy*) kromě aktualizací Service Healthch událostí. Pro každé předplatné Azure existuje jeden protokol aktivit.   |
| Tenant Azure | [Protokoly Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)  | Obsahuje historii přihlašovací aktivity a záznam pro audit změn provedených v Azure Active Directory pro konkrétního tenanta.   |


![Přehled protokolů platformy](media/platform-logs-overview/logs-overview.png)

## <a name="viewing-platform-logs"></a>Zobrazení protokolů platformy
[Protokol aktivit](activity-log-view.md) a [protokoly Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) můžete zobrazit v Azure Portal. Chcete-li je zobrazit, je třeba odeslat protokoly prostředků do [cílového umístění](#destinations) .


## <a name="destinations"></a>Cíle
Protokoly platforem můžete odesílat do jednoho nebo více cílů v následující tabulce v závislosti na požadavcích na monitorování. 

| Destination | Scénář | Odkazy |
|:---|:---|:---|:---|
| Pracovní prostor Log Analytics | Analyzujte protokoly s dalšími daty monitorování a využijte Azure Monitorch funkcí, jako jsou například dotazy a výstrahy protokolu. | [Protokoly prostředků](resource-logs-collect-storage.md)<br>[Protokol aktivit](activity-log-collect.md)<br>[Protokoly adresářů aktivit Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Úložiště Azure | Archivujte protokoly pro audit, statickou analýzu nebo zálohování. |[Protokoly prostředků](archive-diagnostic-logs.md)<br>[Protokol aktivit](activity-log-export.md)<br>[Protokoly adresářů aktivit Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centrum událostí | Streamujte protokoly do systémů protokolování a telemetrie třetích stran.  |[Protokoly prostředků](resource-logs-stream-event-hubs.md)<br>[Protokol aktivit](activity-log-export.md)<br>[Protokoly adresářů aktivit Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |


## <a name="diagnostic-settings-and-log-profiles"></a>Nastavení diagnostiky a profily protokolů
Nakonfigurujte cíle pro protokoly prostředků a protokoly Azure Active Directory [vytvořením nastavení diagnostiky](diagnostic-settings.md). Nakonfigurujte cíle pro protokol aktivit tak, že [vytvoříte profil protokolu](activity-log-export.md) nebo [ho připojíte k pracovnímu prostoru Log Analytics](activity-log-collect.md).

Nastavení diagnostiky a profil protokolu definují následující:

- Jeden nebo více cílů pro odeslání vybraných protokolů a metrik.
- Které kategorie protokolů a metriky z prostředku se odesílají do cílů.
- Pokud je jako cíl vybraný účet úložiště, jak dlouho se mají uchovávat jednotlivé kategorie protokolů



## <a name="next-steps"></a>Další kroky

* [Další informace o protokolu aktivit](activity-logs-overview.md)
* [Další informace o protokolech prostředků](resource-logs-overview.md)
* [Zobrazení schématu protokolu prostředků pro různé služby Azure](diagnostic-logs-schema.md)
