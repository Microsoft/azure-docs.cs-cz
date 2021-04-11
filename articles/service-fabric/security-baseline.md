---
title: Základní hodnoty zabezpečení Azure pro Service Fabric
description: Základní Service Fabric zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ee294cff85bb71b5c2a238fcf985fc870ed32618
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285432"
---
# <a name="azure-security-baseline-for-service-fabric"></a>Základní hodnoty zabezpečení Azure pro Service Fabric

Tato základní hodnota zabezpečení platí pro Service Fabric pokynů od [verze 1.0 služby Azure Security test](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Service Fabric.

> [!NOTE]
> **Ovládací prvky** , které se nevztahují na Service Fabric nebo pro které je odpovědnost od Microsoftu, jsou vyloučené. Pokud chcete zjistit, jak Service Fabric kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si **[celý soubor mapování standardních hodnot Service Fabric Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/service-fabric-security-baseline-v1.1.xlsx)**.

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: Ujistěte se, že všechna nasazení Virtual Network podsítí mají skupinu zabezpečení sítě použitou pro řízení přístupu k síti specifická pro důvěryhodné porty a zdroje vaší aplikace. 

- [Nasazení brány Azure Firewall pomocí šablony](../firewall/deploy-template.md)

- [Vytváření hraničních sítí pomocí skupin zabezpečení sítě Azure (skupin zabezpečení sítě)](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices#use-network-isolation-and-security-with-azure-service-fabric)

- [Jak integrovat svůj cluster Azure Service Fabric se stávající virtuální sítí](service-fabric-patterns-networking.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových karet

**Doprovodné** materiály: použijte Security Center a opravte doporučení k ochraně sítě pro virtuální síť, podsíť a skupinu zabezpečení sítě, která se používá k zabezpečení clusteru Azure Service Fabric. Povolte protokol toků skupin zabezpečení sítě a odešlete protokoly do účtu Azure Storage k auditu provozu. Do pracovního prostoru Azure Log Analytics můžete také odesílat protokoly skupin zabezpečení sítě a pomocí Azure Analýza provozu poskytovat přehledy o toku provozu v cloudu Azure. Mezi výhody Azure Analýza provozu je schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak povolit a používat Azure Analýza provozu](../network-watcher/traffic-analytics.md)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: Poskytněte front-end bránu, která poskytuje jeden bod příchozího přenosu dat pro uživatele, zařízení nebo jiné aplikace. Služba Azure API Management se přímo integruje s Service Fabric, což vám umožní zabezpečit přístup k back-endové službě, zabránit útokům DOS pomocí omezování a ověřovat klíče rozhraní API, tokeny JWT, certifikáty a další přihlašovací údaje.

Zvažte nasazení firewallu webových aplikací Azure (WAF) před kritickými webovými aplikacemi pro další kontrolu příchozího provozu. Povolte nastavení diagnostiky pro protokoly WAF a ingestování do účtu úložiště, centra událostí nebo Log Analytics pracovního prostoru.

- [Service Fabric se službou Azure API Management – Přehled](service-fabric-api-management-overview.md)

- [Integrace služby API Management v interní virtuální síti se službou Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Jak nasadit Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: u ochrany před útoky DDoS povolte službu Azure DDoS Standard Protection ve virtuální síti, ve které je nasazený cluster Azure Service Fabric. K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

- [Jak nakonfigurovat DDoS Protection](../ddos-protection/manage-ddos-protection.md)

- [Pochopení Azure Security Center integrované analýzy hrozeb](../security-center/azure-defender.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Pokyny**: povolení protokolů toku skupin zabezpečení sítě pro skupiny zabezpečení sítě připojené k podsíti používané k ochraně Service Fabricho clusteru. Zaznamenejte NSG Flow do účtu Azure Storage pro generování záznamů toků. Pokud se to vyžaduje pro prošetření aktivity neobvyklé, povolte zachytávání paketů služby Azure Network Watcher.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení Network Watcher](../network-watcher/network-watcher-create.md)

- [Použití analýzy provozu k vizualizaci protokolů toku NSG](../network-watcher/traffic-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: vyberte nabídku z Azure Marketplace, která podporuje funkce ID/IP adresy s možnostmi kontroly zatížení.  Pokud zjišťování neoprávněných vniknutí nebo prevence na základě kontroly zatížení není požadavkem, je možné použít Azure Firewall s analýzou hrozeb. Azure Firewall filtrování na základě logiky hrozeb může upozorňovat a zamítnout provoz do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu. 

Nasaďte řešení brány firewall podle svého výběru na základě hranic sítě vaší organizace za účelem detekce nebo odepření škodlivého provozu. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md) 

- [Postup konfigurace výstrah pomocí Azure Firewall](../firewall/threat-intel.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Pokyny**: nasazení služby Azure Application Gateway pro webové aplikace s POVOLENým protokolem HTTPS/SSL u důvěryhodných certifikátů. 

- [Postup nasazení Application Gateway](../application-gateway/quick-create-portal.md)

- [Postup konfigurace Application Gateway pro použití protokolu HTTPS](../application-gateway/create-ssl-portal.md)

- [Princip vyrovnávání zatížení vrstvy 7 pomocí bran webových aplikací Azure](../application-gateway/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: pomocí značek služby virtuální sítě definujte řízení přístupu k síti u skupin zabezpečení sítě (NSG), které jsou připojené k podsíti, ve které je nasazený cluster Azure Service Fabric. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Značky služby virtuální sítě](../virtual-network/service-tags-overview.md)

- [Osvědčené postupy pro Service Fabric sítě](service-fabric-best-practices-networking.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky týkající se vašeho clusteru Azure Service Fabric. Pomocí aliasů Azure Policy v oborech názvů Microsoft. ServiceFabric a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě pro cluster Azure Service Fabric.

Pomocí plánů Azure můžete také zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, řízení a zásady služby Azure RBAC v rámci jedné definice podrobného plánu. Podrobné sestavování můžete snadno použít pro nová předplatná a prostředí a vyladit řízení a správu prostřednictvím správy verzí.

- [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro skupiny zabezpečení sítě a další prostředky související se zabezpečením sítě a tokem přenosů, které jsou přidružené k vašemu Service Fabric clusteru. Pro jednotlivá pravidla skupin zabezpečení sítě použijte pole Popis k určení potřeby podniku, doby trvání a tak dále pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo rozhraní příkazového řádku Azure (CLI) můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Jak vytvořit virtuální síť](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s nasazeními Azure Service Fabric. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: můžete připojit cluster Azure Service Fabric, aby Azure monitor agregovaná data zabezpečení generovaná clusterem. Podívejte se na příklady problémy s diagnostikou a řešení pomocí Service Fabric.

- [Konfigurace Azure Monitor protokolů Integration s Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Nastavení protokolů Azure Monitor pro kontejnery monitorování v Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Diagnostikování běžných Service Fabric scénářů](service-fabric-diagnostics-common-scenarios.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: Povolte Azure monitor pro cluster Service Fabric a nasměrujte ho do pracovního prostoru Log Analytics. Tím se zaprotokolují relevantní informace o clusteru a metriky OS pro všechny uzly clusteru Azure Service Fabric.

- [Konfigurace Azure Monitor protokolů Integration s Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Nastavení protokolů Azure Monitor pro kontejnery monitorování v Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Jak nasadit agenta Log Analytics do svých uzlů](service-fabric-diagnostics-oms-agent.md)

- [Log Analytics prohledávání protokolu](/azure/azure-monitor/log-query/log-query-overview)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: připojení clusteru Azure Service Fabric k Azure monitor. Ujistěte se, že použitý pracovní prostor Log Analytics má dobu uchování protokolu nastavenou v souladu s předpisy pro dodržování předpisů vaší organizace.

- [Konfigurace Azure Monitor protokolů Integration s Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Nastavení protokolů Azure Monitor pro kontejnery monitorování v Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Jak nasadit agenta Log Analytics do svých uzlů](service-fabric-diagnostics-oms-agent.md) 

- [Jak nakonfigurovat dobu uchování Log Analytics pracovního prostoru](/azure/azure-monitor/platform/manage-cost-storage)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: připojení clusteru Service Fabric k Azure monitor. Ujistěte se, že použitý pracovní prostor Log Analytics má dobu uchování protokolu nastavenou v souladu s předpisy pro dodržování předpisů vaší organizace.

- [Konfigurace Azure Monitor protokolů Integration s Service Fabric](service-fabric-diagnostics-oms-setup.md)

- [Nastavení protokolů Azure Monitor pro kontejnery monitorování v Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Jak nasadit agenta Log Analytics do svých uzlů](service-fabric-diagnostics-oms-agent.md)

- [Jak nakonfigurovat dobu uchování Log Analytics pracovního prostoru](/azure/azure-monitor/platform/manage-cost-storage)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: použití dotazů v pracovním prostoru Azure Log Analytics k dotazování protokolů služby Azure Service Fabric.

- [Log Analytics prohledávání protokolu](/azure/azure-monitor/log-query/log-query-overview)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Pokyny**: pracovní prostor Azure Log Analytics slouží k monitorování a upozorňování na aktivity neobvyklé v protokolech zabezpečení a událostech souvisejících s vaším clusterem Azure Service Fabric.

- [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Upozornění na data protokolu Log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: ve výchozím nastavení je Windows Defender nainstalovaný na windows serveru 2016. Pokud nepoužíváte Windows Defender, přečtěte si dokumentaci k Antimaleware pro pravidla konfigurace. Program Windows Defender není podporován v systému Linux.

- [Podrobnosti najdete v tématu antivirová ochrana v programu Windows Defender na Windows serveru 2016.](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Pokyny**: implementace řešení třetí strany pro protokolování DNS.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Pokyny**: ruční konfigurace protokolování konzoly na základě jednotlivých uzlů.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Udržujte si záznam místního účtu pro správu, který se vytvoří během zřizování clusteru Azure Service Fabric clusteru, i u všech dalších účtů, které vytvoříte. Kromě toho, pokud se používá Integrace Azure Active Directory (Azure AD), Azure AD má předdefinované role, které se musí explicitně přiřadit a proto se Queryable. Pomocí modulu Azure AD PowerShell můžete provádět dotazy ad hoc a zjišťovat účty, které jsou členy skupin pro správu.

Kromě toho můžete použít Azure Security Center doporučení pro správu identit a přístupu.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Jak monitorovat identitu a přístup pomocí Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné** materiály: při zřizování clusteru vyžaduje Azure vytvořit nová hesla pro webový portál. Neexistují žádná výchozí hesla, která by bylo možné změnit, ale můžete zadat různá hesla pro přístup k webovému portálu.

- [Vytvoření na webu Azure Portal](service-fabric-cluster-creation-via-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Pokyny**: integrace ověřování pro Service Fabric s Azure Active Directory (Azure AD). Vytvořte zásady a postupy týkající se používání vyhrazených účtů pro správu.

Kromě toho můžete použít Security Center doporučení pro správu identit a přístupu.

- [Nastavení ověřování klientů Azure AD](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster#set-up-azure-active-directory-client-authentication)

- [Jak monitorovat identitu a přístup pomocí Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné** materiály: kdykoli je to možné, použijte službu Azure Active Directory (Azure AD) SSO místo konfigurace jednotlivých samostatných přihlašovacích údajů pro každou službu. Použijte Security Center doporučení pro správu identit a přístupu. 

- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a sledujte Security Center doporučení pro správu identit a přístupu.

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny**: použití pracovní stanice s privilegovaným přístupem (privilegovaným přístupem) s nakonfigurovaným vícefaktorového ověřováním, které je nakonfigurované pro přihlášení k a konfiguraci Service Fabricch clusterů a souvisejících prostředků.

- [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použijte Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Navíc můžete pomocí zjišťování rizik Azure AD zobrazovat výstrahy a sestavy týkající se rizikového chování uživatelů.

- [Postup nasazení Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Vysvětlení zjišťování rizik Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí. 

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné** materiály: použijte Azure Active Directory (Azure AD) jako centrální ověřování a autorizační systém k zabezpečení přístupu ke koncovým bodům správy Service Fabric clusterů. Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Nastavení služby Azure AD pro Service Fabric ověřování klientů](service-fabric-cluster-creation-setup-aad.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. ServiceFabric**:

[!INCLUDE [Resource Policy for Microsoft.ServiceFabric 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.servicefabric-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: použití ověřování Azure Active Directory (Azure AD) s clusterem Service Fabric. Azure AD poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatele se může pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-alert-on-account-login-behavior-deviation"></a>3,11: upozornění na odchylku chování přihlášení k účtu

**Pokyny**: pomocí Azure Active Directory (Azure AD) přihlašování a protokolů auditu můžete monitorovat pokusy o přístup k deaktivovaným účtům. Tyto protokoly je možné integrovat do jakéhokoli nástroje SIEM/monitoring třetí strany.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD, odesláním protokolů auditu a přihlašovacích protokolů do pracovního prostoru Azure Log Analytics. Nakonfigurujte požadované výstrahy v pracovním prostoru Azure Log Analytics.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Pokyny**: použití funkcí Azure Active Directory (Azure AD) pro rizika a ochranu identity ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: pomocí značek u prostředků souvisejících s nasazeními clusterů Service Fabric můžete pomáhat při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Prostředky by měly být oddělené Virtual Network nebo podsítí, vhodně označené a zabezpečené pomocí skupin zabezpečení sítě nebo Azure Firewall. Prostředky, které ukládají nebo zpracovávají citlivá data, by měly být dostatečně izolované. Pokud Virtual Machines ukládáte nebo zpracováváte citlivá data, implementujte zásady a postupy, abyste je mohli vypnout, pokud se nepoužívají. 

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak nakonfigurovat výstrahu nebo upozornění a odepřít pomocí Azure Firewall](../firewall/threat-intel.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: nasaďte automatizovaný nástroj na hraničních sítích, které monitorují neoprávněný přenos citlivých informací a zablokují tyto přenosy, a upozorní odborníky na zabezpečení informací. 

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat. 

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: šifrování všech citlivých informací během přenosu. Ujistěte se, že klienti, kteří se připojují k prostředkům Azure, můžou vyjednávat TLS 1,2 nebo vyšší. 

Pro Service Fabric vzájemného ověřování mezi uzly použijte certifikát X. 509 pro identitu serveru a šifrování TLS pro komunikaci HTTP. V clusteru je možné nainstalovat libovolný počet dalších certifikátů pro účely zabezpečení aplikací, včetně šifrování a dešifrování hodnot konfigurace aplikace a dat napříč uzly během replikace.
Pokud je to možné, postupujte podle Security Center doporučení pro šifrování v klidovém režimu a šifrování. 

- [Pochopení šifrování při přenosu pomocí Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

- [Service Fabric scénáře zabezpečení clusteru](service-fabric-cluster-security.md)

- [Průvodce odstraňováním potíží s Service Fabric pro konfiguraci TLS](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Pokyny**: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Storage nebo výpočetní prostředky. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: u Service Fabric clusterů ukládajících nebo zpracovávajících citlivé údaje označíte cluster a související prostředky jako citlivé pomocí značek. Pro Azure Storage nebo výpočetní prostředky ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: používejte pro všechny prostředky Azure šifrování v klidovém provozu. Microsoft doporučuje povolit správu šifrovacích klíčů v Azure, ale v některých případech je k dispozici možnost spravovat vlastní klíče. 

- [Principy šifrování neaktivních uložených dat v Azure](../security/fundamentals/encryption-atrest.md)  

- [Postup konfigurace šifrovacích klíčů spravovaných zákazníkem](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Povolení šifrování disku pro uzly clusteru Azure Service Fabric ve Windows](service-fabric-enable-azure-disk-encryption-windows.md)

- [Povolení šifrování disku pro uzly clusteru Azure Service Fabric v systému Linux](service-fabric-enable-azure-disk-encryption-linux.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. ServiceFabric**:

[!INCLUDE [Resource Policy for Microsoft.ServiceFabric 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.servicefabric-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet upozornění, když se změny projeví u kritických prostředků Azure. 

- [Vytvoření upozornění pro události protokolu aktivit Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: pravidelně spouštějte službu Service Fabric selhání analýzy a služby chaos, aby se simulovaly chyby v celém clusteru, aby se posoudila odolnost a spolehlivost vašich služeb.

Dodržujte doporučení od Security Center při provádění posouzení ohrožení zabezpečení na virtuálních počítačích Azure a imagí kontejnerů. 

K provádění posouzení ohrožení zabezpečení v síťových zařízeních a webových aplikacích použijte řešení třetí strany. Při provádění vzdálených kontrol nepoužívejte jediný, trvalá a administrativní účet. Zvažte implementaci metodologie zřizování JIT pro účet skenování. Přihlašovací údaje pro účet pro kontrolu by měly být chráněné, monitorované a používané jenom pro kontrolu ohrožení zabezpečení. 

- [Seznámení se službou Service Fabric analýzy chyb](service-fabric-testability-overview.md)

- [NaChaos řízených v clusterech Service Fabric](service-fabric-controlled-chaos.md)

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: povolení automatických upgradů bitových kopií operačního systému na službě Virtual Machine Scale Sets vašeho clusteru Service Fabric. 

K otestování oprav operačního systému nejdřív před tím, než budete pokračovat v produkčním prostředí, použijte ruční Trigger pro upgrady imagí operačního systému v sadě škálování. Všimněte si, že možnost ruční aktivace neposkytuje integrované vrácení zpět. Monitorujte opravy operačního systému pomocí Update Management z Azure Automation. 

- [Správa oprav pro uzly clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

- [Automatické upgrady imagí operačního systému pomocí Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

- [Jak zajistit aktuálnost virtuálních počítačů pomocí nejnovějšího modelu sady škálování](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

- [Přehled Azure Automation Update Management](/azure/automation/update-management/update-mgmt-overview)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizovaného řešení pro správu oprav pro softwarové tituly třetích stran

**Doprovodné** materiály: povolení automatických upgradů bitových kopií operačního systému na službě Virtual Machine Scale Sets vašeho clusteru Azure Service Fabric. Aplikace orchestrace opravy (POA) je alternativní řešení určené pro Service Fabric clusterů hostovaných mimo Azure. POA se dá použít s clustery Azure s dalšími nároky na hostování.

- [Správa oprav pro uzly clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

- [Automatické upgrady imagí operačního systému pomocí Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

- [Postup konfigurace plánu oprav operačního systému pro clustery Service Fabric](service-fabric-patch-orchestration-application.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Pokyny**: Exportovat výsledky kontroly v konzistentních intervalech a porovnat výsledky a ověřit, zda byly chyby zabezpečení opraveny. Pokud používáte doporučení ke správě ohrožení zabezpečení navržená Security Center, můžete se na portál vybraného řešení překlopit a zobrazit historická data kontroly.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: Využijte společný program pro vyhodnocování rizik (např. běžný systém vyhodnocování chyb zabezpečení) nebo výchozí hodnocení rizik poskytovaná skenovacím nástrojem třetí strany.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci vašich předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.). Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure i prostředků

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Pokyny**: definování schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

Žádné povolené typy prostředků

Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat nebo zjišťovat prostředky v rámci předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Pokyny**: Implementujte řešení třetí strany pro monitorování uzlů clusteru pro neschválené softwarové aplikace.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Pokyny**: pomocí Azure Resource graphu můžete v rámci svých předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.), včetně clusterů Service Fabric. Odeberte všechny neschválené prostředky Azure, které zjistíte. Pro Service Fabric uzly clusteru implementujte řešení třetí strany, které vám umožní odebrat nebo upozornit na neschválený software.

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: pro Service Fabric uzly clusteru implementujte řešení třetích stran, které zabraňuje neoprávněnému softwaru v provádění.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení služeb, které můžete zřídit ve vašem prostředí.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné** materiály: u uzlů clusteru Azure Service Fabric implementujte řešení třetí strany, které zabrání v provádění neautorizovaných typů souborů.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat se správcem prostředků Azure tak, že nakonfigurujete "blokování přístupu" pro aplikaci "Microsoft Azure Management". 

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Pokyny**: pomocí konfigurací specifických pro operační systém nebo prostředků třetích stran omezte schopnost uživatelů spouštět skripty v rámci výpočetních prostředků Azure.

- [Například způsob řízení spouštění skriptu prostředí PowerShell v prostředích systému Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: software, který je potřeba pro obchodní operace, ale může pro organizaci zvýšit riziko, že by se měl izolovat v rámci vlastního virtuálního počítače nebo virtuální sítě a dostatečně zabezpečený pomocí Azure firewall nebo skupiny zabezpečení sítě. 

- [Jak vytvořit virtuální síť](../virtual-network/quick-create-portal.md) 

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. ServiceFabric můžete vytvořit vlastní zásady pro auditování nebo vymáhání konfigurace sítě Service Fabric clusteru.

- [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: Service Fabric image operačních systémů spravují a spravují společnosti Microsoft. Zákazník zodpovědný za implementaci zabezpečených konfigurací pro operační systém uzlů clusteru

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: použijte Azure Policy [odepřít] a [nasazení, pokud neexistují] efekty pro vymáhání zabezpečených nastavení clusterů Azure Service Fabric a souvisejících prostředků.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: Service Fabric bitových kopií operačního systému clusteru spravovaných a spravovaných Microsoftem. Zákazník zodpovídá za implementaci konfigurace stavu na úrovni operačního systému.

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Pokud používáte vlastní definice Azure Policy, použijte k bezpečnému ukládání a správě kódu službu Azure DevOps nebo Azure Repos.

- [Jak v Azure DevOps ukládat kód](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Dokumentace k Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: Pokud používáte vlastní image, použijte řízení přístupu na základě role Azure (Azure RBAC), abyste zajistili přístup k imagí jenom autorizovaným uživatelům. V případě imagí kontejnerů je uložte do Azure Container Registry a využijte Azure RBAC, aby bylo zajištěno, že přístup k obrázkům budou mít jenom autorizovaní uživatelé. 

- [Pochopení Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Vysvětlení služby Azure RBAC pro Container Registry](../container-registry/container-registry-roles.md) 

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. ServiceFabric můžete vytvořit vlastní zásady pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: použijte aliasy Azure Policy v oboru názvů "Microsoft. ServiceFabric" k vytvoření vlastních zásad pro auditování nebo prosazování konfigurace Service Fabric clusteru.

- [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Pokyny**: použijte Security Center k provedení kontrol směrného plánu pro nastavení operačního systému a Docker pro kontejnery. 

- [Porozumění doporučení týkajících se kontejnerů Azure Security Center](../security-center/container-security.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: použití identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů pro vaše cloudové aplikace. 

- [Použití spravovaných identit pro Azure s Service Fabric](concepts-managed-identity.md)

- [Konfigurace podpory spravovaných identit pro nový cluster Service Fabric](configure-new-azure-service-fabric-enable-managed-identity.md)

- [Použití spravované identity s aplikací Service Fabric](how-to-managed-identity-service-fabric-app-code.md)

- [Podpora KeyVaultReference pro aplikace Service Fabric](service-fabric-keyvault-references.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: spravované identity se dají používat v clusterech Service Fabric nasazených v Azure a pro aplikace nasazené jako prostředky Azure. Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování pomocí služby Azure Active Directory (Azure AD) včetně Key Vault bez jakýchkoli přihlašovacích údajů v kódu.

- [Použití spravovaných identit pro Azure s Service Fabric](concepts-managed-identity.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Doprovodné** materiály: Pokud používáte jakýkoliv kód související s nasazením služby Azure Service Fabric, můžete implementovat kontrolu přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

K automatickému střídání certifikátů clusteru Service Fabric použijte Azure Key Vault.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Správa certifikátů v Service Fabric clusterech](https://docs.microsoft.com/azure/service-fabric/cluster-security-certificate-management#certificate-rotation)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné** materiály: ve výchozím nastavení je antivirová ochrana v programu Windows Defender nainstalovaná v systému Windows Server 2016. Uživatelské rozhraní je ve výchozím nastavení nainstalováno u některých SKU, ale není vyžadováno.

Pokud nepoužíváte Windows Defender, přečtěte si dokumentaci k antimalwaru pro pravidla konfigurace. Program Windows Defender není podporován v systému Linux.

- [Vysvětlení antivirové ochrany v programu Windows Defender v systému Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: Služba zálohování a obnovení v Service Fabric umožňuje snadné a automatické zálohování informací uložených ve stavových službách. Zálohování dat aplikací je v pravidelných intervalech zásadní pro ochranu před ztrátou dat a nedostupností služby. Service Fabric poskytuje volitelnou službu pro zálohování a obnovení, která umožňuje konfigurovat pravidelné zálohování stavových Reliable Services (včetně služeb actor) bez nutnosti psát další kód. Také usnadňuje obnovení dříve vytvořených záloh.

- [Periodické zálohování a obnovení v clusteru Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné** materiály: povolení služby obnovení zálohování ve vašem clusteru Service Fabric a vytváření zásad zálohování pro pravidelné zálohování stavových služeb a na vyžádání. Zálohujte klíče spravované zákazníkem v rámci Azure Key Vault.

- [Periodické zálohování a obnovení v clusteru Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

- [Princip pravidelné konfigurace zálohování v Azure Service Fabric](service-fabric-backuprestoreservice-configure-periodic-backup.md)

- [Postup zálohování klíčů trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: Ujistěte se, že máte možnost provádět obnovení ze služby obnovení záloh tím, že pravidelně kontrolujete informace o konfiguraci zálohování a dostupné zálohy. Test obnovení zálohovaných klíčů spravovaných zákazníkem

- [Princip pravidelné konfigurace zálohování v Azure Service Fabric](service-fabric-backuprestoreservice-configure-periodic-backup.md)

- [Obnovování zálohy v Azure Service Fabric](service-fabric-backup-restore-service-trigger-restore.md)

- [Postup obnovení klíčů trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: zálohy ze služby Service Fabric Backup Restore Service používají účet Azure Storage v předplatném. Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete poskytovat klíče spravované zákazníkem pro šifrování dat úložiště.

Pokud používáte spravované klíče zákazníka, ujistěte se, že Soft-Delete v Key Vault povolí ochranu klíčů proti náhodnému nebo škodlivému odstranění.

- [Šifrování neaktivních uložených dat ve službě Azure Storage](../storage/common/storage-service-encryption.md)

- [Postup povolení Soft-Delete v Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: vývoj Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování incidentů a správu, které se mají zjišťovat, na kontrolu po jednotlivých událostech. 

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Pomoc při tvorbě vlastního plánu odpovědí na incidenty najdete v příručce pro zpracování incidentů v počítači s NIST.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak je Security Center ve vyhledávání nebo v metrikě, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Navíc můžete označit odběry pomocí značek a vytvořit systém pojmenování k identifikaci a kategorizaci prostředků Azure, zejména těch, které zpracovávají citlivá data.  Je vaše zodpovědnost za to, že je možné určit prioritu nápravy výstrah na základě závažnosti prostředků a prostředí Azure, ve kterých došlo k incidentu. 

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán. 

- [Průvodce NIST pro testování, školení a cvičení programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy. 

- [Jak nastavit kontakt zabezpečení pro Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Security Center Data můžete streamovat výstrahy na Sentinel.

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

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
