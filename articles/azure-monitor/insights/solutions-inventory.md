---
title: Inventarizace řešení monitorování v Azure | Microsoft Docs
description: Řešení monitorování v Azure Monitor jsou kolekce pravidel pro logiku, vizualizaci a získávání dat, která poskytují metriky v určité oblasti problému.  Tento článek poskytuje seznam řešení monitorování, která jsou k dispozici od Microsoftu, a podrobnosti o jejich metodě a četnosti sběru dat.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: b87179c79489bf781619b70b19ca8982f2e38dff
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816812"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Podrobnosti o inventáři a shromažďování dat pro řešení monitorování v Azure
[Monitorovací řešení](solutions.md) používají služby v Azure k získání znalostí o provozu konkrétní aplikace nebo služby. Řešení monitorování obvykle shromažďují data protokolu a poskytují dotazy a zobrazení k analýze shromážděných dat. Můžete přidat řešení monitorování Azure Monitor pro všechny aplikace a služby, které používáte. Jsou obvykle k dispozici zdarma, ale shromažďují data, která by mohla vyvolat poplatky za využití.

Tento článek obsahuje seznam [řešení monitorování](solutions.md) , která jsou k dispozici od Microsoftu, s odkazy na jejich podrobnou dokumentaci.  Poskytuje také informace o jejich metodě a četnosti sběru dat do Azure Monitor.  Informace v tomto článku můžete použít k identifikaci různých dostupných řešení a k pochopení požadavků na tok dat a připojení pro různá řešení monitorování.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Seznam řešení monitorování

Následující tabulka uvádí [řešení monitorování](solutions.md) v Azure, která poskytuje Microsoft. Položka ve sloupci znamená, že řešení shromažďuje data do Azure Monitor pomocí této metody.  Pokud řešení nemá vybrané žádné sloupce, zapisuje se přímo do Azure Monitor z jiné služby Azure. Další informace najdete na odkazu pro každé z nich ke své podrobné dokumentaci.

Následující vysvětlení sloupců:

- **Microsoft Monitoring Agent** – agent používaný v systému Windows a Linux ke spuštění sady Management Pack z nástroje Microsoft System Center – Operations Manager (OM) a monitorování řešení z Azure. V této konfiguraci je agent připojený přímo k Azure Monitor bez připojení ke skupině pro správu Operations Manager. 
- **Operations Manager** – identický Agent jako Microsoft Monitoring Agent. V této konfiguraci je [připojená k Operations Manager skupině pro správu](../platform/om-agents.md) , která je připojená k Azure monitor. 
-  **Azure Storage** – řešení shromažďuje data z účtu Azure Storage. 
- **Operations Manager požadováno?** – Pro shromažďování dat řešení monitorování se vyžaduje skupina pro správu připojeného Operations Manager. 
- **Operations Manager dat agenta odeslaných prostřednictvím skupiny pro správu** – Pokud je agent [připojen ke skupině pro správu OM](../platform/om-agents.md), budou data odesílána Azure monitor z Management Server. V takovém případě se Agent nemusí připojovat přímo k Azure Monitor. Není-li toto políčko zaškrtnuto, budou data odesílána z agenta přímo do Azure Monitor i v případě, že je agent připojen ke skupině pro správu OM. Bude muset být schopná komunikovat s Azure Monitor přes [bránu Log Analytics](../platform/gateway.md).
- **Frekvence shromažďování** – Určuje četnost shromažďování dat pomocí řešení monitorování. 



| **Řešení monitorování** | **Platforma** | **Microsoft Monitoring Agent** | **Agent Operations Manageru** | **Úložiště Azure** | **Operations Manager požadováno?** | **Data agenta Operations Manager odeslána prostřednictvím skupiny pro správu** | **Četnost shromažďování dat** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Analýza protokolů aktivit](../platform/activity-log-collect.md) | Azure | | | | | | při oznámení |
| [Posouzení AD](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dní |
| [Stav replikace AD](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dní |
| [Stav agenta](solution-agenthealth.md) | Windows a Linux | &#8226; | &#8226; | | | &#8226; | 1 min. |
| [Alert Management](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |při doručení |
| [Alert Management](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 min. |
| [Alert Management](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuty |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | Není k dispozici |
| [Application Insights Connector (zastaralé)](../platform/app-insights-connector.md) | Azure | | | |  |  | při oznámení |
| [Automation Hybrid Worker](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | Není k dispozici |
| [Azure Application Gateway Analytics](azure-networking-analytics.md) | Azure |  |  |  |  |  | při oznámení |
| **Řešení monitorování** | **Platforma** | **Microsoft Monitoring Agent** | **Agent Operations Manageru** | **Úložiště Azure** | **Operations Manager požadováno?** | **Data agenta Operations Manager odeslána prostřednictvím skupiny pro správu** | **Četnost shromažďování dat** |
| [Azure Network Security Group Analytics (zastaralé)](azure-networking-analytics.md) | Azure |  |  |  |  |  | při oznámení |
| [Azure SQL Analytics (Preview)](azure-sql.md) | Windows | | | | | | 1 min. |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | při oznámení |
| [Capacity and Performance (Preview)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |při doručení |
| [Change Tracking](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[se liší](../../automation/change-tracking.md#change-tracking-and-inventory-data-collection) |
| [Change Tracking](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[se liší](../../automation/change-tracking.md#change-tracking-and-inventory-data-collection) |
| [Containers](containers.md) | Windows a Linux | &#8226; | &#8226; |  |  |  | 3 minuty |
| [Key Vault Analytics](azure-key-vault.md) |Windows | | | | | |při oznámení |
| [Malware Assessment](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |po hodinách |
| [Sledování výkonu sítě](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Protokol TCP handshakes každých 5 sekund, data odesílaná každé 3 minuty |
| [Office 365 Analytics (Preview)](solution-office-365.md) |Windows | | | | | |při oznámení |
| **Řešení monitorování** | **Platforma** | **Microsoft Monitoring Agent** | **Agent Operations Manageru** | **Úložiště Azure** | **Operations Manager požadováno?** | **Data agenta Operations Manager odeslána prostřednictvím skupiny pro správu** | **Četnost shromažďování dat** |
| [Analýza služby Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minut |
| [Mapa služeb](service-map.md) | Windows a Linux | &#8226; | &#8226; |  |  |  | 15 sekund |
| [Posouzení SQL](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dní |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |při doručení |
| [System Center Operations Manager Assessment (Preview)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sedm dní |
| [Správa aktualizací](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |minimálně dvakrát denně a 15 minut po instalaci aktualizace |
| [Upgrade Readiness](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dny |
| [VMware Monitoring (zastaralé)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuty |
| [Wire Data 2.0 (Preview)](wire-data.md) |Windows (2012 R2/8,1 nebo novější) |&#8226; |&#8226; | | | | 1 min. |




## <a name="next-steps"></a>Další kroky
* Naučte se [instalovat a používat řešení monitorování](solutions.md).
* Naučte se [vytvářet dotazy](../log-query/log-query-overview.md) k analýze dat shromažďovaných monitorovacími řešeními.
