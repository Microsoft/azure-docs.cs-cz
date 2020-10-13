---
title: Správa životního cyklu virtuálních počítačů řešení Azure VMware
description: Naučte se spravovat všechny aspekty životního cyklu virtuálních počítačů řešení Azure VMware pomocí Microsoft Azurech nativních nástrojů.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 452fa6bf4610c9d0dcb46587242e8d6df9cebd0d
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950359"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Správa životního cyklu virtuálních počítačů řešení Azure VMware

Microsoft Azure nativní nástroje vám umožní monitorovat a spravovat virtuální počítače v prostředí Azure. Ještě umožňují také monitorovat a spravovat vaše virtuální počítače v řešení Azure VMware a vašich místních virtuálních počítačích. V tomto přehledu se podíváme na integrované architektury monitorování Azure a o tom, jak můžete pomocí svých nativních nástrojů spravovat vaše virtuální počítače s řešením Azure VMware během svého životního cyklu.

## <a name="benefits"></a>Výhody

- Nativní služby Azure je možné použít ke správě virtuálních počítačů v hybridním prostředí (Azure, řešení Azure VMware a místní).
- Integrované monitorování a viditelnost vašich řešení Azure, Azure VMware a místních virtuálních počítačů.
- Pomocí Azure Update Management v Azure Automation můžete spravovat aktualizace operačního systému pro počítače se systémem Windows i Linux. 
- Azure Security Center poskytuje rozšířenou ochranu před internetovými útoky, včetně:
    - Monitorování integrity souborů
    - Výstrahy zabezpečení nesouborového souboru
    - Posouzení opravy operačního systému
    - Vyhodnocení nezabezpečených konfigurací zabezpečení
    - Posouzení služby Endpoint Protection 
- Snadné nasazení Microsoft Monitoring Agent (MMA) pomocí ARC Azure pro nové virtuální počítače. 
- Pracovní prostor Log Analytics v Azure Monitor umožňuje shromažďování protokolů a čítače výkonu pomocí MMA nebo rozšíření. Shromážděte data a protokoly do jediného bodu a prezentují data různým nativním službám Azure. 
- Přidané výhody Azure Monitor zahrnují: 
    - Bezproblémové monitorování 
    - Lepší viditelnost infrastruktury 
    - Rychlá oznámení 
    - Automatické řešení 
    - Efektivita nákladů 

## <a name="integrated-azure-monitoring-architecture"></a>Integrovaná architektura monitorování Azure

Následující diagram znázorňuje integrovanou architekturu monitorování pro virtuální počítače řešení Azure VMware.

