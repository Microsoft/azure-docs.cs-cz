---
title: Základní hodnoty zabezpečení Azure pro Azure Automation
description: Základní hodnoty zabezpečení Azure pro automatizaci
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4be9b802f5284663c2834f9d9c59fb709269bd53
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572590"
---
# <a name="azure-security-baseline-for-automation"></a>Základní hodnoty zabezpečení Azure pro automatizaci

Základní plán zabezpečení Azure pro automatizaci obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné** materiály: účet Azure Automation ještě nepodporuje privátní propojení Azure pro omezení přístupu ke službě prostřednictvím privátních koncových bodů. Sady Runbook, které se ověřují a spouštějí u prostředků v Azure, běží v izolovaném prostoru Azure a využívají sdílené back-endové prostředky, které Microsoft zodpovídá za izolaci od sebe. jejich síť není omezená a má přístup k veřejným prostředkům. Azure Automation aktuálně nemá integraci virtuální sítě pro privátní sítě nad rámec podpory pro procesy Hybrid Runbook Worker. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud chcete získat další izolaci pro své Runbooky, můžete použít hybridní pracovní procesy Runbooku běžící na virtuálních počítačích Azure. Když vytváříte virtuální počítač Azure, musíte vytvořit virtuální síť (VNet) nebo použít stávající virtuální síť a nakonfigurovat virtuální počítače s podsítí. Zajistěte, aby všechny nasazené podsítě používaly skupinu zabezpečení sítě s ovládacími prvky pro přístup k síti, které jsou specifické pro vaše aplikace a důvěryhodné porty a zdroje. Požadavky na konkrétní službu najdete v doporučení zabezpečení pro konkrétní službu.

Případně, pokud máte konkrétní požadavek, Azure Firewall mohou být použity také k jeho splnění.

* [Virtuální sítě a virtuální počítače v Azure](../virtual-machines/network-overview.md)

