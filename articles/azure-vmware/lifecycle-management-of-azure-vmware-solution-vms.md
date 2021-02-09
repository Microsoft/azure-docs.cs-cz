---
title: Správa životního cyklu virtuálních počítačů řešení Azure VMware
description: Naučte se spravovat všechny aspekty životního cyklu virtuálních počítačů řešení Azure VMware pomocí Microsoft Azurech nativních nástrojů.
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: d8224a37e46b336ebf889fe1c075930f34f10ca4
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988538"
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
- Snadno nasaďte agenta Log Analytics s využitím podpory rozšíření virtuálních počítačů na serverech s podporou ARC Azure pro nové a stávající virtuální počítače. 
- Pracovní prostor Log Analytics v Azure Monitor umožňuje shromažďování protokolů a čítače výkonu pomocí agenta Log Analytics nebo rozšíření. Shromážděte data a protokoly do jediného bodu a prezentují data různým nativním službám Azure. 
- Přidané výhody Azure Monitor zahrnují: 
    - Bezproblémové monitorování 
    - Lepší viditelnost infrastruktury 
    - Rychlá oznámení 
    - Automatické řešení 
    - Efektivita nákladů 

## <a name="integrated-azure-monitoring-architecture"></a>Integrovaná architektura monitorování Azure

Následující diagram znázorňuje integrovanou architekturu monitorování pro virtuální počítače řešení Azure VMware.

