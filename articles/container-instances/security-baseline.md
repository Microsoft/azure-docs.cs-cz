---
title: Základní hodnoty zabezpečení Azure pro Container Instances
description: Základní hodnoty zabezpečení Azure pro Container Instances
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3f4e855c52488652b88a3b14e9817f1755167b67
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806117"
---
# <a name="azure-security-baseline-for-container-instances"></a>Základní hodnoty zabezpečení Azure pro Container Instances

Základní plán zabezpečení Azure pro Container Instances obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Chraňte prostředky pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network

**Pokyny**: Azure Virtual Network poskytuje zabezpečenou a privátní síť pro vaše Azure a místní prostředky. Integrujte své skupiny kontejnerů v Azure Container Instances s virtuální sítí Azure. 

* [Scénáře a prostředky virtuální sítě – Azure Container Instances](./container-instances-virtual-network-concepts.md)

* [Nasazení instancí kontejnerů do virtuální sítě Azure](./container-instances-vnet.md)

* [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

* [Jak nasadit a nakonfigurovat Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)


**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuální sítě, podsítí a síťových karet.

**Pokyny**: použijte Azure Security Center a opravte doporučení k ochraně sítě, abyste chránili vaše síťové prostředky v Azure. Povolte protokoly toku NSG a odešlete protokoly do účtu úložiště pro audit provozu.

* [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Ochrana síťových prostředků](../security-center/security-center-network-recommendations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Pokyny**: nasazení firewallu webových aplikací Azure (WAF) před kritickými webovými aplikacemi hostovanými v Azure Container Instances pro další kontrolu příchozího provozu. Povolte nastavení diagnostiky pro protokoly WAF a ingestování do účtu úložiště, centra událostí nebo Log Analytics pracovního prostoru.

* [Jak nasadit Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)



**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: povolení DDoS Standard Protection v Azure Virtual Networks pro ochranu před útoky DDoS. K odepření komunikace se známými škodlivými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb. Nasaďte Azure Firewall na všech hranicích sítě organizace s povolenou funkcí Analýza hrozeb a nakonfigurované na "výstrahy a zamítnutí" pro škodlivý síťový provoz. Použijte Azure Security Center k přístupu k síti jenom v čase, abyste mohli nakonfigurovat skupin zabezpečení sítě tak, aby po omezenou dobu omezila počet vystavování koncových bodů na schválené IP adresy Pomocí Azure Security Center adaptivního posílení zabezpečení sítě doporučujeme doporučit konfigurace NSG, které omezují porty a zdrojové IP adresy na základě skutečného provozu a analýzy hrozeb. 

* [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Pochopení Azure Security Center integrované analýzy hrozeb](../security-center/azure-defender.md)

* [Pochopení Azure Security Center adaptivního posílení zabezpečení sítě](../security-center/security-center-adaptive-network-hardening.md)

* [Azure Security Center pouze v čase Access Control sítě](../security-center/security-center-just-in-time.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: záznam síťových paketů a protokolů toků

**Pokyny**: Pokud používáte skupiny zabezpečení sítě (skupin zabezpečení sítě) s implementací virtuální sítě, povolte protokoly NSG Flow pro NSG připojené k podsíti delegované do Azure Container Instances. Zaznamenejte NSG Flow do účtu Azure Storage pro generování záznamů toků. Pokud se to vyžaduje pro prošetření aktivity neobvyklé, povolte zachytávání paketů služby Azure Network Watcher.

* [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Postup povolení Network Watcher](../network-watcher/network-watcher-create.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: vyberte nabídku z Azure Marketplace, která podporuje funkce ID/IP adresy s možnostmi kontroly zatížení. Pokud zjišťování neoprávněných vniknutí nebo prevence na základě kontroly zatížení není požadavkem, je možné použít Azure Firewall s analýzou hrozeb. Azure Firewall filtrování na základě logiky hrozeb může upozorňovat a zamítnout provoz do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu.

Nasaďte řešení brány firewall podle svého výběru na základě hranic sítě vaší organizace za účelem detekce nebo odepření škodlivého provozu.

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Postup konfigurace výstrah pomocí Azure Firewall](../firewall/threat-intel.md)

* [Nasazení ve virtuální síti – Azure Container Instances](./container-instances-vnet.md) 



**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Pokyny**: nasazení služby Azure Application Gateway pro webové aplikace s POVOLENým protokolem HTTPS/SSL u důvěryhodných certifikátů.

* [Postup nasazení Application Gateway](../application-gateway/quick-create-portal.md)

* [Postup konfigurace Application Gateway pro použití protokolu HTTPS](../application-gateway/create-ssl-portal.md) 

* [Princip vyrovnávání zatížení vrstvy 7 pomocí bran webových aplikací Azure](../application-gateway/overview.md)

* [Zveřejňuje statickou IP adresu pro skupinu kontejnerů.](./container-instances-application-gateway.md)

* [Nasazení ve virtuální síti – Azure Container Instances](./container-instances-vnet.md)



**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: použití značek Virtual Network služby k definování řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny. 

Skupiny zabezpečení aplikací můžete použít také ke zjednodušení složité konfigurace zabezpečení. Skupiny zabezpečení aplikací umožňují konfigurovat zabezpečení sítě jako přirozené rozšíření struktury aplikace. Můžete seskupovat virtuální počítače a na základě těchto skupin definovat zásady zabezpečení sítě. 

* [Pochopení a použití značek služeb](../virtual-network/service-tags-overview.md) 

* [Pochopení a použití skupin zabezpečení aplikací](../virtual-network/network-security-groups-overview.md#application-security-groups)

* [Nasazení ve virtuální síti – Azure Container Instances](./container-instances-vnet.md)



**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: pomocí Azure modrotisky můžete také zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou šablony Azure Resource Manageru, ovládací prvky a zásady služby Azure RBAC v rámci jedné definice podrobného plánu. Můžete použít podrobný plán na nová předplatná a vyladit řízení a správu prostřednictvím správy verzí. 

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Ukázky Azure Policy pro sítě](../governance/policy/samples/built-in-policies.md#network)

* [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)



**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro skupin zabezpečení sítě a další prostředky, které se týkají zabezpečení sítě a toku provozu. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z vestavěných definic zásad Azure souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že se vytvoří všechny prostředky s značkami a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

* [Nasazení ve virtuální síti – Azure Container Instances](./container-instances-vnet.md)

* [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vašimi instancemi kontejnerů. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

* [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Vytváření výstrah v Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Pokyny**: Microsoft udržuje časové zdroje pro prostředky Azure, ale máte možnost spravovat nastavení synchronizace času pro výpočetní prostředky. Například spusťte příkaz synchronizace času ve spuštěném kontejneru.

* [Jak nakonfigurovat časovou synchronizaci pro výpočetní prostředky Azure](../virtual-machines/windows/time-sync.md)

* [Spuštění příkazu ve spuštěné instanci kontejneru Azure](./container-instances-exec.md)



**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: ingestování protokolů prostřednictvím Azure monitor k agregaci dat zabezpečení vygenerovaných skupinou kontejnerů Azure. V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé nebo archivní úložiště.

* [Protokolování skupiny kontejnerů a instance s protokoly Azure Monitor](./container-instances-log-analytics.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: Azure monitor shromažďuje protokoly prostředků (dříve nazývané diagnostické protokoly) pro události řízené uživateli v registru. Azure Container Instances obsahuje integrovanou podporu pro odesílání protokolů skupin kontejnerů a dat událostí a protokolů kontejnerů do Azure Monitor protokolů.

* [Protokolování skupiny kontejnerů a instance s protokoly Azure Monitor](../container-registry/container-registry-diagnostics-audit-logs.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: nelze použít. Tyto zásady jsou určené pro IaaS výpočetní prostředky.


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v rámci Azure monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

* [Postup nastavení parametrů uchovávání protokolů pro Log Analytics pracovní prostory](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů pro neobvyklé chování a pravidelné kontroly výsledků. Pomocí Log Analytics pracovního prostoru Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu. 

* [Protokolování skupiny kontejnerů a instance s protokoly Azure Monitor](./container-instances-log-analytics.md)

* [Principy Log Analyticsho pracovního prostoru](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Jak provádět vlastní dotazy v Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé

**Doprovodné** materiály: použijte Log Analytics pracovní prostor pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech. 

* [Protokolování skupiny kontejnerů a instance s protokoly Azure Monitor](./container-instances-log-analytics.md)

* [Upozornění na data protokolu Log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: Poskytněte vlastní řešení ochrany proti malwaru a shromažďování událostí, pokud je to potřeba ke spuštění v kontejneru. 


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné** materiály: v případě potřeby zadejte vlastní řešení pro dotazování protokolů DNS v kontejneru.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné** materiály: v případě potřeby nakonfigurujte protokolování konzoly ve spuštěné instanci kontejneru.

* [Spuštění příkazu ve spuštěné instanci kontejneru Azure](./container-instances-exec.md)



**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Azure Active Directory (Azure AD) mají předdefinované role, které se musí explicitně přiřadit a které jsou Queryable. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu.

Pokud používáte službu Azure Container Registry s Azure Container Instances, můžete pro každý registr kontejnerů Azure sledovat, jestli je integrovaný účet správce povolený nebo zakázaný. Zakáže účet, pokud se nepoužívá.

* [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Účet správce Azure Container Registry](../container-registry/container-registry-authentication.md#admin-account)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné** materiály: Azure Active Directory (Azure AD) nemá koncept výchozích hesel. Další prostředky Azure, které vyžadují heslo, vynutí vytvoření hesla s požadavky na složitost a minimální délkou hesla, která se liší v závislosti na službě. Zodpovídáte za aplikace třetích stran a služby Marketplace, které mohou používat výchozí hesla.

Pokud používáte službu Azure Container Registry se službou Azure Container Instances, pokud je výchozí účet správce služby Azure Container Registry povolený, vytvoří se automaticky složitá hesla, která by se měla střídat. Zakáže účet, pokud se nepoužívá.

* [Účet správce Azure Container Registry](../container-registry/container-registry-authentication.md#admin-account)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Pokud používáte službu Azure Container Registry s Azure Container Instances, vytvořte procedury pro povolení předdefinovaného účtu správce registru kontejneru. Zakáže účet, pokud se nepoužívá.

* [Pochopení Azure Security Center identity a přístupu](../security-center/security-center-identity-access.md)

* [Účet správce Azure Container Registry](../container-registry/container-registry-authentication.md#admin-account)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné** materiály: kdykoli je to možné, použijte Azure Active Directory jednotného přihlašování (SSO) místo konfigurace jednotlivých samostatných přihlašovacích údajů pro každou službu. Použijte Azure Security Center doporučení pro správu identit a přístupu.

* [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte službu Multi-Factor Authentication (Azure AD) pro Azure Active Directory (Azure AD) a postupujte podle Azure Security Center doporučení pro správu identit a přístupu.

* [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Doprovodné** materiály: použití privilegovaným přístupem (privilegovaných pracovních stanic) s MFA nakonfigurovaným pro přihlášení a konfiguraci prostředků Azure.

* [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: protokolování a upozornění na podezřelou aktivitu z účtů pro správu

**Doprovodné** materiály: použití sestav zabezpečení Azure Active Directory (Azure AD) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu.

* [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](../active-directory/identity-protection/overview-identity-protection.md)

* [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

* [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

* [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

* [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)

* [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorování pokusů o přístup k deaktivovaným účtům

**Doprovodné** materiály: máte přístup k aktivitě pro přihlášení k Azure Active Directory (Azure AD), událostem auditu a rizikovým protokolům událostí, které vám umožní integrovat se všemi nástroji/Monitoring (Security Information and Event Management) (Siem).

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro Azure Active Directory uživatelských účtů a odesláním protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics.

* [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Pokyny**: použití funkcí Azure Active Directory (Azure AD) pro rizika a ochranu identity ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů.

* [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: není aktuálně k dispozici; Customer Lockbox se v tuto chvíli nepodporuje pro Azure Container Instances.

* [Seznam podporovaných služeb Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Pokyny**: použití značek prostředků k usnadnění při sledování služby Azure Container Instances, které ukládají nebo zpracovávají citlivé informace. 

Označení a verze kontejnerových obrázků, které vám pomůžou při sledování imagí, které ukládají nebo zpracovávají citlivé informace.

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

* [Doporučení pro označování a naznačení verzí imagí kontejneru](../container-registry/container-registry-image-tag-version.md)


**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Doprovodné** materiály: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Prostředky by měly být oddělené podle virtuální sítě a podsítě, musí se vhodně označit a zabezpečit pomocí NSG nebo Azure Firewall. Prostředky, které ukládají nebo zpracovávají citlivá data, by měly být dostatečně izolované.

* [Spuštění příkazu ve spuštěné instanci kontejneru Azure](./container-instances-exec.md)

* [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

* [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

* [Nasazení ve virtuální síti – Azure Container Instances](./container-instances-vnet.md) 
* [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

* [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

* [Jak nakonfigurovat výstrahu nebo upozornění a odepřít pomocí Azure Firewall](../firewall/threat-intel.md)


**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: nasaďte automatizovaný nástroj na hraničních sítích, které monitorují neoprávněný přenos citlivých informací a zablokují tyto přenosy, a upozorní odborníky na zabezpečení informací. Monitorujte a zablokujte neoprávněné přenosy informací ze sdílených složek Azure a dalších svazků připojených k instancím kontejnerů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

* [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

* [Nasazení ve virtuální síti – Azure Container Instances](./container-instances-vnet.md) 

* [Připojení sdílené složky ve službě Azure Container Instances](./container-instances-volume-azure-files.md)


**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: Ujistěte se, že všichni klienti, kteří se připojují ke skupinám kontejnerů Azure, můžou vyjednávat TLS 1,2 nebo vyšší. Microsoft Azure prostředky vyjednávat TLS 1,2 ve výchozím nastavení.

Pokud je to možné, postupujte podle Azure Security Center doporučení pro šifrování v klidovém režimu a šifrování.

* [Pochopení šifrování při přenosu pomocí Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: funkce pro identifikaci, klasifikaci a ochranu před únikem informací nejsou aktuálně k dispozici pro Azure Container Instances. Označte skupiny kontejnerů, které mohou zpracovávat citlivé informace jako takové a implementovat řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

* [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Sdílené

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: použití řízení přístupu na základě role Azure (Azure RBAC) k řízení přístupu k Azure Container Instances dat a prostředků. 

* [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro IaaS výpočetní prostředky.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

* [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Sdílené

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: ve výchozím nastavení jsou všechna data nasazení v Azure Container Instances v klidovém stavu zašifrovaná pomocí klíčů spravovaných Microsoftem. Volitelně můžete spravovat šifrování pomocí vlastního klíče (klíč spravovaný zákazníkem).

* [Principy šifrování neaktivních uložených dat v Azure](../security/fundamentals/encryption-atrest.md)

* [Šifrovat data nasazení pomocí Azure Container Instances](./container-instances-encrypt-data.md)



**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají do skupin kontejnerů a instancí kontejnerů. 

* [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/platform/alerts-activity-log.md)


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](../security/benchmarks/security-control-vulnerability-management.md)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: implementace řešení pro skenování imagí kontejnerů v privátním registru a identifikaci potenciálních ohrožení zabezpečení. Dodržujte doporučení od Azure Security Center při provádění posouzení ohrožení zabezpečení u imagí kontejneru uložených v Azure Container Registry. Volitelně můžete nasadit řešení jiných výrobců z Azure Marketplace k provádění posouzení ohrožení zabezpečení imagí.

* [Požadavky na zabezpečení pro Azure Container Instances](./container-instances-image-security.md)

* [Azure Container Registry integrace s Security Center](../security-center/defender-for-container-registries-introduction.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: Společnost Microsoft provádí správu oprav v základních systémech, které podporují spuštěné kontejnery.

Použijte vlastní řešení nebo řešení třetí strany k opravě imagí kontejneru. Pokud ukládáte image kontejnerů v Azure Container Registry, spusťte úlohy Azure Container Registry pro automatizaci aktualizací imagí aplikace v registru kontejnerů na základě oprav zabezpečení nebo jiných aktualizací v imagí základního operačního systému.

* [Požadavky na zabezpečení pro Azure Container Instances](./container-instances-image-security.md)

* [Základní aktualizace obrázků pro úlohy Azure Container Registry](../container-registry/container-registry-tasks-base-images.md)


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: nasazení automatizovaného řešení pro správu oprav softwaru třetí strany

**Pokyny**: k opravě imagí kontejneru použijte vlastní řešení nebo řešení třetí strany. Pokud ukládáte image kontejnerů v Azure Container Registry, spusťte úlohy Azure Container Registry pro automatizaci aktualizací imagí aplikace v registru kontejnerů na základě oprav zabezpečení nebo jiných aktualizací v imagí základního operačního systému.

* [Požadavky na zabezpečení pro Azure Container Instances](./container-instances-image-security.md)

* [Základní aktualizace obrázků pro úlohy ACR](../container-registry/container-registry-tasks-base-images.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: Exportovat výsledky kontroly imagí v konzistentních intervalech a porovnat výsledky a ověřit, zda byly chyby zabezpečení opraveny. Pokud ukládáte image kontejnerů v Azure Container Registry, Integrujte registr do Azure Security Center a povolte tak pravidelné prohledávání imagí kontejneru pro ohrožení zabezpečení. Volitelně můžete nasadit řešení jiných výrobců z Azure Marketplace a provádět pravidelné kontroly ohrožení zabezpečení imagí.

* [Požadavky na zabezpečení pro Azure Container Instances](./container-instances-image-security.md)

* [Azure Container Registry integrace s Security Center](../security-center/defender-for-container-registries-introduction.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: Pokud ukládáte image kontejneru do Azure Container Registry, Integrujte registr s Azure Security Center a umožněte tak pravidelné prohledávání imagí kontejneru pro ohrožení zabezpečení a klasifikaci rizik. Volitelně můžete nasadit řešení třetích stran z Azure Marketplace a provádět pravidelné kontroly ohrožení zabezpečení imagí a hodnocení rizik.

* [Požadavky na zabezpečení pro Azure Container Instances](./container-instances-image-security.md)

* [Azure Container Registry integrace s Security Center](../security-center/defender-for-container-registries-introduction.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: použijte Azure Asset Discovery.

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci vašich předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.). Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure i prostředků

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

* [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

* [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro Azure Container Instances a souvisejících prostředků, které dávají metadata k logickému uspořádání do taxonomie.

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

* [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

* [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: udržování inventáře schválených prostředků Azure a softwarových titulů

**Pokyny**: budete muset vytvořit inventarizaci schválených prostředků Azure podle potřeb vaší organizace.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit ve vašich předplatných.

Pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat prostředky. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

* [Audit dodržování předpisů pro služby Azure Container Registry pomocí Azure Policy](../container-registry/container-registry-azure-policy.md)

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Pokyny**: implementace vlastního řešení nebo řešení třetí strany pro inventarizaci softwaru pro schválené aplikace v kontejneru. 

Implementujte řešení pro skenování imagí kontejnerů v privátním registru a Identifikujte potenciální ohrožení zabezpečení. Použijte doporučení z Azure Security Center k provedení posouzení ohrožení zabezpečení u imagí kontejneru uložených v Azure Container Registry. Volitelně můžete nasadit řešení jiných výrobců z Azure Marketplace k provádění posouzení ohrožení zabezpečení imagí.

Sledujte protokoly Azure Container Instances pro chování neobvyklé a pravidelně kontrolujte výsledky. Pomocí Log Analytics pracovního prostoru Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu.

* [Protokolování skupiny kontejnerů a instance s protokoly Azure Monitor](./container-instances-log-analytics.md)

* [Principy Log Analyticsho pracovního prostoru](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Jak provádět vlastní dotazy v Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

* [Požadavky na zabezpečení pro Azure Container Instances](./container-instances-image-security.md)
* [Azure Container Registry integrace s Security Center](../security-center/defender-for-container-registries-introduction.md)



**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: Azure Automation poskytuje úplnou kontrolu během nasazení, operací a vyřazení úloh a prostředků z provozu. Můžete implementovat vlastní řešení pro odebrání neautorizovaných prostředků a softwarových aplikací Azure.

* [Seznámení se službou Azure Automation](../automation/automation-intro.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: značky a image kontejneru verze, které vám pomůžou při sledování imagí, které spouštějí schválené aplikace.
* [Doporučení pro označování a naznačení verzí imagí kontejneru](../container-registry/container-registry-image-tag-version.md)


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení služeb, které můžete zřídit ve vašem prostředí.

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/index.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="610-implement-approved-application-list"></a>6,10: Implementujte schválený seznam aplikací

**Doprovodné** materiály: značky a image kontejneru verze, které vám pomůžou při sledování imagí, které spouštějí schválené aplikace.
* [Doporučení pro označování a naznačení verzí imagí kontejneru](../container-registry/container-registry-image-tag-version.md)


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsbrdiv"></a>6,11: <div>Omezení schopnosti uživatelů pracovat s Azure Resource Manager prostřednictvím skriptů<br></div>

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat se správcem prostředků Azure tak, že nakonfigurujete "blokování přístupu" pro aplikaci "Microsoft Azure Management". 

* [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: všichni uživatelé s přístupem k Azure Container Instances můžou spouštět skripty v kontejnerech.

Spravujte a zkontrolujte přístup k Azure Container Instances prostředkům pomocí různých předplatných Azure nebo skupin pro správu nebo izolujte prostředky pomocí virtuálních sítí a skupin zabezpečení sítě nebo Azure Firewall.

* [Spuštění příkazu ve spuštěné instanci kontejneru Azure](./container-instances-exec.md)

* [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

* [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)

* [Nasazení ve virtuální síti – Azure Container Instances](./container-instances-vnet.md)

* [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

* [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)



**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: software, který je potřeba pro obchodní operace, ale může pro organizaci zvýšit riziko, že by se měl izolovat v rámci vlastní virtuální sítě a dostatečně zabezpečený pomocí Azure firewall nebo skupiny zabezpečení sítě.

* [Nasazení ve virtuální síti – Azure Container Instances](./container-instances-vnet.md) 

* [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Doprovodné** materiály: Údržba schválené konfigurace skupiny kontejnerů pomocí šablony Azure Resource Manager nebo export do souboru YAML. K údržbě konfigurací zabezpečení pro související prostředky Azure použijte Azure Policy.

* [Skupiny kontejnerů ve službě Azure Container Instances](container-instances-container-groups.md#deployment)

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Pokyny**: k opravě imagí kontejneru použijte vlastní řešení nebo řešení třetí strany. Pokud ukládáte image kontejnerů v Azure Container Registry, spusťte úlohy Azure Container Registry pro automatizaci aktualizací imagí aplikace v registru kontejnerů na základě oprav zabezpečení nebo jiných aktualizací v imagí základního operačního systému. 

* [Základní aktualizace obrázků pro úlohy Azure Container Registry](../container-registry/container-registry-tasks-base-images.md)



**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: použijte zásady Azure [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení napříč prostředky Azure.

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: implementace řešení pro skenování imagí kontejnerů v privátním registru a identifikaci potenciálních ohrožení zabezpečení v KONFIGURACÍCH operačních systémů. Použijte doporučení z Azure Security Center k provedení posouzení ohrožení zabezpečení u imagí kontejneru uložených v Azure Container Registry. Volitelně můžete nasadit řešení jiných výrobců z Azure Marketplace k provádění posouzení ohrožení zabezpečení imagí.

Použijte vlastní řešení nebo řešení třetí strany k opravě imagí kontejneru. Pokud ukládáte image kontejnerů v Azure Container Registry, spusťte úlohy Azure Container Registry pro automatizaci aktualizací imagí aplikace v registru kontejnerů na základě oprav zabezpečení nebo jiných aktualizací v imagí základního operačního systému. 

* [Bezpečnostní doporučení pro monitorování a kontrolu kontejnerů pro Azure Container Instances](./container-instances-image-security.md)

* [Azure Container Registry integrace s Security Center](../security-center/defender-for-container-registries-introduction.md)
* [Základní aktualizace obrázků pro úlohy Azure Container Registry](../container-registry/container-registry-tasks-base-images.md)



**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: ukládejte a SPRAVUJTE šablony ARM, soubory YAML a vlastní definice zásad Azure ve správě zdrojového kódu bezpečně.

* [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentace k Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: ukládejte image kontejnerů v Azure Container registry a využijte Azure RBAC, abyste zajistili přístup k imagí jenom autorizovaným uživatelům.

* [Pochopení Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Vysvětlení služby Azure RBAC pro Container Registry](../container-registry/container-registry-roles.md)

* [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: nasazení nástrojů pro správu konfigurace systému

**Doprovodné** materiály: použijte Azure Policy k upozornění, auditu a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: nasaďte nástroje pro správu konfigurace systému pro operační systémy.

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro IaaS výpočetní prostředky.


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Implementujte automatizované monitorování konfigurace pro služby Azure.

**Pokyny**: použijte Azure Security Center k provádění kontrol standardních hodnot vašich prostředků Azure.

Pomocí Azure Policy můžete umístit omezení typu prostředků, které se dají vytvořit v předplatných.

* [Jak opravit doporučení v Azure Security Center](../security-center/security-center-remediate-recommendations.md)

* [Audit dodržování předpisů pro služby Azure Container Registry pomocí Azure Policy](../container-registry/container-registry-azure-policy.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Pokyny**: Pokud použijete Azure Container Registry k ukládání imagí kontejneru, použijte Azure Security Center k provádění kontrol směrného plánu pro nastavení operačního systému a Docker pro kontejnery.

* [Porozumění doporučení týkajících se kontejnerů Azure Security Center](../security-center/container-security.md)


**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: použití identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů pro vaše cloudové aplikace.

* [Integrace se spravovanými identitami Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Vytvoření Key Vault](../key-vault/secrets/quick-create-portal.md)

* [Ověření Key Vault](../key-vault/general/authentication.md)

* [Postup přiřazení zásady přístupu Key Vault](../key-vault/general/assign-access-policy-portal.md)

* [Použití spravovaných identit se službou Azure Container Instances](./container-instances-managed-identity.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: použití spravovaných identit k poskytování služeb Azure s automaticky spravovanou identitou ve službě Azure AD. Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů v kódu.

* [Postup konfigurace spravovaných identit](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Použití spravovaných identit se službou Azure Container Instances](./container-instances-managed-identity.md)



**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

* [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Container Instances), ale neběží na zákaznickém obsahu.

Předem Prohledejte všechny soubory nahrané do nevýpočetních prostředků Azure, například App Service, Data Lake Storage, Blob Storage atd. 


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro IaaS výpočetní prostředky.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Container Instances), ale neběží na zákaznickém obsahu.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: Povolte Azure Backup a nakonfigurujte zdroj zálohy (například sdílenou složku připojenou ke skupinám kontejnerů) a také požadovanou četnost a dobu uchování. 

* [Postup povolení Azure Backup](../backup/index.yml)

* [Připojení sdílené složky ve službě Azure Container Instances](./container-instances-volume-azure-files.md)


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

**Pokyny**: zálohování klíčů spravovaných zákazníkem v Azure Key Vault pomocí nástrojů příkazového řádku Azure nebo sad SDK.

Volitelně můžete zálohovat image kontejnerů importem z jednoho registru do jiného.
* [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Import imagí kontejneru do registru kontejneru](../container-registry/container-registry-import-images.md)



**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

**Pokyny**: testování obnovení zálohovaných zákaznických klíčů v Azure Key Vault pomocí nástrojů příkazového řádku Azure nebo sad SDK.

* [Postup obnovení klíčů Azure Key Vault v Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Šifrovat data nasazení – Azure Container Instances](./container-instances-encrypt-data.md)



**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

**Pokyny**: můžete povolit Soft-Delete v Azure Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění.

* [Postup povolení Soft-Delete v Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení.

Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače NIST, která pomáhá při vytváření vlastního plánu reakce na incidenty.

* [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

* [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Příručka pro zpracování incidentů zabezpečení počítače NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Azure Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Navíc jasně označte odběry (například. produkční, neprodukční) a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.


**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

* [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

* [Nastavení kontaktu ve službě Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

* [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

* [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

* [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech důležitých zjištění zabezpečení do 60 dnů.

**Doprovodné** materiály: řiďte se pravidly společnosti Microsoft o zapojení, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft.

* [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)