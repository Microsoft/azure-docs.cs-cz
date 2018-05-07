---
title: Podrobnosti o shromažďování dat pro řešení pro správu v Azure | Microsoft Docs
description: Řešení pro správu v Azure jsou kolekce logiku, vizualizace a data pořízení pravidel, které poskytují metriky seskupit kolem oblasti konkrétní problém.  Tento článek obsahuje seznam řešení pro správu k dispozici od společnosti Microsoft a podrobnosti o jejich metoda a frekvenci shromažďování dat.
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
ms.date: 05/01/2018
ms.author: bwren
ms.openlocfilehash: cab92187c6c36dc4670e202b15b35db8ad510239
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Podrobnosti o shromažďování dat pro řešení pro správu v Azure
Tento článek obsahuje seznam [řešení pro správu](monitoring-solutions.md) od společnosti Microsoft k dispozici s odkazy na jejich podrobnou dokumentaci.  Také obsahuje informace o jejich metoda a frekvenci shromažďování dat do analýzy protokolů.  K identifikaci různých řešení, které jsou k dispozici a lépe porozumět požadavkům připojení a toku dat pro řešení pro správu jiný, můžete použít informace v tomto článku. 

## <a name="list-of-management-solutions"></a>Seznam řešení pro správu

Následující tabulka uvádí [řešení pro správu](monitoring-solutions.md) v Azure od společnosti Microsoft. Položku ve sloupci znamená, že řešení shromažďuje data do analýzy protokolů pomocí dané metody.  Pokud řešení nemá žádné sloupce vybraná, pak zapíše přímo k Log Analytics z jiné služby Azure. Pomocí následujícího odkazu pro každé z nich k jeho podrobnou dokumentaci pro další informace.

Vysvětlení sloupců jsou následující:

- **Agent monitorování Microsoft** -agenta použitý v systému Windows a Linux ke spuštění sady Management pack z SCOM a Správa řešení z Azure. V této konfiguraci agenta je připojený přímo k Log Analytics bez připojení ke skupině pro správu nástroje Operations Manager. 
- **Nástroj Operations Manager** -identické agenta jako agenta Microsoft monitoring agent. V této konfiguraci je [připojené ke skupině pro správu nástroje Operations Manager](../log-analytics/log-analytics-om-agents.md) který je připojený k analýze protokolů. 
-  **Úložiště Azure** -řešení shromažďuje data z účtu úložiště Azure. 
- **Nástroj Operations Manager vyžaduje?** Je nezbytná pro shromažďování dat do řešení pro správu – připojená skupina správy nástroje Operations Manager. 
- **Dat agenta nástroje Operations Manager odeslána prostřednictvím skupiny pro správu** – Pokud je agent [připojené ke skupině pro správu SCOM](../log-analytics/log-analytics-om-agents.md), pak odeslání dat k analýze protokolů ze serveru pro správu. V takovém případě agenta nemusí připojit přímo k Log Analytics. Pokud není toto políčko zaškrtnuto, data se pak odešlou od agenta přímo k Log Analytics i v případě, že agent je připojený ke skupině pro správu SCOM. buď bude muset být schopni komunikovat k analýze protokolů prostřednictvím [OMS brány](../log-analytics/log-analytics-oms-gateway.md).
- **Frekvence kolekce** -určuje četnost, že data jsou shromažďována řešení pro správu. 