![Integrovaná architektura monitorování Azure](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>Než začnete

Pokud s Azure začínáte nebo neznáte některou z výše uvedených služeb, přečtěte si následující články:

- [Přehled ověřování účtů služby Automation](../automation/automation-security-overview.md)
- [Návrh Azure Monitorch protokolů nasazení](../azure-monitor/platform/design-logs-deployment.md) a [Azure monitor](../azure-monitor/overview.md)
- [Plánování](../security-center/security-center-planning-and-operations-guide.md) a [podporované platformy](../security-center/security-center-os-coverage.md) pro Azure Security Center
- [Povolit Azure Monitor pro virtuální počítače – přehled](../azure-monitor/insights/vminsights-enable-overview.md)
- [Co jsou servery s podporou ARC Azure?](../azure-arc/servers/overview.md) a [co je Kubernetes s podporou Azure ARC?](../azure-arc/kubernetes/overview.md)
- [Přehled Update Managementu](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>Integrace a nasazení nativních služeb Azure

### <a name="enable-azure-update-management"></a>Povolit Azure Update Management

Azure Update Management v Azure Automation spravují aktualizace operačního systému pro počítače s Windows a Linux v hybridním prostředí. Monitoruje dodržování předpisů a přepošle výstrahy na odchylky oprav, které Azure Monitor k nápravě. Azure Update Management se musí připojit k pracovnímu prostoru Log Analytics a pomocí uložených dat vyhodnotit stav aktualizací na vašich virtuálních počítačích.

1.  Než budete moct přidat Log Analytics do Azure Update Management, musíte nejprve [vytvořit Azure Automation účet](../automation/automation-create-standalone-account.md). Pokud dáváte přednost vytvoření účtu pomocí šablony, přečtěte si téma [Vytvoření účtu Automation pomocí šablony Azure Resource Manager](../automation/quickstart-create-automation-account-template.md).

2. **Log Analytics pracovní prostor** umožňuje shromažďování protokolů a shromažďování čítačů výkonu pomocí agenta Log Analytics nebo rozšíření. Pokud chcete vytvořit pracovní prostor Log Analytics, přečtěte si téma [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/learn/quick-create-workspace.md). Pokud budete chtít, můžete také vytvořit pracovní prostor prostřednictvím rozhraní příkazového [řádku](../azure-monitor/learn/quick-create-workspace-cli.md), [PowerShellu](../azure-monitor/platform/powershell-workspace-configuration.md)nebo [šablony Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md).

3. Pokud chcete povolit Azure Update Management pro vaše virtuální počítače, přečtěte si téma [povolení Update Management z účtu Automation](../automation/update-management/enable-from-automation-account.md). V tomto procesu propojíte Log Analytics pracovní prostor s vaším účtem Automation. 
 
4. Po přidání virtuálních počítačů do Azure Update Management můžete [nasadit aktualizace na virtuální počítače a zkontrolovat výsledky](../automation/update-management/deploy-updates.md). 

### <a name="enable-azure-security-center"></a>Povolit Azure Security Center

Azure Security Center poskytuje rozšířenou ochranu před hrozbami napříč vašimi hybridními úlohami v cloudu i místně. Vyhodnotí ohrožení zabezpečení virtuálních počítačů řešení Azure VMware a v případě potřeby vyvolává výstrahy. Tyto výstrahy zabezpečení je možné přeslat do Azure Monitor pro řešení.

Azure Security Center nevyžaduje nasazení. Další informace najdete v seznamu [podporovaných funkcí pro virtuální počítače](../security-center/security-center-services.md).

1. Pokud chcete přidat virtuální počítače řešení VMware Azure a virtuální počítače mimo Azure do Security Center, přečtěte si téma [rychlý Start: nastavení Azure Security Center](../security-center/security-center-get-started.md). 

2. Po přidání virtuálních počítačů řešení Azure VMware nebo virtuálních počítačů z prostředí mimo Azure povolte v Security Center Azure Defender. Security Center vyhodnotí virtuální počítače pro potenciální problémy se zabezpečením. Nabízí také doporučení na kartě Přehled. Další informace najdete v tématu [doporučení zabezpečení v Azure Security Center](../security-center/security-center-recommendations.md).

3. V Azure Security Center můžete definovat zásady zabezpečení. Informace o konfiguraci zásad zabezpečení najdete v tématu [práce se zásadami zabezpečení](../security-center/tutorial-security-policy.md).

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Připojení virtuálních počítačů k serverům s podporou ARC Azure

Azure ARC rozšiřuje správu Azure do jakékoli infrastruktury, včetně řešení Azure VMware, místních nebo jiných cloudových platforem.

- Informace o povolení serverů s podporou ARC Azure pro více virtuálních počítačů se systémem Windows nebo Linux najdete v tématu věnovaném [připojení hybridních počítačů k Azure ve velkém měřítku](../azure-arc/servers/onboard-service-principal.md).

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Připojení hybridních clusterů Kubernetes s povoleným Kubernetesem ARC

Cluster Kubernetes hostovaný v prostředí řešení Azure VMware můžete připojit pomocí Kubernetes s povoleným ARC Azure. 

- Další informace najdete v tématu [Vytvoření instančního objektu s podporou ARC Azure](../azure-arc/kubernetes/create-onboarding-service-principal.md).

### <a name="deploy-the-log-analytics-agent"></a>Nasazení agenta Log Analytics

Virtuální počítače řešení Azure VMware je možné monitorovat prostřednictvím agenta Log Analytics (také označovaného jako Microsoft Monitoring Agent (MMA) nebo agent OMS Linux). Během povolování Azure Automation Update Management jste již vytvořili Log Analytics pracovní prostor.

- Nasaďte agenta Log Analytics pomocí [podpory rozšíření virtuálního počítače pro servery s podporou ARC Azure](../azure-arc/servers/manage-vm-extensions.md).

### <a name="enable-azure-monitor"></a>Povolit Azure Monitor

Azure Monitor je komplexní řešení pro shromažďování, analýzu a provoz telemetrie z vašeho cloudového a místního prostředí. Nevyžaduje žádné nasazení. Pomocí Azure Monitor můžete monitorovat výkon hostovaného operačního systému a zjišťovat a mapovat závislosti aplikací pro řešení Azure VMware nebo místní virtuální počítače.

- Azure Monitor umožňuje shromažďovat data z různých zdrojů a monitorovat a analyzovat je. Další informace najdete v tématu [zdroje dat monitorování pro Azure monitor](../azure-monitor/platform/data-sources.md).

- Shromažďování různých typů dat pro analýzu, vizualizaci a upozorňování. Další informace najdete v tématu [Azure monitor datovou platformou](../azure-monitor/platform/data-platform.md).

- Pokud chcete nakonfigurovat Azure Monitor pomocí pracovního prostoru Log Analytics, přečtěte si téma [konfigurace log Analyticsho pracovního prostoru pro Azure monitor pro virtuální počítače](../azure-monitor/insights/vminsights-configure-workspace.md).

- Můžete vytvořit pravidla upozornění k identifikaci problémů ve vašem prostředí, jako je třeba vysoké využití prostředků, chybějící opravy, nedostatek místa na disku a prezenční signál virtuálních počítačů. Můžete také nastavit automatizovanou odezvu na zjištěné události odesláním výstrahy do nástrojů pro správu IT služeb (ITSM). Oznámení o detekci výstrah lze také odeslat e-mailem. Chcete-li vytvořit taková pravidla, přečtěte si téma:
    - [Umožňuje vytvářet, zobrazovat a spravovat výstrahy metrik pomocí Azure monitor](../azure-monitor/platform/alerts-metric.md).
    - [Umožňuje vytvářet, zobrazovat a spravovat výstrahy protokolu pomocí Azure monitor](../azure-monitor/platform/alerts-log.md).
    - [Pravidla akcí](../azure-monitor/platform/alerts-action-rules.md) pro nastavení automatizovaných akcí a oznámení.
    - [Připojte Azure k nástrojům pro ITSM pomocí IT Service Management Connectoru](../azure-monitor/platform/itsmc-overview.md).
    
 ## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s používáním nativních nástrojů Azure pro správu virtuálních počítačů Azure VMware v průběhu svého životního cyklu, se můžete seznámit s těmito řešeními:

- [Ochrana virtuálních počítačů řešení Azure VMware pomocí Azure Security Center](azure-security-integration.md).
- [Nastavování Azure Backup Server pro řešení Azure VMware](set-up-backup-server-for-azure-vmware-solution.md).
- [Integrace řešení Azure VMware do architektury hub a paprsků](concepts-hub-and-spoke.md).
