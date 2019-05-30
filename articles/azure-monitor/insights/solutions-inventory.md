---
title: Seznam sledování řešení v Azure | Dokumentace Microsoftu
description: Monitorování řešení ve službě Azure Monitor jsou kolekce logiku, vizualizaci, pravidel a získávání dat, které poskytují metriky zaměřené na oblast konkrétního problému.  Tento článek obsahuje seznam řešení, které jsou k dispozici od společnosti Microsoft a podrobnosti o jejich metoda a četnost shromažďování dat monitorování.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 9398815ea75c0eacd99a6e40c569254fac671cbb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234019"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Inventář a data podrobností kolekce pro monitorování řešení v Azure
[Řešení monitorování](solutions.md) využívat služby v Azure a poskytuje další informace o provozu určitá aplikace nebo služby. Monitorování řešení obvykle shromažďovat data protokolu a poskytují dotazů a zobrazení pro analýzy shromážděných dat. Monitorování řešení můžete přidat do Azure monitoru pro všechny aplikace a služby, které používáte. Jsou obvykle dostupné v žádné náklady, ale shromažďovat data, která může vyvolat poplatky za využívání.

Tento článek obsahuje seznam [montioring řešení](solutions.md) od Microsoftu k dispozici s odkazy na jejich podrobnou dokumentaci.  Poskytuje také informace o jejich metoda a četnost shromažďování dat do služby Azure Monitor.  K identifikaci různých řešení, které jsou k dispozici a lépe porozumět požadavkům datového toku a připojení pro různá řešení monitorování, můžete použít informace v tomto článku.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Seznam řešení monitorování

Následující tabulce jsou uvedeny [řešení monitorování](solutions.md) v Azure poskytované společností Microsoft. Údaj ve sloupci znamená, že řešení shromažďuje data do služby Azure Monitor pomocí této metody.  Pokud řešení neobsahuje žádné sloupce vybraná, pak zapíše přímo do Azure monitoru z jiné služby Azure. Pomocí následujícího odkazu pro každé z nich na jeho podrobnou dokumentaci pro další informace.

Vysvětlení sloupců jsou následující:

- **Agent sledování Microsoft** – agenta ve Windows a Linuxu používá ke spouštění balíčku vedení společnosti z SCOM a sledování řešení z Azure. V této konfiguraci připojení agenta přímo do Azure monitoru bez připojení k skupinu pro správu nástroje Operations Manager. 
- **Nástroj Operations Manager** – identické agenta jako agenta Microsoft monitoring agent. V této konfiguraci obsahuje [připojené skupiny pro správu Operations Manageru](../platform/om-agents.md) , který je připojený k Azure Monitor. 
-  **Azure Storage** – řešení shromažďuje data z účtu služby Azure storage. 
- **Nástroj Operations Manager vyžaduje?** – Připojené skupiny pro správu nástroje Operations Manager je vyžadován pro shromažďování dat podle řešení monitorování. 
- **Dat agenta nástroje Operations Manager odeslaná pomocí skupiny pro správu** – Pokud je agent [připojené ke skupině pro správu nástroje SCOM](../platform/om-agents.md), pak se odešlou do Azure monitoru ze serveru pro správu. V takovém případě agenta nemusí připojit přímo do Azure monitoru. Pokud toto políčko není zaškrtnuto, pak data odesílána z agenta přímo do Azure monitoru i v případě, agent je připojený ke skupině pro správu nástroje SCOM. Ji budou muset být schopné komunikovat prostřednictvím Azure Monitor [Log Analytics gateway](../platform/gateway.md).
- **Četnost shromažďování dat** – Určuje, jak často, že shromažďuje data řešení monitorování. 



| **Řešení pro monitorování** | **Platforma** | **Agent sledování Microsoft** | **Agent nástroje Operations Manager** | **Úložiště Azure** | **Nástroj Operations Manager vyžaduje?** | **Dat agenta nástroje Operations Manager odeslaná pomocí skupiny pro správu** | **Četnost shromažďování dat** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Aktivita Log analytics](../platform/activity-log-collect.md) | Azure | | | | | | v oznámení |
| [Posouzení AD](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dní |
| [Stav replikace AD](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dní |
| [Stav agenta](solution-agenthealth.md) | Windows a Linux | &#8226; | &#8226; | | | &#8226; | 1 minuta |
| [Správa výstrah](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |Při doručení |
| [Správa výstrah](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 minuta |
| [Správa výstrah](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuty |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | neuvedeno |
| [Application Insights Connector (zastaralé)](../platform/app-insights-connector.md) | Azure | | | |  |  | v oznámení |
| [Hybrid Worker služby Automation](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | neuvedeno |
| [Analýzy Azure Application Gateway](azure-networking-analytics.md) | Azure |  |  |  |  |  | v oznámení |
| **Řešení pro monitorování** | **Platforma** | **Agent sledování Microsoft** | **Agent nástroje Operations Manager** | **Úložiště Azure** | **Nástroj Operations Manager vyžaduje?** | **Dat agenta nástroje Operations Manager odeslaná pomocí skupiny pro správu** | **Četnost shromažďování dat** |
| [Skupiny zabezpečení sítě Azure Analytics (zastaralé)](azure-networking-analytics.md) | Azure |  |  |  |  |  | v oznámení |
| [Azure SQL Analytics (Náhled)](azure-sql.md) | Windows | | | | | | 1 minuta |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | v oznámení |
| [Kapacita a výkon (Preview)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |Při doručení |
| [Sledování změn](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[Se liší](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Sledování změn](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[Se liší](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Containers](containers.md) | Windows a Linux | &#8226; | &#8226; |  |  |  | 3 minuty |
| [Analýza služby Key Vault](azure-key-vault.md) |Windows | | | | | |v oznámení |
| [Posouzení malwaru](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |každou hodinu |
| [Sledování výkonu sítě](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Počet metod Handshake protokolu TCP každých 5 sekund dat odeslaných každé 3 minuty |
| [Office 365 Analytics (Náhled)](solution-office-365.md) |Windows | | | | | |v oznámení |
| **Řešení pro monitorování** | **Platforma** | **Agent sledování Microsoft** | **Agent nástroje Operations Manager** | **Úložiště Azure** | **Nástroj Operations Manager vyžaduje?** | **Dat agenta nástroje Operations Manager odeslaná pomocí skupiny pro správu** | **Četnost shromažďování dat** |
| [Analýza služby Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minut |
| [Mapa služeb](service-map.md) | Windows a Linux | &#8226; | &#8226; |  |  |  | 15 sekund |
| [Posouzení SQL](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dní |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |Při doručení |
| [System Center Operations Manager Assessment (Preview)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sedm dní |
| [Správa aktualizací](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |minimálně 2 časy za den a 15 minut po instalaci aktualizace |
| [Upgrade Readiness](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dny |
| [VMware Monitoring (zastaralé)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuty |
| [Wire Data 2.0 (Preview)](wire-data.md) |Windows (2012 R2 / 8.1 a novější) |&#8226; |&#8226; | | | | 1 minuta |




## <a name="next-steps"></a>Další postup
* Zjistěte, jak [instalace a použití sledování řešení](solutions.md).
* Zjistěte, jak [vytvářet dotazy](../log-query/log-query-overview.md) analyzovat data shromážděná službou sledování řešení.