![Integrovaná architektura monitorování Azure](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="integrating-and-deploying-azure-native-services"></a>Integrace a nasazení nativních služeb Azure

**Azure ARC** rozšiřuje správu Azure do jakékoli infrastruktury, včetně řešení Azure VMware, místních nebo jiných cloudových platforem. Azure ARC se dá nasadit tak, že se cluster Azure Kubernetes Service (AKS) nainstaluje do prostředí řešení Azure VMware. Další informace najdete v tématu [připojení clusteru Kubernetes s povoleným ARC Azure](../azure-arc/kubernetes/connect-cluster.md).

Virtuální počítače řešení Azure VMware je možné monitorovat prostřednictvím MMA (také označovaného jako agent Log Analytics agenta nebo OMS Linux). MMA se dá nainstalovat automaticky, když se virtuální počítače zřídí prostřednictvím pracovních postupů životního cyklu virtuálního počítače ARC. MMA se dá nainstalovat taky při nasazování virtuálních počítačů ze šablony v vCenter. znovu s virtuálními počítači zřízenými prostřednictvím pracovních postupů ARC. Všechny zřízené virtuální počítače řešení Azure VMware můžou mít MMA nainstalované a odesílat protokoly do pracovního prostoru Azure Log Analytics. Další informace najdete v tématu [Přehled agenta Log Analytics](../azure-monitor/platform/log-analytics-agent.md).

**Log Analytics pracovní prostor** umožňuje shromažďování protokolů a shromažďování čítače výkonu pomocí MMA nebo rozšíření. Pokud chcete vytvořit pracovní prostor Log Analytics, přečtěte si téma [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/learn/quick-create-workspace.md).
- Pokud chcete přidat virtuální počítače s Windows do vašeho pracovního prostoru Log Analytics, přečtěte si téma [instalace agenta Log Analytics do počítačů se systémem Windows](../azure-monitor/platform/agent-windows.md).
- Pokud chcete přidat virtuální počítače se systémem Linux do pracovního prostoru Log Analytics, přečtěte si téma [instalace agenta Log Analytics do počítačů se systémem Linux](../azure-monitor/platform/agent-linux.md).

**Azure Update Management** v Azure Automation spravují aktualizace operačního systému pro počítače s Windows a Linux v hybridním prostředí. Monitoruje dodržování předpisů a přepošle výstrahy na odchylky oprav, které Azure Monitor k nápravě. Azure Update Management se musí připojit k pracovnímu prostoru Log Analytics a pomocí uložených dat vyhodnotit stav aktualizací na vašich virtuálních počítačích.
- Pokud chcete přidat Log Analytics do Azure Update Management, musíte nejprve [vytvořit Azure Automation účet](../automation/automation-create-standalone-account.md).
- Pokud chcete propojit pracovní prostor Log Analytics s vaším účtem Automation, přečtěte si téma [pracovní prostor Log Analytics a účet Automation](../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).
- Pokud chcete povolit Azure Update Management pro vaše virtuální počítače, přečtěte si téma [povolení Update Management z účtu Automation](../automation/update-management/update-mgmt-enable-automation-account.md).
- Po přidání virtuálních počítačů do Azure Update Management můžete [nasadit aktualizace na virtuální počítače a zkontrolovat výsledky](../automation/update-management/update-mgmt-deploy-updates.md). 

**Azure Security Center** poskytuje rozšířenou ochranu před hrozbami napříč vašimi hybridními úlohami v cloudu i místně. Vyhodnotí ohrožení zabezpečení virtuálních počítačů řešení Azure VMware a v případě potřeby vyvolává výstrahy. Tyto výstrahy zabezpečení je možné přeslat do Azure Monitor pro řešení.
- Azure Security Center nevyžaduje nasazení. Další informace najdete v seznamu [podporovaných funkcí pro virtuální počítače](../security-center/security-center-services.md).
- Pokud chcete přidat virtuální počítače řešení VMware Azure a virtuální počítače mimo Azure do Azure Security Center, přečtěte si téma připojení [počítačů s Windows k Azure Security Center](../security-center/quickstart-onboard-machines.md) a [zprovoznění počítačů se systémem Linux do Azure Security Center](../security-center/quickstart-onboard-machines.md).
- Po přidání virtuálních počítačů Azure Security Center analyzuje stav zabezpečení prostředků, aby identifikoval potenciální ohrožení zabezpečení. Nabízí také doporučení na kartě Přehled. Další informace najdete v tématu [doporučení zabezpečení v Azure Security Center](../security-center/security-center-recommendations.md).
- V Azure Security Center můžete definovat zásady zabezpečení. Informace o konfiguraci zásad zabezpečení najdete v tématu [práce se zásadami zabezpečení](../security-center/tutorial-security-policy.md).

**Azure monitor** je komplexní řešení pro shromažďování, analýzu a provoz telemetrie z vašeho cloudového a místního prostředí. Nevyžaduje žádné nasazení.
- Azure Monitor umožňuje shromažďovat data z různých zdrojů a monitorovat a analyzovat je. Další informace najdete v tématu [zdroje dat monitorování pro Azure monitor](../azure-monitor/platform/data-sources.md).
- Můžete také shromažďovat různé typy dat pro analýzy, vizualizaci a upozorňování. Další informace najdete v tématu [Azure monitor datovou platformou](../azure-monitor/platform/data-platform.md).
- Pokud chcete nakonfigurovat Azure Monitor pomocí pracovního prostoru Log Analytics, přečtěte si téma [konfigurace log Analyticsho pracovního prostoru pro Azure monitor pro virtuální počítače](../azure-monitor/insights/vminsights-configure-workspace.md).
- Můžete vytvořit pravidla upozornění k identifikaci problémů ve vašem prostředí, jako je třeba vysoké využití prostředků, chybějící opravy, nedostatek místa na disku a prezenční signál virtuálních počítačů. Můžete také nastavit automatizovanou odezvu na zjištěné události odesláním výstrahy do nástrojů pro správu IT služeb (ITSM). Oznámení o detekci výstrah lze také odeslat e-mailem. Chcete-li vytvořit taková pravidla, přečtěte si téma:
    - [Umožňuje vytvářet, zobrazovat a spravovat výstrahy metrik pomocí Azure monitor](../azure-monitor/platform/alerts-metric.md).
    - [Umožňuje vytvářet, zobrazovat a spravovat výstrahy protokolu pomocí Azure monitor](../azure-monitor/platform/alerts-log.md).
    - [Pravidla akcí](../azure-monitor/platform/alerts-action-rules.md) pro nastavení automatizovaných akcí a oznámení.
    - [Připojte Azure k nástrojům pro ITSM pomocí IT Service Management Connectoru](../azure-monitor/platform/itsmc-overview.md).

**Platforma jako služba (PaaS) Azure** je prostředí pro vývoj a nasazení v cloudu s prostředky, které umožňují dodávat cloudové aplikace. Například můžete integrovat Azure SQL Database s virtuálními počítači řešení Azure VMware. Výstrahy SQL se pak můžou korelovat s výstrahami virtuálních počítačů řešení Azure VMware. Řekněme například, že SQL Databaseá fáze aplikace je v rámci Azure PAAS a vrstva webové aplikace stejné aplikace je hostována ve vašich virtuálních počítačích řešení Azure VMware. Výstrahy databáze je pak možné korelovat s výstrahami webových aplikací. Řešení potíží je zjednodušené díky jednotné integrované viditelnosti Azure, řešení Azure VMware a místních virtuálních počítačů.