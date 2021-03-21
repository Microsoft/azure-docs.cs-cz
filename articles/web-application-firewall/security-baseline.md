---
title: Základní hodnota zabezpečení Azure pro Firewall webových aplikací Azure
description: Základní hodnoty zabezpečení brány firewall webových aplikací Azure poskytují pokyny a zdroje pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ae6b4f38772cd6c6755ece78fb5c47834a616204
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211630"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Základní hodnota zabezpečení Azure pro Firewall webových aplikací Azure

Tato základní hodnota zabezpečení se týká pokynů z webu [Azure Security test 1,0](../security/benchmarks/overview-v1.md) na bránu firewall webových aplikací Azure. Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah se seskupuje podle **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů týkajících se brány firewall webových aplikací Azure. Neexistují **ovládací prvky** , které se nevztahují na bránu firewall webových aplikací Azure. 

Pokud chcete zjistit, jak se Firewall webových aplikací Azure zcela mapuje na bezpečnostní srovnávací testy zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení služby Firewall webových aplikací Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Pokyny**: použijte Microsoft Azure firewallu webových aplikací (WAF) pro centralizovanou ochranu webových aplikací před běžnými útoky a ohroženími zabezpečení, jako je například injektáže SQL a skriptování mezi weby. 

- Zjistitelný režim: Tento režim slouží ke studiu síťového provozu, pochopení a kontrole falešně pozitivních hodnot. Monitoruje a protokoluje všechny výstrahy na hrozby. Ujistěte se, že jsou vybrané a zapnuté diagnostické a WAF protokoly. Všimněte si, že WAF neblokuje příchozí požadavky, když pracuje v režimu detekce.
- Režim prevence: blokuje vniknutí a útoky zjištěné pravidly. Útočník obdrží výjimku "403 neoprávněný přístup" a připojení je zavřené. Režim prevence zaznamenává takové útoky do protokolů WAF.

Naplánujte provoz sítě pomocí režimu detekce WAF. Po určení potřeb přenosů nakonfigurujte WAF v režimu prevence tak, aby Bock nežádoucí provoz.

Sledujte doporučení s vysokou závažností od Security Center pro všechny prostředky s podporou webu, které nejsou chráněné nástrojem WAF.  

- [Skupiny pravidel a pravidla pro počítačový rezervační Firewall webových aplikací](ag/application-gateway-crs-rulegroups-rules.md) 

