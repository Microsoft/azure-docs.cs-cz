---
title: Podrobnosti shromažďování dat pro řešení pro správu v Azure | Dokumentace Microsoftu
description: Řešení pro správu v Azure jsou kolekce logiku, vizualizaci, pravidel a získávání dat, které poskytují metriky zaměřené na oblast konkrétního problému.  Tento článek obsahuje seznam řešení pro správu k dispozici od společnosti Microsoft a podrobnosti o jejich metoda a četnost shromažďování dat.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: cb230dad22e6b0d67a4d6a589e9fba00560a9264
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51715221"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Podrobnosti shromažďování dat pro řešení pro správu v Azure
Tento článek obsahuje seznam [řešení pro správu](solutions.md) od Microsoftu k dispozici s odkazy na jejich podrobnou dokumentaci.  Poskytuje také informace o jejich metoda a četnost shromažďování dat do Log Analytics.  K identifikaci různých řešení, které jsou k dispozici a lépe porozumět požadavkům datového toku a připojení pro jiné řešení, můžete použít informace v tomto článku. 

## <a name="list-of-management-solutions"></a>Seznam řešení pro správu

Následující tabulce jsou uvedeny [řešení pro správu](solutions.md) v Azure poskytované společností Microsoft. Údaj ve sloupci znamená, že řešení shromažďuje data do Log Analytics pomocí této metody.  Pokud řešení neobsahuje žádné sloupce vybraná, pak zapíše přímo ke službě Log Analytics z jiné služby Azure. Pomocí následujícího odkazu pro každé z nich na jeho podrobnou dokumentaci pro další informace.

Vysvětlení sloupců jsou následující:

- **Agent sledování Microsoft** -Agent používá ke spuštění z SCOM a správu řešení z Azure pack vedení společnosti ve Windows a Linuxu. V této konfiguraci agenta připojené přímo k Log Analytics bez připojení k skupinu pro správu nástroje Operations Manager. 
- **Nástroj Operations Manager** – identické agenta jako agenta Microsoft monitoring agent. V této konfiguraci obsahuje [připojené skupiny pro správu Operations Manageru](../../log-analytics/log-analytics-om-agents.md) , který je připojený ke službě Log Analytics. 
-  **Azure Storage** – řešení shromažďuje data z účtu služby Azure storage. 
- **Nástroj Operations Manager vyžaduje?** – Připojené skupiny pro správu Operations Manageru je nezbytná pro shromažďování dat do řešení pro správu. 
- **Dat agenta nástroje Operations Manager odeslaná pomocí skupiny pro správu** – Pokud je agent [připojené ke skupině pro správu nástroje SCOM](../../log-analytics/log-analytics-om-agents.md), pak se odešlou do služby Log Analytics ze serveru pro správu. V takovém případě agenta není nutné se připojit přímo k Log Analytics. Pokud toto políčko není zaškrtnuto, pak data odesílána z agenta přímo ke službě Log Analytics i v případě, agent je připojený ke skupině pro správu nástroje SCOM. Ji budou muset být schopni komunikovat s Log Analytics prostřednictvím [Log Analytics gateway](../../log-analytics/log-analytics-oms-gateway.md).
- **Četnost shromažďování dat** – Určuje, jak často, že shromažďuje data řešení pro správu. 



