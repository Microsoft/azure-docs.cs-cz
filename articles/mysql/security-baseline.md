---
title: Základ zabezpečení Azure pro Azure Database for MySQL
description: Základ zabezpečení Azure pro Azure Database for MySQL
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3e79d6efcbb72b73dedb5b8dfdf965736e7de3e4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757217"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>Základ zabezpečení Azure pro Azure Database for MySQL

Azure Security Baseline pro Azure Database for MySQL obsahuje doporučení, která vám pomůžou zlepšit stav zabezpečení vašeho nasazení.

Směrný plán pro tuto službu vychází z [azure security benchmark verze 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure s našimi pokyny pro osvědčené postupy.

Další informace naleznete v [tématu Přehled směrného plánu zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace naleznete v [tématu Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrana prostředků pomocí skupin zabezpečení sítě nebo brány Azure Firewall ve virtuální síti

**Pokyny:** Konfigurace privátního propojení pro azure databázi pro MySQL s privátními koncovými body. Private Link umožňuje připojení k různým službám PaaS v Azure prostřednictvím privátního koncového bodu. Azure Private Link v podstatě přináší služby Azure uvnitř vaší privátní virtuální sítě (VNet). Provoz mezi vaší virtuální sítí a instancí MySQL putuje páteřní sítí Microsoftu.

Případně můžete použít koncové body služby Virtuální síť k ochraně a omezení přístupu k síti k vaší azure databázi pro implementace MySQL. Pravidla virtuální sítě jsou jednou funkcí zabezpečení brány firewall, která řídí, zda databáze Azure pro mySQL server přijímá komunikaci odesílanou z konkrétních podsítí ve virtuálních sítích.

Můžete také zabezpečit azure database pro server MySQL pomocí pravidel brány firewall. Brána firewall serveru zabrání veškerému přístupu k databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Bránu firewall nakonfigurujete tak, že vytvoříte pravidla brány firewall určující rozsahy přípustných IP adres. Pravidla brány firewall můžete vytvořit na úrovni serveru.

Jak nakonfigurovat privátní propojení pro Azure Database pro MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

Jak vytvořit a spravovat koncové body služby virtuální sítě a pravidla virtuální sítě v Azure Database for MySQL:https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

Jak nakonfigurovat pravidla brány firewall Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal

**Monitorování Centra zabezpečení Azure**: Není k dispozici

**Odpovědnost**: Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Sledování a protokolování konfigurace a provozu sítí, podsítí a síťových rozhraní

**Pokyny:** Když je vaše instance Azure Database for MySQL zabezpečená do privátního koncového bodu, můžete nasadit virtuální počítače ve stejné virtuální síti. Skupinu zabezpečení sítě (NSG) můžete použít ke snížení rizika exfiltrace dat. Povolte protokoly toku nsg a odesílat protokoly do účtu úložiště pro audit provozu. Protokoly toku nsg můžete také odesílat do pracovního prostoru Analýzy protokolů a pomocí traffic analytics poskytovat přehled o toku provozu ve vašem cloudu Azure. Některé výhody Traffic Analytics jsou schopnost vizualizovat aktivitu v síti a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzorce toku provozu a určit chybné konfigurace sítě.

Jak nakonfigurovat privátní propojení pro Azure Database pro MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

Povolení protokolů toku nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit a používat Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrana kritických webových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odepřít komunikaci se známými škodlivými IP adresami

**Pokyny:** Použijte pokročilou ochranu před internetovými hrozbami pro databázi Azure pro MySQL. Pokročilá ochrana před internetovými zprávami detekuje neobvyklé aktivity indikující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Povolte standard ochrany DDoS ve virtuálních sítích přidružených k vaší azure databázi pro instance MySQL, abyste se ochránili před útoky DDoS. Pomocí integrované analýzy hrozeb Centra zabezpečení Azure můžete odepřít komunikaci se známými škodlivými nebo nepoužívanými internetovými IP adresami.

Jak nakonfigurovat pokročilou ochranu před internetovými hrozbami pro databázi Azure pro MySQL:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Jak nakonfigurovat ochranu Před sdos:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Záznam síťových paketů a protokolů toku

**Pokyny:** Když je vaše instance Azure Database for MySQL zabezpečená do privátního koncového bodu, můžete nasadit virtuální počítače ve stejné virtuální síti. Potom můžete nakonfigurovat skupinu zabezpečení sítě (NSG), abyste snížili riziko exfiltrace dat. Povolte protokoly toku nsg a odesílat protokoly do účtu úložiště pro audit provozu. Protokoly toku nsg můžete také odesílat do pracovního prostoru Analýzy protokolů a pomocí traffic analytics poskytovat přehled o toku provozu ve vašem cloudu Azure. Některé výhody Traffic Analytics jsou schopnost vizualizovat aktivitu v síti a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzorce toku provozu a určit chybné konfigurace sítě.

Povolení protokolů toku nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit a používat Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Nasazení síťových systémů detekce vniknutí/prevence vniknutí (IDS/IPS)

**Pokyny:** Použijte pokročilou ochranu před internetovými hrozbami pro databázi Azure pro MySQL. Pokročilá ochrana před internetovými zprávami detekuje neobvyklé aktivity indikující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Jak nakonfigurovat pokročilou ochranu před internetovými hrozbami pro databázi Azure pro MySQL:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Správa návštěvnosti webových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizace složitosti a administrativní režie pravidel zabezpečení sítě

**Pokyny:** Pro prostředky, které potřebují přístup k vaší azure databáze pro instance MySQL, použijte značky služby Virtuální síť k definování řízení přístupu k síti ve skupinách zabezpečení sítě nebo azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu výrobní značky (např. WestUs) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo jako adresy změnit.

Poznámka: Azure Database for MySQL používá značky služby "Microsoft.Sql".

Další informace o používání značek služeb:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Principy využití výrobních značek pro Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet#terminology-and-description

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Udržovat standardní konfigurace zabezpečení pro síťová zařízení

**Pokyny:** Definujte a implementujte standardní konfigurace zabezpečení pro nastavení sítě a síťové prostředky přidružené k vaší azure databázi pro instance MySQL s Azure Policy. Pomocí aliasů zásad Azure v oborech názvů Microsoft.DBforMySQL a Microsoft.Network vytvořte vlastní zásady k auditování nebo vynucení síťové konfigurace databáze Azure pro instance MySQL. Můžete také využít předdefinované definice zásad související se sítí nebo databáze Azure pro instance MySQL, například:

- DDoS Protection Standard by měla být povolena

- Vynutit připojení SSL by mělo být povoleno pro databázové servery MySQL

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Ukázky zásad Azure pro sítě:https://docs.microsoft.com/azure/governance/policy/samples/

Jak vytvořit Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentovat pravidla konfigurace provozu

**Pokyny:** Použití značek pro prostředky související se zabezpečením sítě a tokem provozu pro vaše instance Azure Database for MySQL k poskytování metadat a logické organizace.

Pomocí některé z předdefinovaných definic zásad Azure souvisejících s označováním, jako je například Vyžadovat značku a její hodnotu, můžete zajistit, aby byly všechny prostředky vytvořeny pomocí značek, a upozornit vás na existující neoznačené prostředky.

Azure PowerShell nebo Azure CLI můžete použít k vyhledávání nebo provádění akcí na prostředky na základě jejich značek.

Jak vytvářet a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Použití automatizovaných nástrojů ke sledování konfigurace síťových prostředků a zjišťování změn

**Pokyny:** Pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků souvisejících s vaší databází Azure pro instance MySQL. Vytvořte výstrahy v rámci Azure Monitor, který se aktivuje, když dojde ke změnám kritických síťových prostředků.

Jak zobrazit a načíst události protokolu aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak vytvořit výstrahy ve službě Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace naleznete [v tématu Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Použití schválených zdrojů synchronizace času

**Pokyny**: Microsoft udržuje zdroj času používaný pro prostředky Azure, jako je například Azure Database for MySQL pro časová razítka v protokolech.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurace správy centrálního protokolu zabezpečení

**Pokyny:** Povolte nastavení diagnostiky a protokoly serveru a ingestujte protokoly pro agregaci dat zabezpečení generovaných vaší databází Azure pro instance MySQL. V rámci Azure Monitoru můžete pomocí pracovního prostoru Log Analytics dotazovat a provádět analýzy a používat účty úložiště Azure pro dlouhodobé nebo archivní úložiště. Případně můžete povolit a na palubě data do Azure Sentinelu nebo siem třetí strany.

Principy protokolů serveru pro databázi Azure pro MySQL:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Jak napalubě Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Centra zabezpečení Azure**: Není k dispozici

**Odpovědnost**: Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Povolení protokolování auditování prostředků Azure

**Pokyny:** Povolte nastavení diagnostiky v azure databázi pro mysql instance pro přístup k protokolům auditování, pomalý dotaz a mysql metriky. Ujistěte se, že konkrétně povolíte protokol auditu MySQL. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky. Můžete také povolit nastavení diagnostiky protokolu aktivit Azure a odeslat protokoly do stejného pracovního prostoru Analýzy protokolů nebo účtu úložiště.

Principy protokolů serveru pro databázi Azure pro MySQL:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Jak nakonfigurovat a získat přístup k protokolům pomalých dotazů pro Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

Jak nakonfigurovat a získat přístup k protokolům auditu pro Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

Jak nakonfigurovat nastavení diagnostiky pro protokol aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorování Centra zabezpečení Azure**: Není k dispozici

**Odpovědnost**: Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Shromažďování protokolů zabezpečení z operačních systémů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny:** V rámci Azure Monitor, pro pracovní prostor Log Analytics slouží k uložení databáze Azure pro protokoly MySQL, nastavte dobu uchování v souladu s předpisy vaší organizace dodržování předpisů. Účty úložiště Azure používejte pro dlouhodobé nebo archivní úložiště.

Jak nastavit parametry uchovávání protokolu pro pracovní prostory Analýzy protokolů:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Ukládání protokolů prostředků v účtu úložiště Azure:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="26-monitor-and-review-logs"></a>2.6: Sledování a kontrola protokolů

**Pokyny:** Analyzujte a monitorujte protokoly z databáze Azure pro instance MySQL pro neobvyklé chování. Pomocí analýzy protokolů Azure Monitor můžete kontrolovat protokoly a provádět dotazy na data protokolu. Případně můžete povolit a na palubě data azure sentinelu nebo třetí strany SIEM.

Jak napalubě Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Další informace o analýze protokolů:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak provádět vlastní dotazy v Azure Monitoru:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Povolit upozornění na anomální aktivitu

**Pokyny:** Povolit pokročilou ochranu před internetovými hrozbami pro databázi Azure pro MySQL. Pokročilá ochrana před internetovými zprávami detekuje neobvyklé aktivity indikující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Kromě toho můžete povolit protokoly serveru a nastavení diagnostiky pro MySQL a odesílat protokoly do pracovního prostoru Analýzy protokolů. Založte pracovní prostor Analýzy protokolů do Azure Sentinelu, protože poskytuje řešení automatické odpovědi orchestrace zabezpečení (SOAR). To umožňuje vytvářet a používat playbooky (automatizovaná řešení) k nápravě problémů se zabezpečením.

Jak povolit pokročilou ochranu před internetovými hrozbami pro databázi Azure pro MySQL (Preview):https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Principy protokolů serveru pro databázi Azure pro MySQL:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Jak nakonfigurovat a získat přístup k protokolům pomalých dotazů pro Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

Jak nakonfigurovat a získat přístup k protokolům auditu pro Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

Jak nakonfigurovat nastavení diagnostiky pro protokol aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Jak napalubě Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizovat protokolování antimalwaru

**Pokyny**: Nepoužije se; Azure Database for MySQL nezpracovává ani nevytváří protokoly související s antimalwarem.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="29-enable-dns-query-logging"></a>2.9: Povolení protokolování dotazů DNS

**Pokyny**: Nepoužitelné; Azure Database for MySQL nezpracovává ani nevytváří protokoly související se službou DNS.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="210-enable-command-line-audit-logging"></a>2.10: Povolení protokolování auditu příkazového řádku

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace naleznete [v tématu Zabezpečení: Identita a řízení přístupu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Vedení soupisu administrativních účtů

**Pokyny:** Udržovat soupis uživatelských účtů, které mají přístup pro správu k rovině správy (například portál Azure) databáze Azure pro mysqlinstances. Kromě toho udržovat soupis účtů pro správu, které mají přístup k rovině dat (v rámci samotné databáze) databáze Azure pro instance MySQL. (Při vytváření serveru MySQL zadejte pověření pro uživatele správce. Tento správce lze použít k vytvoření dalších uživatelů MySQL.)

Azure Database for MySQL nepodporuje integrované řízení přístupu založené na rolích, ale můžete vytvořit vlastní role na základě konkrétních možností zprostředkovatele prostředků.

Principy vlastních rolí předplatného Azure:https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Principy azure databáze pro operace zprostředkovatele prostředků MySQL:https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbformysql

Principy správy přístupu pro Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/concepts-security#access-management

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Změna výchozích hesel, pokud je to možné

**Pokyny**: Azure AD nemá koncept výchozí hesla.

Po vytvoření azure databáze pro prostředek MySQL sám, Azure vynutí vytvoření administrativního uživatele se silným heslem. Po vytvoření instance MySQL však můžete použít první účet správce serveru, který jste vytvořili, k vytvoření dalších uživatelů a udělení přístupu pro správu. Při vytváření těchto účtů se ujistěte, že pro každý účet nakonfigurujete jiné silné heslo.

Jak vytvořit další účty pro Azure Database pro MySQL:https://docs.microsoft.com/azure/mysql/howto-create-users

Jak aktualizovat heslo správce:https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Použití vyhrazených administrativních účtů

**Pokyny:** Vytvořte standardní operační postupy týkající se použití vyhrazených účtů pro správu, které mají přístup k vaší azure databázi pro instance MySQL. Pomocí správy identity a přístupu Centra zabezpečení Azure můžete sledovat počet účtů pro správu.

Principy identity a přístupu Centra zabezpečení Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Zjistěte, jak vytvořit uživatele správce v Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/howto-create-users

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Použití jednotného přihlašování (SSO) s Azure Active Directory

**Pokyny:** Přihlášení do databáze Azure pro MySQL se podporuje pomocí uživatelského jména nebo hesla nakonfigurovaného přímo v databázi, stejně jako pomocí identity Služby Azure Active Directory (AD) a s využitím tokenu Azure AD pro připojení. Při použití tokenu Azure AD jsou podporovány různé metody, jako je například uživatel Azure AD, skupina Azure AD nebo aplikace Azure AD, která se připojuje k databázi.

Samostatně řízení přístupu roviny pro MySQL je k dispozici prostřednictvím ROZHRANÍ REST API a podporuje sso. Chcete-li ověřit, nastavte hlavičku autorizace pro vaše požadavky na webový token JSON, který získáte ze služby Azure Active Directory.

K ověřování pomocí Azure Database for MySQL použijte Službu Azure Active Directory:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Principy azure databáze pro rozhraní API MySQL REST:https://docs.microsoft.com/rest/api/mysql/

Principy přisátého řešení zabezpečení pomocí Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Použití vícefaktorového ověřování pro veškerý přístup založený na službě Azure Active Directory

**Pokyny:** Povolte vícefaktorové ověřování Azure Active Directory (MFA) a postupujte podle doporučení Centra identit a správy přístupu Azure Security Center. Při využití tokenů Azure AD pro přihlášení do databáze to umožňuje vyžadovat vícefaktorové ověřování pro přihlášení do databáze.

Jak povolit vícefaktorové povolení v Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

K ověřování pomocí Azure Database for MySQL použijte Službu Azure Active Directory:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Jak sledovat identitu a přístup v rámci Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Pro všechny administrativní úkoly používejte vyhrazené počítače (pracovní stanice s privilegovaným přístupem)

**Pokyny:** Používejte pracovní stanice s privilegovaným přístupem (PAWs) s vícefaktorovým ověřováním (MFA) nakonfigurovaným pro přihlášení a konfiguraci prostředků Azure.

Další informace o pracovních stanicích s privilegovaným přístupem:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové povolení v Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokolování a upozornění na podezřelou aktivitu z administrativních účtů

**Pokyny:** Povolit pokročilou ochranu před internetovými technologiemi pro azure databázi pro MySQL generovat výstrahy pro podezřelé aktivity.

Kromě toho můžete použít Azure AD Privileged Identity Management (PIM) pro generování protokolů a výstrah, když dojde k podezřelé nebo nebezpečné aktivity v prostředí.

Pomocí azure ad detekce rizik pro zobrazení výstrah a sestav na rizikové chování uživatelů.

Jak nakonfigurovat pokročilou ochranu před internetovými hrozbami pro databázi Azure pro MySQL:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Jak nasadit správu privilegovaných identit (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Seznamte se s detekcí rizik Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Správa prostředků Azure z jenom schválených umístění

**Pokyny:** Pomocí pojmenovaných umístění podmíněného přístupu povolte portálu a Azure Resource Manageru přístup pouze z konkrétních logických seskupení rozsahů IP adres nebo zemí nebo oblastí.

Jak nakonfigurovat pojmenovaná umístění v Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="39-use-azure-active-directory"></a>3.9: Použití služby Azure Active Directory

**Pokyny:** Jako centrální systém ověřování a autorizace použijte službu Azure Active Directory (AD). Azure AD chrání data pomocí silnéšifrování pro data v klidovém stavu a při přenosu. Azure AD také soli, hashy a bezpečně ukládá přihlašovací údaje uživatele.

Pro přihlášení do Azure Database for MySQL se doporučuje používat Azure AD a využít token Azure AD pro připojení. Při použití tokenu Azure AD jsou podporovány různé metody, jako je například uživatel Azure AD, skupina Azure AD nebo aplikace Azure AD, která se připojuje k databázi. 

Přihlašovací údaje Azure AD se můžou taky použít pro správu na úrovni roviny správy (například portál Azure) k řízení účtů správce MySQL.

K ověřování pomocí Azure Database for MySQL použijte Službu Azure Active Directory:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Pravidelně kontrolujte a slaďujte přístup uživatelů

**Pokyny:** Zkontrolujte protokoly služby Azure Active Directory, které vám pomohou zjistit zastaralé účty, které mohou zahrnovat účty s rolemi správy Azure Database for MySQL. Kromě toho použijte Azure Identity Access Reviews k efektivní správě členství ve skupinách, přístupu k podnikovým aplikacím, které se mohou použít pro přístup k Azure Database pro MySQL, a přiřazení rolí. Přístup uživatelů by měl být pravidelně kontrolován, například každých 90 dní, aby se zajistilo, že pouze ti praví uživatelé mají nepřetržitý přístup.

Principy sestav Azure ADhttps://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak používat recenze přístupu k identitám Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Sledování pokusů o přístup k deaktivovaným účtům

**Pokyny:** Povolte diagnostická nastavení pro Azure Database for MySQL a Azure Active Directory a odesílněte všechny protokoly do pracovního prostoru Analýzy protokolů. Nakonfigurujte požadované výstrahy (například neúspěšné pokusy o ověření) v rámci analýzy protokolů.

Jak nakonfigurovat a získat přístup k protokolům pomalých dotazů pro Azure Database for MySQL:https://docs.microsoft.com/Azure/mysql/howto-configure-server-logs-in-portal

Jak nakonfigurovat a získat přístup k protokolům auditu pro Azure Database for MySQL:https://docs.microsoft.com/Azure/mysql/howto-configure-audit-logs-portal

Jak integrovat protokoly aktivit Azure do Azure Monitoru:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorování Centra zabezpečení Azure**: Není k dispozici

**Odpovědnost**: Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Upozornění na odchylku chování přihlášení k účtu

**Pokyny:** Povolit pokročilou ochranu před internetovými technologiemi pro azure databázi pro MySQL generovat výstrahy pro podezřelé aktivity.

Pomocí funkcí ochrany identity a detekce rizik služby Azure Active Directory můžete nakonfigurovat automatické odpovědi na zjištěné podezřelé akce. Můžete povolit automatizované odpovědi prostřednictvím Azure Sentinelu k implementaci odpovědí na zabezpečení vaší organizace.

Můžete také ingestovat protokoly do Azure Sentinelu pro další šetření.

Jak nakonfigurovat pokročilou ochranu před internetovými hrozbami pro databázi Azure pro MySQL:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Přehled ochrany identity Azure AD:https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Jak zobrazit Azure AD riskantní přihlášení:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak napalubě Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Centra zabezpečení Azure**: Není k dispozici

**Odpovědnost**: Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Poskytněte společnosti Microsoft přístup k relevantním zákaznickým datům během scénářů podpory

**Pokyny**: Nepoužije se; Customer Lockbox ještě není podporovánpro Azure Database for MySQL.

Seznam služeb podporovaných customer lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

## <a name="data-protection"></a>Ochrana dat

*Další informace naleznete [v tématu Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Udržovat soupis citlivých informací

**Pokyny:** Pomocí značek můžete při sledování azure databáze pro instance MySQL nebo souvisejících prostředků, které ukládají nebo zpracovávají citlivé informace.

Jak vytvářet a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolovat systémy ukládající nebo zpracovávající citlivé informace

**Pokyny**: Implementujte samostatné odběry nebo skupiny pro správu pro vývoj, testování a výrobu. Pomocí kombinace privátních odkazů, koncových bodů služby nebo pravidel brány firewall můžete izolovat a omezit přístup k síti k databázi Azure pro instance MySQL.

Jak vytvořit další předplatná Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:https://docs.microsoft.com/azure/governance/management-groups/create

Jak nakonfigurovat privátní propojení pro Azure Database pro MySQL:https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link

Jak vytvořit a spravovat koncové body služby virtuální sítě a pravidla virtuální sítě v Azure Database for MySQL:https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

Jak nakonfigurovat pravidla brány firewall Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/concepts-firewall-rules


**Monitorování Centra zabezpečení Azure**: Není k dispozici

**Odpovědnost**: Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Sledování a blokování neoprávněného přenosu citlivých informací

**Pokyny:** Při použití virtuálních počítačů Azure pro přístup k Azure Database pro instance MySQL, využijte private link, konfigurace sítě MySQL, skupiny zabezpečení sítě a značky služeb ke zmírnění možnosti exfiltrace dat.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Database for MySQL a zavedla přísné ovládací prvky, aby zabránila ztrátě nebo vystavení zákaznických dat.

Jak zmírnit exfiltraci dat pro Azure Database pro MySQL:https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link#data-exfiltration-prevention

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Šifrování všech citlivých informací při přepravě

**Navádění**: Azure Database for MySQL podporuje připojení serveru MySQL ke klientským aplikacím pomocí ssl (Secure Sockets Layer). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky. Na webu Azure Portal se ujistěte, že je ve výchozím nastavení povoleno "Vynutit připojení SSL" pro všechny instance Azure Database for MySQL.

Aktuálně verze TLS podporovaná pro Azure Database for MySQL jsou TLS 1.0, TLS 1.1, TLS 1.2.

Jak nakonfigurovat šifrování při přenosu pro Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security

**Monitorování Centra zabezpečení Azure**: Není k dispozici

**Odpovědnost**: Sdíleno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Použití aktivního nástroje pro zjišťování k identifikaci citlivých dat

**Pokyny:** Funkce identifikace, klasifikace a prevence ztrát dat ještě nejsou k dispozici pro Azure Database for MySQL. Implementujte řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.

Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Centra zabezpečení Azure**: Není k dispozici

**Odpovědnost**: Sdíleno

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Použití Azure RBAC k řízení přístupu k prostředkům

**Pokyny:** Pomocí řízení přístupu na základě rolí Azure (RBAC) můžete řídit přístup k databázi Azure pro rovinu řízení MySQL (například portál Azure). Pro přístup k rovině dat (v rámci samotné databáze) použijte dotazy SQL k vytvoření uživatelů a konfiguraci uživatelských oprávnění. RBAC nemá vliv na uživatelská oprávnění v rámci databáze.

Jak nakonfigurovat RBAC v Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Konfigurace přístupu uživatelů pomocí databáze SQL pro Azure pro MySQL:https://docs.microsoft.com/azure/mysql/howto-create-users

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Použití ochrany před ztrátou dat na hostiteli k vynucení kontroly přístupu

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Database for MySQL a zavedla přísné ovládací prvky, aby zabránila ztrátě nebo vystavení zákaznických dat.

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Šifrování citlivých informací v klidovém stavu

**Pokyny:** Služba Azure Database for MySQL používá kryptografický modul fips 140-2 pro šifrování dat v klidovém stavu. Data, včetně záloh, jsou šifrována na disku, s výjimkou dočasných souborů vytvořených při spouštění dotazů. Služba používá 256bitovou šifru AES, která je součástí šifrování úložiště Azure, a klíče jsou spravované systémem. Šifrování úložiště je vždycky aktivní a není možné ho zakázat.

Šifrování dat pomocí klíčů spravovaných zákazníkem pro Azure Database for MySQL umožňuje přinést si vlastní klíč (BYOK) pro ochranu dat v klidovém stavu. V tomto okamžiku je nutné požádat o přístup k použití této funkce. Chcete-li tak učinit, obraťte se na:

AskAzureDBforMySQL@service.microsoft.com

Principy šifrování v klidovém stavu pro Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/concepts-security

Jak nakonfigurovat klientem spravované klíče pro Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/concepts-data-encryption-mysql

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokolování a upozorňování na změny kritických prostředků Azure

**Pokyny:** Pomocí Azure Monitor s protokolem aktivit Azure vytvořit výstrahy pro kdy dojde ke změnám na produkční instance Databáze Azure pro MySQL a dalších důležitých nebo souvisejících prostředků.

Jak vytvořit výstrahy pro události protokolu aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace naleznete v [tématu Security Control: Vulnerability Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Spuštění automatizovaných nástrojů pro vyhledávání chyb zabezpečení

**Pokyny**: V současné době není k dispozici; Azure Security Center zatím nepodporuje posouzení ohrožení zabezpečení pro Azure Database for MySQL.

Pokrytí funkcí pro služby Azure PaaS v Azure Security Center:https://docs.microsoft.com/azure/security-center/features-paas

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Nasazení řešení pro správu automatických oprav operačního systému

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Nasazení automatizovaného řešení pro správu softwarových oprav od jiných výrobců

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porovnání prohledávačů ohrožení zabezpečení zády k zadní straně

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Použití procesu hodnocení rizik a upřednostnění nápravy zjištěných zranitelných míst

**Pokyny:** Společnost Microsoft provádí správu chyb zabezpečení v základních systémech, které podporují Azure Database for MySQL.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace naleznete v [tématu Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Použití Azure Asset Discovery

**Pokyny:** Pomocí Azure Resource Graph dotaz a zjistit všechny prostředky (včetně Azure Database for MySQL instance) v rámci předplatného. Ujistěte se, že máte příslušná (číst) oprávnění ve vašem tenantovi a jsou schopni vytvořit výčet všech předplatných Azure, stejně jako prostředky v rámci vašich předplatných.

Jak vytvářet dotazy pomocí Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak zobrazit předplatná Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Principy Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="62-maintain-asset-metadata"></a>6.2: Udržování metadat datových zdrojů

**Pokyny:** Použijte značky pro Azure Database pro instance MySQL a další související prostředky poskytující metadata logicky uspořádat do taxonomie.

Jak vytvořit a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Odstranění neautorizovaných prostředků Azure

**Pokyny:** Použijte značkování, skupiny pro správu a samostatné předplatná, kde je to vhodné, k uspořádání a sledování Azure Database pro instance MySQL a souvisejících prostředků. Pravidelně odsouhlasete zásoby a zajistěte, aby byly z předplatného včas odstraněny neoprávněné prostředky.

Jak vytvořit další předplatná Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:https://docs.microsoft.com/azure/governance/management-groups/create

Jak vytvářet a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Udržovat soupis schválených prostředků Azure a softwarových titulů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorování neschválených prostředků Azure

**Pokyny**: Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

- Nepovolené typy prostředků

- Povolené typy prostředků

Kromě toho použijte Azure Resource Graph k dotazování/zjišťování prostředků v rámci předplatného.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvářet dotazy pomocí Azure Graphu:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Sledování neschválených softwarových aplikací v rámci výpočetních prostředků

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Odebrání neschválených prostředků Azure a softwarových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="68-use-only-approved-applications"></a>6.8: Používejte pouze schválené aplikace

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="69-use-only-approved-azure-services"></a>6.9: Používejte jenom schválené služby Azure

**Pokyny**: Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

- Nepovolené typy prostředků

- Povolené typy prostředků

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odepřít konkrétní typ prostředku pomocí zásad Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="610-implement-approved-application-list"></a>6.10: Implementace schváleného seznamu žádostí

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Omezte možnost uživatelů komunikovat se Správcem prostředků Azure prostřednictvím skriptů

**Pokyny:** Pomocí podmíněného přístupu Azure omezte možnost uživatelů pracovat se Správcem prostředků Azure konfigurací "Blokovat přístup" pro aplikaci Microsoft Azure Management. To může zabránit vytváření a změny prostředků v prostředí s vysokým zabezpečením, jako jsou instance Azure Database for MySQL obsahující citlivé informace.

Jak nakonfigurovat podmíněný přístup pro blokování přístupu ke Správci prostředků Azure:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Omezení možnosti uživatelů spouštět skripty v rámci výpočetních prostředků

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fyzicky nebo logicky oddělit vysoce rizikové aplikace

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace naleznete v [tématu Zabezpečení: Zabezpečená konfigurace](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny:** Definujte a implementujte standardní konfigurace zabezpečení pro vaše instance Azure Database for MySQL pomocí zásad Azure. Pomocí aliasů zásad Azure v oboru názvů Microsoft.DBforMySQL můžete vytvořit vlastní zásady k auditování nebo vynucení síťové konfigurace databáze Azure pro instance MySQL. Můžete také využít předdefinované definice zásad související s vaší databází Azure pro instance MySQL, například:

Vynutit připojení SSL by mělo být povoleno pro databázové servery MySQL

Jak zobrazit dostupné aliasy zásad Azure:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Vytvoření bezpečných konfigurací operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Udržování zabezpečených konfigurací prostředků Azure

**Pokyny:** Pomocí zásad Azure [odepřít] a [nasadit, pokud neexistují] vynutit zabezpečené nastavení napříč prostředky Azure.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Principy efektů zásad Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Udržovat bezpečné konfigurace operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Konfigurace prostředků Azure bezpečně ukládá

**Pokyny:** Pokud používáte vlastní definice zásad Azure pro vaše azure databáze pro instance MySQL a související ch prostředků, použijte Azure Repos bezpečně ukládat a spravovat svůj kód.

Jak ukládat kód v Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentace k azure repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpečné ukládání vlastních bitových kopií operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Nasazení nástrojů pro správu konfigurace systému

**Pokyny**: Pomocí aliasů zásad Azure v oboru názvů Microsoft.DBforMySQL vytvořte vlastní zásady pro výstrahu, audit ování a vynucení konfigurací systému. Kromě toho vytvořte proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Nasazení nástrojů pro správu konfigurace systému pro operační systémy

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementace automatického monitorování konfigurace pro služby Azure

**Pokyny**: Pomocí aliasů zásad Azure v oboru názvů Microsoft.DBforMySQL vytvořte vlastní zásady pro výstrahu, audit ování a vynucení konfigurací systému. Pomocí zásad Azure [audit] [odepřít] a [nasadit, pokud neexistují] automaticky vynutit konfigurace pro vaše Azure Database pro instance MySQL a související prostředky.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementace automatizovaného monitorování konfigurace operačních systémů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpečná správa tajných kódů Azure

**Pokyny:** Pro virtuální počítače Azure nebo webové aplikace spuštěné ve službě Azure App Service, které se používají k přístupu k vaší databázi Azure pro instance MySQL, použijte identitu spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení azure databáze pro správu tajných služeb MySQL. Ujistěte se, že je povoleno měkké odstranění trezoru klíčů.

Jak integrovat s Azure Spravované identity:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak vytvořit trezor klíčů:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zajistit ověřování trezoru klíčů se spravovanou identitou:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpečná a automatická správa identit

**Pokyny:** Instance Azure Database for MySQL podporuje ověřování Azure Active Directory (ve verzi Preview) pro přístup k databázím.  Při vytváření instance Azure Database for MySQL zadejte přihlašovací údaje pro uživatele správce. Tento správce lze použít k vytvoření dalších uživatelů databáze.  

Pro virtuální počítače Azure nebo webové aplikace spuštěné ve službě Azure App Service, které se používají k přístupu k vaší databázi Azure pro instance MySQL, použijte identitu spravované služby ve spojení s Azure Key Vault k ukládání a načítání přihlašovacích údajů pro instanci Azure Database for MySQL. Ujistěte se, že je povoleno měkké odstranění trezoru klíčů.

Pomocí spravovaných identit můžete poskytovat služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory (AD). Spravované identity umožňuje ověření na libovolnou službu, která podporuje ověřování Azure AD, včetně trezoru klíčů, bez pověření ve vašem kódu.

Jak nakonfigurovat spravované identity:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Jak integrovat s Azure Spravované identity:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminujte nezamýšlenou expozici pověření

**Pokyny**: Implementujte skener pověření k identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů také podpoří přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je azure key vault.

Jak nastavit skener pověření:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace naleznete v [tématu Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Používejte centrálně spravovaný antimalwarový software

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

Microsoft anti-malware je povolena na podkladovém hostiteli, který podporuje služby Azure (například Azure Database for SQL), ale neběží na obsah zákazníka.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Předprohledávací soubory, které se mají nahrát do nevýpočetních prostředků Azure

**Pokyny:** Microsoft anti-malware je povolena na podkladovém hostiteli, který podporuje služby Azure (například Azure Database for MySQL), ale neběží na obsah zákazníka.

Předem proskente veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, jako je app service, storage data lake, blob storage, Azure Database for MySQL atd. Společnost Microsoft nemá v těchto případech přístup k vašim datům.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Zajistěte aktualizaci antimalwarového softwaru a podpisů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

Microsoft anti-malware je povolena na podkladovém hostiteli, který podporuje služby Azure (například Azure Database for MySQL), ale neběží na obsah zákazníka.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Není k mů e pojem

## <a name="data-recovery"></a>Obnovení dat

*Další informace naleznete [v tématu Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zajistěte pravidelné automatické zálohování

**Pokyny:** Azure Database for MySQL přebírá zálohy datových souborů a transakční protokol. V závislosti na podporované maximální velikosti úložiště buď pořizujeme úplné a rozdílové zálohy (4 TB max servery úložiště) nebo zálohy snímků (až 16 TB max servery úložiště). Tyto zálohy umožňují obnovit server na libovolný bod v čase v rámci nakonfigurované doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Volitelně jej můžete nakonfigurovat až na 35 dní. Všechny zálohy jsou šifrovány pomocí 256bitového šifrování AES.

Principy záloh pro Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/concepts-backup

Principy počáteční konfigurace Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/tutorial-design-database-using-portal

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Sdíleno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Proveďte kompletní zálohy systému a zálohujte všechny klienty spravované klíče

**Pokyny**: Azure Database for MySQL automaticky vytvoří zálohy serveru a uloží je do místně redundantní nebo geograficky redundantní úložiště podle volby uživatele. Zálohy lze použít k obnovení serveru do určitého bodu v čase. Zálohování a obnovení jsou nezbytnou součástí každé strategie kontinuity podnikání, protože chrání vaše data před náhodným poškozením nebo odstraněním. 

Pokud pomocí Azure Key Vault ukládat přihlašovací údaje pro azure databáze pro instance MySQL, zajistit pravidelné automatické zálohování klíčů. 

Principy záloh pro Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/howto-restore-server-portal 

Jak zálohovat klíče trezoru klíčů:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Sdíleno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny:** V Azure Database for MySQL, provádění obnovení vytvoří nový server z původního serveru zálohy. K dispozici jsou dva typy obnovení: obnovení v čase v čase a geografické obnovení. Obnovení bodu v čase je k dispozici s možností redundance zálohování a vytvoří nový server ve stejné oblasti jako původní server. Geografické obnovení je k dispozici pouze v případě, že jste server nakonfigurovali pro geograficky redundantní úložiště a umožňuje obnovit server do jiné oblasti.

Odhadovaná doba obnovení závisí na několika faktorech, včetně velikosti databáze, velikosti transakčního protokolu, šířky pásma sítě a celkového počtu databází, které se současně obnovují ve stejné oblasti. Doba zotavení je obvykle kratší než 12 hodin.

Pravidelně otestujte obnovení databáze Azure pro instance MySQL.

Principy zálohování a obnovení v Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/concepts-backup

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zajištění ochrany záloh a klíčů spravovaných zákazníkem

**Pokyny:** Azure Database for MySQL trvá úplné, rozdílové a transakční protokol zálohy. Tyto zálohy umožňují obnovit server na libovolný bod v čase v rámci nakonfigurované doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Volitelně jej můžete nakonfigurovat až na 35 dní. Všechny zálohy jsou šifrovány pomocí 256bitového šifrování AES. Ujistěte se, že je povoleno měkké odstranění trezoru klíčů.

Principy zálohování a obnovení v Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/concepts-backup

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace naleznete [v tématu Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Vytvoření průvodce odezvou na incidenty

**Pokyny:** Vytvořte průvodce odezvou na incidenty pro vaši organizaci. Ujistěte se, že existují písemné plány reakce na incidenty, které definují všechny role personálu, stejně jako fáze zpracování incidentů / správy od detekce až po kontrolu po incidentu.

Jak nakonfigurovat automatizaci pracovních postupů v Centru zabezpečení Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Pokyny pro vytváření vlastního procesu reakce na bezpečnostní incidenty:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomie incidentu v centru Microsoft Security Response Center:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Zákazník může také využít nist počítačové bezpečnosti Incident Handling Guide na pomoc při vytváření vlastního plánu reakce na incidenty:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Vytvoření postupu hodnocení incidentů a stanovení priorit

**Pokyny**: Centrum zabezpečení přiřazuje každé výstraze závažnost, která vám pomůže určit prioritu, které výstrahy by měly být nejprve prošetřeny. Závažnost je založena na tom, jak sebevědomý Security Center je v hledání nebo analytice slouží k vydání výstrahy, stejně jako úroveň spolehlivosti, že tam byl škodlivý záměr za aktivitu, která vedla k upozornění.

Navíc jasně označit odběry (pro ex. výroby, non-prod) a vytvořit systém pojmenování pro jasně identifikovat a kategorizovat prostředky Azure.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="103-test-security-response-procedures"></a>10.3: Zkušební postupy odezvy zabezpečení

**Pokyny**: Proveďte cvičení k testování schopností reakce na incidenty vašich systémů na pravidelné kadenci. Identifikujte slabá místa a mezery a podle potřeby plán revidujte.

Podívejte se na publikaci NIST: Průvodce testovacími, školicími a cvičebními programy pro plány a schopnosti IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Poskytněte kontaktní údaje bezpečnostních incidentů a nakonfigurujte upozornění na bezpečnostní incidenty

**Pokyny**: Kontaktní informace o bezpečnostních incidentech společnost Microsoft použije k tomu, aby vás kontaktovala, pokud centrum MSRC (Microsoft Security Response Center) zjistí, že k datům zákazníka byla přístupná nezákonná nebo neoprávněná strana.  Zkontrolujte incidenty po faktu, abyste zajistili, že budou problémy vyřešeny.

Jak nastavit kontakt zabezpečení Centra zabezpečení Azure:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Začleňte výstrahy zabezpečení do systému reakce na incidenty

**Pokyny:** Exportujte výstrahy a doporučení Centra zabezpečení Azure pomocí funkce Nepřetržitý export. Nepřetržitý export umožňuje exportovat výstrahy a doporučení buď ručně, nebo průběžně. Datový konektor Azure Security Center můžete použít k streamování výstrah Sentinel.

Jak nakonfigurovat nepřetržitý export:https://docs.microsoft.com/azure/security-center/continuous-export

Jak streamovat výstrahy do Azure Sentinelu:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizace reakce na výstrahy zabezpečení

**Pokyny:** Pomocí funkce Automatizace pracovních postupů v Centru zabezpečení Azure můžete automaticky aktivovat odpovědi prostřednictvím "Logic Apps" na výstrahy zabezpečení a doporučení.

Konfigurace automatizace pracovních postupů a aplikací logiky:https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace naleznete [v tématu Bezpečnostní kontrola: Penetrační testy a cvičení červeného týmu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Proveďte pravidelné penetrační testování prostředků Azure a zajistěte nápravu všech kritických zjištění zabezpečení do 60 dnů

**Pokyny**: Postupujte podle pravidel společnosti Microsoft a ujistěte se, že vaše penetrační testy neporušují zásady společnosti Microsoft:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Další informace o strategii společnosti Microsoft a provádění červených týmů a testování průniku živých webů proti cloudové infrastruktuře, službám a aplikacím spravovanému společností Microsoft naleznete zde:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

## <a name="next-steps"></a>Další kroky

- Podívejte se na [srovnávací test zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Další informace o [plánech zásad zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
