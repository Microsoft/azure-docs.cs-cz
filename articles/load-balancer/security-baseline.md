---
title: Základní hodnoty zabezpečení Azure pro Azure Load Balancer
description: Základní Azure Load Balancer zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 64e0a8cfcaf00c55038fbe5d1cdc7423b681b690
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589257"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Základní hodnoty zabezpečení Azure pro Azure Load Balancer

Tato základní hodnota zabezpečení platí pro Microsoft Azure Load Balancer pokyny od [verze 1,0 zabezpečení Azure Security test](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Azure Load Balancer. **Ovládací prvky** , které se nevztahují k Azure Load Balancer byly vyloučeny.

 
Pokud chcete zjistit, jak Azure Load Balancer kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Azure Load Balancer Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: pomocí interních nástrojů pro vyrovnávání zatížení Azure Povolte jenom přenosy do back-endu prostředků z určitých virtuálních sítí nebo partnerských virtuálních sítí bez ohrožení Internetu. Implementace externího Load Balancer se zdrojovou sítí

Překládání adres (SNAT) k maskování IP adres prostředků back-endu z důvodu ochrany před expozicí Internetu

Azure nabízí dva typy nabídek Load Balancer, Standard a Basic. Použijte Standard Load Balancer pro všechny produkční úlohy. Implementujte skupiny zabezpečení sítě a umožněte přístup jenom k důvěryhodným portům a rozsahům IP adres vaší aplikace. V případech, kdy neexistuje žádná skupina zabezpečení sítě přiřazená k podsíti back-endu nebo síťovému rozhraní virtuálních počítačů back-endu, nebude přenosy z nástroje pro vyrovnávání zatížení povolena. Pomocí standardních nástrojů pro vyrovnávání zatížení poskytněte odchozí pravidla pro definování odchozího překladu adres (NAT) se skupinou zabezpečení sítě. Zkontrolujte tato odchozí pravidla a Nalaďte chování odchozích připojení.

Použití Standard Load Balancer se doporučuje pro produkční úlohy a obvykle se základní Load Balancer používá jenom k testování, protože základní typ je otevřený pro připojení z Internetu ve výchozím nastavení a nevyžaduje pro tuto operaci skupiny zabezpečení sítě.

- [Odchozí připojení v Azure](load-balancer-outbound-connections.md)

- [Upgrade veřejné Load Balancer Azure](upgrade-basic-standard.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy integrovaných definic – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových karet

**Doprovodné** materiály: Load Balancer je průchozí služba, protože spoléhá na pravidla skupin zabezpečení sítě používaná u prostředků back-endu a nakonfigurovaná odchozí pravidla pro řízení přístupu k Internetu.

Zkontrolujte odchozí pravidla konfigurovaná pro váš Standard Load Balancer v okně odchozí pravidla Load Balancer a v okně pravidla vyrovnávání zatížení, kde můžete mít povolená implicitní odchozí pravidla.

Monitorujte počet odchozích připojení a sledujte, jak často se vaše prostředky na internetu blíží. 

Použijte Security Center a pořiďte si doporučení k zabezpečení síťových prostředků Azure pomocí doporučení pro ochranu sítě.

Použijte doporučení zabezpečení pro back-end prostředky a povolte protokoly toku skupin zabezpečení sítě a odešlete protokoly na účet Azure Storage pro auditování.

Také odešlete protokoly toku do Log Analytics pracovního prostoru a potom pomocí Analýza provozu poskytnete přehled o vzorech přenosů v cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu, identifikovat aktivní body a bezpečnostní hrozby, pochopit vzory toku přenosů dat a odhalit nesprávné konfigurace sítě.

- [Jak povolit protokoly toku skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Návody zkontroluje statistiku odchozího připojení.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy integrovaných definic – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: explicitně definujte připojení k Internetu a platné zdrojové IP adresy prostřednictvím odchozích pravidel a skupin zabezpečení sítě s vaším Load Balancer, aby se pro ochranu vašich webových aplikací používala Analýza hrozeb Microsoftu.

- [Integrace Azure Firewall](../firewall/integrate-lb.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: povolení standardní ochrany DDoS (Azure Distributed of Denial of Service) na Azure Virtual Network pro ochranu před útoky DDoS. 

Nasaďte Azure Firewall na všech hranicích sítě organizace s povoleným filtrováním na základě hrozeb a nakonfigurovanými na "výstrahy a zamítnutí" pro škodlivý síťový provoz.

 

Pomocí Security Center ochrany před internetovými útoky zjistíte komunikaci se známými škodlivými IP adresami. 

Standard Load Balancer je navržená tak, aby byla standardně zabezpečená a byla součástí privátního a izolovaného Virtual Networku. Je uzavřený do příchozích toků, pokud je neotevřou skupiny zabezpečení sítě, aby explicitně povolovaly povolený provoz a nepovolovaly známé škodlivé IP adresy. Pokud v rámci Load Balancer neexistuje skupina zabezpečení sítě v podsíti nebo síťové kartě prostředku virtuálního počítače, provoz není povolený pro přístup k tomuto prostředku. 

Nasaďte Azure Firewall na všech hranicích sítě organizace s povoleným filtrováním na základě hrozeb, které je povolené a nakonfigurované na "výstrahy a zamítnutí", aby se zabránilo útokům ze škodlivých IP adres. 

Implementujte pokyny pro integraci Azure Firewall s Load Balancer.

Pomocí Security Center funkcí ochrany před hrozbami zjistíte komunikaci se známými škodlivými IP adresami. 

Security Center (úroveň Standard) poskytuje přístup k virtuálnímu počítači za běhu a nakonfiguruje povolené zdrojové IP adresy tak, aby povolovaly přístup jenom ze schválených IP adres nebo rozsahů.
 

Použijte funkci adaptivního posílení sítě Security Center k doporučení konfigurace skupiny zabezpečení sítě, které omezují porty a zdrojové IP adresy na základě skutečného provozu a analýzy hrozeb.
 

- [Správa Azure DDoS Protection Standard pomocí Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall filtrování na základě logiky hrozeb](../firewall/threat-intel.md)

- [Ochrana před hrozbami v Azure Security Center](../security-center/azure-defender.md)

- [Zabezpečení portů pro správu s využitím přístupu za běhu](../security-center/security-center-just-in-time.md)

- [Adaptivní posílení zabezpečení sítě v Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

- [Integrace Azure Firewall s využitím Load Balancer](../firewall/overview.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy integrovaných definic – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné** materiály: povolení zachytávání paketů Network Watcher pro zkoumání aktivit neobvyklé.

- [Postup vytvoření instance Network Watcher](../network-watcher/network-watcher-create.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy integrovaných definic – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: Implementujte nabídku z Azure Marketplace, která podporuje funkce ID/IP adres s možnostmi kontroly zatížení pro prostředí vaší Load Balancer. 

Pokud kontrola datové části není požadavkem, použijte Azure Firewall analýzu hrozeb. Azure Firewall filtrování založené na základě logiky hrozeb slouží k upozorňování a zablokování provozu do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu.

Nasaďte řešení brány firewall podle svého výběru na základě hranic sítě vaší organizace za účelem detekce a blokování škodlivého provozu.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Postup konfigurace výstrah pomocí Azure Firewall](../firewall/threat-intel.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Pokyny**: explicitně definujte připojení k Internetu a platné zdrojové IP adresy prostřednictvím odchozích pravidel a skupin zabezpečení sítě s vaším Load Balancer, abyste mohli chránit své webové aplikace pomocí funkcí analýzy hrozeb společnosti Microsoft.

- [Integrace Azure Firewall](../firewall/integrate-lb.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: použití značek služby místo specifických IP adres při vytváření pravidel zabezpečení. Zadejte název značky služby v poli zdroj nebo cíl pravidla, aby bylo možné povolit nebo odepřít provoz odpovídající službě. 

Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny. 

Ve výchozím nastavení každá skupina zabezpečení sítě zahrnuje značku služby AzureLoadBalancer, která povoluje provoz sondy stavu. 

Všechny značky služeb, které jsou k dispozici pro použití v pravidlech skupiny zabezpečení sítě, najdete v dokumentaci k Azure.

- [Dostupné značky služeb](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky pomocí Azure Policy.

Pomocí Azure modrotisky můžete zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou šablony Azure Resource Manageru, řízení a zásady Azure RBAC, a to v jediné definici podrobného plánu. 

Použijte podrobný plán na nová předplatná a dolaďte řízení a správu prostřednictvím správy verzí.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ukázky Azure Policy pro sítě](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky prostředků pro skupiny zabezpečení sítě a další prostředky, které se týkají zabezpečení sítě a toku provozu. 

Pomocí pole Popis můžete zdokumentovat pravidla, která umožňují provoz do/ze sítě pro jednotlivá pravidla skupiny zabezpečení sítě.

Implementujte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", která zajišťuje, že se všechny prostředky vytvoří s značkami a upozorní na všechny existující neoznačené prostředky.

Pomocí Azure PowerShell nebo rozhraní příkazového řádku Azure můžete vyhledat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Postup vytvoření Virtual Network Azure](../virtual-network/quick-create-portal.md)

- [Postup filtrování síťového provozu pomocí pravidel skupiny zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace prostředků a zjišťovat změny vašich prostředků Azure. 

V Azure Monitor můžete vytvářet výstrahy, které vám upozorní na změnu důležitých zdrojů.

- [Jak zobrazit a načíst události protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: Projděte si protokol aktivit ve vašich předplatných a zkontrolujte změny vašich odchozích pravidel a skupin zabezpečení sítě pro vaše nástroje pro vyrovnávání zatížení. 

Prohlédněte si protokoly interního hostitele, abyste zajistili zabezpečení back-end prostředků.

Exportujte tyto protokoly do Log Analytics nebo jiné úložné platformy. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Povolte a zapněte tato data do Azure Sentinel nebo SIEM třetí strany na základě podnikových požadavků vaší organizace.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Jak shromažďovat protokoly interního hostitele virtuálních počítačů Azure pomocí Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Protokoly aktivit platformy](../azure-monitor/essentials/activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: Projděte si informace o protokolování a auditování roviny správy zaznamenaných pomocí protokolů aktivit pro základní Load Balancer. Tato nastavení zachycení jsou ve výchozím nastavení povolená. 

Pomocí protokolů aktivit můžete monitorovat akce v prostředcích a zobrazit všechny aktivity a jejich stav. 

Určete, jaké operace byly provedeny u prostředků ve vašem předplatném pomocí protokolů aktivit: 

- Kdo operaci zahájil
- Při výskytu operace
- stav operace

- hodnoty dalších vlastností, které vám mohou při průzkumu operace.

Načtěte informace z protokolu aktivit prostřednictvím Azure PowerShell, rozhraní příkazového řádku Azure (CLI), Azure REST API nebo Azure Portal. 

Implementujte multidimenzionální diagnostiku pro možnosti konfigurace Standard Load Balancer s využitím Azure Monitor.  Mezi ně patří dostupné metriky pro zabezpečení zahrnují informace o připojeních zdrojových síťových adres (SNAT), portech. K dispozici jsou také další metriky pro SYN (synchronizace) paketů a čítače paketů. 

Načtěte multidimenzionální metriky programově prostřednictvím rozhraní API a zapište je do účtu úložiště prostřednictvím možnosti všechny metriky.

Pomocí balíčku obsahu protokoly auditování Azure se službou Microsoft Power BI můžete analyzovat data pomocí předem nakonfigurovaných řídicích panelů nebo je přizpůsobit podle vašich požadavků.

Zasílat protokoly do centra událostí nebo do pracovního prostoru Log Analytics. Můžou se taky extrahovat z úložiště objektů BLOB v Azure a zobrazovat v různých nástrojích, jako je Excel a Power BI. 

Povolte a zapněte data do Azure Sentinel nebo SIEM třetí strany na základě vašich obchodních požadavků.

- [Přečtěte si tento článek s podrobnými pokyny pro jednotlivé metody popsané v tématu operace auditu s Správce prostředků](../azure-resource-manager/management/view-activity-logs.md)

- [Protokoly služby Azure Monitor pro veřejný Load Balancer úrovně Basic](load-balancer-monitor-log.md)

- [Zobrazení protokolů aktivit pro monitorování akcí u prostředků](../azure-resource-manager/management/view-activity-logs.md)

- [Načtěte multidimenzionální metriky prostřednictvím rozhraní API.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: protokol aktivit je ve výchozím nastavení povolený a v úložišti protokolů událostí Azure se zachovává 90 dní. Nastavte dobu uchování Log Analytics pracovního prostoru podle předpisů pro dodržování předpisů vaší organizace v Azure Monitor. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

- [Zobrazení protokolů aktivit pro sledování akcí v článku o prostředcích](../azure-resource-manager/management/view-activity-logs.md)

- [Změnit dobu uchovávání dat v Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: monitorování, Správa a řešení potíží s Standard Load Balancer prostředky pomocí stránky Load Balancer v Azure Portal a Resource Health stránky v části Azure monitor. Dostupné metriky pro zabezpečení zahrnují informace o připojeních zdrojových síťových adres (SNAT), portech. K dispozici jsou také metriky o paketech SYN (Synchronize) a čítačích paketů. 

Pomocí Azure Monitor můžete zkontrolovat stav testu stavu koncového bodu s multidimenzionálními metrikami pro standardní, externí a interní služby pro vyrovnávání zatížení. Tyto metriky Shromážděte programově prostřednictvím rozhraní API a zapisují se do účtu úložiště prostřednictvím možnosti všechny metriky.

Protokoly Azure Monitor nejsou k dispozici pro interní základní nástroje pro vyrovnávání zatížení. 

Pomocí Azure Monitor můžete zobrazit souhrnné informace o stavu sondy stavu pro každý back-end fond pro základní externí Load Balancer, jako je počet instancí ve fondu back-endu, který nepřijímá požadavky z Load Balancer kvůli selhání sond stavu. 

Implementujte protokolování s využitím Azure Operational Insights a poskytněte statistiku o stavu nástroje pro vyrovnávání zatížení. 

Pomocí protokolů aktivit můžete zobrazit výstrahy a monitorovat akce u prostředků a jejich stav v předplatných Azure. Protokoly aktivit jsou ve výchozím nastavení povolené a dají se zobrazit v Azure Portal. 

Použijte Microsoft Power BI s balíčkem obsahu protokoly auditování Azure a analyzujte data pomocí předem nakonfigurovaných řídicích panelů. Upravte zobrazení tak, aby vyhovovalo vašim požadavkům na základě obchodních potřeb. 

Zasílat protokoly do centra událostí nebo do pracovního prostoru Log Analytics. Můžou se taky extrahovat z úložiště objektů BLOB v Azure a zobrazovat v různých nástrojích, jako je Excel a Power BI. Data můžete povolit a zařadit do Azure Sentinel nebo SIEM třetí strany.

- [Sondy stavu Load Balanceru](load-balancer-custom-probe-overview.md)

- [Rozhraní REST API služby Azure Monitor](/rest/api/monitor)

- [Jak načíst metriky prostřednictvím REST API](/rest/api/monitor/metrics/list)

- [Diagnostika Standard Load Balancer s metrikami, výstrahami a stavem prostředků](load-balancer-standard-diagnostics.md)

- [Protokoly služby Azure Monitor pro veřejný Load Balancer úrovně Basic](load-balancer-monitor-log.md)

- [Zobrazit metriky nástroje pro vyrovnávání zatížení v Azure Portal](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: použijte Security Center s Log Analytics pracovním prostorem pro monitorování a upozorňování na aktivitu neobvyklé související se Load Balancer v protokolech zabezpečení a událostech.

Povolte a zapněte data pro Azure Sentinel nebo nástroj SIEM třetí strany.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Upozornění na data protokolu Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Pokyny**: řízení přístupu na základě role v Azure (Azure RBAC) umožňuje spravovat přístup k prostředkům Azure, jako jsou Load Balancer, prostřednictvím přiřazení rolí. Přiřaďte tyto role uživatelům, skupinám instančních objektů a spravovaným identitám.

Předem definované a předdefinované role pro určité prostředky pomocí nástrojů, jako je Azure CLI, Azure PowerShell nebo Azure Portal.

- [Postup získání role adresáře v Azure Active Directory (Azure AD) pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a sledujte doporučení pro správu identit a přístupu Security Center.

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny**: použití pracovních stanic s privilegovaným přístupem (privilegovaným přístupem) s nakonfigurovaným vícefaktorového ověřováním nakonfigurovaným pro správu a přístup k síťovým prostředkům Azure. 

- [Další informace o pracovních stanicích s privilegovaným přístupem](/security/compass/privileged-access-devices)

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné** materiály: použijte Azure Active Directory (Azure AD) jako centrální ověřování a systém autorizace pro vaše služby. Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data a také soli, hodnoty hash a bezpečně ukládá přihlašovací údaje uživatele.  

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Pokyny**: pomocí Azure Active Directory (Azure AD) poskytněte protokoly, které vám pomůžou zjistit zastaralé účty. 

Kontroly přístupu k identitám Azure je možné provádět k efektivní správě členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů by měl být pravidelně přezkoumán, aby se zajistilo, že všichni aktivní uživatelé budou mít přístup i nadále.

- [Pochopení sestav Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: Integrace Azure Active Directory (Azure AD) přihlašovacích aktivit, zdrojů auditu a rizikového protokolu událostí s jakýmkoli nástrojem pro Siem nebo monitorování na základě vašeho přístupu.

Zjednodušte tento proces vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Všechny požadované výstrahy je možné nakonfigurovat v rámci Log Analytics pracovního prostoru.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Doprovodné** materiály: použití funkcí ochrany rizik a Identity v Azure Active Directory (Azure AD) ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů. Ingestování dat do Azure Sentinel pro případ dalších šetření.

- [Zobrazení rizikových přihlášení Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: Ujistěte se, že všichni klienti, kteří se připojují k prostředkům Azure, můžou vyjednávat TLS 1,2 nebo vyšší.

Pokud je to možné, postupujte podle Azure Security Center doporučení pro šifrování v klidovém režimu a šifrování.

- [Pochopení šifrování při přenosu pomocí Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: ke správě přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: použití služby Azure RBAC k řízení přístupu k prostředkům Load Balancer.

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: Load Balancer je průchozí služba, která neukládá zákaznická data. Je součástí základní platformy, která je spravovaná Microsoftem. 

Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a ztrátou dat zákazníků. 

Aby bylo zajištěno zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat. 

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy, když se změny projeví u kritických prostředků Azure, jako jsou například nástroje pro vyrovnávání zatížení používané pro důležité provozní úlohy.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se v předplatných dotázatte na všechny prostředky (například výpočetní prostředky, úložiště, síť, porty, protokoly atd.). Pro vytváření a používání aktuálních prostředků se doporučuje Azure Resource Manager.

Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v předplatných mohli vytvořit výčet všech předplatných Azure

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure s metadaty k logickému uspořádání podle taxonomie.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry tam, kde je to vhodné, k uspořádání a sledování prostředků. 

Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků ze svých předplatných.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: Vytvořte seznam schválených prostředků Azure podle potřeb vaší organizace, které můžete využít jako mechanismus povolených. Tím umožníte, aby vaše organizace zavedla nové dostupné služby Azure poté, co je formálně vyhodnocena a schválena typickými procesy hodnocení zabezpečení vaší organizace.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných.

Dotazování a zjišťování prostředků pomocí Azure Resource graphu v rámci vlastněných předplatných. 

Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Příklady předdefinovaných modulů Azure Policy pro virtuální síť](/azure/virtual-network/policy-samples)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Doprovodné** materiály: použití podmíněného přístupu Azure Active Directory (Azure AD) k omezení schopnosti uživatelů pracovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: software, který je vyžadován pro obchodní operace, ale může pro organizaci zvýšit riziko, že by se měl izolovat v rámci vlastního virtuálního počítače nebo virtuální sítě a dostatečně zabezpečený pomocí Azure firewall nebo skupiny zabezpečení sítě.

- [Jak vytvořit virtuální síť](../virtual-network/quick-create-portal.md)

- [Vytvoření skupiny zabezpečení sítě s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace prostředků Azure. Použijte předdefinované definice Azure Policy.

Azure Resource Manager má možnost Exportovat šablonu do JavaScript Object Notation (JSON), která by měla být přezkoumána, aby konfigurace splňovala požadavky na zabezpečení vaší organizace.

Exportujte šablony Azure Resource Manager do formátů JavaScript Object Notation (JSON) a pravidelně je zkontrolujte, abyste zajistili, že konfigurace splňují požadavky na zabezpečení vaší organizace.

Implementujte doporučení z Security Center jako standardní hodnoty konfigurace pro vaše prostředky Azure.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.  Můžete také použít šablony Azure Resource Manager k údržbě konfigurace zabezpečení vašich prostředků Azure, které vaše organizace vyžaduje. 

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Přehled šablon Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny**: pomocí Azure DevOps bezpečně ukládejte a spravujte kód, třeba vlastní definice Azure Policy, šablony Azure Resource Manager a požadované konfigurační skripty pro stav.

Udělení nebo odmítnutí oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným ve službě Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps nebo ve službě Azure AD, pokud jsou integrované s TFS.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro prostředky Azure pomocí Azure Policy.  Pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich prostředků Azure. Implementujte integrované definice zásad, které se vztahují k vašim konkrétním Azure Load Balancer prostředkům.  K nasazení změn konfigurace taky použijte Azure Automation. do vašich Azure Load Balancerch prostředků.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak používat aliasy](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: použijte Security Center k provádění kontrol standardních hodnot vašich prostředků Azure a Azure Policy k oznamování a auditování konfigurací prostředků.

- [Jak opravit doporučení v Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení. 

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. 

Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Označení odběrů pomocí značek a vytvoření názvového systému pro identifikaci a kategorizaci prostředků Azure, zejména těch, které zpracovávají citlivá data.  

Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a pak podle potřeby upravte plán odpovědí. 

- [Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy. 

- [Jak nastavit kontakt zabezpečení pro Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: vyexportujte výstrahy a doporučení pro Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. 

Použijte funkci průběžného exportu v Security Center, která umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. 

Využijte Security Center Data Connector pro streamování výstrah do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Security Center k automatickému spouštění odpovědí na výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure.

- [Jak nakonfigurovat automatizaci pracovního postupu v zabezpečení zadejte](../security-center/workflow-automation.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: postupujte podle pravidel pro testování průniku Microsoft Cloud, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem. 

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
