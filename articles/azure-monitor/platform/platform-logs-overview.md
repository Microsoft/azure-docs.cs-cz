---
title: Přehled protokolů platformy Azure | Dokumenty společnosti Microsoft
description: Přehled protokolů v Azure Monitoru, které poskytují bohatá, častá data o provozu prostředku Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659316"
---
# <a name="overview-of-azure-platform-logs"></a>Přehled protokolů platformy Azure
Protokoly platformy poskytují podrobné diagnostické a auditující informace pro prostředky Azure a platformu Azure, na které závisí. Jsou generovány automaticky, i když je třeba nakonfigurovat určité protokoly platformy, které mají být předány do jednoho nebo více cílů, které mají být zachovány. Tento článek obsahuje přehled protokolů platformy, včetně informací, které poskytují a jak je můžete nakonfigurovat pro shromažďování a analýzu.

## <a name="types-of-platform-logs"></a>Typy protokolů platformy
V následující tabulce jsou uvedeny protokoly konkrétní platformy, které jsou k dispozici v různých vrstvách Azure.

| Protokol | Vrstva | Popis |
|:---|:---|:---|
| Protokoly prostředků | Prostředky Azure | Poskytněte přehled o operacích, které byly provedeny v rámci prostředku Azure *(rovina dat*), například získání tajného klíče z trezoru klíčů nebo podání požadavku do databáze. Obsah protokolů prostředků se liší podle služby Azure a typu prostředků.<br><br>*Protokoly prostředků byly dříve označovány jako diagnostické protokoly.*  |
| Protokol aktivit | předplatné Azure | Poskytuje přehled o operacích na každém prostředku Azure v předplatném z vnějšku *(rovina správy)* kromě aktualizací událostí stavu služby. Pomocí protokolu aktivit určete, _co_, _kdo_a _kdy_ pro všechny operace zápisu (PUT, POST, DELETE) převzaté z prostředků ve vašem předplatném. Můžete také pochopit stav operace a další relevantní vlastnosti.  Pro každé předplatné Azure existuje jeden protokol aktivit. |
| Protokoly služby Azure Active Directory | Tenant Azure |  Obsahuje historii přihlašovací aktivity a auditní stopy změn provedených ve službě Azure Active Directory pro konkrétního klienta. Podívejte se na [co jsou sestavy Služby Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md)   |

> [!NOTE]
> Protokol aktivit Azure je primárně pro aktivity, které se vyskytují ve Správci prostředků Azure. Nesleduje prostředky pomocí modelu Classic/RDFE. Některé klasické typy prostředků mají ve Správci prostředků Azure zprostředkovatele prostředků proxy (například Microsoft.ClassicCompute). Pokud pracujete s klasickým typem prostředků prostřednictvím Správce prostředků Azure pomocí těchto zprostředkovatelů prostředků proxy, operace se zobrazí v protokolu aktivit. Pokud pracujete s klasickým typem prostředků mimo proxy servery Azure Resource Manager, vaše akce se zaznamenají jenom do protokolu operací. Protokol operace lze procházet v samostatné části portálu.

![Přehled protokolů platformy](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Zobrazení protokolů platformy
Existují různé možnosti pro zobrazení a analýzu různých protokolů platformy Azure.

- Zobrazení protokolu aktivit na webu Azure portal a přístup k událostem z PowerShellu a CLI. Podrobnosti najdete [v tématu Zobrazení a načtení událostí protokolu aktivit Azure.](activity-log-view.md) 
- Zobrazení sestav zabezpečení a aktivit služby Azure Active Directory na webu Azure Portal Podívejte [se, co jsou sestavy Služby Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md)  podrobnosti.
- Protokoly prostředků jsou automaticky generovány podporovanými prostředky Azure, ale nejsou k dispozici k zobrazení, pokud je neodešlete do [cíle](#destinations). 

## <a name="destinations"></a>Cíle
Protokoly platformy můžete odeslat do jednoho nebo více cílů v následující tabulce v závislosti na požadavcích na monitorování. Nakonfigurujte cíle protokolů platformy [vytvořením nastavení Diagnostika](diagnostic-settings.md).

| Cíl | Scénář | Odkazy |
|:---|:---|:---|:---|
| Pracovní prostor služby Log Analytics | Analyzujte protokoly s dalšími daty monitorování a využijte funkce Azure Monitoru, jako jsou dotazy protokolu a výstrahy. | [Protokol aktivit a protokoly zdrojů](resource-logs-collect-workspace.md)<br>[Protokoly adresáře aktivit Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Úložiště Azure | Archivujte protokoly pro audit, statickou analýzu nebo zálohování. |[Protokol aktivit a protokoly zdrojů](archive-diagnostic-logs.md)<br>[Protokoly adresáře aktivit Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centrum událostí | Streamujte protokoly do protokolovacích a telemetrických systémů jiných výrobců.  |[Protokol aktivit a protokoly zdrojů](resource-logs-stream-event-hubs.md)<br>[Protokoly adresáře aktivit Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Další kroky

* [Zobrazení schématu protokolu aktivit pro různé kategorie](activity-log-schema.md)
* [Zobrazení schématu protokolu prostředků pro různé služby Azure](diagnostic-logs-schema.md)
