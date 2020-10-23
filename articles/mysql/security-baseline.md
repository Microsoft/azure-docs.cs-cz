---
title: Základní hodnoty zabezpečení Azure pro Azure Database for MySQL
description: Základní Azure Database for MySQL zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: mysql
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 67eba8bdc3bdc3b44a3f98692c8ffd7fb7958eb6
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424037"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>Základní hodnoty zabezpečení Azure pro Azure Database for MySQL

Základní plán zabezpečení Azure pro Azure Database for MySQL obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: konfigurace privátního odkazu pro Azure Database for MySQL se soukromými koncovými body. Private Link umožňuje připojení k různým službám PaaS v Azure přes privátní koncový bod. Azure Private Link v podstatě přináší služby Azure do vaší privátní virtuální sítě. Přenosy mezi vaší virtuální sítí a instancí MySQL cestují v páteřní síti Microsoftu.

Alternativně můžete použít koncové body služby Virtual Network k ochraně a omezení síťového přístupu k vašim Azure Database for MySQL implementaci. Pravidla virtuální sítě jsou jednou funkcí zabezpečení brány firewall, která určuje, jestli váš server Azure Database for MySQL přijímá komunikaci, která se odesílají z konkrétních podsítí ve virtuálních sítích.

Můžete také zabezpečit Azure Database for MySQL server pomocí pravidel brány firewall. Brána firewall serveru znemožní přístup k vašemu databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Bránu firewall nakonfigurujete tak, že vytvoříte pravidla brány firewall určující rozsahy přípustných IP adres. Pravidla brány firewall můžete vytvořit na úrovni serveru.

- [Postup konfigurace privátního odkazu pro Azure Database for MySQL](howto-configure-privatelink-portal.md)

- [Jak vytvořit a spravovat koncové body služby virtuální sítě a pravidla virtuální sítě v Azure Database for MySQL](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

- [Postup konfigurace Azure Database for MySQL pravidel brány firewall](howto-manage-firewall-using-portal.md)

**Monitorování Azure Security Center**: není k dispozici.

**Zodpovědnost**: zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné**materiály: Pokud je vaše instance Azure Database for MySQL zabezpečená pro soukromý koncový bod, můžete virtuální počítače nasadit ve stejné virtuální síti. Ke snížení rizika exfiltrace dat můžete použít skupinu zabezpečení sítě (NSG). Povolte protokoly toku NSG a odešlete protokoly do účtu úložiště pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Postup konfigurace privátního odkazu pro Azure Database for MySQL](howto-configure-privatelink-portal.md)

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: použití rozšířené ochrany před internetovými útoky pro Azure Database for MySQL. Rozšířená ochrana před internetovými útoky detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Povolte DDoS Protection Standard ve virtuálních sítích přidružených k vašim Azure Database for MySQL instancím, abyste se mohli chránit před útoky DDoS. K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