| **Řešení pro správu** | **Platforma** | **Agent monitorování Microsoft** | **Agent nástroje Operations Manager** | **Úložiště Azure** | **Nástroj Operations Manager vyžaduje?** | **Dat agenta nástroje Operations Manager odeslána prostřednictvím skupiny pro správu** | **Frekvence kolekce** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Activity Log Analytics](../log-analytics/log-analytics-activity.md) | Azure | | | | | | v oznámení |
| [Posouzení AD](../log-analytics/log-analytics-ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dní |
| [Stav replikace AD](../log-analytics/log-analytics-ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 dní |
| [Stav agenta](../operations-management-suite/oms-solution-agenthealth.md) | Windows a Linux | &#8226; | &#8226; | | | &#8226; | 1 minuta |
| [Výstrahy správy](../log-analytics/log-analytics-solution-alert-management.md) (Nagios) |Linux |&#8226; | | | | |v případě přijetí |
| [Výstrahy správy](../log-analytics/log-analytics-solution-alert-management.md) (Zabbix) |Linux |&#8226; | | | | |1 minuta |
| [Výstrahy správy](../log-analytics/log-analytics-solution-alert-management.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 minuty |
| [Azure Site Recovery](../site-recovery/site-recovery-overview.md) | Azure | | | | | | neuvedeno |
| [Konektor služby Statistika aplikace (Preview)](../log-analytics/log-analytics-app-insights-connector.md) | Azure | | | |  |  | v oznámení |
| [Automatizace hybridní pracovní proces](../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | neuvedeno |
| [Analýza brány Azure aplikace](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | v oznámení |
| **Řešení pro správu** | **Platforma** | **Agent monitorování Microsoft** | **Agent nástroje Operations Manager** | **Úložiště Azure** | **Nástroj Operations Manager vyžaduje?** | **Dat agenta nástroje Operations Manager odeslána prostřednictvím skupiny pro správu** | **Frekvence kolekce** |
| [Skupina zabezpečení sítě Azure Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | v oznámení |
| [Analýza Azure SQL (Preview)](../log-analytics/log-analytics-azure-sql.md) | Windows | | | | | | 1 minuta |
| [Backup](../backup/backup-introduction-to-azure-backup.md) | Azure |  |  |  |  |  | neuvedeno |
| [Kapacitu a výkon (Preview)](../log-analytics/log-analytics-capacity.md) |Windows |&#8226; |&#8226; | | |&#8226; |v případě přijetí |
| [Sledování změn](../log-analytics/log-analytics-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |každou hodinu |
| [Sledování změn](../log-analytics/log-analytics-change-tracking.md) |Linux |&#8226; | | | | |každou hodinu |
| [Containers](../log-analytics/log-analytics-containers.md) | Windows a Linux | &#8226; | &#8226; |  |  |  | 3 minuty |
| [Analýza služby Key Vault](../log-analytics/log-analytics-azure-key-vault.md) |Windows | | | | | |v oznámení |
| [Posouzení malwaru](../log-analytics/log-analytics-malware.md) |Windows |&#8226; |&#8226; | | |&#8226; |každou hodinu |
| [Sledování výkonu sítě](../log-analytics/log-analytics-network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP metodou handshake každých 5 sekund, data odeslána každé 3 minuty |
| [Analýza Office 365 (Preview)](../operations-management-suite/oms-solution-office-365.md) |Windows | | | | | |v oznámení |
| **Řešení pro správu** | **Platforma** | **Agent monitorování Microsoft** | **Agent nástroje Operations Manager** | **Úložiště Azure** | **Nástroj Operations Manager vyžaduje?** | **Dat agenta nástroje Operations Manager odeslána prostřednictvím skupiny pro správu** | **Frekvence kolekce** |
| [Zabezpečení a Audit](../operations-management-suite/oms-security-getting-started.md) (Syslog) | Linux | &#8226; | | |  |  | v případě přijetí |
| [Zabezpečení a Audit](../operations-management-suite/oms-security-getting-started.md) (protokolů událostí zabezpečení) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | v případě přijetí |
| [Zabezpečení a Audit](../operations-management-suite/oms-security-getting-started.md) (brány Firewall protokoly) |Windows |&#8226; |&#8226; |  |  |  |v případě přijetí |
| [Služba Fabric Analytics (Preview)](../log-analytics/log-analytics-service-fabric.md) |Windows | | |&#8226; | | |5 minut |
| [Mapa služeb](../operations-management-suite/operations-management-suite-service-map.md) | Windows a Linux | &#8226; | &#8226; |  |  |  | 15 sekund |
| [Posouzení SQL](../log-analytics/log-analytics-sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 dní |
| [SurfaceHub](../log-analytics/log-analytics-surface-hubs.md) |Windows |&#8226; | | | | |v případě přijetí |
| [System Center Operations Manager Assessment (Preview)](../log-analytics/log-analytics-scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | sedm dní |
| [Správa aktualizací](../operations-management-suite/oms-solution-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |aspoň 2 časy denně a 15 minut po instalaci aktualizace |
| [Upgrade Readiness](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 dny |
| [VMware monitorování (Preview)](../log-analytics/log-analytics-vmware.md) | Linux | &#8226; |  |  |  |  | 3 minuty |
| [Data kabelové sítě 2.0 (Preview)](../log-analytics/log-analytics-wire-data.md) |Windows (2012 R2 / 8.1 nebo novější) |&#8226; |&#8226; | | | | 1 minuta |




## <a name="next-steps"></a>Další postup
* Zjistěte, jak [vytváření dotazů](../log-analytics/log-analytics-log-searches.md) analyzovat data shromažďovaná společností řešení pro správu.
