---
title: Inventář řešení monitorování v Azure | Dokumenty společnosti Microsoft
description: Řešení monitorování ve službě Azure Monitor jsou kolekce pravidel logiky, vizualizace a získávání dat, která poskytují metriky otočné kolem konkrétní problémové oblasti.  Tento článek obsahuje seznam řešení monitorování dostupných od společnosti Microsoft a podrobnosti o jejich způsobu a četnosti shromažďování dat.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: 7b88d957bce45bf518fc77584f1691de8010459a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663125"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Podrobnosti o inventáři a shromažďování dat pro řešení monitorování v Azure
[Monitorování řešení](solutions.md) využívají služby v Azure k poskytnutí dalších informací o provozu konkrétní aplikace nebo služby. Řešení monitorování obvykle shromažďují data protokolu a poskytují dotazy a zobrazení pro analýzu shromážděných dat. Do Azure Monitoru můžete přidat řešení monitorování pro všechny aplikace a služby, které používáte. Obvykle jsou k dispozici bez nákladů, ale shromažďují data, která by mohla vyvolat poplatky za využití.

Tento článek obsahuje seznam [montioring řešení](solutions.md) k dispozici od společnosti Microsoft s odkazy na jejich podrobnou dokumentaci.  Poskytuje také informace o jejich metodě a četnosti shromažďování dat do Azure Monitoru.  Informace v tomto článku můžete použít k identifikaci různých dostupných řešení a k pochopení požadavků na tok dat a připojení pro různá řešení monitorování.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Seznam monitorovacích řešení

V následující tabulce jsou uvedena [řešení monitorování](solutions.md) v Azure poskytované společností Microsoft. Položka ve sloupci znamená, že řešení shromažďuje data do Azure Monitoru pomocí této metody.  Pokud řešení nemá žádné sloupce vybrané, pak zapíše přímo do Azure Monitor z jiné služby Azure. Další informace naleznete na odkazu pro každou z nich na jeho podrobnou dokumentaci.

Vysvětlení sloupců jsou následující:

- **Agent monitorování Microsoftu** – Agent používaný ve Windows a Linuxu ke spuštění sady Managements Pack od SCOM a monitorovacích řešení z Azure. V této konfiguraci je agent připojený přímo k Azure Monitoru, aniž by byl připojen ke skupině správy Operations Manageru. 
- **Operations Manager** - Identické agent jako agent monitorování společnosti Microsoft. V této konfiguraci je [připojen ke skupině správy Operations Manager,](../platform/om-agents.md) který je připojený k Azure Monitoru. 
-  **Azure Storage** – řešení shromažďuje data z účtu úložiště Azure. 
- **Vyžaduje se provozní manažer?** - Připojená skupina pro správu Operations Manageru je vyžadována pro sběr dat pomocí monitorovacího řešení. 
- **Data agenta Operations Manageru odeslaná prostřednictvím skupiny pro správu** – Pokud je agent [připojený ke skupině pro správu SCOM](../platform/om-agents.md), budou data odeslána do služby Azure Monitor ze serveru pro správu. V takovém případě agent nemusí připojení přímo k Azure Monitoru. Pokud toto pole není zaškrtnuté, data se odesílají z agenta přímo do Azure Monitoru i v případě, že je agent připojený ke skupině pro správu SCOM. Bude muset být schopen komunikovat s Azure Monitor prostřednictvím [brány Log Analytics](../platform/gateway.md).
- **Frekvence sběru** - Určuje frekvenci, která jsou shromažďována monitorovacím řešením. 



| **Monitorovací řešení** | **Platforma** | **Agent monitorování společnosti Microsoft** | **Agent Operations Manageru** | **Úložiště Azure** | **Vyžaduje se provozní manažer?** | **Data agenta Operations Manager odeslaná prostřednictvím skupiny pro správu** | **Četnost shromažďování dat** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Analýza protokolu aktivit](../platform/activity-log-collect.md) | Azure | | | | | | na oznámení |
| [Posouzení AD](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dní |
| [Stav replikace AD](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dní |
| [Stav agenta](solution-agenthealth.md) | Windows a Linux | &#8226; | &#8226; | | | &#8226; | 1 minuta |
| [Správa výstrah](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |při příjezdu |
| [Správa výstrah](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minuta |
| [Správa výstrah](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuty |
| [Obnovení webu Azure](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | neuvedeno |
| [Konektor Přehledy aplikací (zastaralé)](../platform/app-insights-connector.md) | Azure | | | |  |  | na oznámení |
| [Hybridní pracovník automatizace](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | neuvedeno |
| [Azure Application Gateway Analytics](azure-networking-analytics.md) | Azure |  |  |  |  |  | na oznámení |
| **Monitorovací řešení** | **Platforma** | **Agent monitorování společnosti Microsoft** | **Agent Operations Manageru** | **Úložiště Azure** | **Vyžaduje se provozní manažer?** | **Data agenta Operations Manager odeslaná prostřednictvím skupiny pro správu** | **Četnost shromažďování dat** |
| [Azure Network Security Group Analytics (zastaralé)](azure-networking-analytics.md) | Azure |  |  |  |  |  | na oznámení |
| [Azure SQL Analytics (preview)](azure-sql.md) | Windows | | | | | | 1 minuta |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | na oznámení |
| [Kapacita a výkon (náhled)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |při příjezdu |
| [Sledování změn](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[Liší](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Sledování změn](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[Liší](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Kontejnery](containers.md) | Windows a Linux | &#8226; | &#8226; |  |  |  | 3 minuty |
| [Analýza služby Key Vault](azure-key-vault.md) |Windows | | | | | |na oznámení |
| [Hodnocení malwaru](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |Hodinové |
| [Sledování výkonu sítě](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP handshakes každých 5 sekund, data odeslaná každé 3 minuty |
| [Office 365 Analytics (preview)](solution-office-365.md) |Windows | | | | | |na oznámení |
| **Monitorovací řešení** | **Platforma** | **Agent monitorování společnosti Microsoft** | **Agent Operations Manageru** | **Úložiště Azure** | **Vyžaduje se provozní manažer?** | **Data agenta Operations Manager odeslaná prostřednictvím skupiny pro správu** | **Četnost shromažďování dat** |
| [Analýza služby Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minut |
| [Mapa služeb](service-map.md) | Windows a Linux | &#8226; | &#8226; |  |  |  | 15 sekund |
| [Posouzení SQL](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dní |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |při příjezdu |
| [Hodnocení operačního manažera systémového centra (náhled)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sedm dní |
| [Update Management](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |nejméně 2x denně a 15 minut po instalaci aktualizace |
| [Upgrade Readiness](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dny |
| [Monitorování vmware (zastaralé)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuty |
| [Data vodiče 2.0 (náhled)](wire-data.md) |Windows (2012 R2 / 8.1 nebo novější) |&#8226; |&#8226; | | | | 1 minuta |




## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak [nainstalovat a používat monitorovací řešení](solutions.md).
* Zjistěte, jak [vytvářet dotazy](../log-query/log-query-overview.md) k analýze dat shromážděných pomocí řešení monitorování.