- [Jak nakonfigurovat rozšířenou ochranu před internetovými útoky pro Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [Jak nakonfigurovat DDoS Protection](../virtual-network/manage-ddos-protection.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné**materiály: Pokud je vaše instance Azure Database for MySQL zabezpečená pro soukromý koncový bod, můžete virtuální počítače nasadit ve stejné virtuální síti. Pak můžete nakonfigurovat skupinu zabezpečení sítě (NSG), aby se snížilo riziko exfiltrace dat. Povolte protokoly toku NSG a odešlete protokoly do účtu úložiště pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny**: použití rozšířené ochrany před internetovými útoky pro Azure Database for MySQL. Rozšířená ochrana před internetovými útoky detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

- [Jak nakonfigurovat rozšířenou ochranu před internetovými útoky pro Azure Database for MySQL](howto-database-threat-protection-portal.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné**materiály: u prostředků, které potřebují přístup k instancím Azure Database for MySQL, použijte značky služby Virtual Network a definujte řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. SQL. WestUs) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo zamítnout přenos pro odpovídající službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Poznámka: Azure Database for MySQL používá značky služby Microsoft. SQL.

- [Další informace o použití značek služeb](../virtual-network/service-tags-overview.md)

- [Pochopení použití značek služby pro Azure Database for MySQL](concepts-data-access-and-security-vnet.md#terminology-and-description)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro nastavení sítě a síťové prostředky přidružené k vašim Azure Database for MySQL instancím Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. DBforMySQL a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich Azure Database for MySQLch instancí. Můžete také využít integrované definice zásad týkající se sítě nebo vašich Azure Database for MySQL instancí, například:

- Měla by být povolená DDoS Protection Standard.

- Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ukázky Azure Policy pro sítě](/azure/governance/policy/samples/)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné**materiály: používejte značky pro prostředky, které se týkají zabezpečení sítě a toku provozu pro vaše Azure Database for MySQL instance pro poskytování metadat a logické organizace.

Použijte libovolné předdefinované Azure Policy definice týkající se označování, jako je například **vyžadovat značku a její hodnotu** , aby se zajistilo, že všechny prostředky jsou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vašimi Azure Database for MySQL instancemi. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Pokyny**: Společnost Microsoft udržuje zdroj času používaný pro prostředky Azure, například Azure Database for MySQL pro časová razítka v protokolech.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: povolení nastavení diagnostiky a protokolů serveru a příjem protokolů pro agregaci dat zabezpečení generovaných vašimi Azure Database for MySQL instancemi. V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé nebo archivní úložiště. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

- [Vysvětlení protokolů serveru pro Azure Database for MySQL](concepts-monitoring.md#server-logs)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování Azure Security Center**: není k dispozici.

**Zodpovědnost**: zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: povolení nastavení diagnostiky na instancích Azure Database for MySQL pro přístup k protokolům pro audit, pomalé dotazy a MySQL. Ujistěte se, že jste konkrétně povolili protokol auditu MySQL. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky. Můžete také povolit nastavení diagnostiky protokolů aktivit Azure a odesílat protokoly do stejného Log Analytics pracovního prostoru nebo účtu úložiště.

- [Vysvětlení protokolů serveru pro Azure Database for MySQL](concepts-monitoring.md#server-logs)

- [Jak nakonfigurovat a přistupovat k protokolům pomalým dotazům pro Azure Database for MySQL](howto-configure-server-logs-in-portal.md)

- [Postup konfigurace a přístup k protokolům auditu pro Azure Database for MySQL](howto-configure-audit-logs-portal.md)

- [Jak nakonfigurovat nastavení diagnostiky pro protokol aktivit Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Monitorování Azure Security Center**: není k dispozici.

**Zodpovědnost**: zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné**materiály: v rámci Azure monitor pro pracovní prostor Log Analytics, který se používá k ukládání protokolů Azure Database for MySQL, nastavte dobu uchování podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

- [Postup nastavení parametrů uchovávání protokolů pro Log Analytics pracovní prostory](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Ukládání protokolů prostředků v Azure Storagem účtu](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů z Azure Database for MySQL instancí pro chování neobvyklé. Pomocí Log Analytics Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo jiného dodavatele SIEM.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Další informace o Log Analytics](../azure-monitor/log-query/get-started-portal.md)

- [Jak provádět vlastní dotazy v Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Pokyny**: povolení rozšířené ochrany před internetovými útoky pro Azure Database for MySQL. Rozšířená ochrana před internetovými útoky detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Kromě toho můžete povolit protokoly serveru a nastavení diagnostiky pro MySQL a odesílat protokoly do pracovního prostoru Log Analytics. Připojte pracovní prostor Log Analytics do Azure Sentinel, protože poskytuje řešení pro automatizované odpovědi na orchestraci zabezpečení (společnosti). To umožňuje vytvořit playbooky (automatizovaná řešení) a použít je k nápravě problémů zabezpečení.

- [Jak povolit rozšířenou ochranu před internetovými útoky pro Azure Database for MySQL (Preview)](howto-database-threat-protection-portal.md)

- [Vysvětlení protokolů serveru pro Azure Database for MySQL](concepts-monitoring.md#server-logs)

- [Jak nakonfigurovat a přistupovat k protokolům pomalým dotazům pro Azure Database for MySQL](howto-configure-server-logs-in-portal.md)

- [Postup konfigurace a přístup k protokolům auditu pro Azure Database for MySQL](howto-configure-audit-logs-portal.md)

- [Jak nakonfigurovat nastavení diagnostiky pro protokol aktivit Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné**materiály: nepoužitelné; Azure Database for MySQL nezpracovává ani nevytváří protokoly související s malwarem.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné**materiály: není k dispozici; Azure Database for MySQL nezpracovává ani nevytváří protokoly související s DNS.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné**materiály: udržování inventáře uživatelských účtů, které mají přístup správce k rovině správy (například Azure Portal) vaší Azure Database for MySQLinstances. Kromě toho udržujte inventarizaci účtů pro správu, které mají přístup k rovině dat (v samotné databázi) vašich Azure Database for MySQL instancí. (Při vytváření serveru MySQL poskytnete přihlašovací údaje pro uživatele správce. Tento správce se dá použít k vytvoření dalších uživatelů MySQL.)

Azure Database for MySQL nepodporuje integrované řízení přístupu založené na rolích, ale můžete vytvořit vlastní role na základě konkrétních možností poskytovatele prostředků.

- [Porozumění vlastním rolím pro předplatné Azure](../role-based-access-control/custom-roles.md) 

- [Principy Azure Database for MySQL operací poskytovatele prostředků](../role-based-access-control/resource-provider-operations.md#microsoftdbformysql)

- [Pochopení správy přístupu pro Azure Database for MySQL](concepts-security.md#access-management)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné**materiály: Azure AD nemá koncept výchozích hesel.

Při vytváření samotného Azure Database for MySQL prostředku vynutí Azure vytvoření administrativního uživatele se silným heslem. Po vytvoření instance MySQL ale můžete použít první účet správce serveru, který jste vytvořili, a vytvořit další uživatele a udělit jim přístup pro správu. Při vytváření těchto účtů se ujistěte, že pro každý účet nakonfigurujete jiné silné heslo.

- [Vytvoření dalších účtů pro Azure Database for MySQL](howto-create-users.md)

- [Postup aktualizace hesla správce](howto-create-manage-server-portal.md#update-admin-password)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné**materiály: vytvořte standardní operační postupy kolem použití vyhrazených účtů pro správu, které mají přístup k vašim instancím Azure Database for MySQL. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

- [Pochopení Azure Security Center identity a přístupu](../security-center/security-center-identity-access.md)

- [Naučte se vytvářet uživatele správců v Azure Database for MySQL](howto-create-users.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné**materiály: přihlášení k Azure Database for MySQL je podporované pomocí uživatelského jména a hesla, které je nakonfigurované přímo v databázi, a také pomocí Azure Active Directory (AD) identity a využití tokenu Azure AD k připojení. Při použití tokenu Azure AD se podporují různé metody, jako je například uživatel Azure AD, skupina Azure AD nebo aplikace Azure AD připojující se k databázi.

Přístup k řídicí rovině pro MySQL je k dispozici samostatně prostřednictvím REST API a podporuje jednotné přihlašování. Pro ověření nastavte hlavičku autorizace pro vaše požadavky na JSON Web Token, které získáte z Azure Active Directory.

- [Použití Azure Active Directory k ověřování pomocí Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

- [Pochopení Azure Database for MySQL REST API](/rest/api/mysql/)

- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné**materiály: povolte Azure Active Directory Multi-Factor Authentication (MFA) a sledujte Azure Security Center doporučení pro správu identit a přístupu. Při použití tokenů Azure AD pro přihlášení k vaší databázi vám to umožní vyžadovat vícefaktorové ověřování pro přihlášení k databázi.

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Použití Azure Active Directory k ověřování pomocí Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Pokyny**: použití pracovních stanic s privilegovaným přístupem (privilegovaným přístupem) s nakonfigurovaným Multi-Factor Authentication (MFA), které jsou nakonfigurovány pro přihlášení a konfiguraci prostředků Azure.

- [Další informace o pracovních stanicích s privilegovaným přístupem](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné**materiály: povolení rozšířené ochrany před internetovými útoky pro Azure Database for MySQL pro generování výstrah pro podezřelou aktivitu.

Kromě toho můžete použít Azure AD Privileged Identity Management (PIM) pro generování protokolů a výstrah, když dojde k podezřelé nebo nebezpečné aktivitě v prostředí.

Pomocí zjišťování rizik Azure AD můžete zobrazit výstrahy a sestavy týkající se rizikového chování uživatelů.

- [Jak nakonfigurovat rozšířenou ochranu před internetovými útoky pro Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [Postup nasazení Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Vysvětlení zjišťování rizik Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu umožněte portálu a Azure Resource Manager přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné**materiály: jako centrální ověřování a systém autorizací použijte Azure Active Directory (AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Pro přihlášení k Azure Database for MySQL se doporučuje používat Azure AD a k připojení využít token Azure AD. Při použití tokenu Azure AD se podporují různé metody, jako je například uživatel Azure AD, skupina Azure AD nebo aplikace Azure AD připojující se k databázi. 

Přihlašovací údaje Azure AD se můžou používat taky pro správu na úrovni roviny správy (například Azure Portal) k řízení účtů správce MySQL.

- [Použití Azure Active Directory k ověřování pomocí Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné**materiály: Přečtěte si protokoly Azure Active Directory, které vám pomůžou zjistit zastaralé účty, které můžou zahrnovat Azure Database for MySQL administrativních rolí. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím, které se dají použít pro přístup k Azure Database for MySQL a přiřazení rolí. Přístup uživatelů by se měl pravidelně kontrolovat, například každých 90 dní, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Pochopení sestav Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné**materiály: povolení nastavení diagnostiky pro Azure Database for MySQL a Azure Active Directory a posílání všech protokolů do log Analyticsho pracovního prostoru. Nakonfigurujte požadovaná upozornění (například neúspěšné pokusy o ověření) v rámci Log Analytics.

- [Jak nakonfigurovat a přistupovat k protokolům pomalým dotazům pro Azure Database for MySQL](/Azure/mysql/howto-configure-server-logs-in-portal)

- [Postup konfigurace a přístup k protokolům auditu pro Azure Database for MySQL](/Azure/mysql/howto-configure-audit-logs-portal)

- [Jak integrovat protokoly aktivit Azure do Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitorování Azure Security Center**: není k dispozici.

**Zodpovědnost**: zákazník

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Doprovodné**materiály: povolení rozšířené ochrany před internetovými útoky pro Azure Database for MySQL pro generování výstrah pro podezřelou aktivitu.

Pomocí funkcí Azure Active Directory Identity Protection a detekce rizik můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce. Můžete povolit automatizované odezvy prostřednictvím služby Azure Sentinel, abyste mohli implementovat reakce na zabezpečení vaší organizace.

K dalšímu zkoumání můžete také ingestovat protokoly do Azure Sentinel.

- [Jak nakonfigurovat rozšířenou ochranu před internetovými útoky pro Azure Database for MySQL](howto-database-threat-protection-portal.md)

- [Přehled Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak zobrazit rizikové přihlašování Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování Azure Security Center**: není k dispozici.

**Zodpovědnost**: zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné**materiály: nepoužitelné; Customer Lockbox ještě není pro Azure Database for MySQL podporovaná.

- [Seznam podporovaných služeb Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné**materiály: pomocí značek pomáhají při sledování Azure Database for MySQLch instancí nebo souvisejících prostředků, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Pomocí kombinace privátních odkazů, koncových bodů služby a/nebo pravidel brány firewall můžete izolovat a omezit síťový přístup k instancím Azure Database for MySQL.

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create)

- [Postup konfigurace privátního odkazu pro Azure Database for MySQL](concepts-data-access-security-private-link.md)

- [Jak vytvořit a spravovat koncové body služby virtuální sítě a pravidla virtuální sítě v Azure Database for MySQL](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

- [Postup konfigurace Azure Database for MySQL pravidel brány firewall](concepts-firewall-rules.md)

**Monitorování Azure Security Center**: není k dispozici.

**Zodpovědnost**: zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Pokyny**: při použití virtuálních počítačů Azure k přístupu k instancím Azure Database for MySQL, využijte možnost privátního propojení, konfigurace sítě MySQL, skupiny zabezpečení sítě a značky služby, aby se zmírnila možnost exfiltraceí dat.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Database for MySQL a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Jak zmírnit exfiltrace dat pro Azure Database for MySQL](concepts-data-access-security-private-link.md#data-exfiltration-prevention)

- [Pochopení ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: Azure Database for MySQL podporuje připojení serveru MySQL ke klientským aplikacím pomocí SSL (Secure SOCKETS Layer) (SSL). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky. V Azure Portal Ujistěte se, že je ve výchozím nastavení povolená možnost vykonat připojení SSL pro všechny vaše Azure Database for MySQL instance.

V současné době je verze TLS podporovaná pro Azure Database for MySQL TLS 1,0, TLS 1,1, TLS 1,2.

- [Postup konfigurace šifrování při přenosu pro Azure Database for MySQL](concepts-ssl-connection-security.md)

**Monitorování Azure Security Center**: není k dispozici.

**Odpovědnost**: sdílená

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné**materiály: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Database for MySQL. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Pochopení ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: není k dispozici.

**Odpovědnost**: sdílená

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: použijte řízení přístupu na základě role Azure (Azure RBAC) k řízení přístupu k Azure Database for MySQL rovině ovládacího prvku (např. Azure Portal). Pro přístup k rovině dat (v samotné databázi) použijte dotazy SQL k vytvoření uživatelů a konfiguraci oprávnění uživatele. Azure RBAC nemá vliv na uživatelská oprávnění v rámci databáze.

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Jak nakonfigurovat přístup uživatelů pomocí SQL pro Azure Database for MySQL](howto-create-users.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Database for MySQL a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Pochopení ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: Služba Azure Database for MySQL používá šifrovací modul ověřený standardem FIPS 140-2 pro šifrování úložiště dat v klidovém provozu. Data včetně záloh se šifrují na disku, s výjimkou dočasných souborů vytvořených při spouštění dotazů. Služba používá algoritmus AES 256-bit, který je součástí šifrování úložiště Azure, a klíče jsou spravované systémem. Šifrování úložiště je vždycky aktivní a není možné ho zakázat.

Šifrování dat pomocí klíčů spravovaných zákazníkem pro službu Azure Database for MySQL umožňuje šifrovat neaktivní uložená data s použitím vlastního klíče. V tuto chvíli musíte požádat o přístup k používání této možnosti. Chcete-li to provést, obraťte se na:

AskAzureDBforMySQL@service.microsoft.com

- [Vysvětlení šifrování v klidovém umístění pro Azure Database for MySQL](concepts-security.md)

- [Postup konfigurace klíčů spravovaných zákazníkem pro Azure Database for MySQL](concepts-data-encryption-mysql.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné**materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají v produkčních instancích Azure Database for MySQL a dalších důležitých nebo souvisejících prostředcích.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné**materiály: dodržujte doporučení od Azure Security Center o zabezpečení Azure Database for MySQL a souvisejících prostředků.

Microsoft provádí správu ohrožení zabezpečení v základních systémech, které podporují Azure Database for MySQL.

- [Pochopení Azure Security Center doporučení](../security-center/recommendations-reference.md)

- [Pokrytí funkcí pro služby Azure PaaS v Azure Security Center](../security-center/features-paas.md)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné**materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizovaného řešení pro správu oprav pro softwarové tituly třetích stran

**Doprovodné**materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné**materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Pokyny**: Společnost Microsoft provádí správu ohrožení zabezpečení v základních systémech, které podporují Azure Database for MySQL.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny prostředky (včetně instancí Azure Database for MySQL) v rámci vašich předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné**materiály: použití značek pro Azure Database for MySQL instancí a dalších souvisejících prostředků, které dávají metadata k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné**materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování Azure Database for MySQLch instancí a souvisejících prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create)

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Kromě toho se pomocí grafu prostředků Azure Dotazujte na prostředky v rámci předplatných a vyhledejte je.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management. To může zabránit vytváření a změnám prostředků v prostředí s vysokým zabezpečením, jako jsou například instance Azure Database for MySQL obsahující citlivé údaje.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure Database for MySQL instance pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů **Microsoft. DBforMySQL** můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich Azure Database for MySQLch instancí. Můžete také využít integrované definice zásad související s vašimi Azure Database for MySQL instancemi, například:

Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.

- [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné**materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné**materiály: Pokud používáte vlastní definice Azure Policy pro vaše Azure Database for MySQL instance a související prostředky, použijte Azure Repos k bezpečnému ukládání a správě kódu.

- [Jak v Azure DevOps ukládat kód](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentace k Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. DBforMySQL můžete vytvořit vlastní zásady pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: použijte aliasy Azure Policy v oboru názvů **Microsoft. DBforMySQL** k vytvoření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. K automatickému vymáhání konfigurací pro vaše Azure Database for MySQL instance a související prostředky použijte Azure Policy [audit], [Deny] a [nasazení, pokud neexistuje].

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné**materiály: u Azure Virtual Machines nebo webových aplikací běžících na Azure App Service se používá pro přístup k instancím Azure Database for MySQL použijte identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů Azure Database for MySQL. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

- [Integrace se spravovanými identitami Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Vytvoření Key Vault](../key-vault/general/quick-create-portal.md)

- [Jak zajistit Key Vault ověřování pomocí spravované identity](/azure/key-vault/general/managed-identity)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné**materiály: instance Azure Database for MySQL podporuje Azure Active Directory ověřování pro přístup k databázím.  Při vytváření instance Azure Database for MySQL poskytnete přihlašovací údaje pro uživatele správce. Tento správce se dá použít k vytvoření dalších uživatelů databáze.  

V případě Azure Virtual Machines nebo webových aplikací běžících na Azure App Service se používá pro přístup k instancím Azure Database for MySQL použijte Identita spravované služby ve spojení s Azure Key Vault k ukládání a načítání přihlašovacích údajů pro Azure Database for MySQL instance. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou v Azure Active Directory (AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

- [Postup konfigurace spravovaných identit](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Integrace se spravovanými identitami Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použijte centrálně spravovaný antimalwarový software

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Database for SQL), ale neběží na zákaznickém obsahu.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure (například Azure Database for MySQL), ale neběží na zákaznickém obsahu.

Předem prohledejte veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, jako jsou App Service, Data Lake Storage, Blob Storage, Azure Database for MySQL atd. Společnost Microsoft nemá přístup k vašim datům v těchto instancích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Database for MySQL), ale neběží na zákaznickém obsahu.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: není k dispozici

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné**materiály: Azure Database for MySQL přebírají zálohy datových souborů a transakčního protokolu. V závislosti na podporované maximální velikosti úložiště vezmeme úplné a rozdílové zálohy (4 TB max. servery úložiště) nebo zálohy snímků (až 16 TB maximálních úložných serverů). Tyto zálohy umožňují obnovit server k jakémukoli časovému okamžiku v rámci nakonfigurované doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Volitelně je můžete nakonfigurovat až 35 dní. Všechny zálohy se šifrují s využitím 256bitového šifrování AES.

- [Principy zálohování pro Azure Database for MySQL](concepts-backup.md)

- [Principy počáteční konfigurace Azure Database for MySQL](tutorial-design-database-using-portal.md)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné**materiály: Azure Database for MySQL automaticky vytvoří zálohy serveru a uloží je do místně redundantního nebo geograficky redundantního úložiště v závislosti na volbě uživatele. Zálohy lze použít k obnovení serveru do určitého bodu v čase. Zálohování a obnovení jsou důležitou součástí jakékoli strategie pro provozní kontinuitu, protože chrání vaše data před náhodným poškozením nebo odstraněním. 

Pokud chcete ukládat přihlašovací údaje pro instance Azure Database for MySQL pomocí Azure Key Vault, zajistěte pravidelné automatizované zálohování vašich klíčů. 

- [Principy zálohování pro Azure Database for MySQL](howto-restore-server-portal.md) 

- [Postup zálohování klíčů Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné**materiály: v Azure Database for MySQL obnovení vytvoří nový server ze zálohy původního serveru. K dispozici jsou dva typy obnovení: obnovení k bodu v čase a geografické obnovení. Obnovení k určitému bodu v čase je dostupné s možností redundance zálohy a vytvoří nový server ve stejné oblasti jako původní server. Geografické obnovení je k dispozici pouze v případě, že jste server nakonfigurovali pro geograficky redundantní úložiště a máte možnost obnovit server do jiné oblasti.

Odhadovaná doba obnovení závisí na několika faktorech, včetně velikostí databází, velikosti transakčního protokolu, šířky pásma sítě a celkového počtu databází obnovování ve stejné oblasti ve stejnou dobu. Doba obnovení je obvykle méně než 12 hodin.

Pravidelně testujte obnovení instancí Azure Database for MySQL.

- [Principy zálohování a obnovení v Azure Database for MySQL](concepts-backup.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné**materiály: Azure Database for MySQL přebírá úplné a rozdílové zálohy a zálohování protokolů transakcí. Tyto zálohy umožňují obnovit server k jakémukoli časovému okamžiku v rámci nakonfigurované doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Volitelně je můžete nakonfigurovat až 35 dní. Všechny zálohy se šifrují s využitím 256bitového šifrování AES. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

- [Principy zálohování a obnovení v Azure Database for MySQL](concepts-backup.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: Vytvoření Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů z detekce až po přezkoumání po jednotlivých událostech.

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné**materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné**materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné**materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana.  Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné**materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat ověřovací data výstrah.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Jak streamovat výstrahy do Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné**materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné**materiály: Využijte pravidla zapojení Microsoftu, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [V této části najdete další informace o strategii Microsoftu a provádění testování v rámci červeného seskupování a testování průniku na cloudové infrastruktuře, služby a aplikace spravované Microsoftem.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](/azure/security/benchmarks/overview)
- Další informace o [plánech zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