| **Řešení pro správu** | **Platforma** | **Agent sledování Microsoft** | **Agent nástroje Operations Manager** | **Úložiště Azure** | **Nástroj Operations Manager vyžaduje?** | **Dat agenta nástroje Operations Manager odeslaná pomocí skupiny pro správu** | **Četnost shromažďování dat** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Activity Log Analytics](../../log-analytics/log-analytics-activity.md) | Azure | | | | | | v oznámení |
| [Posouzení AD](../../log-analytics/log-analytics-ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dní |
| [Stav replikace AD](../../log-analytics/log-analytics-ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dní |
| [Stav agenta](solution-agenthealth.md) | Windows a Linux | &#8226; | &#8226; | | | &#8226; | 1 minuta |
| [Správa výstrah](../../log-analytics/log-analytics-solution-alert-management.md) (Nagios) |Linux |&#8226; | | | | |Při doručení |
| [Správa výstrah](../../log-analytics/log-analytics-solution-alert-management.md) (Zabbix) |Linux |&#8226; | | | | |1 minuta |
| [Správa výstrah](../../log-analytics/log-analytics-solution-alert-management.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuty |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | neuvedeno |
| [Application Insights Connector (Preview)](../../log-analytics/log-analytics-app-insights-connector.md) | Azure | | | |  |  | v oznámení |
| [Hybrid Worker služby Automation](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | neuvedeno |
| [Analýzy Azure Application Gateway](../../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | v oznámení |
| **Řešení pro správu** | **Platforma** | **Agent sledování Microsoft** | **Agent nástroje Operations Manager** | **Úložiště Azure** | **Nástroj Operations Manager vyžaduje?** | **Dat agenta nástroje Operations Manager odeslaná pomocí skupiny pro správu** | **Četnost shromažďování dat** |
| [Skupiny zabezpečení sítě Azure Analytics (zastaralé)](../../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | v oznámení |
| [Azure SQL Analytics (Náhled)](../../log-analytics/log-analytics-azure-sql.md) | Windows | | | | | | 1 minuta |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | v oznámení |
| [Kapacita a výkon (Preview)](../../log-analytics/log-analytics-capacity.md) |Windows |&#8226; |&#8226; | | |&#8226; |Při doručení |
| [Sledování změn](../../automation/automation-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |každou hodinu |
| [Sledování změn](../../automation/automation-change-tracking.md) |Linux |&#8226; | | | | |každou hodinu |
| [Containers](../../log-analytics/log-analytics-containers.md) | Windows a Linux | &#8226; | &#8226; |  |  |  | 3 minuty |
| [Analýza služby Key Vault](../../log-analytics/log-analytics-azure-key-vault.md) |Windows | | | | | |v oznámení |
| [Posouzení malwaru](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |každou hodinu |
| [Sledování výkonu sítě](../../log-analytics/log-analytics-network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Počet metod Handshake protokolu TCP každých 5 sekund dat odeslaných každé 3 minuty |
| [Office 365 Analytics (Náhled)](solution-office-365.md) |Windows | | | | | |v oznámení |
| **Řešení pro správu** | **Platforma** | **Agent sledování Microsoft** | **Agent nástroje Operations Manager** | **Úložiště Azure** | **Nástroj Operations Manager vyžaduje?** | **Dat agenta nástroje Operations Manager odeslaná pomocí skupiny pro správu** | **Četnost shromažďování dat** |
| [Analýza služby Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 minut |
| [Mapa služeb](../../monitoring/monitoring-service-map.md) | Windows a Linux | &#8226; | &#8226; |  |  |  | 15 sekund |
| [Posouzení SQL](../../log-analytics/log-analytics-sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dní |
| [SurfaceHub](../../log-analytics/log-analytics-surface-hubs.md) |Windows |&#8226; | | | | |Při doručení |
| [System Center Operations Manager Assessment (Preview)](../../log-analytics/log-analytics-scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sedm dní |
| [Správa aktualizací](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |minimálně 2 časy za den a 15 minut po instalaci aktualizace |
| [Upgrade Readiness](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dny |
| [VMware Monitoring (zastaralé)](../../log-analytics/log-analytics-vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuty |
| [Wire Data 2.0 (Preview)](../../log-analytics/log-analytics-wire-data.md) |Windows (2012 R2 / 8.1 a novější) |&#8226; |&#8226; | | | | 1 minuta |




## <a name="next-steps"></a>Další postup
* Zjistěte, jak [vytvářet dotazy](../../log-analytics/log-analytics-queries.md) k analýze dat shromažďovaných řešeními pro správu.
