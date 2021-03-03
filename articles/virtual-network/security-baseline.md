---
title: Základní hodnoty zabezpečení Azure pro Virtual Network
description: Základní Virtual Network zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 420cd4a5b39f7c5864cf9802d02643e23c8ad24e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666558"
---
# <a name="azure-security-baseline-for-virtual-network"></a>Základní hodnoty zabezpečení Azure pro Virtual Network

Tyto základní hodnoty zabezpečení se týkají pokynů z [Azure Security test 1,0](../security/benchmarks/overview-v1.md) na Azure Virtual Network. Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah se seskupuje podle **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů vztahujících se na Azure Virtual Network. **Ovládací prvky** , které se nevztahují k Azure Virtual Network jsou vyloučené.

Pokud chcete zjistit, jak se Azure Virtual Network zcela mapuje na bezpečnostní srovnávací testy zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení azure Virtual Network](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné** materiály: použijte Security Center a sledujte doporučení k ochraně síťových prostředků v Azure pomocí doporučení pro ochranu sítě. 

Odešlete do Log Analytics pracovního prostoru protokoly toku skupin zabezpečení sítě a pomocí Analýza provozu poskytněte přehledy o toku provozu do cloudu Azure. Analýza provozu nabízí možnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě. 

Pomocí protokolů Azure Monitor můžete poskytnout přehledy o vašem prostředí. Pracovní prostor by se měl použít k kompletování a analýze dat a může se integrovat s dalšími službami Azure, jako jsou Application Insights a Security Center. 

Vyberte protokoly prostředků, které se odešlou do účtu služby Azure Storage nebo do centra událostí. K analýze protokolů se dá použít i jiná platforma. 

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Pochopení zabezpečení sítě, které poskytuje Security Center](../security-center/security-center-network-recommendations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: povolení služby DDoS (Distributed Denial of Service) ve službě Azure Virtual Network pro ochranu před útoky DDoS.

Nasaďte Azure Firewall na všech hranicích sítě organizace s povoleným filtrováním na základě hrozeb a nakonfigurovanými na "výstrahy a zamítnutí" pro škodlivý síťový provoz.

Pomocí funkcí ochrany před internetovými útoky Security Center zjistit komunikaci se známými škodlivými IP adresami.

Použijte doporučení adaptivního zesílení sítě Security Center pro konfigurace skupin zabezpečení sítě, které omezují porty a zdrojové IP adresy na základě skutečného provozu a analýzy hrozeb. 

- [Správa Azure DDoS Protection Standard pomocí Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall filtrování na základě logiky hrozeb](../firewall/threat-intel.md)

- [Ochrana před hrozbami v Security Center](../security-center/azure-defender.md)

- [Adaptivní posílení zabezpečení sítě v Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Pokyny**: Kromě běžně dostupných nástrojů pro zachytávání paketů pro zaznamenávání síťových paketů používejte zachytávání paketů VPN Gateway. 

Můžete si také projít řešení založená na agentech nebo síťové virtuální zařízení, která poskytují funkce pro přístup k terminálovému místu (klepnutím) nebo viditelnost sítě prostřednictvím partnerských řešení zprostředkovatele paketů dostupných v Azure Marketplace nabídky.

- [Konfigurace zachycení paketů pro brány VPN](../vpn-gateway/packet-capture.md) 

- [Partner síťového virtuálního zařízení](https://azure.microsoft.com/solutions/network-appliances)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: použijte Azure firewall nasazené ve vaší virtuální síti s povoleným analýzou hrozeb. Využijte Azure Firewall filtrování založené na základě logiky hrozeb k upozornění nebo zamítnutí provozu do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu. 

Můžete také vybrat vhodnou nabídku z Azure Marketplace, která podporuje funkce ID/IP adresy s možnostmi kontroly zatížení.

Nasaďte řešení brány firewall podle svého výběru na základě hranic sítě vaší organizace za účelem detekce nebo odepření škodlivého provozu.

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Postup konfigurace výstrah pomocí Azure Firewall](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: použití značek Virtual Network služby k definování řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall. Značky služeb lze použít místo konkrétních IP adres při vytváření pravidel zabezpečení. Povolte nebo zakažte provoz pro odpovídající službu zadáním názvu značky služby (například ApiManagement) do příslušného zdrojového nebo cílového pole pravidla. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Pomocí skupin zabezpečení aplikací můžete zjednodušit složitou konfiguraci zabezpečení. Skupiny zabezpečení aplikací umožňují konfigurovat zabezpečení sítě jako přirozené rozšíření struktury aplikace. Díky tomu můžete seskupovat virtuální počítače a definovat zásady zabezpečení sítě na základě těchto skupin.

- [Pochopení a použití značek služeb](service-tags-overview.md)

- [Pochopení a použití skupin zabezpečení aplikací](./network-security-groups-overview.md#application-security-groups)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky pomocí Azure Policy a kontrola integrovaných definic zásad sítě pro implementaci.

Přečtěte si výchozí zásady pro Security Center, které obsahují dostupná doporučení zabezpečení související s vašimi virtuálními sítěmi.

Pomocí Azure modrotisky můžete zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, přiřazení řízení přístupu na základě role (Azure RBAC) a zásady v jediné definici podrobného plánu. Azure Blueprint můžete použít na nové odběry pro vyladěné řízení a správu prostřednictvím správy verzí. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ukázky Azure Policy pro sítě](../governance/policy/samples/built-in-policies.md#network) 

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

- [Povolit monitorování v Azure Security Center](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Security%20Center/AzureSecurityCenter.json)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro skupiny zabezpečení sítě a další prostředky, které se týkají zabezpečení sítě a toku provozu. Pole Popis použijte k zadání obchodních potřeb, doby trvání a dalších informací pro všechna pravidla, která umožňují provoz do/ze sítě pro jednotlivá pravidla skupiny zabezpečení sítě.
Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Zvolením možnosti Azure PowerShell nebo Azure CLI můžete vyhledat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Vytvoření Virtual Network](quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](tutorial-filter-network-traffic.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace prostředků a zjišťovat změny ve vaší virtuální síti. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: povolení Azure monitor pro přístup k protokolům auditu a aktivitám, které zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky. 

V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.
Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Zobrazení a načtení událostí protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: povolení Azure monitor pro přístup k protokolům auditu a aktivitám, které zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Zobrazení a načtení událostí protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v rámci Azure monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé nebo archivní ukládání úložiště protokolu zabezpečení.

- [Změnit dobu uchovávání dat v Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů pro neobvyklé chování a pravidelné kontroly výsledků. Použijte Log Analytics pracovní prostor Azure Monitor k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé nebo archivní úložiště. 

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Principy Log Analyticsho pracovního prostoru](../azure-monitor/logs/log-analytics-tutorial.md)

- [Jak provádět vlastní dotazy v Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: použití Security Center s Log Analytics pracovním prostorem pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech.

Alternativně můžete povolit a zařadit data do Azure Sentinel nebo SIEM třetí strany pro upozorňování.

- [Správa výstrah v Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Upozornění na data protokolu Log Analytics](../azure-monitor/alerts/tutorial-response.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Pokyny**: implementace řešení od jiného výrobce z Azure Marketplace pro řešení protokolování DNS podle potřeby vaší organizace.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Používejte předdefinované role správce Azure Active Directory (Azure AD), které se dají explicitně přiřadit a jsou Queryable. 

Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí správy identit a přístupu Security Center můžete monitorovat počet účtů pro správu.

Pomocí Azure AD Privileged Identity Management privilegovaných rolí pro služby a Azure Resource Manager společnosti Microsoft povolte přístup za běhu nebo jen tolik. 

- [Další informace o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Pokyny**: použití jednotného přihlašování s Azure Active Directory (Azure AD) místo konfigurace jednotlivých samostatných přihlašovacích údajů pro každou službu. Použijte doporučení pro správu identit a přístupu Security Center.

- [Jednotné přihlašování k aplikacím v Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md) 

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: povolte službu Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) a použijte doporučení pro správu identit a přístupů Security Center.

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Pokyny**: použití pracovních stanic s privilegovaným přístupem (privilegovaným přístupem) s nakonfigurovaným Multi-Factor Authentication (MFA), které jsou nakonfigurovány pro přihlášení a přístup k síťovým prostředkům Azure.

- [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: využití zjišťování rizik Azure Active Directory (Azure AD) k zobrazení výstrah a sestav na základě rizikového chování uživatelů. 

Ingestovat výstrahy Security Center detekce rizik do Azure Monitor a nakonfigurovat vlastní výstrahy a oznámení pomocí skupin akcí.

- [Principy zjišťování rizik Security Center (podezřelá aktivita)](../active-directory/identity-protection/overview-identity-protection.md) 

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

- [Jak nakonfigurovat skupiny akcí pro vlastní výstrahy a oznámení](../azure-monitor/alerts/action-groups.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné** materiály: použijte Azure Active Directory (Azure AD) jako centrální ověřování a systém autorizace pro vaše služby. Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data a také soli, hodnoty hash a bezpečně ukládá přihlašovací údaje uživatele.  

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Pokyny**: pomocí Azure Active Directory (Azure AD) poskytněte protokoly, které vám pomůžou zjistit zastaralé účty. 

Kontroly přístupu k identitám Azure je možné provádět k efektivní správě členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů by měl být pravidelně přezkoumán, aby se zajistilo, že všichni aktivní uživatelé budou mít přístup i nadále.

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: Integrace Azure Active Directory (Azure AD) přihlašovacích aktivit, zdrojů auditu a rizikového protokolu událostí s jakýmkoli nástrojem pro Siem nebo monitorování na základě vašeho přístupu. 

Zjednodušte tento proces vytvořením nastavení diagnostiky pro Azure Active Directory uživatelských účtů a odesláním protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. Všechny požadované výstrahy je možné nakonfigurovat v rámci Log Analytics pracovního prostoru.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Pokyny**: použití funkcí pro rizika a ochranu Identity v Azure Active Directory (Azure AD) ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů ve vaší virtuální síti. Ingestování dat do Azure Sentinel pro případ dalších šetření.

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: šifrování všech citlivých informací během přenosu. Ujistěte se, že klienti, kteří se připojují k prostředkům Azure ve virtuálních sítích, můžou vyjednávat TLS 1,2 nebo vyšší. Sledujte Security Center doporučení pro šifrování v klidovém provozu a šifrování při přenosu. 

Společnost Microsoft poskytuje několik možností, které mohou zákazníci využívat k internímu přenosu dat v rámci sítě Azure a externě přes Internet pro koncového uživatele. Mezi ně patří komunikace prostřednictvím virtuálních privátních sítí (s využitím šifrování IPsec/IKE), TLS (Transport Layer Security) 1,2 nebo novější (prostřednictvím komponent Azure, jako jsou například Application Gateway nebo front-in Azure), protokolů přímo na virtuálních počítačích Azure (například Windows IPsec nebo SMB) a dalších.

Kromě toho je pro veškerý provoz Azure na cestách mezi datovými centry Azure povolená možnost šifrování ve výchozím nastavení pomocí MACsec (Standard IEEE na vrstvě Data-Link), aby se zajistila důvěrnost a integrita zákaznických dat.

- [Pochopení šifrování při přenosu pomocí Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: ke správě přístupu k prostředkům použijte službu Azure RBAC. 

**Pokyny**: ke správě přístupu k datům a prostředkům použijte řízení přístupu na základě role Azure (Azure RBAC). V opačném případě použijte metody řízení přístupu ke konkrétním službám. 

Vyberte předdefinované role, jako je vlastník, přispěvatel nebo přispěvatel sítě, a přiřaďte roli příslušnému oboru. Můžete například přiřadit podmnožinu funkcí virtuální sítě s konkrétními oprávněními požadovanými pro virtuální sítě pro kteroukoli z těchto rolí. 

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Plánování virtuálních sítí](virtual-network-vnet-plan-design-arm.md#permissions)

- [Kontrola předdefinovaných rolí Azure](../role-based-access-control/built-in-roles.md#networking)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokoly aktivit Azure můžete vytvářet upozornění na důležité prostředky Azure, jako jsou třeba virtuální sítě a skupiny zabezpečení sítě.

- [Protokolování diagnostiky pro skupinu zabezpečení sítě](virtual-network-nsg-manage-log.md)

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny síťové prostředky, jako jsou virtuální sítě, podsítě v rámci předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md) 

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování virtuální sítě a souvisejících prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md) 

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md) 

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Pokyny**: budete muset vytvořit inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb vaší organizace.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků 

- Povolené typy prostředků 

Dotazování nebo zjišťování prostředků v rámci předplatných pomocí Azure Resource graphu v prostředích s vysokým zabezpečením, jako jsou například účty Azure Storage. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

- [Příklady předdefinovaných modulů Azure Policy pro virtuální síť](./policy-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: Zabraňte vytváření nebo používání prostředků pomocí Azure Policy podle požadavků organizace. Implementujte procesy pro odebrání neautorizovaných prostředků.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků 

- Povolené typy prostředků 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/index.md)

- [Příklady předdefinovaných modulů Azure Policy pro virtuální síť](./policy-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: použití podmíněného přístupu Azure k omezení schopnosti uživatele pracovat s Azure Resource Manager konfigurací "blokovat přístup" pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování a prosazování konfigurace síťových prostředků Azure a také integrované Azure Policy definice.

Exportujte všechny šablony sestavení pomocí Azure Resource Manager ve formuláři JavaScript Object Notation (JSON) a zkontrolujte je, abyste zajistili, že konfigurace splňují nebo překračují požadavky zabezpečení vaší organizace.

Implementujte doporučení z Security Center jako standardní hodnoty konfigurace pro vaše prostředky Azure.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Příklady předdefinovaných modulů Azure Policy pro virtuální síť](./policy-reference.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: pomocí Azure Resource Manager šablon a zásad Azure můžete bezpečně nakonfigurovat prostředky Azure přidružené k virtuální síti a souvisejícím prostředkům.  Šablony Azure Resource Manager jsou soubory založené na formátu JSON (JavaScript Object Notation) používané k nasazování virtuálních počítačů společně s prostředky Azure. Společnost Microsoft provádí údržbu základních šablon.  

Použijte Azure Policy [odepřít] a [nasadit, pokud neexistují] efekty pro vymáhání zabezpečených nastavení napříč prostředky Azure.

- [Informace o vytváření šablon Azure Resource Manager](../virtual-machines/windows/ps-template.md) 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Porozumění efektům Azure Policy](../governance/policy/concepts/effects.md)

- [Ukázkové šablony Azure Resource Manageru pro virtuální síť](template-samples.md)

- [Příklady předdefinovaných modulů Azure Policy pro virtuální síť](./policy-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: pomocí Azure DevOps bezpečně ukládejte a spravujte svůj kód, jako jsou vlastní zásady Azure, Azure Resource Manager šablony a požadované konfigurační skripty stavu. a tak dále.

Musíte mít oprávnění pro přístup k prostředkům, které chcete spravovat v Azure DevOps, jako je například váš kód, sestavení a sledování práce. Většina oprávnění je udělována prostřednictvím integrovaných skupin zabezpečení. Můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps nebo Active Directory, pokud jsou integrované s Team Foundation Server.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro prostředky Azure pomocí Azure Policy. Pomocí aliasů Azure Policy můžete vytvářet vlastní zásady, které umožňují auditovat nebo vymáhat konfiguraci sítě vašich prostředků Azure a všechny integrované definice zásad týkající se konkrétních prostředků. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak používat aliasy](../governance/policy/concepts/definition-structure.md#aliases)

- [Příklady předdefinovaných modulů Azure Policy pro virtuální síť](./policy-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: použijte Security Center k provádění kontrol směrného plánu pro Azure Virtual Network a souvisejících prostředků. Pomocí Azure Policy můžete upozorňovat a auditovat konfigurace prostředků Azure.

- [Jak opravit doporučení v Security Center](../security-center/security-center-remediate-recommendations.md)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Příklady předdefinovaných modulů Azure Policy pro virtuální síť](./policy-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: použití identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů pro prostředky Azure hostované v Azure Virtual Network.

- [Integrace se spravovanými identitami Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Vytvoření Key Vault](../key-vault/secrets/quick-create-portal.md) 

- [Jak zajistit Key Vault ověřování pomocí spravované identity](../key-vault/general/assign-access-policy-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: k nasazení virtuální sítě a souvisejících prostředků použijte Azure Resource Manager. Azure Resource Manager poskytuje možnost exportovat šablony, které se dají použít jako zálohy k obnovení virtuální sítě a souvisejících prostředků.  Použijte Azure Automation k pravidelnému volání rozhraní API pro export Azure Resource Manager šablony.

- [Přehled Azure Resource Manageru](../azure-resource-manager/management/overview.md)

- [Ukázkové šablony Azure Resource Manageru pro virtuální síť](template-samples.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Skupiny prostředků – Exportovat šablonu](/rest/api/resources/resourcegroups/exporttemplate)

- [Úvod do Azure Automation](../automation/automation-intro.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: k nasazení virtuální sítě a souvisejících prostředků použijte Azure Resource Manager. Azure Resource Manager poskytuje možnost exportovat šablony, které se dají použít jako zálohy k obnovení virtuální sítě a souvisejících prostředků. Použijte Azure Automation k pravidelnému volání rozhraní API pro export Azure Resource Manager šablony. Zálohujte klíče spravované zákazníkem v rámci Azure Key Vault.

- [Přehled Azure Resource Manageru](../azure-resource-manager/management/overview.md)

- [Ukázkové šablony Azure Resource Manageru pro virtuální síť](template-samples.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Skupiny prostředků – Exportovat šablonu](/rest/api/resources/resourcegroups/exporttemplate)

- [Úvod do Azure Automation](../automation/automation-intro.md)

- [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: pravidelně provádějte nasazení Azure Resource Manager šablon do izolovaného předplatného a proveďte test obnovení zálohovaných klíčů spravovaných zákazníkem.

- [Nasazení prostředků pomocí šablon ARM a Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: pomocí Azure DevOps bezpečně ukládejte a spravujte kód, třeba vlastní definice Azure Policy a šablony Azure Resource Manager. 

Udělení nebo odmítnutí oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps nebo Active Directory, pokud jsou integrované s Team Foundation Server.  

K ochraně klíčů spravovaných zákazníkem použijte řízení přístupu na základě role Azure (Azure RBAC).   

Povolit Soft-Delete a vyprázdnit ochranu v Key Vault a chránit tak klíče proti náhodnému nebo škodlivému odstranění.  

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Postup povolení ochrany Soft-Delete a vyprázdnění v Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) 

- [Obnovitelné odstranění objektů blob služby Azure Storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení.  

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Využijte příručku pro zpracování incidentů zabezpečení počítače NIST, která vám pomůže při vytváření vlastního plánu odpovědí na incidenty.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Jasně označte předplatná (například produkci nebo neprodukční) pomocí značek a vytvořte systém pro pojmenování, který bude jasně identifikovat a kategorizovat prostředky Azure, zejména ty, které zpracovávají citlivá data.  Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení v Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: vyexportujte výstrahy a doporučení pro Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. 

Pomocí konektoru Security Center Data můžete také streamovat výstrahy do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" pro výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: řiďte se pravidly společnosti Microsoft o zapojení, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft.

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)