---
title: Základní hodnoty zabezpečení Azure pro Virtual Machine Scale Sets
description: Základní Virtual Machine Scale Sets zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d55e53ba455a4b91b4f57ea08b250320a5467c2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89398440"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Základní hodnoty zabezpečení Azure pro Virtual Machine Scale Sets

Základní plán zabezpečení Azure pro Virtual Machine Scale Sets obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: Když vytváříte virtuální počítač Azure, musíte vytvořit virtuální síť nebo použít stávající virtuální síť a nakonfigurovat virtuální počítač s podsítí. Zajistěte, aby všechny nasazené podsítě používaly skupinu zabezpečení sítě s ovládacími prvky pro přístup k síti, které jsou specifické pro vaše aplikace a důvěryhodné porty a zdroje.

Případně, pokud máte konkrétní případ použití pro centralizovanou bránu firewall, Azure Firewall lze také použít ke splnění těchto požadavků.

* [Síťové služby pro škálovací sady virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking)

* [Vytvoření Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Vytvoření NSG s konfigurací zabezpečení](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Jak nasadit a nakonfigurovat Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Pokyny**: pomocí Azure Security Center identifikujte a sledujte doporučení o ochraně sítě, která vám pomůžou zabezpečit vaše prostředky virtuálních počítačů Azure v Azure. Povolte protokoly toku NSG a odešlete protokoly do účtu úložiště pro audit provozu pro virtuální počítače na neobvyklou aktivitu.

* [Jak povolit protokoly toku NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné**materiály: Pokud k hostování webových aplikací používáte sadu škálování virtuálních počítačů (VMSS), použijte k omezení síťového provozu, portů a protokolů skupinu zabezpečení sítě (NSG) v podsíti VMSS. Při konfiguraci skupin zabezpečení sítě postupujte podle nejnižšího privilegovaného přístupu k síti, aby bylo možné do aplikace pouze vyžadovat požadovaný provoz.

Můžete také nasadit Firewall webových aplikací Azure (WAF) před kritickými webovými aplikacemi pro další kontrolu příchozího provozu. Povolte nastavení diagnostiky pro protokoly WAF a ingestování do účtu úložiště, centra událostí nebo Log Analytics pracovního prostoru.

* [Síťové služby pro škálovací sady virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking)

* [Vytvoření aplikační brány pomocí brány firewall webových aplikací pomocí Azure Portal](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-web-application-firewall-portal)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné**materiály: ve virtuálních sítích povolte distribuovanou službu DOS (DDoS) standard Protection, abyste se mohli chránit před útoky DDoS. Pomocí Azure Security Center integrované analýzy hrozeb můžete sledovat komunikaci se známými škodlivými IP adresami. Nakonfigurujte Azure Firewall na všech segmentech Virtual Network, s povolenou funkcí Analýza hrozeb a nakonfigurované na "výstrahy a zamítnutí" pro škodlivý síťový provoz.

K omezení rizika Windows Virtual Machines na schválené IP adresy po omezenou dobu můžete použít Azure Security Center jenom v čase. Pomocí Azure Security Center adaptivního posílení zabezpečení sítě můžete také doporučit konfigurace NSG, které omezují porty a zdrojové IP adresy na základě skutečného provozu a analýzy hrozeb.

* [Jak nakonfigurovat DDoS Protection](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Postup nasazení Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Pochopení Azure Security Center integrované analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

* [Pochopení Azure Security Center adaptivního posílení zabezpečení sítě](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

* [Pochopení Azure Security Center k síťovému Access Control v čase](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Pokyny**: můžete nahrávat protokoly toku NSG do účtu úložiště a vygenerovat záznamy toků pro Azure Virtual Machines. Při zkoumání aktivity neobvyklé můžete povolit zachytávání paketů Network Watcher, aby bylo možné zkontrolovat síťový provoz pro neobvyklou a neočekávanou aktivitu.

* [Jak povolit protokoly toku NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Postup povolení Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné**materiály: kombinací zachycení paketů, které poskytuje Network Watcher a nástroje Open Source ID, můžete zjišťovat neoprávněné vniknutí do sítě pro nejrůznější hrozby. V případě potřeby můžete také nasadit Azure Firewall na segmenty Virtual Network, s povolenou funkcí Analýza hrozeb a nakonfigurovanou na "výstrahy a zamítnutí" pro škodlivý síťový provoz.

* [Zjišťování neoprávněných vniknutí k síti pomocí Network Watcher a open source nástrojů](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools)

* [Postup nasazení Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Postup konfigurace výstrah pomocí Azure Firewall](https://docs.microsoft.com/azure/firewall/threat-intel)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Pokyny**: Pokud k hostování webových aplikací používáte VMSS (Virtual Machine Scale set), můžete nasadit Azure Application Gateway pro webové aplikace s POVOLENým protokolem HTTPS/SSL pro důvěryhodné certifikáty. Pomocí Azure Application Gateway nasměrujete webový provoz aplikace na konkrétní prostředky tím, že do portů přiřadíte naslouchací procesy, vytvoříte pravidla a přidáte prostředky do back-endu, jako je VMSS atd.

* [Postup nasazení Application Gateway](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

* [Postup konfigurace Application Gateway pro použití protokolu HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

* [Vytvoření škálovací sady, která odkazuje na aplikační bránu](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#create-a-scale-set-that-references-an-application-gateway)

* [Princip vyrovnávání zatížení vrstvy 7 pomocí bran webových aplikací Azure](https://docs.microsoft.com/azure/application-gateway/overview)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné**materiály: použití značek Virtual Network služby k definování řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall nakonfigurovaných pro virtuální počítače Azure. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

* [Pochopení a používání značek služeb](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure Virtual Machine Scale Sets pomocí Azure Policy. Pomocí Azure modrotisky můžete také zjednodušit rozsáhlá nasazení virtuálních počítačů Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, přiřazení rolí a přiřazení Azure Policy v jediné definici podrobného plánu. Můžete použít podrobný plán na předplatná a povolit správu prostředků prostřednictvím správy verzí podrobného plánu.

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Seznamte se se šablonami sady škálování virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-start)

* [Ukázky Azure Policy pro sítě](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [Vytvoření Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné**materiály: můžete použít značky pro skupiny zabezpečení sítě (NSG) a další prostředky související se zabezpečením sítě a tokem provozu nakonfigurovanými pro vaše virtuální počítače s Windows. Pro jednotlivá pravidla NSG použijte pole Popis a určete obchodní potřebu nebo dobu trvání pro všechna pravidla, která umožňují provoz do/ze sítě.

* [Vytváření a používání značek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Vytvoření Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Vytvoření NSG s konfigurací zabezpečení](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat změny konfigurací síťových prostředků, které se týkají vaší sady škálování virtuálních počítačů Azure. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny kritických nastavení sítě nebo prostředků.

Použijte Azure Policy k ověření (nebo nápravě) konfigurací síťových prostředků souvisejících se sadou škálování virtuálního počítače.

* [Jak zobrazit a načíst události protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Vytváření výstrah v Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Ukázky Azure Policy pro sítě](https://docs.microsoft.com/azure/governance/policy/samples/#network)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Pokyny**: Microsoft udržuje časové zdroje pro prostředky Azure, ale máte možnost spravovat nastavení synchronizace času pro Virtual Machines.

* [Jak nakonfigurovat časovou synchronizaci pro výpočetní prostředky Azure Windows](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

* [Jak nakonfigurovat časovou synchronizaci pro výpočetní prostředky Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné**materiály: protokoly aktivit se dají použít k auditování operací a akcí prováděných v prostředcích sady škálování virtuálních počítačů. Protokol aktivit obsahuje všechny operace zápisu (PUT, POST, DELETE) pro vaše prostředky s výjimkou operací čtení (GET). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

Můžete povolit a začlenit data protokolu vytvořená z protokolů aktivit Azure nebo prostředků virtuálních počítačů do Azure Sentinel nebo SIEM třetí strany pro správu centrálních protokolů zabezpečení.

Použijte Azure Security Center k poskytnutí monitorování protokolu událostí zabezpečení pro Azure Virtual Machines. Daný objem dat, který protokol událostí zabezpečení generuje, není ve výchozím nastavení uložen.

Pokud vaše organizace chce zachovat data protokolu událostí zabezpečení z virtuálního počítače, může být uložená v pracovním prostoru Log Analytics v požadované vrstvě shromažďování dat nakonfigurované v rámci Azure Security Center.

* [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Jak připojit Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

* [Shromažďování dat v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Jak monitorovat virtuální počítače v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné**materiály: protokoly aktivit se dají použít k auditování operací a akcí prováděných v prostředcích sady škálování virtuálních počítačů. Protokol aktivit obsahuje všechny operace zápisu (PUT, POST, DELETE) pro vaše prostředky s výjimkou operací čtení (GET). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

Povolte shromažďování diagnostických dat hostovaného operačního systému nasazením diagnostického rozšíření na vašem Virtual Machines (VM). Pomocí diagnostického rozšíření můžete shromažďovat diagnostická data, jako jsou protokoly aplikací nebo čítače výkonu z virtuálního počítače Azure.

Pro pokročilou představu o aplikacích a službách, které podporuje sada škálování virtuálního počítače Azure, můžete povolit jak Azure Monitor pro virtuální počítače, tak Application Insights. Pomocí Application Insights můžete monitorovat aplikaci a zachytit telemetrii, jako jsou požadavky HTTP, výjimky atd., takže můžete sladit problémy mezi virtuálními počítači a vaší aplikací.

* [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Zobrazení a načtení událostí protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Jak monitorovat virtuální počítače v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

* [Přehled služby Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné**materiály: použijte Azure Security Center k poskytnutí monitorování protokolu událostí zabezpečení pro Azure Virtual Machines. Daný objem dat, který protokol událostí zabezpečení generuje, není ve výchozím nastavení uložen.

Pokud vaše organizace chce zachovat data protokolu událostí zabezpečení z virtuálního počítače, může být uložená v pracovním prostoru Log Analytics v požadované vrstvě shromažďování dat nakonfigurované v rámci Azure Security Center.

* [Shromažďování dat v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Jak monitorovat virtuální počítače v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny**: Ujistěte se, že všechny účty úložiště nebo pracovní prostory Log Analytics používané pro ukládání protokolů virtuálních počítačů mají dobu uchování protokolu nastavenou v souladu s pravidly dodržování předpisů vaší organizace.

* [Jak monitorovat virtuální počítače v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

* [Jak nakonfigurovat dobu uchování Log Analytics pracovního prostoru](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů pro neobvyklé chování a pravidelné kontroly výsledků. Pomocí Azure Monitor můžete zkontrolovat protokoly a provádět dotazy na data protokolu.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany a monitorovat a kontrolovat protokoly.

* [Jak připojit Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Principy Log Analyticsho pracovního prostoru](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Jak provádět vlastní dotazy v Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné**materiály: použijte Azure Security Center nakonfigurované s Log Analytics pracovním prostorem pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech pro Azure Virtual Machines.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany a nastavit upozornění pro aktivitu neobvyklé.

* [Jak připojit Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Správa výstrah v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [Upozornění na data protokolu Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné**materiály: můžete použít Microsoft Anti-malware pro Azure Cloud Services a Virtual Machines a nakonfigurovat virtuální počítače s Windows, aby protokoloval události na účet Azure Storage. Nakonfigurujte Log Analytics pracovní prostor pro ingestování událostí z účtů úložiště a v případě potřeby vytvořte výstrahy. Použijte doporučení v Azure Security Center: "COMPUTE &amp; Apps". Pro virtuální počítače se systémem Linux budete potřebovat nástroj třetí strany pro detekci ohrožení zabezpečení proti malwaru.

* [Jak nakonfigurovat Microsoft Anti-malware pro Cloud Services a Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Jak povolit monitorování na úrovni hosta pro Virtual Machines](https://docs.microsoft.com/azure/cost-management/azure-vm-extended-metrics)

* [Pokyny pro připojování serverů se systémem Linux do Azure Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-linux-computer)

* [Následující odkaz poskytuje doporučené bezpečnostní pokyny Microsoftu, které můžou sloužit jako seznam kritérií pro vybraný software o ohrožení zabezpečení.](https://docs.microsoft.com/azure/virtual-machines/linux/security-recommendations)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Pokyny**: implementace řešení od jiného výrobce z Azure Marketplace pro řešení protokolování DNS podle potřeby vaší organizace.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Pokyny**: Azure Security Center poskytuje monitorování protokolu událostí zabezpečení pro Azure Virtual Machines (VM). Security Center zřídí Microsoft Monitoring Agent na všech podporovaných virtuálních počítačích Azure a všech nově vytvořených, pokud je povolené Automatické zřizování, nebo můžete agenta nainstalovat ručně. Agent umožňuje událost vytvoření procesu 4688 a pole CommandLine v události 4688. Nové procesy vytvořené na virtuálním počítači se zaznamenávají protokolem událostí a monitorují služby zjišťování Security Center.

Pro virtuální počítače se systémem Linux můžete ručně nakonfigurovat protokolování konzoly na bázi jednotlivých uzlů a použít k ukládání dat syslogy. K prohlížení protokolů a provádění dotazů na datech syslog z virtuálních počítačů Azure můžete také použít pracovní prostor Log Analytics Azure Monitor.

* [Shromažďování dat v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Jak provádět vlastní dotazy v Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Zdroje dat Syslogu ve službě Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a řízení přístupu](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné**materiály: i když je Azure Active Directory doporučená metoda pro správu uživatelského přístupu, můžou mít Azure Virtual Machines místní účty. Místní i doménové účty by měly být přezkoumány a spravovány, normálně s minimálními nároky. Kromě toho využijte Azure Privileged Identity Management pro účty pro správu, které se používají pro přístup k prostředkům virtuálních počítačů.

* [Informace pro místní účty jsou k dispozici na adrese](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

* [Informace o Privileged identity Manageru](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: Azure Virtual Machine Scale Set a Azure Active Directory nemají koncept výchozích hesel. Zákazník zodpovědný za aplikace třetích stran a služby Marketplace, které mohou používat výchozí hesla.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné**materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu, které mají přístup k vašim virtuálním počítačům. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu. Všechny účty správců používané pro přístup k prostředkům virtuálního počítače Azure můžete spravovat taky pomocí Azure Privileged Identity Management (PIM). Azure Privileged Identity Management poskytuje několik možností, jako je například zvýšení úrovně v čase, vyžadování Multi-Factor Authentication před předpokladem role a možností delegování, aby oprávnění byla k dispozici pouze pro určité časové rámce a vyžadovala schvalovatele.

* [Pochopení Azure Security Center identity a přístupu](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Informace o Privileged identity Manageru](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné**materiály: kdykoli je to možné, použijte jednotné přihlašování s Azure Active Directory, ale nekonfigurujte jednotlivé samostatné přihlašovací údaje na službu. Použijte Azure Security Center doporučení pro správu identit a přístupu.

* [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

* [Jak monitorovat identitu a přístup v rámci Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné**materiály: Povolte Azure AD MFA a sledujte Azure Security Center doporučení pro správu identit a přístupu.

* [Jak povolit vícefaktorové ověřování v Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Jak monitorovat identitu a přístup v rámci Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Doprovodné**materiály: použití privilegovaným přístupem (privilegovaných pracovních stanic) s MFA nakonfigurovaným pro přihlášení a konfiguraci prostředků Azure.

* [Další informace o pracovních stanicích s privilegovaným přístupem](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Jak povolit vícefaktorové ověřování v Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Pokyny**: použití Azure AD PRIVILEGED Identity Management (PIM) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Pomocí zjišťování rizik Azure AD můžete zobrazit výstrahy a sestavy týkající se rizikového chování uživatelů. V případě potřeby může zákazník ingestovat Azure Security Center výstrahy detekce rizik do Azure Monitor a nakonfigurovat vlastní výstrahy a oznámení pomocí skupin akcí.

* [Postup nasazení Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Principy zjišťování rizik Azure Security Center (podezřelá aktivita)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

* [Jak integrovat protokoly aktivit Azure do Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Jak nakonfigurovat skupiny akcí pro vlastní výstrahy a oznámení](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí Azure Active Directory zásad podmíněného přístupu a pojmenovaných umístění povolte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

* [Postup konfigurace pojmenovaných umístění v Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele. Spravované identity můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů v kódu. Váš kód, který běží na virtuálním počítači, může použít jeho spravovanou identitu k vyžádání přístupových tokenů pro služby, které podporují ověřování Azure AD.

* [Jak vytvořit a nakonfigurovat instanci Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

* [Přehled spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné**materiály: Azure AD poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Kromě toho můžete pomocí Azure Active Directory kontroly přístupu k identitám efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatele se může pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé. Při používání virtuálních počítačů Azure budete muset zkontrolovat místní skupiny zabezpečení a uživatele, abyste se ujistili, že neexistují žádné neočekávané účty, které by mohly ohrozit systém.

* [Jak používat recenze Azure identity Access](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Pokyny**: Konfigurace nastavení diagnostiky pro Azure Active Directory pro odesílání protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. K prohlížení protokolů a provádění dotazů na data protokolů z virtuálních počítačů Azure taky použijte Azure Monitor.

* [Principy Log Analyticsho pracovního prostoru](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Jak integrovat protokoly aktivit Azure do Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Jak provádět vlastní dotazy v Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Jak monitorovat virtuální počítače v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Doprovodné**materiály: použití funkcí rizika a ochrany Identity v Azure Active Directory ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce související s prostředky vašeho účtu úložiště. Pomocí služby Azure Sentinel byste měli povolit automatizované odezvy pro implementaci reakcí zabezpečení vaší organizace.

* [Jak zobrazit rizikové přihlašování Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Jak nakonfigurovat a povolit zásady rizik ochrany identity](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Jak připojit Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné**materiály: ve scénářích podpory, kde Microsoft potřebuje přístup k zákaznickým datům (například během žádosti o podporu), použijte Customer Lockbox pro virtuální počítače Azure ke kontrole a schválení nebo odmítnutí žádostí o přístup k datům zákazníků.

* [Porozumění Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné**materiály: používejte značky, které pomáhají při sledování virtuálních počítačů Azure, které ukládají nebo zpracovávají citlivé informace.

* [Vytváření a používání značek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Prostředky by měly být oddělené virtuální sítí nebo podsítí, vhodně označené a zabezpečené v rámci skupiny zabezpečení sítě (NSG) nebo pomocí Azure Firewall. Pokud Virtual Machines ukládáte nebo zpracováváte citlivá data, implementujte zásady a postupy pro jejich vypnutí, pokud se nepoužívají.

* [Vytvoření dalších předplatných Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Postup vytvoření Skupiny pro správu](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Vytváření a používání značek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Vytvoření Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Vytvoření NSG s konfigurací zabezpečení](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Postup nasazení Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Jak nakonfigurovat výstrahu nebo upozornění a odepřít pomocí Azure Firewall](https://docs.microsoft.com/azure/firewall/threat-intel)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné**materiály: implementace řešení třetí strany na hraničních sítích, které monitorují neoprávněný přenos citlivých informací a blokují takové přenosy při upozornění na odborníky na zabezpečení informací.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý, aby se mohl chránit před ztrátou a ztrátou dat zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

* [Pochopení ochrany zákaznických dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné**materiály: data přenášená do, z a mezi Virtual Machines (VM), která používají systém Windows, se šifrují mnoha různými způsoby v závislosti na povaze připojení, jako je třeba připojení k virtuálnímu počítači v relaci RDP nebo SSH.

Microsoft používá protokol TLS (Transport Layer Security) k ochraně dat při cestování mezi Cloud Services a zákazníky.

* [Šifrování v přenosech na virtuálních počítačích](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#in-transit-encryption-in-vms)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Pokyny**: k identifikaci všech citlivých informací uložených, zpracovávaných nebo odeslaných technologickými systémy organizace, včetně těch, které se nacházejí v lokalitě nebo u poskytovatele vzdálené služby, a aktualizaci inventáře citlivých informací organizace, použijte nástroj pro aktivní zjišťování třetí strany.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Doprovodné**materiály: použití řízení přístupu na základě role v Azure (Azure RBAC) umožňuje oddělení povinností v rámci týmu a udělení přístupu jenom uživatelům na virtuálním počítači (VM), které potřebují k provádění svých úloh. Místo udělení všech neomezených oprávnění na virtuálním počítači můžete použít jenom určité akce. Řízení přístupu pro virtuální počítač můžete nakonfigurovat v Azure Portal pomocí rozhraní příkazového řádku Azure nebo Azure PowerShell.

* [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

* [Předdefinované role v Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Pokyny**: implementace nástroje třetí strany, jako je automatizované řešení ochrany před únikem informací na hostiteli, aby vynutila řízení přístupu, aby se zmírnila rizika narušení dat.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné**materiály: virtuální disky na Virtual Machines (VM) jsou v klidovém stavu zašifrované, a to buď pomocí šifrování na straně serveru, nebo pomocí služby Azure Disk Encryption (ADE). Azure Disk Encryption využívá funkci DM-Crypt systému Linux k šifrování spravovaných disků pomocí klíčů spravovaných zákazníkem v rámci virtuálního počítače hosta. Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem se zlepšuje v ADE tím, že vám umožní používat pro vaše virtuální počítače jakékoli typy operačních systémů a image šifrováním dat ve službě úložiště.

* [Azure Disk Encryption pro Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-overview)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro změny virtuálních počítačů, které se týkají sady škálování a souvisejících prostředků.

* [Vytvoření upozornění pro události protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Protokolování Analýzy úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](/azure/security/benchmarks/security-control-vulnerability-management)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné**materiály: dodržujte doporučení od Azure Security Center při provádění posouzení ohrožení zabezpečení ve službě Azure Virtual Machines. Použijte doporučené řešení zabezpečení Azure nebo řešení třetí strany pro provádění posouzení ohrožení zabezpečení vašich virtuálních počítačů.

* [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné**materiály: Povolte automatické upgrady operačního systému pro podporované verze operačních systémů nebo pro vlastní image uložené v galerii sdílených imagí.

* [Automatické upgrady operačního systému pro Virtual Machine Scale Sets v Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizovaného řešení pro správu oprav pro softwarové tituly třetích stran

**Pokyny**: Azure Virtual Machine Scale Sets (VMSS) může používat automatické upgrady bitových kopií operačního systému. K základním virtuálním počítačům v VMSS můžete použít rozšíření DSC (Konfigurace požadovaného stavu) Azure. DSC se používá ke konfiguraci virtuálních počítačů tak, jak se dostanou do online režimu, takže běží na požadovaném softwaru.

* [Použití Virtual Machine Scale Sets s rozšířením Azure DSC](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-dsc)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Pokyny**: Exportovat výsledky kontroly v konzistentních intervalech a porovnat výsledky a ověřit, zda byly chyby zabezpečení opraveny. Při použití doporučení správy ohrožení zabezpečení navrhovaného Azure Security Center se může zákazník na portálu vybraného řešení překlopit a zobrazit historická data kontroly.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné**materiály: použijte výchozí hodnocení rizik (zabezpečené skóre) poskytované Azure Security Center.

* [Pochopení Azure Security Center zabezpečeného skóre](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny prostředky (včetně virtuálních počítačů) v rámci vašich předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

* [Jak vytvářet dotazy pomocí Azure graphu](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Jak zobrazit vaše předplatná Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Pochopení Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné**materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání v závislosti na taxonomii.

* [Vytváření a používání značek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné**materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování Virtual Machinesch škálování a souvisejících prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

* [Vytvoření dalších předplatných Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Postup vytvoření Skupiny pro správu](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Vytváření a používání značek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné**materiály: Vytvořte inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb organizace.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné**materiály: pomocí Azure Policy můžete klást omezení typů prostředků, které se dají vytvořit v zákaznických předplatných, a to pomocí těchto integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

Kromě toho můžete pomocí grafu prostředků Azure dotazovat nebo zjišťovat prostředky v rámci předplatných. To může pomáhat v prostředích s vysokým zabezpečením, jako jsou ta s účty úložiště.

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak vytvářet dotazy pomocí Azure graphu](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné**materiály: Azure Automation poskytuje úplnou kontrolu během nasazení, operací a vyřazení úloh a prostředků z provozu. Využijte inventarizaci virtuálních počítačů Azure k automatizaci shromažďování informací o veškerém softwaru na Virtual Machines. Poznámka: název softwaru, verze, Vydavatel a čas aktualizace jsou dostupné z Azure Portal. Aby bylo možné získat přístup k datu instalace a dalším informacím, musí zákazník vyžadovat diagnostiku na úrovni hosta a přenést protokoly událostí systému Windows do Log Analytics pracovního prostoru.

Aktuálně Adaptivní řízení aplikací nejsou k dispozici pro Virtual Machine Scale Sets.

* [Seznámení se službou Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

* [Jak povolit inventář virtuálních počítačů Azure](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné**materiály: Azure Automation poskytuje úplnou kontrolu během nasazení, operací a vyřazení úloh a prostředků z provozu. K identifikaci veškerého softwaru nainstalovaného v Virtual Machines můžete použít Change Tracking. Můžete implementovat vlastní proces nebo použít konfiguraci Azure Automation stav pro odebrání neautorizovaného softwaru.

* [Seznámení se službou Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

* [Sledování změn ve vašem prostředí pomocí Change Tracking řešení](https://docs.microsoft.com/azure/automation/change-tracking)

* [Přehled konfigurace stavu Azure Automation](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné**materiály: aktuálně Adaptivní řízení aplikací není k dispozici pro Virtual Machine Scale Sets. Použijte software třetí strany k řízení využívání jenom pro schválené aplikace.

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné**materiály: pomocí Azure Policy můžete klást omezení typů prostředků, které se dají vytvořit v zákaznických předplatných, a to pomocí těchto integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné**materiály: aktuálně Adaptivní řízení aplikací není k dispozici pro Virtual Machine Scale Sets. Implementujte řešení třetích stran, pokud to nevyhovuje požadavkům vaší organizace.

* [Jak používat Azure Security Center Adaptivní řízení aplikací](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací "blokování přístupu" pro aplikaci Microsoft Azure Management.

* [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné**materiály: v závislosti na typu skriptů můžete pomocí konfigurací specifických pro operační systém nebo prostředků třetích stran omezit schopnost uživatelů spouštět skripty v rámci výpočetních prostředků Azure.

* [Řízení spouštění skriptu PowerShellu v prostředích Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné**materiály: vysoce rizikové aplikace nasazené ve vašem prostředí Azure mohou být izolované pomocí virtuální sítě, podsítě, předplatných, skupin pro správu atd. a dostatečně zabezpečených pomocí Azure firewall, firewallu webových aplikací (WAF) nebo skupiny zabezpečení sítě (NSG).

* [Virtuální sítě a virtuální počítače v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/network-overview)

* [Přehled Azure Firewall](https://docs.microsoft.com/azure/firewall/overview)

* [Přehled Firewallu webových aplikací](https://docs.microsoft.com/azure/web-application-firewall/overview)

* [Přehled zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview)

* [Přehled služby Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

* [Uspořádání prostředků s využitím skupin pro správu Azure](https://docs.microsoft.com/azure/governance/management-groups/overview)

* [Průvodce rozhodováním ohledně předplatného](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: k údržbě konfigurací zabezpečení pro všechny prostředky Azure použijte Azure Policy nebo Azure Security Center. Azure Resource Manager taky umožňuje exportovat šablonu do JavaScript Object Notation (JSON), která by se měla zkontrolovat, aby se zajistilo, že konfigurace splňují požadavky na zabezpečení vaší společnosti.

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Informace o tom, jak stáhnout šablonu virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Pokyny**: použijte Azure Security Center doporučení [napravení ohrožení zabezpečení v konfiguracích zabezpečení v Virtual Machines], abyste zachovali konfigurace zabezpečení ve všech výpočetních prostředcích.

* [Jak monitorovat Azure Security Center doporučení](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

* [Jak opravit Azure Security Center doporučení](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: pomocí Azure Resource Manager šablon a zásad Azure můžete bezpečně nakonfigurovat prostředky Azure přidružené k sadám Virtual Machines Scale. Šablony Azure Resource Manager jsou soubory založené na formátu JSON, které se používají k nasazení virtuálního počítače společně s prostředky Azure a vlastní šablona bude potřeba zachovat. Společnost Microsoft provádí údržbu základních šablon. Použijte zásady Azure [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení napříč prostředky Azure.

* [Informace o vytváření šablon Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Porozumění efektům Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné**materiály: existuje několik možností, jak spravovat zabezpečenou konfiguraci pro Azure Virtual Machines (VM) pro nasazení:

1. Šablony Azure Resource Manager: Jedná se o soubory založené na formátu JSON, které slouží k nasazení virtuálního počítače z Azure Portal a vlastní šablonu bude nutné zachovat. Společnost Microsoft provádí údržbu základních šablon.

2. Vlastní virtuální pevný disk (VHD): v některých případech se může vyžadovat, aby se použily vlastní soubory VHD, jako třeba při řešení složitých prostředí, která není možné spravovat prostřednictvím jiných prostředků.

3. Konfigurace stavu Azure Automation: po nasazení základního operačního systému lze tuto možnost použít pro podrobnější kontrolu nastavení a vynutila prostřednictvím rozhraní Automation.

Pro většinu scénářů se může při schůzi a údržbě požadavků na zabezpečení pomáhat šablony základních virtuálních počítačů společnosti Microsoft kombinované s Azure Automation konfigurací požadovaného stavu.

* [Informace o tom, jak stáhnout šablonu virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

* [Informace o vytváření šablon ARM](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [Jak nahrát vlastní virtuální pevný disk virtuálního počítače do Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny**: použijte Azure DevOps k bezpečnému ukládání a správě kódu, jako jsou vlastní zásady Azure, Azure Resource Manager šablony, skripty pro konfiguraci požadovaného stavu atd.  Pro přístup k prostředkům, které spravujete v Azure DevOps, jako je váš kód, sestavení a sledování práce, musíte mít oprávnění pro tyto konkrétní prostředky. Většina oprávnění je poskytována prostřednictvím integrovaných skupin zabezpečení, jak je popsáno v tématu oprávnění a přístup. Můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps nebo Active Directory, pokud jsou integrované s TFS.

* [Jak v Azure DevOps ukládat kód](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [O oprávněních a skupinách v Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné**materiály: Pokud používáte vlastní image (např. virtuální pevný disk), použijte řízení přístupu na základě role Azure (Azure RBAC) a zajistěte, aby k nim měli přístup jenom autorizovaní uživatelé.

* [Pochopení Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

* [Jak nakonfigurovat službu Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné**materiály: využijte Azure Policy k oznamování, auditu a prosazování systémových konfigurací pro virtuální počítače. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Doprovodné**materiály: konfigurace stavu Azure Automation je služba správy konfigurace pro požadované uzly Konfigurace stavu (DSC) v jakémkoli cloudovém nebo místním datacentru. Umožňuje snadnou a snadnou škálovatelnost v tisících počítačů z centrálního a bezpečného umístění. Můžete snadno připojit počítače, přiřazovat je k deklarativním konfiguracím a zobrazovat sestavy, které zobrazují kompatibilitu jednotlivých počítačů s požadovaným stavem, který jste zadali.

* [Připojování počítačů pro správu podle konfigurace stavu Azure Automation](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné**materiály: využijte Azure Security Center k provádění kontrol standardních hodnot vašich virtuálních počítačů Azure. Mezi další metody pro automatizovanou konfiguraci patří použití konfigurace stavu Azure Automation.

* [Jak opravit doporučení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

* [Začínáme s konfigurací stavu Azure Automation](https://docs.microsoft.com/azure/automation/automation-dsc-getting-started)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné**materiály: konfigurace stavu Azure Automation je služba správy konfigurace pro požadované uzly Konfigurace stavu (DSC) v jakémkoli cloudovém nebo místním datacentru. Umožňuje snadnou a snadnou škálovatelnost v tisících počítačů z centrálního a bezpečného umístění. Můžete snadno připojit počítače, přiřazovat je k deklarativním konfiguracím a zobrazovat sestavy, které zobrazují kompatibilitu jednotlivých počítačů s požadovaným stavem, který jste zadali.

* [Připojování počítačů pro správu podle konfigurace stavu Azure Automation](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné**materiály: použití identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů pro vaše cloudové aplikace.

* [Integrace se spravovanými identitami Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

* [Vytvoření Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [Ověření Key Vault](../key-vault/general/authentication.md)

* [Postup přiřazení zásady přístupu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné**materiály: použití spravovaných identit k poskytování služeb Azure s automaticky spravovanou identitou ve službě Azure AD. Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

* [Postup konfigurace spravovaných identit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

* [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použijte centrálně spravovaný antimalwarový software

**Pokyny**: použití antimalwarového programu Microsoft Antimalware pro virtuální počítače Azure s Windows umožňuje nepřetržitě monitorovat a chránit vaše prostředky. Na virtuálním počítači Azure Linux budete potřebovat nástroj třetí strany pro ochranu proti malwaru.

* [Jak nakonfigurovat Microsoft Antimalware pro Cloud Services a Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Doprovodné**materiály: neplatí pro virtuální počítače Azure, protože se jedná o výpočetní prostředek.

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: netýká se

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné**materiály: při nasazení pro virtuální počítače s Windows bude Microsoft Antimalware pro Azure automaticky instalovat nejnovější signatury, platformy a aktualizace stroje ve výchozím nastavení. Použijte doporučení v Azure Security Center: "COMPUTE &amp; Apps", abyste zajistili aktuálnost všech koncových bodů s nejnovějšími podpisy. OPERAČNÍ systém Windows je možné dále chránit pomocí dalšího zabezpečení a omezit tak riziko virů nebo malwarových útoků pomocí služby Microsoft Defender Advanced Threat Protection, která se integruje s Azure Security Center.

Na virtuálním počítači Azure Linux budete potřebovat nástroj třetí strany pro ochranu proti malwaru.

* [Jak nasadit Microsoft Antimalware pro Azure Cloud Services a Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Rozšířená ochrana před internetovými útoky v programu Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

* [Jak nakonfigurovat Microsoft Antimalware pro Cloud Services a Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/security-recommendations)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: vytvoření snímku instance sady škálování virtuálních počítačů Azure nebo spravovaného disku připojeného k instanci pomocí prostředí PowerShell nebo rozhraní REST API. Můžete také použít Azure Automation ke spouštění zálohovacích skriptů v pravidelných intervalech.

* [Jak pořídit snímek instance sady škálování virtuálního počítače a spravovaného disku](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Úvod do Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné**materiály: Vytvořte snímky virtuálních počítačů Azure nebo spravované disky připojené k těmto instancím pomocí prostředí PowerShell nebo rozhraní REST API. Zálohujte všechny klíče spravované zákazníkem v rámci Azure Key Vault.

Povolte Azure Backup a cílovou službu Azure Virtual Machines (VM) a také požadovanou frekvenci a dobu uchování. To zahrnuje kompletní zálohu stavu systému. Pokud používáte Azure Disk Encryption, zálohování virtuálních počítačů Azure automaticky zpracovává zálohu klíčů spravovaných zákazníkem.

* [Zálohování virtuálních počítačů Azure, které používají šifrování](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

* [Přehled zálohování virtuálních počítačů Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)

* [Jak pořídit snímek instance sady škálování virtuálního počítače a spravovaného disku](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Postup zálohování klíčů trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné**materiály: Zajistěte, aby bylo možné pravidelně provádět obnovování dat ze spravovaného disku v rámci Azure Backup. V případě potřeby proveďte test obsahu obnovení do izolované virtuální sítě nebo předplatného. Zákazník testuje obnovení zálohovaných klíčů spravovaných zákazníkem.

Pokud používáte Azure Disk Encryption, můžete obnovit sady škálování virtuálních počítačů pomocí šifrovacích klíčů disku. Při použití šifrování disku můžete virtuální počítač Azure obnovit pomocí klíčů pro šifrování disku.

* [Zálohování virtuálních počítačů Azure, které používají šifrování](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

* [Obnovení disku a vytvoření obnoveného virtuálního počítače v Azure](https://docs.microsoft.com/azure/backup/tutorial-restore-disk)

* [Postup obnovení klíčů trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Jak povolit šifrování disku pro Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-overview)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné**materiály: povolení odstranění ochrany pro spravovaný disk pomocí zámků. Povolit Soft-Delete a vyprázdnit ochranu v Key Vault a chránit tak klíče proti náhodnému nebo škodlivému odstranění.

* [Zamknutí prostředků, aby se zabránilo neočekávaným změnám](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)

* [Přehled ochrany proti odstranění a mazání Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: Vytvoření Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů z detekce až po přezkoumání po jednotlivých událostech.

* [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Využijte příručku pro zpracování incidentů zabezpečení počítače NIST, která vám pomůže při vytváření vlastního plánu odpovědí na incidenty.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné**materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka pomocí značek a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure, zejména těch, která zpracovávají citlivá data. Máte zodpovědnost za to, že je možné určit prioritu nápravy výstrah na základě závažnosti prostředků a prostředí Azure, ve kterých došlo k incidentu.

* [Výstrahy zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [Používání značek k uspořádání prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné**materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

* [Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné**materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

* [Jak nastavit kontakt zabezpečení Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: vyexportujte výstrahy a doporučení pro Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

* [Postup konfigurace průběžného exportu](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Jak streamovat výstrahy do Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné**materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" pro výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure.

* [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné**materiály: řiďte se pravidly společnosti Microsoft o zapojení, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft.

* [Pravidla testování průniku pro zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](/azure/security/benchmarks/overview)
- Další informace o [plánech zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
