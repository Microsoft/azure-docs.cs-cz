---
title: Základní hodnoty zabezpečení Azure pro Application Gateway
description: Základní Application Gateway zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: application-gateway
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: efb70d8d37f23dea501127d768f5654b1a4668c5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566434"
---
# <a name="azure-security-baseline-for-application-gateway"></a>Základní hodnoty zabezpečení Azure pro Application Gateway

Tato základní hodnota zabezpečení platí pro Application Gateway pokynů od [zabezpečení Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Application Gateway. **Ovládací prvky** , které se nevztahují k Application Gateway byly vyloučeny.

 
Pokud chcete zjistit, jak Application Gateway kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Application Gateway Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: Ujistěte se, že všechna Virtual Network nasazení podsítí Application Gateway Azure obsahuje skupinu zabezpečení sítě (NSG) s ovládacími prvky pro přístup k síti, které jsou specifické pro důvěryhodné porty a zdroje vaší aplikace. I když jsou v Azure Application Gateway podporovány skupiny zabezpečení sítě, existují určitá omezení a požadavky, které musí být dodrženy, aby vaše NSG a Azure Application Gateway fungovaly podle očekávání.

- [Vysvětlení omezení a požadavků v používání skupin zabezpečení sítě s Azure Application Gateway](configuration-overview.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy integrovaných definic – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné** materiály: u skupin zabezpečení sítě (skupin zabezpečení sítě) přidružených k vašim podsítím Azure Application Gateway povolit protokoly toku NSG a odesílat protokoly do účtu úložiště pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

Poznámka: v některých případech se v protokolech NSG Flow přidružených k vašim podsítím Azure Application Gateway nezobrazuje povolený provoz. Pokud vaše konfigurace odpovídá následujícímu scénáři, neuvidíte v protokolech toku NSG povolený provoz:

- Nasadili jste Application Gateway v2
- Máte NSGu v podsíti služby Application Gateway.
- Povolili jste protokoly toku NSG na těchto NSG.

Další informace najdete v níže uvedených odkazech.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Nejčastější dotazy týkající se diagnostiky a protokolování pro Azure Application Gateway](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy integrovaných definic – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Pokyny**: nasazení firewallu webových aplikací Azure (WAF) před kritickými webovými aplikacemi pro další kontrolu příchozího provozu. Firewall webových aplikací (WAF) je služba (funkce Azure Application Gateway), která poskytuje centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. Služba Azure WAF vám může pomáhat zabezpečit vaše Azure App Service webové aplikace tím, že zkontroluje příchozí webový provoz a zablokuje útoky, jako jsou například injektáže SQL, skriptování mezi weby, nakládku malwaru a útoky DDoS. WAF je založená na pravidlech z OWASP (Open Web Application Security Project) základních sad pravidel 3,1 (jenom WAF_v2), 3,0 a 2.2.9. 

- [Vysvětlení funkcí služby Azure Application Gateway](features.md)

- [Pochopení Azure WAF](../web-application-firewall/ag/ag-overview.md)

- [Jak nasadit Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: povolení DDoS Standard Protection na virtuálních sítích Azure přidružených k provozním instancím Azure Application Gateway pro ochranu před útoky DDoS. K odepření komunikace se známými škodlivými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

- [Jak nakonfigurovat DDoS Protection](../ddos-protection/manage-ddos-protection.md)

- [Pochopení Azure Security Center integrované analýzy hrozeb](../security-center/azure-defender.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy integrovaných definic – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné** materiály: u skupin zabezpečení sítě (skupin zabezpečení sítě) přidružených k vašim podsítím Azure Application Gateway povolit protokoly toku NSG a odesílat protokoly do účtu úložiště pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

Poznámka: v některých případech se v protokolech NSG Flow přidružených k vašim podsítím Azure Application Gateway nezobrazuje povolený provoz. Pokud vaše konfigurace odpovídá následujícímu scénáři, neuvidíte v protokolech toku NSG povolený provoz:

- Nasadili jste Application Gateway v2
- Máte NSGu v podsíti služby Application Gateway.
- Povolili jste protokoly toku NSG na těchto NSG.

Další informace najdete v níže uvedených odkazech.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Nejčastější dotazy týkající se diagnostiky a protokolování pro Azure Application Gateway](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy integrovaných definic – Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny**: nasazení firewallu webových aplikací Azure (WAF) před kritickými webovými aplikacemi pro další kontrolu příchozího provozu. Firewall webových aplikací (WAF) je služba (funkce Azure Application Gateway), která poskytuje centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. Služba Azure WAF vám může pomáhat zabezpečit vaše Azure App Service webové aplikace tím, že zkontroluje příchozí webový provoz a zablokuje útoky, jako jsou například injektáže SQL, skriptování mezi weby, nakládku malwaru a útoky DDoS. WAF je založená na pravidlech z OWASP (Open Web Application Security Project) základních sad pravidel 3,1 (jenom WAF_v2), 3,0 a 2.2.9. 

Alternativně je k dispozici několik možností Marketplace, jako je Barracuda WAF pro Azure, které jsou k dispozici na Azure Marketplace, které zahrnují funkce ID/IP adres.

- [Vysvětlení funkcí služby Azure Application Gateway](features.md)

- [Pochopení Azure WAF](../web-application-firewall/ag/ag-overview.md)

- [Jak nasadit Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Principy cloudové služby Barracuda WAF](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Pokyny**: nasazení služby Azure Application Gateway pro webové aplikace s POVOLENým protokolem HTTPS/SSL u důvěryhodných certifikátů.

- [Postup nasazení Application Gateway](quick-create-portal.md)

- [Postup konfigurace Application Gateway pro použití protokolu HTTPS](create-ssl-portal.md)

- [Princip vyrovnávání zatížení vrstvy 7 pomocí bran webových aplikací Azure](overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: použití značek Virtual Network služby k definování řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. GatewayManager) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Pro skupiny zabezpečení sítě (skupin zabezpečení sítě) přidružené k vašim podsítím Azure Application Gateway musíte povolen příchozí internetový provoz na portech TCP 65503-65534 pro SKU Application Gateway V1 a porty TCP 65200-65535 pro SKU v2 s cílovou podsítí jako libovolný a zdroj jako značka služby GatewayManager. Tento rozsah portů je nutný pro komunikaci s infrastrukturou Azure. Tyto porty jsou chráněné (uzamčené) pomocí certifikátů Azure. Externí entity, včetně zákazníků těchto bran, nemůžou komunikovat na těchto koncových bodech.

- [Pochopení a použití značek služeb](../virtual-network/service-tags-overview.md)

- [Přehled konfigurace Azure Application Gateway](configuration-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro nastavení sítě související s nasazeními Azure Application Gateway. Pomocí aliasů Azure Policy v oboru názvů Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě vašich bran Azure Application Gateway, virtuálních sítí Azure a skupin zabezpečení sítě. Můžete také využít vestavěnou definici zásad.

Plány Azure můžete použít také ke zjednodušení rozsáhlých nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, řízení přístupu na základě role Azure (Azure RBAC) a zásady v jediné definici podrobného plánu. V rámci správy verzí můžete snadno použít podrobný plán na nová předplatná, prostředí a vyladit řízení a správu.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Pokyny**: používejte značky pro skupiny zabezpečení sítě (skupin zabezpečení sítě) přidružené k vaší podsíti Azure Application Gateway a také všechny další prostředky, které se týkají zabezpečení sítě a toku provozu. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z vestavěných definic zásad Azure souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že se vytvoří všechny prostředky s značkami a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny nastavení sítě a prostředků souvisejících s nasazeními Azure Application Gateway. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny kritických nastavení sítě nebo prostředků.

- [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: pro protokolování auditu řídicích rovin povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni ovládacího prvku pro Azure Application Gateway a souvisejících prostředků, jako jsou třeba skupiny zabezpečení sítě (skupin zabezpečení sítě), se používají k ochraně podsítě Azure Application Gateway.

Kromě protokolů aktivit můžete nakonfigurovat nastavení diagnostiky pro nasazení služby Azure Application Gateway. nastavení diagnostiky se používají ke konfiguraci exportu protokolů a metrik platforem pro prostředek do cílového umístění podle vašeho výběru (účty úložiště, Event Hubs a Log Analytics).

Azure Application Gateway také nabízí integrovanou integraci s Azure Application Insights. Application Insights shromažďuje údaje o protokolech, výkonu a chybách. Application Insights automaticky detekuje anomálie výkonu a zahrnuje výkonné analytické nástroje, které vám pomůžou diagnostikovat problémy a pochopit, jak se používají vaše webové aplikace. Průběžný export můžete povolit pro export telemetrie z Application Insights do centralizovaného umístění, aby data byla delší než standardní doba uchovávání dat.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Postup povolení nastavení diagnostiky pro Azure Application Gateway](application-gateway-diagnostics.md)

- [Postup povolení Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Postup konfigurace průběžného exportu](../azure-monitor/app/export-telemetry.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: pro protokolování auditu řídicích rovin povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni ovládacího prvku pro Azure Application Gateway a souvisejících prostředků, jako jsou třeba skupiny zabezpečení sítě (skupin zabezpečení sítě), se používají k ochraně podsítě Azure Application Gateway.

Kromě protokolů aktivit můžete nakonfigurovat nastavení diagnostiky pro nasazení služby Azure Application Gateway. nastavení diagnostiky se používají ke konfiguraci exportu protokolů a metrik platforem pro prostředek do cílového umístění podle vašeho výběru (účty úložiště, Event Hubs a Log Analytics).

Azure Application Gateway také nabízí integrovanou integraci s Azure Application Insights. Application Insights shromažďuje údaje o protokolech, výkonu a chybách. Application Insights automaticky detekuje anomálie výkonu a zahrnuje výkonné analytické nástroje, které vám pomůžou diagnostikovat problémy a pochopit, jak se používají vaše webové aplikace. Průběžný export můžete povolit pro export telemetrie z Application Insights do centralizovaného umístění, aby data byla delší než standardní doba uchovávání dat.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Postup povolení nastavení diagnostiky pro Azure Application Gateway](application-gateway-diagnostics.md)

- [Postup povolení Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Postup konfigurace průběžného exportu](../azure-monitor/app/export-telemetry.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v rámci Azure monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

- [Postup nastavení parametrů uchovávání protokolů pro Log Analytics pracovní prostory](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: Povolte nastavení diagnostiky protokolu aktivit Azure a také nastavení diagnostiky pro váš Application Gateway Azure a protokoly odešlete do pracovního prostoru Log Analytics. Můžete provádět dotazy v Log Analytics k hledání podmínek, identifikaci trendů, analýze vzorů a poskytování mnoha dalších přehledů na základě shromážděných dat.

Pomocí Azure Monitor pro sítě získáte komplexní přehled o stavu a metrikách všech nasazených síťových prostředků, včetně vašich aplikačních bran Azure. 

Volitelně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Postup povolení nastavení diagnostiky pro Azure Application Gateway](application-gateway-diagnostics.md)

- [Jak používat Azure Monitor pro sítě](../azure-monitor/insights/network-insights-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Pokyny**: nasaďte SKU Azure Web Application firewall (WAF) v2 SKU před kritickými webovými aplikacemi za účelem další kontroly příchozího provozu. Firewall webových aplikací (WAF) je služba (funkce Azure Application Gateway), která poskytuje centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. Služba Azure WAF vám může pomáhat zabezpečit vaše Azure App Service webové aplikace tím, že zkontroluje příchozí webový provoz a zablokuje útoky, jako jsou například injektáže SQL, skriptování mezi weby, nakládku malwaru a útoky DDoS. WAF je založená na pravidlech z OWASP (Open Web Application Security Project) základních sad pravidel 3,1 (jenom WAF_v2), 3,0 a 2.2.9. 

Povolte nastavení diagnostiky protokolu aktivit Azure a také nastavení diagnostiky pro Azure WAF a odešlete protokoly do pracovního prostoru Log Analytics. Můžete provádět dotazy v Log Analytics k hledání podmínek, identifikaci trendů, analýze vzorů a poskytování mnoha dalších přehledů na základě shromážděných dat.  Můžete vytvářet výstrahy na základě vašich dotazů na pracovní prostor Log Analytics.

Pomocí Azure Monitor pro sítě získáte komplexní přehled o stavu a metrikách všech nasazených síťových prostředků, včetně vašich aplikačních bran Azure. V konzole Azure Monitor pro sítě můžete zobrazit a vytvořit výstrahy pro Azure Application Gateway.

- [Jak nasadit Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Postup povolení nastavení diagnostiky pro Azure Application Gateway](application-gateway-diagnostics.md)

- [Jak používat Azure Monitor pro sítě](../azure-monitor/insights/network-insights-overview.md)

- [Vytvoření upozornění v rámci Azure](../azure-monitor/alerts/tutorial-response.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Pokyny**: nasazení firewallu webových aplikací Azure (WAF) v2 před kritickými webovými aplikacemi pro další kontrolu příchozího provozu. Firewall webových aplikací (WAF) je služba (funkce Azure Application Gateway), která poskytuje centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. Služba Azure WAF vám může pomáhat zabezpečit vaše Azure App Service webové aplikace tím, že zkontroluje příchozí webový provoz a zablokuje útoky, jako jsou například injektáže SQL, skriptování mezi weby, nakládku malwaru a útoky DDoS.

Nakonfigurujte nastavení diagnostiky pro nasazení služby Azure Application Gateway. nastavení diagnostiky se používají ke konfiguraci exportu protokolů a metrik platforem pro prostředek do cílového umístění podle vašeho výběru (účty úložiště, Event Hubs a Log Analytics).

- [Jak nasadit Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Jak nakonfigurovat nastavení diagnostiky pro Azure WAF](application-gateway-diagnostics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Azure Active Directory (Azure AD) mají předdefinované role, které se musí explicitně přiřadit a které jsou Queryable. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: přístup roviny řízení přístupu k Azure Application Gateway je řízen prostřednictvím Azure Active Directory (Azure AD). Azure AD nemá koncept výchozích hesel.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Kromě toho můžete použít doporučení z Azure Security Center nebo integrovaných zásad Azure, jako je například:

- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.

Další informace najdete v níže uvedených odkazech.

- [Použití Azure Security Center k monitorování identity a přístupu (Preview)](../security-center/security-center-identity-access.md)

- [Jak používat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Pokyny**: pomocí registrace aplikace Azure (instanční objekt) načtěte token, který se dá použít k interakci se službou Azure Application Gateway prostřednictvím volání rozhraní API.

- [Volání rozhraní Azure REST API](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Postup registrace klientské aplikace (instančního objektu) pomocí Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informace o rozhraní API pro Azure Recovery Services](/rest/api/recoveryservices/)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a sledujte Azure Security Center doporučení pro správu identit a přístupu.

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Doprovodné** materiály: Používejte privilegovaným přístupem (Privileged Access Workstations) s nakonfigurovaným vícefaktorového ověřováním, které je nakonfigurované pro přihlášení k prostředkům Azure a jejich konfiguraci.

- [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použití sestav zabezpečení Azure Active Directory (Azure AD) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu.

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](../active-directory/identity-protection/overview-identity-protection.md)

- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: máte přístup k aktivitě pro přihlášení k Azure Active Directory (Azure AD), událostem auditu a rizikovým protokolům událostí, které umožňují integraci s jakýmkoli nástrojem Siem/monitoring.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Pokyny**: pomocí funkcí Azure Active Directory (Azure AD) Identity Protection a detekce rizik můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce související s identitami uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace. 

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Zajistěte, aby všechna Virtual Network nasazení podsítí Application Gateway Azure používala skupinu zabezpečení sítě (NSG) s ovládacími prvky pro přístup k síti, které jsou specifické pro důvěryhodné porty a zdroje vaší aplikace. I když jsou v Azure Application Gateway podporovány skupiny zabezpečení sítě, existují určitá omezení a požadavky, které musí být dodrženy, aby vaše NSG a Azure Application Gateway fungovaly podle očekávání.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Vysvětlení omezení a požadavků v používání skupin zabezpečení sítě s Azure Application Gateway](configuration-overview.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Pokyny**: Ujistěte se, že všechna Virtual Network nasazení podsítí Application Gateway Azure obsahuje skupinu zabezpečení sítě (NSG) s ovládacími prvky pro přístup k síti, které jsou specifické pro důvěryhodné porty a zdroje vaší aplikace. Omezte odchozí provoz jenom na důvěryhodná umístění, aby se zabránilo zmírnění hrozby exfiltrace dat. I když jsou v Azure Application Gateway podporovány skupiny zabezpečení sítě, existují určitá omezení a požadavky, které musí být dodrženy, aby vaše NSG a Azure Application Gateway fungovaly podle očekávání.

- [Vysvětlení omezení a požadavků v používání skupin zabezpečení sítě s Azure Application Gateway](configuration-overview.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: Konfigurace komplexního šifrování s protokolem TLS pro vaše aplikační brány Azure.

- [Jak nakonfigurovat kompletní protokol TLS pomocí Azure Application Gateway](end-to-end-ssl-portal.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte řízení přístupu na základě role

**Pokyny**: použijte řízení přístupu na základě role Azure (Azure RBAC) k řízení přístupu k rovině ovládacího prvku Azure Application Gateway (Azure Portal).

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet upozornění na případy, kdy změny probíhají v produkčních instancích Azure Application Gateway, i v jiných důležitých nebo souvisejících prostředcích.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: aktuálně není k dispozici; posouzení ohrožení zabezpečení v Azure Security Center ještě není pro Azure Application Gateway dostupné.

Nadřazená platforma byla zkontrolována a opravena společností Microsoft. Přečtěte si bezpečnostní mechanismy, které jsou dostupné pro Azure Application Gateway, abyste snížili ohrožení zabezpečení související s konfigurací.

- [Pokrytí funkcí (včetně posouzení ohrožení zabezpečení) pro služby Azure PaaS](../security-center/features-paas.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: ještě není k dispozici; posouzení ohrožení zabezpečení v Azure Security Center ještě není pro Azure Application Gateway dostupné.

Nadřazená platforma byla zkontrolována a opravena společností Microsoft. Přečtěte si bezpečnostní mechanismy, které jsou dostupné pro Azure Application Gateway, abyste snížili ohrožení zabezpečení související s konfigurací.

- [Pokrytí funkcí (včetně posouzení ohrožení zabezpečení) pro služby Azure PaaS](../security-center/features-paas.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: ještě není k dispozici; posouzení ohrožení zabezpečení v Azure Security Center ještě není pro Azure Application Gateway dostupné.

Nadřazená platforma byla zkontrolována a opravena společností Microsoft. Přečtěte si bezpečnostní mechanismy, které jsou dostupné pro Azure Application Gateway, abyste snížili ohrožení zabezpečení související s konfigurací.

- [Pokrytí funkcí (včetně posouzení ohrožení zabezpečení) pro služby Azure PaaS](../security-center/features-paas.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci vašich předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.). Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure i prostředků

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků Azure. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Kromě toho použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných zákazníka, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

Další informace najdete v níže uvedených odkazech.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit ve vašich předplatných. 

Pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat prostředky.  Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

Další informace najdete v níže uvedených odkazech.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: Nakonfigurujte podmíněný přístup Azure tak, aby uživatelé mohli komunikovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Zajistěte, aby všechna Virtual Network nasazení podsítí Application Gateway Azure používala skupinu zabezpečení sítě (NSG) s ovládacími prvky pro přístup k síti, které jsou specifické pro důvěryhodné porty a zdroje vaší aplikace. I když jsou v Azure Application Gateway podporovány skupiny zabezpečení sítě, existují určitá omezení a požadavky, které musí být dodrženy, aby vaše NSG a Azure Application Gateway fungovaly podle očekávání.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Vysvětlení omezení a požadavků v používání skupin zabezpečení sítě s Azure Application Gateway](configuration-overview.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro nastavení sítě související s nasazeními Azure Application Gateway. Pomocí aliasů Azure Policy v oboru názvů Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě vašich bran Azure Application Gateway, virtuálních sítí Azure a skupin zabezpečení sítě. Můžete také využít vestavěnou definici zásad.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: použijte zásady Azure [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení napříč prostředky Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Pokud používáte vlastní definice zásad Azure, použijte k bezpečnému ukládání a správě kódu službu Azure DevOps nebo Azure Repos.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentace k Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. Network k vytváření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. Network k vytváření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. K automatickému vymáhání konfigurací pro prostředky Azure použijte zásady Azure [audit], [Deny] a [nasazení, pokud neexistuje].

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: použití spravovaných identit k poskytnutí Application Gateway Azure s automaticky spravovanou identitou v Azure Active Directory (Azure AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

Používejte Azure Key Vault k bezpečnému ukládání certifikátů. Azure Key Vault je úložiště tajné databáze spravované platformou, které můžete použít k ochraně tajných klíčů, klíčů a certifikátů SSL. Azure Application Gateway podporuje integraci s Key Vault pro certifikáty serveru, které jsou připojené k naslouchacím procesům s povoleným protokolem HTTPS. Tato podpora je omezená na SKU Application Gateway v2.

- [Jak nakonfigurovat ukončení protokolu SSL pomocí Key Vault certifikátů pomocí Azure PowerShell](configure-keyvault-ps.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: použití spravovaných identit k poskytnutí Application Gateway Azure s automaticky spravovanou identitou v Azure Active Directory (Azure AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

Používejte Azure Key Vault k bezpečnému ukládání certifikátů. Azure Key Vault je úložiště tajné databáze spravované platformou, které můžete použít k ochraně tajných klíčů, klíčů a certifikátů SSL. Azure Application Gateway podporuje integraci s Key Vault pro certifikáty serveru, které jsou připojené k naslouchacím procesům s povoleným protokolem HTTPS. Tato podpora je omezená na SKU Application Gateway v2.

- [Jak nakonfigurovat ukončení protokolu SSL pomocí Key Vault certifikátů pomocí Azure PowerShell](configure-keyvault-ps.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použijte centrálně spravovaný antimalwarový software

**Pokyny**: nasazení firewallu webových aplikací Azure (WAF) v2 před kritickými webovými aplikacemi pro další kontrolu příchozího provozu. Firewall webových aplikací (WAF) je služba (funkce Azure Application Gateway), která poskytuje centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. Služba Azure WAF vám může pomáhat zabezpečit vaše Azure App Service webové aplikace tím, že zkontroluje příchozí webový provoz a zablokuje útoky, jako jsou například injektáže SQL, skriptování mezi weby, nakládku malwaru a útoky DDoS.

Nakonfigurujte nastavení diagnostiky pro nasazení služby Azure Application Gateway. nastavení diagnostiky se používají ke konfiguraci exportu protokolů a metrik platforem pro prostředek do cílového umístění podle vašeho výběru (účty úložiště, Event Hubs a Log Analytics).

- [Jak nasadit Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Jak nakonfigurovat nastavení diagnostiky pro Azure WAF](application-gateway-diagnostics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: při použití firewallu webových aplikací Azure (WAF) můžete nakonfigurovat zásady WAF. Zásady WAF se skládají ze dvou typů pravidel zabezpečení: vlastní pravidla, která jsou vytvořená zákazníkem, a sady spravovaných pravidel, které jsou kolekcí předem nakonfigurovaných sad pravidel spravovaných Azure. Sady pravidel spravované pomocí Azure poskytují snadný způsob, jak nasadit ochranu proti běžné sadě bezpečnostních hrozeb. Vzhledem k tomu, že tato RuleSets spravuje Azure, pravidla se aktualizují podle potřeby k ochraně před novými signaturami útoku.

- [Porozumění sadám pravidel WAF spravovaných v Azure](../web-application-firewall/ag/ag-overview.md#waf-policy-and-rules)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: v Azure Application Gateway neukládají zákaznická data. Pokud ale používáte vlastní definice zásad Azure, použijte k bezpečnému ukládání a správě kódu službu Azure DevOps nebo Azure Repos.

Sada Azure DevOps Services využívá řadu funkcí úložiště Azure k zajištění dostupnosti dat v případě selhání hardwaru, přerušení služeb nebo havárie v oblasti. Tým Azure DevOps nadto dodržuje postupy, které chrání data před náhodným nebo nepřátelským odstraněním.

- [Pochopení dostupnosti dat v Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentace k Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: zálohování certifikátů spravovaných zákazníkem v rámci služby Azure Key Vault.

- [Postup zálohování certifikátů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny**: testování obnovení zálohovaných certifikátů spravovaných zákazníkem.

- [Postup obnovení certifikátů trezoru klíčů](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: Ujistěte se, že je pro Azure Key Vault povolené obnovitelné odstranění. Obnovitelné odstranění umožňuje obnovit odstraněné trezory klíčů a objekty trezoru, jako jsou klíče, tajné klíče a certifikáty.

- [Použití obnovitelného odstranění Azure Key Vault](../key-vault/general/key-vault-recovery.md)

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

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana.  Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: postupujte podle pravidel pro testování průniku Microsoft Cloud, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem. 

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](../security/benchmarks/overview.md).
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)
