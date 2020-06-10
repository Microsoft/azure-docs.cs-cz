---
title: Přehled protokolů platformy Azure | Microsoft Docs
description: Přehled protokolů v Azure Monitor poskytujících bohatou a častou práci o provozu prostředku Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c61a7b46a2f1cc6fa518e5001d5e1905946d5e8e
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84610079"
---
# <a name="overview-of-azure-platform-logs"></a>Přehled protokolů platformy Azure
Protokoly platforem poskytují podrobné informace o diagnostice a auditování pro prostředky Azure a platformu Azure, na které jsou závislé. Generují se automaticky, přestože potřebujete nakonfigurovat některé protokoly platformy, které se mají přemístit do jednoho nebo více cílů, aby je bylo možné zachovat. Tento článek obsahuje přehled protokolů platforem, včetně informací, které poskytují, a o tom, jak je můžete nakonfigurovat pro shromažďování a analýzu.

## <a name="types-of-platform-logs"></a>Typy protokolů platformy
V následující tabulce jsou uvedeny konkrétní protokoly platformy, které jsou k dispozici v různých vrstvách Azure.

| Protokol | Vrstva | Popis |
|:---|:---|:---|
| Protokoly prostředků | Prostředky Azure | Poskytněte přehled o operacích, které byly provedeny v rámci prostředku Azure ( *rovina dat*), například získání tajného kódu z Key Vault nebo vytvoření žádosti do databáze. Obsah protokolů prostředků se liší podle typu prostředku a služby Azure.<br><br>*Protokoly prostředků se dřív odkazovaly na diagnostické protokoly.*  |
| Protokol aktivit | předplatné Azure | Poskytuje přehled o operacích u každého prostředku Azure v předplatném mimo (*rovinu správy*) kromě aktualizací Service Healthch událostí. Pomocí protokolu aktivit můžete určit, _kdo_a _kdy_ se u prostředků ve vašem předplatném mají _dělat_operace zápisu (Put, post, DELETE). Můžete také pochopit stav operace a dalších relevantních vlastností.  Pro každé předplatné Azure existuje jeden protokol aktivit. |
| Protokoly Azure Active Directory | Tenant Azure |  Obsahuje historii přihlašovací aktivity a záznam pro audit změn provedených v Azure Active Directory pro konkrétního tenanta. Úplný popis protokolů Azure Active Directory najdete v tématu [co jsou sestavy Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md) .   |

> [!NOTE]
> Protokol aktivit Azure je primárně pro aktivity, ke kterým dochází v Azure Resource Manager. Nesleduje prostředky pomocí modelu Classic/RDFE. Některé typy klasických prostředků mají poskytovatele prostředků proxy v Azure Resource Manager (například Microsoft. ClassicCompute). Pokud pracujete s klasickým typem prostředku prostřednictvím Azure Resource Manager pomocí těchto zprostředkovatelů prostředků proxy, operace se zobrazí v protokolu aktivit. Pokud pracujete s klasickým typem prostředku mimo Azure Resource Manager proxy, vaše akce se zaznamenávají pouze do protokolu operací. Protokol operací se dá procházet v samostatné části portálu.

![Přehled protokolů platformy](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Zobrazení protokolů platformy
K dispozici jsou různé možnosti pro zobrazení a analýzu různých protokolů platformy Azure.

- Zobrazení protokolu aktivit v Azure Portal a přístup k událostem z PowerShellu a rozhraní příkazového řádku. Podrobnosti najdete v tématu [zobrazení a načtení událostí protokolu aktivit Azure](activity-log-view.md) . 
- Zobrazení Azure Active Directorych sestav zabezpečení a aktivit v Azure Portal. Podívejte [se, co jsou sestavy Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md)  , kde najdete podrobnosti.
- Protokoly prostředků se automaticky generují pomocí podporovaných prostředků Azure, ale nedají se zobrazit, pokud je neodešlete do [cílového umístění](#destinations). 

## <a name="destinations"></a>Cíle
Protokoly platforem můžete odesílat do jednoho nebo více cílů v následující tabulce v závislosti na požadavcích na monitorování. Nakonfigurujte cíle pro protokoly platforem [vytvořením nastavení diagnostiky](diagnostic-settings.md).

| Cíl | Scénář | Reference |
|:---|:---|:---|:---|
| Pracovní prostor služby Log Analytics | Analyzujte protokoly s dalšími daty monitorování a využijte Azure Monitorch funkcí, jako jsou například dotazy a výstrahy protokolu. | [Protokol aktivit a protokoly prostředků](resource-logs-collect-workspace.md)<br>[Protokoly Azure Active Directory](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Archivujte protokoly pro audit, statickou analýzu nebo zálohování. |[Protokol aktivit a protokoly prostředků](archive-diagnostic-logs.md)<br>[Protokoly Azure Active Directory](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centrum událostí | Streamujte protokoly do systémů protokolování a telemetrie třetích stran.  |[Protokol aktivit a protokoly prostředků](resource-logs-stream-event-hubs.md)<br>[Protokoly Azure Active Directory](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Další kroky

* [Zobrazení schématu protokolu aktivit pro různé kategorie](activity-log-schema.md)
* [Zobrazení schématu protokolu prostředků pro různé služby Azure](diagnostic-logs-schema.md)