* [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

* [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

* [Jak nasadit a nakonfigurovat Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Prostředí pro spuštění sady Runbook](./automation-runbook-execution.md#runbook-execution-environment)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových karet

**Doprovodné** materiály: Azure Automation aktuálně nemá integraci virtuální sítě pro privátní sítě nad rámec podpory pro procesy Hybrid Runbook Worker. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud používáte Hybrid Runbook Worker zálohovaný virtuálními počítači Azure, ujistěte se, že podsíť obsahující tyto pracovní procesy je povolená se skupinou zabezpečení sítě (NSG), a nakonfigurujte protokoly toku pro přeposílání protokolů do účtu úložiště pro audit provozu. Protokoly toku NSG můžete také přesměrovat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

I když se pravidla NSG a uživatelsky definované trasy nevztahují na soukromý koncový bod, protokoly toku NSG a informace monitorování pro odchozí připojení se pořád podporují a dají se použít.

* [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: Azure Automation aktuálně nemá integraci virtuální sítě pro privátní sítě nad rámec podpory pro procesy Hybrid Runbook Worker. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud používáte hybridní pracovní procesy Runbooku, které jsou zajištěné virtuálními počítači Azure, zajistěte ve svých virtuálních sítích hostujících procesy Hybrid Runbook Worker (DDoS) standard Protection, abyste se mohli chránit před útoky DDoS. Pomocí Azure Security Center integrované analýzy hrozeb můžete komunikaci se známými škodlivými IP adresami odepřít. Nakonfigurujte Azure Firewall na všech segmentech Virtual Network, s povolenou funkcí Analýza hrozeb a nastavte **výstrahy a odmítnutí** pro škodlivý síťový provoz.

Přístup k síti pro virtuální počítače s Windows můžete omezit tak, že na základě omezeného časového období omezíte vystavení virtuálních počítačů s Windows na schválené IP adresy pomocí nástroje Azure Security Center jenom v čase. K omezení portů a zdrojových IP adres na základě skutečného provozu a analýzy hrozeb použijte taky Azure Security Center doporučení adaptivního posílení zabezpečení sítě pro konfigurace NSG.

* [Jak nakonfigurovat DDoS Protection](../ddos-protection/manage-ddos-protection.md)

* [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Pochopení Azure Security Center integrované analýzy hrozeb](../security-center/azure-defender.md)

* [Pochopení Azure Security Center adaptivního posílení zabezpečení sítě](../security-center/security-center-adaptive-network-hardening.md)

* [Pochopení Azure Security Center k síťovému Access Control v čase](../security-center/security-center-just-in-time.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné** materiály: Azure Automation v současné době nemá integraci virtuální sítě pro privátní sítě, a to nad rámec podpory Hybrid Runbook Worker, tento ovládací prvek se nedá použít, pokud používáte službu Inbox bez hybridních pracovních procesů.

Pokud používáte hybridní pracovní procesy Runbooku, které jsou zajištěné virtuálními počítači Azure, můžete nahrávat protokoly NSG Flow do účtu úložiště a vygenerovat záznamy toků pro Azure Virtual Machines, které fungují jako pracovní procesy Runbooku. Při zkoumání aktivity neobvyklé můžete povolit zachytávání paketů Network Watcher, aby bylo možné zkontrolovat síťový provoz pro neobvyklou a neočekávanou aktivitu.

* [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Postup povolení Network Watcher](../network-watcher/network-watcher-create.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: Azure Automation aktuálně nemá integraci virtuální sítě pro privátní sítě nad rámec podpory pro procesy Hybrid Runbook Worker. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud používáte hybridní pracovní procesy Runbooku hostované na virtuálních počítačích Azure, můžete kombinovat zachycení paketů poskytovaná Network Watcher a open source ID pomocí nástrojů pro detekci neoprávněného vniknutí do sítě pro zajištění široké škály hrozeb pro tyto pracovní počítače. V případě potřeby můžete také nasadit Azure Firewall do segmentů Virtual Network, s povolenou funkcí Analýza hrozeb a nakonfigurovanou na "výstrahy a zamítnutí" pro škodlivý síťový provoz.

* [Zjišťování neoprávněných vniknutí k síti pomocí Network Watcher a open source nástrojů](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Postup konfigurace výstrah pomocí Azure Firewall](../firewall/threat-intel.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: pomocí značek Virtual Network služeb můžete definovat řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall nakonfigurovaných v Azure, které vyžadují přístup k vašim prostředkům Automation. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (například GuestAndHybridManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

* [Pochopení a používání značek služeb](../virtual-network/service-tags-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky, které používá Azure Automation s Azure Policy.

Pomocí plánů Azure můžete také zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou šablony Azure Resource Manageru, řízení a zásady Azure RBAC, a to v jediné definici podrobného plánu. Můžete použít podrobný plán na nová předplatná a vyladit řízení a správu prostřednictvím správy verzí.

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Ukázky Azure Policy pro sítě](../governance/policy/samples/built-in-policies.md#network)

* [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro skupin zabezpečení sítě a další prostředky, které se týkají zabezpečení sítě a toku provozu. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

* [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

* [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace prostředků a zjišťovat změny v síťových prostředcích. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických prostředcích.

* [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Vytváření výstrah v Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Doprovodné** materiály: Microsoft udržuje časové zdroje pro prostředky Azure. Máte ale možnost spravovat nastavení synchronizace času pro všechny procesy Hybrid Runbook Worker běžící na virtuálních počítačích s Windows.

* [Jak nakonfigurovat časovou synchronizaci pro výpočetní prostředky Azure](../virtual-machines/windows/time-sync.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: dopředná data protokolu Azure monitor protokoly pro agregaci dat zabezpečení vygenerovaných prostředky Azure Automation. V rámci Azure Monitor můžete pomocí dotazů protokolu vyhledávat a provádět analýzy a používat účty Azure Storage k dlouhodobému a Archivačnímu úložišti. Azure Automation může do pracovního prostoru Log Analytics odeslat stav úlohy Runbooku, datové proudy úloh, data konfigurace stavu služby Automation, správu aktualizací a protokoly změn nebo inventáře. Tyto informace jsou viditelné z rozhraní API Azure Portal, Azure PowerShell a Azure Monitor protokolů, které vám umožní provádět jednoduché šetření.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

* [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

* [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Předávání dat úloh Azure Automation do protokolů Azure Monitoru](./automation-manage-send-joblogs-log-analytics.md)

* [Integrace DSC s protokoly Azure Monitor](./automation-dsc-diagnostics.md)

* [Podporované oblasti pro propojený pracovní prostor Log Analytics](./how-to/region-mappings.md)

* [Dotazy Update Management protokoly](./update-management/query-logs.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: povolení Azure monitor pro přístup k protokolům auditu a aktivitám, které zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky.

* [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

* [Zobrazení a načtení událostí protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: při použití Azure Automation u více tenantů pracujících s klienty Runbooku tento ovládací prvek není k dispozici a platforma zpracovává základní virtuální počítače.

Při použití funkce Hybrid Runbook Worker Azure Security Center poskytuje monitorování protokolu událostí zabezpečení pro virtuální počítače s Windows. Pokud by vaše organizace měla uchovávat data protokolu událostí zabezpečení, může být uložená v rámci úrovně shromažďování dat, v takovém případě se může dotazovat na Log Analytics. Existují různé úrovně: minimální, společné a všechny, které jsou podrobně popsané v následujícím odkazu.

* [Konfigurace úrovně shromažďování dat v rámci Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v rámci Azure monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

* [Změnit dobu uchovávání dat v Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

* [Podrobnosti o uchovávání dat pro účty Automation](./automation-managing-data.md#data-retention)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů pro neobvyklé chování a pravidelné kontroly výsledků. Pomocí Azure Monitor dotazů protokolu můžete prohlížet protokoly a provádět dotazy na data protokolu.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

* [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Pochopení dotazů protokolu v Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)

* [Jak provádět vlastní dotazy v Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: použijte Azure Security Center se Azure monitor pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech.

Alternativně můžete povolit a začlenit data do Azure Sentinel.

* [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

* [Upozornění na data protokolu Azure Monitor](../azure-monitor/alerts/tutorial-response.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: při použití Azure Automation s více klienty Runbook Worker není tento ovládací prvek k dispozici a platforma zpracovává základní virtuální počítače.

Pokud ale používáte funkci Hybrid Runbook Worker, můžete použít Microsoft Anti-malware pro Azure Cloud Services a virtuální počítače. Nakonfigurujte virtuální počítače pro protokolování událostí na účet Azure Storage. Nakonfigurujte Log Analytics pracovní prostor pro ingestování událostí z účtů úložiště a v případě potřeby vytvořte výstrahy. Použijte doporučení v Azure Security Center: "COMPUTE &amp; Apps".

* [Jak nakonfigurovat Microsoft Anti-malware pro Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md)

* [Jak povolit monitorování na úrovni hosta pro virtuální počítače](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Pokyny**: implementace řešení od jiného výrobce z Azure Marketplace pro řešení protokolování DNS podle potřeby vaší organizace.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné** materiály: při použití Azure Automation u více tenantů pracujících s klienty Runbooku tento ovládací prvek není k dispozici a platforma zpracovává základní virtuální počítače.

Při použití funkce Hybrid Runbook Worker ale Azure Security Center poskytuje monitorování protokolu událostí zabezpečení pro virtuální počítače Azure. Security Center zřídí agenta Log Analytics na všech podporovaných virtuálních počítačích Azure a všechny nově vytvořené, pokud je povolené Automatické zřizování. Nebo můžete agenta nainstalovat ručně. Agent umožňuje událost vytvoření procesu 4688 a pole CommandLine v události 4688. Nové procesy vytvořené na virtuálním počítači jsou zaznamenané protokolem událostí a monitorují služby zjišťování Security Center.

* [Shromažďování dat v Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: použijte Azure Active Directory předdefinované role správce, které se dají explicitně přiřadit a můžou se dotazovat. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu. Při každém použití účtů služby Automation jako účtu Spustit jako pro vaše Runbooky se ujistěte, že tyto instanční objekty jsou ve vašem inventáři sledovány, protože mají často čas se zvýšenými oprávněními. Odstraňte všechny nepoužívané účty Spustit jako pro minimalizaci vystaveného prostoru pro útoky.

* [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole)

* [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember)

* [Odstranění účet Spustit jako nebo Spustit jako pro Classic](./delete-run-as-account.md)

* [Správa účtu Azure Automation spustit jako](./manage-runas-account.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: Azure Automation účet nemá koncept výchozích hesel. Zákazníci jsou odpovědní za aplikace třetích stran a služby Marketplace, které mohou používat výchozí hesla, která se spouštějí na službě nebo její procesy Hybrid Runbook Worker.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu. Při každém použití účtů služby Automation jako účtu Spustit jako pro vaše Runbooky se ujistěte, že tyto instanční objekty jsou ve vašem inventáři sledovány, protože mají často čas se zvýšenými oprávněními. Oborujte tyto identity s nejmenšími privilegovanými oprávněními, které potřebují, aby vaše Runbooky úspěšně prováděly automatizovaný proces. Odstraňte všechny nepoužívané účty Spustit jako pro minimalizaci vystaveného prostoru pro útoky.

Pomocí Azure AD Privileged Identity Management privilegovaných rolí pro služby společnosti Microsoft a Azure Resource Manager můžete také povolit přístup za běhu nebo jen tolik.

* [Další informace o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

* [Odstranění účet Spustit jako nebo Spustit jako pro Classic](./delete-run-as-account.md)

* [Správa účtu Azure Automation spustit jako](./manage-runas-account.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné** materiály: kdykoli je to možné, použijte jednotné přihlašování s Azure Active Directory, ale nekonfigurujte jednotlivé samostatné přihlašovací údaje na službu. Použijte Azure Security Center doporučení pro správu identit a přístupu.

* [Jednotné přihlašování k aplikacím v Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md)

* [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

* [Použití Azure AD k ověření v Azure](./automation-use-azure-ad.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte Azure AD Multi-Factor Authentication (MFA) a sledujte Azure Security Center doporučení pro správu identit a přístupu.

* [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="36-use-dedicated-machines-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače pro všechny úlohy správy

**Doprovodné** materiály: použití privilegovaným přístupem s ověřováním Multi-Factor Authentication je nakonfigurované pro přihlášení a konfiguraci prostředků účtů Azure Automation v produkčních prostředích.

* [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: Využijte zjišťování rizik Azure AD k zobrazení výstrah a sestav na základě rizikového chování uživatelů. V případě potřeby může zákazník přeposílat Azure Security Center výstrahy detekce rizik Azure Monitor a nakonfigurovat vlastní výstrahy a oznámení pomocí skupin akcí.

* [Principy zjišťování rizik Azure Security Center (podezřelá aktivita)](../active-directory/identity-protection/overview-identity-protection.md)

* [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Jak nakonfigurovat skupiny akcí pro vlastní výstrahy a oznámení](../azure-monitor/alerts/action-groups.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Doprovodné** materiály: doporučuje se používat pojmenovaná umístění podmíněného přístupu k povolení přístupu jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

* [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte službu Azure AD. Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele. Pokud používáte hybridní pracovní procesy Runbooku, můžete místo účtů spustit jako využít spravované identity, aby bylo možné zajistit bezproblémové zabezpečení zabezpečených oprávnění.

* [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

* [Použití ověřování Runbooku u spravovaných identit](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure AD poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé. Při každém použití účtů účtu Spustit jako pro vaše Runbooky zajistěte, aby tyto instanční objekty byly také sledovány ve vašem inventáři, protože mají často vyšší oprávnění. Odstraňte všechny nepoužívané účty Spustit jako pro minimalizaci vystaveného prostoru pro útoky.

* [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)

* [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

* [Odstranění účet Spustit jako nebo Spustit jako pro Classic](./delete-run-as-account.md)

* [Správa účtu Azure Automation spustit jako](./manage-runas-account.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: máte přístup k aktivitám přihlášení ke službě Azure AD, k auditu a rizikovým zdrojům protokolu událostí, které vám umožní integraci s jakýmkoli nástrojem Siem/monitoring.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro Azure Active Directory uživatelských účtů a odesláním protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics.

* [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Doprovodné** materiály: použití funkcí rizika a ochrany identity v Azure AD ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů v síťovém prostředku. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.

* [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: u účtů Azure Automation může podpora Microsoftu přistupovat k metadatům prostředků platformy během otevřeného případu podpory bez použití jiného nástroje.

Pokud ale použijete hybridní pracovní procesy Runbooku, které jsou zajištěné virtuálními počítači Azure a potřebujete získat přístup k zákaznickým datům (například během žádosti o podporu), použijte Customer Lockbox (Preview) pro virtuální počítače Azure a zkontrolujte a schvalte nebo odmítněte žádosti o přístup k datům zákazníků.

* [Porozumění Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: pomocí značek pomáhají při sledování Azure Automationch prostředků, které ukládají nebo zpracovávají citlivé informace.

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Izolujte prostředí pomocí samostatných prostředků účtu Automation. Prostředky jako procesy Hybrid Runbook Worker by se měly oddělit pomocí virtuální sítě/podsítě, vhodně označit a zabezpečit v rámci skupiny zabezpečení sítě (NSG) nebo Azure Firewall. U virtuálních počítačů, které ukládají nebo zpracovávají citlivá data, implementujte zásady a postupy pro jejich vypnutí, pokud se nepoužívají.

* [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

* [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

* [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

* [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

* [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Jak nakonfigurovat výstrahu nebo upozornění a odepřít pomocí Azure Firewall](../firewall/threat-intel.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: při použití funkce Hybrid Runbook Worker použijte řešení třetích stran z Azure Marketplace na hraničních sítích, které monitorují neoprávněný přenos citlivých informací a zablokují tyto přenosy, a upozorní odborníky na zabezpečení informací.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft zpracovává veškerý obsah zákazníků jako citlivý a chráněný proti ztrátám a expozici zákaznických dat. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

* [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: šifrování všech citlivých informací během přenosu. Ujistěte se, že všichni klienti připojující se k prostředkům Azure v Azure Virtual Networks můžou vyjednávat TLS 1,2 nebo vyšší. Azure Automation plně podporuje a vynutila přenosovou vrstvu (TLS) 1,2 a všechna volání klientů nebo novější verze všech externích koncových bodů HTPS (prostřednictvím webhooků, uzlů DSC, hybrid Runbook Worker).

Pokud je to možné, postupujte podle Azure Security Center doporučení pro šifrování v klidovém režimu a šifrování.

* [Pochopení šifrování při přenosu pomocí Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Azure Automation vynucení TLS 1,2](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Pokyny**: k identifikaci všech citlivých informací uložených, zpracovávaných nebo odeslaných technologickými systémy organizace, včetně těch, které se nacházejí v lokalitě nebo u poskytovatele vzdálené služby, a aktualizaci inventáře citlivých informací organizace, použijte nástroj pro aktivní zjišťování třetí strany.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: použití řízení přístupu na základě role Azure (Azure RBAC) k řízení přístupu k prostředkům Azure Automation pomocí vestavěných definic rolí, přiřazení přístupu uživatelům, kteří přistupují k vašim prostředkům Automation, v rámci přístupového modelu s nejnižším oprávněním nebo s dostatečným oprávněním. Pokud používáte hybridní pracovní procesy Runbooku, využijte spravované identity pro tyto virtuální počítače, abyste se vyhnuli používání instančních objektů. při použití s více klienty nebo procesy Hybrid Runbook Worker se ujistěte, že se k identitě pracovních procesů Runbooku používaly náležitě vymezená oprávnění Azure RBAC.

* [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

* [Oprávnění sady Runbook pro Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

* [Správa oprávnění rolí a zabezpečení](./automation-role-based-access-control.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: Azure Automation aktuálně nezveřejňuje podkladové virtuální počítače Runbooku pro více tenantů a tato činnost je zpracována platformou. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud používáte hybridní pracovní procesy Runbooku, které jsou zajištěné virtuálními počítači Azure, musíte použít řešení ochrany před únikem informací od jiného výrobce, abyste vynutili řízení přístupu k vašim hostovaným Hybrid Runbook Worker virtuálním počítačům.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: použití klíčů spravovaných zákazníkem pomocí Azure Automation. Azure Automation podporuje použití klíčů spravovaných zákazníkem k šifrování všech používaných zabezpečených prostředků, jako jsou například přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Využijte šifrované proměnné se sadami Runbook pro všechny vaše trvalé vyhledávání proměnných musí zabránit nezamýšlené expozici.

Pokud používáte hybridní pracovní procesy Runbooku, virtuální disky na virtuálních počítačích se zašifrují v klidovém stavu pomocí šifrování na straně serveru nebo přes ADE (Azure Disk Encryption). Azure Disk Encryption využívá funkci BitLocker systému Windows k šifrování spravovaných disků pomocí klíčů spravovaných zákazníkem v rámci virtuálního počítače hosta. Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem se zlepšuje v ADE tím, že vám umožní používat pro vaše virtuální počítače jakékoli typy operačních systémů a image šifrováním dat ve službě úložiště.

* [Šifrování na straně serveru Azure Managed disks](../virtual-machines/disk-encryption.md)

* [Azure Disk Encryption pro virtuální počítače s Windows](../virtual-machines/windows/disk-encryption-overview.md)

* [Použití klíčů spravovaných zákazníkem pro účet Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Spravované proměnné v Azure Automation](./shared-resources/variables.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet upozornění na důležité prostředky Azure, jako jsou síťové součásti, účty Azure Automation a runbooky.

* [Protokolování diagnostiky pro skupinu zabezpečení sítě](../private-link/private-link-overview.md#logging-and-monitoring)

* [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](../security/benchmarks/security-control-vulnerability-management.md)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: dodržujte doporučení od Azure Security Center při provádění posouzení ohrožení zabezpečení vašich prostředků Azure.

* [Doporučení zabezpečení v Azure Security Center](../security-center/security-center-recommendations.md)

* [Referenční informace o Security Center doporučení](../security-center/recommendations-reference.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: Azure Automation aktuálně nezveřejňuje podkladové virtuální počítače Runbooku pro více tenantů a tato činnost je zpracována platformou. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud používáte hybridní pracovní procesy Runbooku, které jsou zajištěné virtuálními počítači Azure, použijte Azure Update Management ke správě aktualizací a oprav pro virtuální počítače. Update Management spoléhá na místně nakonfigurované úložiště aktualizací, které opraví podporované systémy Windows. Nástroje, jako je System Center Updates Publisher (Updates Publisher), umožňují publikovat vlastní aktualizace do Windows Server Update Services (WSUS). Tento scénář umožňuje Update Management opravit počítače, které používají Configuration Manager jako úložiště aktualizací se softwarem třetích stran.

* [Update Management v Azure](./update-management/overview.md)

* [Správa aktualizací a oprav pro virtuální počítače](./update-management/manage-updates-for-vm.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizovaného řešení pro správu oprav pro softwarové tituly třetích stran

**Doprovodné** materiály: Azure Automation aktuálně nezveřejňuje podkladové virtuální počítače Runbooku pro více tenantů a tato činnost je zpracována platformou. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud používáte hybridní pracovní procesy Runbooku, které jsou zajištěné virtuálními počítači Azure, můžete pomocí Azure Update Management spravovat aktualizace a opravy pro virtuální počítače. Update Management spoléhá na místně nakonfigurované úložiště aktualizací, které opraví podporované systémy Windows. Nástroje, jako je System Center Updates Publisher (Updates Publisher), umožňují publikovat vlastní aktualizace do Windows Server Update Services (WSUS). Tento scénář umožňuje Update Management opravy počítačů, které používají Configuration Manager jako úložiště aktualizací se softwarem třetích stran.

* [Řešení Update Management v Azure](./update-management/overview.md)

* [Správa aktualizací a oprav pro virtuální počítače Azure](./update-management/manage-updates-for-vm.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Pokyny**: Exportovat výsledky kontroly v konzistentních intervalech a porovnat výsledky a ověřit, zda byly chyby zabezpečení opraveny. Při použití doporučení správy ohrožení zabezpečení navrhovaného Azure Security Center se může zákazník na portálu vybraného řešení překlopit a zobrazit historická data kontroly.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: použijte výchozí hodnocení rizik (Secure skore) poskytované Azure Security Center, které vám pomůžou určit prioritu nápravy zjištěných chyb zabezpečení.

* [Pochopení Azure Security Center zabezpečeného skóre](../security-center/secure-score-security-controls.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny Azure Automation prostředky v rámci předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

* [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

* [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription)

* [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování Azure Automationch prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného. Odstraňte všechny nepoužívané účty Spustit jako pro minimalizaci vystaveného prostoru pro útoky.

* [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

* [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

* [Odstranění účet Spustit jako nebo Spustit jako pro Classic](./delete-run-as-account.md)

* [Správa účtu Azure Automation spustit jako](./manage-runas-account.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Pokyny**: budete muset vytvořit inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb vaší organizace.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

K dotazování a zjišťování prostředků v rámci předplatných navíc použijte graf prostředků Azure. To může pomáhat v prostředích s vysokým zabezpečením, jako jsou ta s účty úložiště.

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

* [Azure Policy ukázkové předdefinované pro Azure Automation](./policy-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné** materiály: Nabídka Azure Automation aktuálně nezveřejňuje základní virtuální počítače Runbooku pro více tenantů, které jsou zpracovávány platformou. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez hybridních pracovních procesů. Je však možné instalovat, odebírat a spravovat prostředí PowerShell nebo moduly Pythonu, ke kterým mají Runbooky přístup prostřednictvím portálu nebo rutin. Neschválený nebo starý modul by měl být pro Runbooky odebrán nebo aktualizován.

Pokud používáte hybridní pracovní procesy Runbooku, které zajišťuje Azure Virtual Machines, Azure Automation poskytuje úplnou kontrolu během nasazování, provozu a vyřazení úloh a prostředků z provozu. Využijte inventarizaci virtuálních počítačů Azure k automatizaci shromažďování informací o veškerém softwaru na Virtual Machines. Z Azure Portal je k dispozici název softwaru, verze, Vydavatel a čas aktualizace. Aby bylo možné získat přístup k datu instalace a dalším informacím, musí zákazník vyžadovat diagnostiku na úrovni hosta a přenést protokoly událostí systému Windows do Log Analytics pracovního prostoru.

* [Seznámení se službou Azure Automation](./automation-intro.md)

* [Jak povolit inventář virtuálních počítačů Azure](./automation-tutorial-installed-software.md)

* [Spravovat modul v Azure Automation](./shared-resources/modules.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Pokyny**: zákazník může zabránit vytváření nebo používání prostředků pomocí Azure Policy podle pokynů společnosti zákazníka. Můžete implementovat vlastní proces pro odebrání neautorizovaných prostředků. V rámci Azure Automation nabídky je možné instalovat, odebírat a spravovat prostředí PowerShell nebo moduly Pythonu, ke kterým mají Runbooky přístup prostřednictvím portálu nebo rutin. Neschválený nebo starý modul by měl být pro Runbooky odebrán nebo aktualizován.

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Spravovat modul v Azure Automation](./shared-resources/modules.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: při použití funkce Hybrid Runbook Worker můžete použít Azure Security Center Adaptivní řízení aplikací, abyste zajistili, že se spustí jenom autorizovaný software, a veškerý neoprávněný software se zablokuje spouštění na Azure Virtual Machines.

* [Jak používat Azure Security Center Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/index.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné** materiály: při použití funkce Hybrid Runbook Worker můžete použít funkci Azure Security Center Adaptivní řízení aplikací u virtuálních počítačů hybridního pracovního procesu.

Adaptivní řízení aplikací je inteligentní, automatizované a ucelené řešení z Azure Security Center, které vám pomůže určit, které aplikace se můžou spouštět na počítačích Azure a mimo Azure (Windows a Linux). Implementujte řešení třetích stran, pokud to nevyhovuje požadavkům vaší organizace.

* [Jak používat Azure Security Center Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: použití zásad podmíněného přístupu Azure k omezení možnosti uživatelů při interakci s Azure Resource Manager konfigurací "blokování přístupu" pro aplikaci Microsoft Azure Management z nezabezpečených nebo neschválených umístění nebo zařízení.

* [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Pokyny**: při použití funkce Hybrid Runbook Worker a v závislosti na typu skriptů můžete použít konfigurace specifické pro operační systém nebo prostředky třetích stran k omezení schopnosti uživatelů spouštět skripty v rámci výpočetních prostředků Azure. Můžete také využít Azure Security Center Adaptivní řízení aplikací, abyste zajistili, že se spustí jenom autorizovaný software, a veškerý neautorizovaný software bude zablokovaný na Azure Virtual Machines.

* [Řízení spouštění skriptu PowerShellu v prostředích Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

* [Jak používat Azure Security Center Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: aplikace s vysokým rizikem nasazené ve vašem prostředí Azure mohou být izolované pomocí různých síťových a kontejnerů prostředků pomocí konstrukcí, jako jsou virtuální sítě, podsíť, předplatná, skupiny pro správu, můžou být dostatečně zabezpečené buď pomocí Azure firewall, firewallu webových aplikací (WAF) nebo skupiny zabezpečení sítě (NSG).

* [Virtuální sítě a virtuální počítače v Azure](../virtual-machines/network-overview.md)

* [Přehled Azure Firewall](../firewall/overview.md)

* [Přehled brány firewall webových aplikací Azure](../web-application-firewall/overview.md)

* [Skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md)

* [Přehled služby Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

* [Uspořádání prostředků s využitím skupin pro správu Azure](../governance/management-groups/overview.md)

* [Průvodce rozhodováním ohledně předplatného](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace Azure Automation a souvisejících prostředků. Můžete také použít předdefinované definice Azure Policy.

Azure Resource Manager taky umožňuje exportovat šablonu do JavaScript Object Notation (JSON), která by se měla zkontrolovat, aby se zajistilo, že konfigurace splňují nebo překračují požadavky zabezpečení vaší organizace.

Pro vaše prostředky Azure můžete také použít doporučení z Azure Security Center jako standardní hodnoty konfigurace zabezpečení.

* [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

* [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy ukázkové předdefinované pro Azure Automation](./policy-reference.md)

* [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: Azure Automation aktuálně nezveřejňuje podkladové virtuální počítače a operační systémy pro více tenantů služby Runbook Worker. Tato platforma je zpracovávána platformou. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud používáte funkci Hybrid Runbook Worker, použijte k údržbě konfigurací zabezpečení na virtuálních počítačích Azure Security Center doporučení [Oprava chyb zabezpečení v konfiguracích zabezpečení v Virtual Machines].

* [Jak monitorovat Azure Security Center doporučení](../security-center/security-center-recommendations.md)

* [Jak opravit Azure Security Center doporučení](../security-center/security-center-remediate-recommendations.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: pomocí Azure Resource Manager šablon a Azure Policy bezpečně nakonfigurujte prostředky Azure přidružené k Azure Automation. Šablony Azure Resource Manager jsou soubory založené na formátu JSON, které slouží k nasazení prostředků Azure, a všechny vlastní šablony bude nutné ukládat a udržovat bezpečně v úložišti kódu. Pomocí funkce integrace správy zdrojového kódu můžete své Runbooky v účtu Automation udržovat v aktuálním stavu pomocí skriptů v úložišti správy zdrojových kódů. Použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.

* [Použití integrace správy zdrojového kódu](./source-control-integration.md)

* [Informace o vytváření šablon Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Porozumění efektům Azure Policy](../governance/policy/concepts/effects.md)

* [Nasazení účtu Automation pomocí šablony Azure Resource Manager](./quickstart-create-automation-account-template.md#deploy-the-template)

* [Azure Policy ukázkové předdefinované pro Azure Automation](./policy-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: Azure Automation aktuálně nezveřejňuje podkladové virtuální počítače a operační systémy pro více tenantů pro klienty Runbook Worker, a to na platformě. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Při použití funkce Hybrid Runbook Worker je k dispozici několik možností, jak spravovat zabezpečenou konfiguraci virtuálních počítačů Azure pro nasazení:

- Šablony Azure Resource Manager: Jedná se o soubory založené na formátu JSON, které slouží k nasazení virtuálního počítače z Azure Portal, a je třeba zachovat vlastní šablonu. Společnost Microsoft provádí údržbu základních šablon.
- Vlastní virtuální pevný disk (VHD): v některých případech se může vyžadovat, aby se použily vlastní soubory VHD, jako třeba při řešení složitých prostředí, která není možné spravovat prostřednictvím jiných prostředků.
- Konfigurace stavu Azure Automation: po nasazení základního operačního systému lze tuto možnost použít pro podrobnější kontrolu nastavení a vynutila prostřednictvím rozhraní Automation.

Pro většinu scénářů se může při schůzi a údržbě požadavků na zabezpečení pomáhat šablony základních virtuálních počítačů společnosti Microsoft v kombinaci s konfigurací stavu Azure Automation.

* [Informace o tom, jak stáhnout šablonu virtuálního počítače](/previous-versions/azure/virtual-machines/windows/download-template)

* [Informace o vytváření šablon ARM](../virtual-machines/windows/ps-template.md)

* [Jak nahrát vlastní virtuální pevný disk virtuálního počítače do Azure](/azure-stack/operator/azure-stack-add-vm-image)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny**: pomocí Azure DevOps bezpečně ukládejte a spravujte váš kód, jako jsou vlastní zásady Azure, šablony Azure Resource Manager a požadované konfigurační skripty stavu. Pokud chcete získat přístup k prostředkům, které spravujete ve službě Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory, pokud jsou integrované s Azure DevOps nebo Active Directory, pokud jsou integrované s TFS. Pomocí funkce integrace správy zdrojového kódu můžete své Runbooky v účtu Automation udržovat v aktuálním stavu pomocí skriptů v úložišti správy zdrojových kódů.

* [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

* [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Použití integrace správy zdrojového kódu](./source-control-integration.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: Azure Automation aktuálně nezveřejňuje podkladové virtuální počítače a operační systémy pro více tenantů pro klienty Runbook Worker, a to na platformě. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud používáte funkci Hybrid Runbook Worker, ujistěte se, že jste správně omezili přístup k vlastní imagi operačního systému ve vašem účtu úložiště, takže k imagi budou mít přístup jenom autorizovaní uživatelé.

* [Pochopení Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro prostředky Azure pomocí Azure Policy. Pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich prostředků Azure. Můžete také využít integrované definice zásad související s vašimi konkrétními prostředky.

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Jak používat aliasy](../governance/policy/concepts/definition-structure.md#aliases)

* [Azure Policy ukázkové předdefinované pro Azure Automation](./policy-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Doprovodné** materiály: Azure Automation aktuálně nezveřejňuje podkladové virtuální počítače a operační systémy pro více tenantů pro klienty Runbook Worker, a to na platformě. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Při použití funkce Hybrid Runbook Worker použijte konfiguraci stavu Azure Automation na Worker Worker, což je služba správy konfigurace pro uzly DSC (pro konfiguraci požadovaných stavů) v jakémkoli cloudu nebo v místním datacentru. Umožňuje snadnou a snadnou škálovatelnost v tisících počítačů z centrálního a bezpečného umístění. Můžete snadno připojit počítače, přiřazovat je k deklarativním konfiguracím a zobrazovat sestavy, které zobrazují kompatibilitu jednotlivých počítačů s požadovaným stavem, který jste zadali.

* [Připojování počítačů pro správu podle konfigurace stavu Azure Automation](./automation-dsc-onboarding.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: pomocí Azure Policy pro upozornění a audit konfigurací prostředků Azure můžete použít zásady ke zjištění určitého prostředku, který není nakonfigurovaný s privátním koncovým bodem.

Pokud používáte funkci Hybrid Runbook Worker, využijte Azure Security Center k provádění kontrol standardních hodnot pro virtuální počítače Azure. Další metody pro automatizovanou konfiguraci zahrnují konfiguraci stavu Azure Automation.

* [Jak opravit doporučení v Azure Security Center](../security-center/security-center-remediate-recommendations.md)

* [Začínáme s konfigurací stavu Azure Automation](./automation-dsc-getting-started.md)

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy ukázkové předdefinované pro Azure Automation](./policy-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: Nabídka Azure Automation aktuálně nezveřejňuje základní virtuální počítače a operační systémy pro více tenantů pro klienty Runbook Worker, které tato platforma zpracovává. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez hybridních pracovních procesů.

Při použití funkce Hybrid Runbook Worker použijte konfiguraci Azure Automation stav pro pracovní procesy Runbooku, což je služba správy konfigurace pro uzly DSC (State Configuration Configuration) v jakémkoli cloudu nebo v místním datacentru. Umožňuje snadnou a snadnou škálovatelnost v tisících počítačů z centrálního a bezpečného umístění. Můžete snadno připojit počítače, přiřazovat je k deklarativním konfiguracím a zobrazovat sestavy, které zobrazují kompatibilitu jednotlivých počítačů s požadovaným stavem, který jste zadali.

* [Připojování počítačů pro správu podle konfigurace stavu Azure Automation](./automation-dsc-onboarding.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: použití identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů pro vaše cloudové aplikace.

* [Použití klíčů spravovaných zákazníkem pro účet Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Použití ověřování Runbooku u spravovaných identit](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

* [Vytvoření Key Vault](../key-vault/general/quick-create-portal.md)

* [Ověření Key Vault](../key-vault/general/authentication.md)

* [Postup přiřazení zásady přístupu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: použití spravovaných identit k poskytování služeb Azure s automaticky spravovanou identitou ve službě Azure AD. Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

* [Postup konfigurace spravovaných identit](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Použití klíčů spravovaných zákazníkem pro účet Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

* [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné** materiály: Nabídka Azure Automation aktuálně nezveřejňuje základní virtuální počítače a operační systémy pro více tenantů pro klienty Runbook Worker, které tato platforma zpracovává. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud používáte funkci Hybrid Runbook Worker, používejte k nepřetržitému monitorování a ochraně vašich prostředků Runbooku Worker službu Microsoft Anti-malware pro virtuální počítače Azure s Windows.

* [Jak nakonfigurovat Microsoft Anti-malware pro Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Doprovodné** materiály: nepoužitelné; Azure Automation jako služba neukládá soubory. Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Automation), ale neběží na vašem obsahu.

* [Pochopení Microsoft antimalwaru pro Azure Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: Azure Automation aktuálně nezveřejňuje podkladové virtuální počítače a operační systémy pro více tenantů pro klienty Runbook Worker, a to na platformě. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Při použití funkce Hybrid Runbook Worker použijte Microsoft Antimalware pro Azure k automatické instalaci nejnovější signatury, platformy a aktualizací stroje ve výchozím nastavení do pracovního procesu Runbook Worker. Použijte doporučení v Azure Security Center: "COMPUTE &amp; Apps", abyste zajistili aktuálnost všech koncových bodů s nejnovějšími podpisy. OPERAČNÍ systém Windows je možné dále chránit pomocí dalšího zabezpečení a omezit tak riziko útoků na virus nebo malware pomocí služby Microsoft Defender Advanced Threat Protection, která se integruje s Azure Security Center.

* [Jak nasadit Microsoft Antimalware pro Azure Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md)

* [Rozšířená ochrana před internetovými útoky v programu Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: pomocí Azure Resource Manager nasaďte účty Azure Automation a související prostředky. Azure Resource Manager poskytuje možnost exportovat šablony, které se dají použít jako zálohy k obnovení účtů Azure Automation a souvisejících prostředků. Použijte Azure Automation k pravidelnému volání rozhraní API pro export Azure Resource Manager šablony.

Pomocí funkce integrace správy zdrojového kódu můžete své Runbooky v účtu Automation udržovat v aktuálním stavu pomocí skriptů v úložišti správy zdrojových kódů.

* [Přehled Azure Resource Manageru](../azure-resource-manager/management/overview.md)

* [Odkaz na šablonu Azure Resource Manager pro prostředky Azure Automation](/azure/templates/microsoft.automation/allversions)

* [Vytvoření účtu Automation pomocí šablony Azure Resource Manager](./quickstart-create-automation-account-template.md)

* [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Skupiny prostředků – Exportovat šablonu](/rest/api/resources/resourcegroups/exporttemplate)

* [Úvod do Azure Automation](./automation-intro.md)

* [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

* [Použití klíčů spravovaných zákazníkem pro účet Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Použití integrace správy zdrojového kódu](./source-control-integration.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: pomocí Azure Resource Manager nasaďte účty Azure Automation a související prostředky. Azure Resource Manager poskytuje možnost exportovat šablony, které se dají použít jako zálohy k obnovení účtů Azure Automation a souvisejících prostředků. Použijte Azure Automation k pravidelnému volání rozhraní API pro export Azure Resource Manager šablony. Zálohování klíčů spravovaných zákazníkem v rámci Azure Key Vault. Runbooky můžete exportovat do souborů skriptu pomocí Azure Portal nebo PowerShellu.

* [Přehled Azure Resource Manageru](../azure-resource-manager/management/overview.md)

* [Odkaz na šablonu Azure Resource Manager pro prostředky Azure Automation](/azure/templates/microsoft.automation/allversions)

* [Vytvoření účtu Automation pomocí šablony Azure Resource Manager](./quickstart-create-automation-account-template.md)

* [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Skupiny prostředků – Exportovat šablonu](/rest/api/resources/resourcegroups/exporttemplate)

* [Úvod do Azure Automation](./automation-intro.md)

* [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

* [Použití klíčů spravovaných zákazníkem pro účet Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Zálohování dat Azure pro účty Automation](./automation-managing-data.md#data-backup)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: Zajistěte, aby v případě potřeby pravidelně prováděly nasazení Azure Resource Manager šablon pro izolované předplatné do izolovaného předplatného. Test obnovení zálohovaných klíčů spravovaných zákazníkem

* [Nasazení prostředků pomocí šablon ARM a Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

* [Postup obnovení klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

* [Použití klíčů spravovaných zákazníkem pro účet Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: pomocí Azure DevOps bezpečně ukládejte a spravujte kód, jako je Azure Resource Manager šablon. K ochraně prostředků, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory, pokud jsou integrované s Azure DevOps nebo Active Directory, pokud jsou integrované s TFS.

Pomocí funkce integrace správy zdrojového kódu můžete své Runbooky v účtu Automation udržovat v aktuálním stavu pomocí skriptů v úložišti správy zdrojových kódů.

* [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

* [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Použití integrace správy zdrojového kódu](./source-control-integration.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení.

* [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka pomocí značek a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure, zejména těch, která zpracovávají citlivá data. Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

* [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

* [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

* [Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

* [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: vyexportujte výstrahy a doporučení pro Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

* [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

* [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" pro výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure.

* [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: řiďte se pravidly společnosti Microsoft o zapojení, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem.

* [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)