- [Režimy WAF na Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF režimy na předních dveřích](afds/afds-overview.md#waf-modes)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: k povolení a blokování provozu použijte vlastní pravidla s bránou firewall webových aplikací Azure (WAF). Například veškerý provoz přicházející z rozsahu IP adres může být zablokován. Nakonfigurujte Azure WAF tak, aby běžel v režimu prevence, který blokuje vniknutí a útoky zjištěné pravidly. Útočník obdrží výjimku "403 neoprávněný přístup" a připojení je zavřené. Režim prevence zaznamenává takové útoky do protokolů WAF.

- [Režimy WAF na Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF režimy na předních dveřích](afds/afds-overview.md#waf-modes)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Pokyny**: Firewall webových aplikací Azure (WAF) je základní součástí ochrany webových aplikací Azure. Využijte Azure WAF k poskytování centralizované ochrany pro webové aplikace před běžnými útoky a ohroženími zabezpečení s předem nakonfigurovanými OWASP podpisy z prvních 10 kategorií.

Přizpůsobte si Azure WAF s pravidly a skupinami pravidel podle požadavků na webovou aplikaci a odstraňte falešně pozitivní výsledky. Přiřaďte zásady Azure WAF pro každou lokalitu za WAF, aby bylo možné nakonfigurovat konkrétní lokalitu. Azure WAF podporuje geografické filtrování, omezení četnosti a nastavení výchozích pravidel spravovaných službou Azure. a vlastní pravidla je možné vytvořit tak, aby vyhovovala potřebám aplikace. 

Nakonfigurujte službu Azure WAF tak, aby běžela v režimu prevence po monitorování standardních hodnot síťového provozu v režimu detekce po určenou dobu. Azure WAF blokuje vniknutí a útoky zjištěné pravidly v režimu prevence. Útočník obdrží výjimku "403 neoprávněný přístup" a připojení je zavřené. Režim prevence zaznamenává takové útoky do protokolů WAF.

- [Režimy WAF na Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF režimy na předních dveřích](afds/afds-overview.md#waf-modes)

- [Skupiny pravidel a pravidla pro počítačový rezervační Firewall webových aplikací](ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: Vytvářejte, přidružte a logicky uspořádejte prostředky v rámci předplatného Azure pomocí značek pro detekci běžných chybných konfigurací aplikací (například Apache a IIS). 

Použijte pravidla a skupiny pravidel na zásady firewallu webových aplikací (WAF) Azure na základě použitých metadat značek.

- [Zásady WAF na Application Gateway](/cli/azure/network/application-gateway/waf-policy) 

- [Zásady WAF na front-dveří](/cli/azure/ext/front-door/network/front-door/waf-policy)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro skupiny zabezpečení sítě přidružené k firewallu webových aplikací Azure (WAF) ve vaší podsíti Azure Application Gateway a také všechny další prostředky, které se týkají zabezpečení sítě a toku provozu. Pro jednotlivá pravidla skupiny zabezpečení sítě použijte pole Popis k určení potřeby podniku, doby trvání a tak dále pro všechna pravidla, která povolují provoz do nebo ze sítě.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Vyberte Azure PowerShell nebo rozhraní příkazového řádku Azure, abyste mohli vyhledávat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny nastavení sítě a prostředků, které souvisejí s nasazeními firewallu webových aplikací Azure (WAF). Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny kritických nastavení sítě nebo prostředků.

- [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Pokyny**: Vytvořte pravidlo sítě pro Firewall webových aplikací Azure (WAF), abyste povolili přístup k serveru NTP pomocí příslušného portu a protokolu, jako je například port 123 přes protokol UDP.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: Konfigurace protokolů brány firewall webových aplikací (WAF) pro Azure, které se mají odeslat do centrálního řešení pro správu protokolu zabezpečení, jako je například Azure Sentinel nebo Siem třetí strany. Tyto protokoly zahrnují protokoly aktivit Azure, diagnostiky a WAF v reálném čase. tyto protokoly se pak dají zobrazit v různých nástrojích, například Azure Monitor, Excelu a Power BI. Protokoly brány firewall webových aplikací Azure poskytují přehled o tom, jaká data Azure WAF vyhodnocuje, odpovídá a blokuje.

Azure Sentinel má integrovaný sešit Azure WAF, který poskytuje přehled událostí zabezpečení na Azure WAF. Tento sešit obsahuje události, odpovídající a blokovaná pravidla a všechno ostatní, co se přihlásilo do protokolů brány firewall. Tato telemetrie se dá využít k tomu, aby se aktivovala automatizace PlayBook, která bude na základě událostí WAF shromažďovaných pomocí Azure Sentinelu oznamovat nebo přijímat nápravné akce.

- [Zobrazení protokolů aktivit](../azure-resource-manager/management/view-activity-logs.md)

- [Diagnostické protokoly pro Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Připojení dat z firewallu webových aplikací Microsoftu do Azure Sentinel](../sentinel/connect-azure-waf.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: povolení protokolování prostředků brány firewall webových aplikací (WAF) v Azure pro přístup k protokolům auditu, zabezpečení a diagnostickým protokolům. Firewall webových aplikací Azure poskytuje podrobné hlášení o všech zjištěných hrozbách, které jsou zpřístupněny v konfigurovaných diagnostických protokolech. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky.

- [Přehled protokolování](ag/ag-overview.md#logging)

- [Přehled dotazování protokolu Azure Monitor](../azure-monitor/logs/log-query-overview.md)

- [Přehled protokolů platformy Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: odešlete protokoly brány firewall webových aplikací (WAF) Azure do vlastního účtu úložiště a definujte zásady uchovávání informací. Pomocí Azure Monitor můžete nastavit dobu uchování pracovního prostoru Log Analytics na základě požadavků vaší organizace na dodržování předpisů.
- [Konfigurace monitorování pro účet úložiště](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: Firewall webových aplikací Azure (WAF) poskytuje podrobné hlášení o každé zjištěné hrozbě. Protokolování je integrováno do protokolů Azure Diagnostics, které poskytují podrobné informace o operacích a chybách, které jsou důležité pro auditování a řešení potíží. 

Instance Azure WAF jsou integrované s Security Center, aby odesílaly výstrahy a informace o stavu pro vytváření sestav. Použijte doporučení Security Center k detekci nechráněných webových aplikací a ochraně těchto ohrožených prostředků. 

Azure Sentinel má integrovaný sešit událostí WAF-firewall, který poskytuje přehled událostí zabezpečení na WAF. Mezi ně patří události, párové a blokované pravidla a všechno ostatní, co se přihlásí do protokolů brány firewall.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/index.yml) 

- [Postup povolení nastavení diagnostiky pro Azure Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Monitorování metrik a protokolů v frontách Azure na předních dveřích](../frontdoor/front-door-diagnostics.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: Povolte nastavení diagnostiky protokolu aktivit Azure a také nastavení diagnostiky pro Azure WAF a odešlete protokoly do pracovního prostoru Log Analytics. Můžete provádět dotazy v Log Analytics k hledání podmínek, identifikaci trendů, analýze vzorů a poskytování mnoha dalších přehledů na základě shromážděných dat. Vytvořte výstrahy pro aktivitu neobvyklé na základě metrik WAF. Například pokud blokovaný počet požadavků překročí "X", "do" Y ".

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Vytvoření upozornění v rámci Azure](../azure-monitor/alerts/tutorial-response.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Pokyny**: nasazení firewallu webových aplikací Azure (WAF) před kritickými webovými aplikacemi pro další kontrolu příchozího provozu. 

Azure WAF poskytuje centralizovanou ochranu webových aplikací před běžnými útoky a ohroženími zabezpečení a chrání vaše aplikace tím, že kontroluje příchozí webový provoz a blokuje útoky, jako jsou například injektáže SQL, skriptování mezi weby, nakládku malwaru a útoky DDoS.

- [Jak nasadit Azure WAF](ag/create-waf-policy-ag.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Azure Active Directory (Azure AD) mají předdefinované role, které se dotazují a musí se explicitně přiřadit. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Pokyny**: v rámci WAF nejsou k dispozici žádná přiřazení místních správců. V prostředí je ale možné Azure Active Directory (Azure AD) role správců, což by mohlo umožňovat řízení správy prostředků Azure WAF.
Je vhodné vytvořit standardní operační postupy kolem použití vyhrazených účtů pro správu, které mají přístup k instancím firewallu webových aplikací Azure (WAF). Pomocí funkcí správy identit a přístupu Security Center můžete monitorovat počet účtů pro správu.

- [Pochopení Azure Security Center identity a přístupu](../security-center/security-center-identity-access.md)

- [Naučte se vytvářet uživatele správců v Azure Database for PostgreSQL](../postgresql/howto-create-users.md#the-server-admin-account)

- [Jak používat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: povolte službu Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) a použijte doporučení pro správu identit a přístupů Security Center.

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Doprovodné** materiály: použití pracovní stanice s privilegovaným přístupem (privilegovaným přístupem) s konfigurací Multi-Factor Authentication (MFA) nakonfigurovaných pro přihlášení a konfiguraci firewallu webových aplikací Azure (WAF) a souvisejících prostředků. 

- [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použití sestav zabezpečení Azure Active Directory (Azure AD) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Pomocí Security Center můžete monitorovat aktivitu identity a přístupu.

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](../active-directory/identity-protection/overview-identity-protection.md)

- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: umožňuje nakonfigurovat podmínku umístění zásad podmíněného přístupu a spravovat pojmenovaná umístění. 

Vytvořte logické skupiny rozsahů IP adres nebo zemí a oblastí s pojmenovanými umístěními. Omezte přístup k citlivým prostředkům, například Azure Key Vault tajných kódů, do nakonfigurovaných pojmenovaných umístění.

- [Jaká je podmínka umístění v Azure Active Directory podmíněný přístup](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data a také soli, hodnoty hash a bezpečně ukládá přihlašovací údaje uživatele.
- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Pomocí kontrol přístupu Azure identity můžete efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Pravidelně kontrolujte přístup uživatelů, aby bylo zajištěno, že pouze aktivní uživatelé budou mít přístup i nadále.

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: Integrace Azure Active Directory (Azure AD) přihlašovacích aktivit, zdrojů auditu a rizikového protokolu událostí s jakýmkoli nástrojem pro Siem nebo monitorování, jako je Azure Sentinel.

Zjednodušte tento proces vytvořením nastavení diagnostiky pro účty Azure Active Directory (Azure AD) a odesláním protokolů auditu a přihlášení do pracovního prostoru Log Analytics. Nakonfigurujte požadované výstrahy v pracovním prostoru Log Analytics.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Pokyny**: použití funkcí pro rizika a ochranu Identity v Azure Active Directory (Azure AD) ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů. Ingestujte data do Azure Sentinel pro další šetření.

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které vám pomůžou při sledování firewallu webových aplikací Azure (WAF) a souvisejících prostředků, které ukládají nebo zpracovávají citlivé informace.
- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: Implementujte izolaci pomocí samostatných předplatných a skupin pro správu pro jednotlivé domény zabezpečení, jako je například typ prostředí a úroveň citlivosti dat, například vývojové, testovací a produkční prostředí. 

Řízení přístupu k prostředkům Azure pomocí řízení přístupu na základě role Azure (RBAC).

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: šifrování všech citlivých informací během přenosu. Ujistěte se, že všichni klienti, kteří se připojují k instancím firewallu webových aplikací Azure (WAF) a souvisejícím prostředkům, můžou vyjednávat TLS 1,2 nebo vyšší.

Pokud je to možné, postupujte podle Security Center doporučení pro šifrování v klidovém režimu a v části šifrování.

- [Pochopení šifrování při přenosu pomocí Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: řízení přístupu k prostředkům Azure pomocí řízení přístupu na základě role Azure (RBAC).
- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: používejte šifrování v klidovém provozu pro všechny prostředky Azure, včetně firewallu webových aplikací Azure (WAF) a souvisejících prostředků. Microsoft doporučuje povolit správu šifrovacích klíčů v Azure, ale v některých případech je k dispozici možnost spravovat vlastní klíče.

- [Principy šifrování neaktivních uložených dat v Azure](../security/fundamentals/encryption-atrest.md)

- [Postup konfigurace šifrovacích klíčů spravovaných zákazníkem](../storage/common/customer-managed-keys-configure-key-vault.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: Nakonfigurujte Firewall webových aplikací Azure (WAF) tak, aby běžel v režimu prevence po monitorování standardních hodnot síťového provozu v režimu detekce pro předem stanovenou dobu. 

Azure WAF v režimu prevence blokuje vniknutí a útoky zjištěné pravidly. Útočník obdrží výjimku "403 neoprávněný přístup" a připojení je zavřené. Režim prevence zaznamenává takové útoky do protokolů WAF.

- [Přehled integrace mezi Application Gateway a Azure Security Center](../security-center/security-center-partner-integration.md)

- [Režimy WAF na Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF režimy na předních dveřích](afds/afds-overview.md#waf-modes)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat nebo zjistit všechny prostředky, jako jsou výpočetní prostředky, úložiště, síť, porty a protokoly, a tak dále v rámci svých předplatných. 

Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se vytvořila výčet všech předplatných Azure i prostředků v rámci předplatných. I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: používejte značky v zásadách firewallu webových aplikací (WAF) Azure. Značky je možné přidružit k prostředkům a použít logicky k organizování přístupu k těmto prostředkům v rámci zákaznického předplatného. 

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování WAF Azure a souvisejících prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: Vytvořte inventář schválených prostředků, včetně konfigurace podle potřeb organizace.

Použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných. Pomocí Azure Resource Graphu se můžete dotazovat na prostředky v rámci jejich předplatných a zjišťovat je. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných.
Pomocí Azure Resource graphu se můžete dotazovat nebo zjistit prostředky firewallu webových aplikací (WAF) Azure v rámci svých předplatných. Zajistěte, aby všechny prostředky Azure WAF a související prostředky v prostředí byly schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: monitorování a odebrání neschválených prostředků Azure WAF s Azure Policy k odepření nasazení Azure WAF nebo určitého typu WAF, například Azure WAF v1 vs v2.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení služeb, které můžete zřídit ve vašem prostředí.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/index.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: použití podmíněného přístupu Azure k omezení schopnosti uživatele pracovat se správcem prostředků Azure pomocí konfigurace "blokování přístupu" pro aplikaci "Microsoft Azure Management".

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Pokyny**: Firewall webových aplikací Azure (WAF) je možné přidružit k různým prostředím prostřednictvím sítí, skupin prostředků nebo předplatných a oddělit aplikace s vysokým rizikem.

- [Přehled služby Azure Virtual Network](../virtual-network/virtual-networks-overview.md)

- [Uspořádání prostředků s využitím skupin pro správu Azure](../governance/management-groups/overview.md)

- [Průvodce rozhodováním ohledně předplatného](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro nastavení sítě související s nasazeními firewallu webových aplikací (WAF) v Azure.
Pomocí aliasů Azure Policy v oboru názvů Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vystavování konfigurace sítě vašich bran Azure Application Gateway, virtuálních sítí, skupin zabezpečení sítě a používání integrovaných definic zásad.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [odepřít] a [nasazení, pokud neexistují] efekty pro vymáhání zabezpečených nastavení napříč firewally webových aplikací Azure (WAF) a souvisejícími prostředky. 

Pomocí Azure Resource Manager šablon můžete udržovat konfiguraci zabezpečení WAF Azure a související prostředky, které vaše organizace vyžaduje.

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Přehled šablon Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: pomocí Azure DevOps bezpečně ukládejte a spravujte svůj kód, jako jsou vlastní zásady Azure a šablony Azure Resource Manager. 

Udělení nebo odmítnutí oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps nebo Active Directory, pokud jsou integrované s Team Foundation Server (TFS).

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. Network k vytváření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Vývoj procesu a kanálu pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Dokumentace k Azure Policy](../governance/policy/index.yml)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. Network k vytváření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. 

Použijte Azure Policy [audit], [Deny] a [nasazení, pokud neexistuje] efekty pro automatické vymáhání konfigurací pro prostředky Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Dokumentace k Azure Policy](../governance/policy/index.yml)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: použití Azure Key Vault k bezpečnému ukládání certifikátů. Azure Key Vault je úložiště tajné databáze spravované platformou, které můžete použít k ochraně tajných klíčů, klíčů a certifikátů SSL. 

Azure Application Gateway podporuje integraci s Key Vault pro certifikáty serveru, které jsou připojené k naslouchacím procesům s povoleným protokolem HTTPS. 

- [Jak nakonfigurovat ukončení protokolu SSL pomocí Key Vault certifikátů pomocí Azure PowerShell](../application-gateway/configure-keyvault-ps.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v kódu, který bude také podporovat přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, například Azure Key Vault.
- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: Ujistěte se, že je pro Azure Key Vault povolené obnovitelné odstranění. Obnovitelné odstranění umožňuje obnovit odstraněné trezory klíčů a objekty trezoru, jako jsou klíče, tajné klíče a certifikáty.

- [Použití obnovitelného odstranění Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: vývoj Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování incidentů a správu, které se mají zjišťovat, na kontrolu po jednotlivých událostech. 

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Pomoc při tvorbě vlastního plánu odpovědí na incidenty najdete v příručce pro zpracování incidentů v počítači s NIST.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.
Jasně označte předplatná (například produkci, neprodukční) a vytvořte názvový systém, který bude jasně identifikovat a kategorizovat prostředky Azure.

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.
- [Postup testování, školení a cvičení programů pro plány a možnosti IT najdete v Průvodci publikováním v NIST.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.
- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel podle požadavků vaší organizace.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.
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