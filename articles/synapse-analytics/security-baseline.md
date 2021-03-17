---
title: Základní hodnoty zabezpečení Azure pro Azure synapse Analytics
description: Základní hodnoty zabezpečení synapse Analytics poskytují pokyny a prostředky pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: synapse-analytics
ms.subservice: security
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9831f70a88aba497eb7d6a759233c3d7d7be62c6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585124"
---
# <a name="azure-security-baseline-for-azure-synapse-analytics"></a>Základní hodnoty zabezpečení Azure pro Azure synapse Analytics

Základní plán zabezpečení Azure pro Azure synapse Analytics obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné** materiály: zabezpečení SQL Server Azure pro virtuální síť prostřednictvím privátního propojení. Privátní odkaz Azure umožňuje přístup ke službám Azure PaaS přes privátní koncový bod ve vaší virtuální síti. Provoz mezi vaší virtuální sítí a službou přenáší páteřní síť Microsoftu.

Případně můžete při připojování ke synapse fondu SQL zúžit rozsah odchozího připojení k databázi SQL pomocí skupiny zabezpečení sítě. Nastavením povolit službám Azure na OFF zakažte všechny přenosy služeb Azure do databáze SQL prostřednictvím veřejného koncového bodu. Ujistěte se, že nejsou v pravidlech brány firewall povolená žádná veřejná IP adresa.

* [Principy privátního odkazu Azure](../private-link/private-link-overview.md)

* [Principy privátního odkazu pro Azure synapse SQL](../azure-sql/database/private-endpoint-overview.md)

* [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

* [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné** materiály: když se připojujete k vašemu rezervovanému fondu SQL a máte Povolené protokoly toku NSG (Network Security Group), protokoly se odesílají do účtu Azure Storage pro auditování provozu.

Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

* [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

* [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

* [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro služby Azure apps nebo výpočetní prostředky hostující webové aplikace.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: použití rozšířené ochrany před internetovými útoky (ATP) pro Azure synapse SQL. ATP detekuje aktivity neobvyklé indikující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití a může aktivovat různé výstrahy, jako je například "potenciální injektáže SQL," a "přístup z neobvyklého umístění". ATP je součástí nabídky rozhraní ADS (Advanced Data Security) a dá se k nim přistupovat a spravovat prostřednictvím centrálního portálu SQL ADS.

Povolit DDoS Protection standard pro virtuální sítě přidružené k Azure synapse SQL pro ochranu před distribuovanými útoky DoS (Denial of Service). K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

* [Principy ATP pro Azure synapse SQL](../azure-sql/database/threat-detection-overview.md)

* [Jak povolit pokročilou zabezpečení dat pro Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

* [Přehled reklam](../azure-sql/database/azure-defender-for-sql.md)

* [Jak nakonfigurovat DDoS Protection](../ddos-protection/manage-ddos-protection.md)

* [Pochopení Azure Security Center integrované analýzy hrozeb](../security-center/azure-defender.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Pokyny**: když se připojujete k vašemu rezervovanému fondu SQL a máte Povolené protokoly toku NSG (Network Security Group), odešlete protokoly do účtu Azure Storage pro auditování provozu. Protokoly toku můžete také odesílat do Log Analyticsho pracovního prostoru nebo je streamovat do Event Hubs. Pokud je to potřeba pro prošetření aktivity neobvyklé, povolte zachytávání paketů Network Watcher.

* [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Postup povolení Network Watcher](../network-watcher/network-watcher-create.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: použití rozšířené ochrany před internetovými útoky (ATP) pro Azure synapse SQL. ATP detekuje aktivity neobvyklé indikující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití a může aktivovat různé výstrahy, jako je například "potenciální injektáže SQL," a "přístup z neobvyklého umístění". ATP je součástí nabídky rozhraní ADS (Advanced Data Security) a dá se k nim přistupovat a spravovat prostřednictvím centrálního portálu SQL ADS. ATP také integruje výstrahy s Azure Security Center.

* [Principy ATP pro Azure synapse SQL](../azure-sql/database/threat-detection-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro služby Azure apps nebo výpočetní prostředky hostující webové aplikace.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: pomocí značek služby virtuální sítě můžete definovat řízení přístupu k síti pro skupiny zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Při použití koncového bodu služby pro vyhrazený fond SQL se vyžaduje odchozí IP adresy pro Azure SQL Database: musí být otevřené skupiny zabezpečení sítě (skupin zabezpečení sítě), aby se mohly Azure SQL Database IP adresy, aby bylo možné připojení. To můžete provést pomocí značek služby NSG pro Azure SQL Database.

* [Pochopení značek služeb pomocí koncových bodů služby pro Azure SQL Database](../azure-sql/database/vnet-service-endpoint-rule-overview.md#limitations)

* [Pochopení a používání značek služeb](../virtual-network/service-tags-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace konfigurací zabezpečení sítě pro prostředky související s vaším vyhrazeným fondem SQL pomocí Azure Policy. Obor názvů Microsoft. SQL můžete použít k definování vlastních definic zásad nebo k používání kterékoli z vestavěných definic zásad, které jsou určené pro ochranu Azure SQL Database/Server Network. Příkladem příslušných integrovaných zásad zabezpečení sítě pro Azure SQL Database Server by byl: "SQL Server by měl používat koncový bod služby virtuální sítě".

Pomocí Azure modrotisky můžete zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou šablony pro správu prostředků Azure, řízení přístupu na základě role Azure (Azure RBAC) a zásady, a to v jediné definici podrobného plánu. Podrobné sestavování můžete snadno použít pro nová předplatná a prostředí a vyladit řízení a správu prostřednictvím správy verzí.

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Pokyny**: používejte značky pro skupiny zabezpečení sítě (NSG) a další zdroje informací týkající se zabezpečení sítě a toku provozu. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vaším vyhrazeným fondem SQL. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

* [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Vytváření výstrah v Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Doprovodné** materiály: Microsoft udržuje časové zdroje pro prostředky Azure. Můžete aktualizovat synchronizaci času pro vaše nasazení výpočtů.

* [Jak nakonfigurovat časovou synchronizaci pro výpočetní prostředky Azure](../virtual-machines/windows/time-sync.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: zásady auditu je možné definovat pro určitou databázi nebo jako výchozí zásady serveru v Azure (které hostují Azure synapse). Zásady serveru se vztahují na všechny existující a nově vytvořené databáze na serveru.

Pokud je auditování serveru povoleno, bude vždy použito pro databázi. Databáze bude auditována bez ohledu na nastavení auditování databáze.

Pokud povolíte auditování, můžete je zapsat do protokolu auditu v účtu Azure Storage, Log Analytics pracovním prostoru nebo Event Hubs.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

* [Nastavení auditování pro prostředky SQL Azure](../azure-sql/database/auditing-overview.md#server-vs-database-level)

* [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: Povolte auditování na úrovni serveru SQL Azure pro vyhrazený fond SQL a vyberte umístění úložiště pro protokoly auditu (Azure Storage, Log Analytics nebo Event Hubs).

Auditování lze povolit na úrovni databáze nebo serveru a je navrženo, aby bylo povoleno na úrovni serveru pouze v případě, že pro určitou databázi nevyžadujete konfiguraci samostatné datové jímky nebo uchování.

* [Postup povolení auditování pro Azure SQL Database](../azure-sql/database/auditing-overview.md)

* [Jak povolit auditování pro server](../azure-sql/database/auditing-overview.md#setup-auditing)

* [Rozdíly v zásadách auditování na úrovni serveru oproti databázi](../azure-sql/database/auditing-overview.md#server-vs-database-level)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: nepoužitelné; Tento srovnávací test je určený pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: při ukládání protokolů souvisejících s vaším vyhrazeným fondem SQL v účtu úložiště, Log Analytics pracovním prostoru nebo centra událostí nastavte dobu uchování protokolu podle předpisů pro dodržování předpisů vaší organizace.

* [Správa životního cyklu úložiště objektů blob v Azure](../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)

* [Postup nastavení parametrů uchovávání protokolů v pracovním prostoru Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

* [Zachytávání událostí streamování v Event Hubs](../event-hubs/event-hubs-capture-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů pro chování neobvyklé a pravidelné kontroly výsledků. Pomocí rozšířené ochrany před internetovými útoky pro Azure SQL Database ve spojení s Azure Security Center upozorní na neobvyklé aktivity související s vaší databází SQL. Případně můžete nakonfigurovat výstrahy na základě hodnot metrik nebo položek protokolu aktivit Azure, které se týkají vaší databáze SQL.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

* [Pochopení rozšířené ochrany před internetovými útoky a upozorňování na Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

* [Jak povolit pokročilou zabezpečení dat pro Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

* [Jak nakonfigurovat vlastní výstrahy pro Azure SQL Database](../azure-sql/database/alerts-insights-configure-portal.md?preserve-view=true&view=azps-1.4.0)

* [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: použití rozšířené ochrany před internetovými útoky (ATP) pro Azure SQL Database ve spojení s Azure Security Center k monitorování a upozornění na aktivitu neobvyklé. ATP je součástí nabídky pokročilého zabezpečení dat (ADS) a dá se k nim přistupovat a spravovat prostřednictvím centrálních reklam SQL na portálu. Služby ADS obsahují funkce pro zjišťování a klasifikaci citlivých dat, zpřístupněníí a zmírnění potenciálních ohrožení zabezpečení databáze a zjišťují se neobvyklé aktivity, které by mohly znamenat hrozbu pro vaši databázi.

Alternativně můžete povolit a začlenit data do Azure Sentinel.

* [Pochopení rozšířené ochrany před internetovými útoky a upozorňování na Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

* [Jak povolit pokročilou zabezpečení dat pro Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

* [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

* [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: nepoužitelné; pro prostředky, které souvisejí s vaším vyhrazeným fondem SQL, je řešení proti malwaru spravované Microsoftem na podkladové platformě.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné** materiály: nepoužitelné; prostředky, které souvisejí s vaším vyhrazeným fondem SQL, nejsou vytvářeny pomocí žádného protokolu DNS.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné** materiály: nepoužitelné; auditování příkazového řádku se nedá použít pro Azure synapse SQL.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: uživatelé se ověřují buď pomocí Azure Active Directory nebo ověřování SQL.

Při prvním nasazení Azure SQL zadejte přihlašovací jméno správce a přidružené heslo pro toto přihlášení. Tento účet správce se nazývá správce serveru. Účty správců pro databázi můžete identifikovat tak, že otevřete Azure Portal a přejdete na kartu vlastnosti vašeho serveru nebo spravované instance. Můžete také nakonfigurovat účet správce Azure AD s úplnými oprávněními pro správu, který je nutný, pokud chcete povolit Azure Active Directory ověřování.

Pro operace správy použijte předdefinované role Azure, které se musí explicitně přiřadit. Pomocí modulu Azure AD PowerShell můžete provádět dotazy ad hoc a zjišťovat účty, které jsou členy skupin pro správu.

* [Ověřování pro SQL Database](../azure-sql/database/security-overview.md#authentication)

* [Vytváření účtů pro uživatele bez oprávnění správce](../azure-sql/database/logins-create-manage.md#create-accounts-for-non-administrator-users)

* [Použití účtu Azure Active Directory k ověřování](../azure-sql/database/logins-create-manage.md#create-additional-logins-and-users-having-administrative-permissions)

* [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

* [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

* [Správa stávajících přihlašovacích údajů a účtů správců v Azure SQL](../azure-sql/database/logins-create-manage.md#existing-logins-and-user-accounts-after-creating-a-new-database)

* [Předdefinované role Azure](../role-based-access-control/built-in-roles.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné** materiály: Azure Active Directory nemá koncept výchozích hesel. Při zřizování vyhrazeného fondu SQL doporučujeme, abyste se rozhodli o integraci ověřování s Azure Active Directory. Pomocí této metody ověřování uživatel odesílá název uživatelského účtu a požaduje, aby služba používala informace přihlašovacích údajů uložené v Azure Active Directory (Azure AD).

* [Jak nakonfigurovat a spravovat Azure Active Directory ověřování pomocí Azure SQL](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#active-directory-password-authentication)

* [Principy ověřování v Azure SQL](../azure-sql/database/logins-create-manage.md#existing-logins-and-user-accounts-after-creating-a-new-database)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: Vytvořte zásady a postupy týkající se používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu, které se přihlásí prostřednictvím Azure Active Directory.

Chcete-li identifikovat účty správců pro databázi, otevřete Azure Portal a přejděte na kartu Vlastnosti serveru nebo spravované instance.

* [Pochopení Azure Security Center identity a přístupu](../security-center/security-center-identity-access.md)

* [Správa stávajících přihlašovacích údajů a účtů správců v Azure SQL](../azure-sql/database/logins-create-manage.md#existing-logins-and-user-accounts-after-creating-a-new-database)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Pokyny**: použijte registraci aplikace Azure (instanční objekt) k získání tokenu, který se dá použít k interakci s datovým skladem na řídicí rovině (Azure Portal) prostřednictvím volání rozhraní API.

* [Volání rozhraní Azure REST API](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Postup registrace klientské aplikace (instančního objektu) ve službě Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Informace o REST API SQL pro Azure synapse](./sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte Azure Active Directory (AD) Multi-Factor Authentication (MFA) a sledujte Azure Security Center doporučení pro správu identit a přístupu.

* [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

* [Pochopení vícefaktorového ověřování v Azure SQL](../azure-sql/database/authentication-mfa-ssms-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Pokyny**: použití pracovní stanice s privilegovaným přístupem (privilegovaným přístupem) s konfigurací Multi-Factor Authentication (MFA) nakonfigurovanou pro přihlášení a konfiguraci prostředků Azure.

* [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použití sestav zabezpečení Azure Active Directory pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě.

Použití rozšířené ochrany před internetovými útoky pro Azure SQL Database ve spojení s Azure Security Center k detekci a upozornění na aktivity neobvyklé indikující neobvyklé a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

SQL Server Audit vám umožní vytvořit audity serveru, které můžou obsahovat specifikace auditu serveru pro události na úrovni serveru a specifikace auditu databáze pro události na úrovni databáze. Auditované události lze zapsat do protokolů událostí nebo auditovat soubory.

* [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](../active-directory/identity-protection/overview-identity-protection.md)

* [Jak monitorovat aktivitu identity uživatelů a přístupu v Azure Security Center](../security-center/security-center-identity-access.md)

* [Kontrola rozšířené ochrany před internetovými útoky a potenciálních výstrah](../azure-sql/database/threat-detection-overview.md#alerts)

* [Pochopení přihlašovacích údajů a uživatelských účtů v Azure SQL](../azure-sql/database/logins-create-manage.md)

* [Pochopení SQL Server auditování](/sql/relational-databases/security/auditing/sql-server-audit-database-engine?preserve-view=true&view=sql-server-ver15)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolte přístup k portálu a správě prostředků Azure jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

* [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: vytvořte správce Azure Active Directory (AD) pro server Azure SQL Database ve vyhrazeném fondu SQL.

* [Jak nakonfigurovat a spravovat ověřování Azure AD pomocí Azure SQL](../azure-sql/database/authentication-aad-configure.md)

* [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Kromě toho můžete pomocí Azure Active Directory kontroly přístupu efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

Při použití ověřování SQL vytvořte v databázi uživatele databáze s omezením. Zajistěte, aby jeden nebo více uživatelů databáze umístili do vlastní databázové role s konkrétními oprávněními, která jsou vhodná pro danou skupinu uživatelů.

* [Jak používat kontroly přístupu](../active-directory/governance/access-reviews-overview.md)

* [Pochopení přihlašovacích údajů a uživatelských účtů v Azure SQL](../azure-sql/database/logins-create-manage.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Pokyny**: konfigurace ověřování Azure Active Directory (AD) pomocí Azure SQL a povolení nastavení diagnostiky pro Azure Active Directory uživatelské účty, odesílání protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. Nakonfigurujte požadované výstrahy v rámci Log Analytics.

Při použití ověřování SQL vytvořte v databázi uživatele databáze s omezením. Zajistěte, aby jeden nebo více uživatelů databáze umístili do vlastní databázové role s konkrétními oprávněními, která jsou vhodná pro danou skupinu uživatelů.

* [Jak používat kontroly přístupu](../active-directory/governance/access-reviews-overview.md)

* [Jak nakonfigurovat a spravovat ověřování Azure AD pomocí Azure SQL Database](../azure-sql/database/authentication-aad-configure.md)

* [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Pochopení přihlašovacích údajů a uživatelských účtů v Azure SQL](../azure-sql/database/logins-create-manage.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Pokyny**: pomocí funkcí Azure Active Directory (Azure AD) Identity Protection a detekce rizik můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce související s identitami uživatelů. Kromě toho můžete k dalšímu zkoumání připojit data do Azure Sentinel a ingestovat.

Při použití ověřování SQL vytvořte v databázi uživatele databáze s omezením. Zajistěte, aby jeden nebo více uživatelů databáze umístili do vlastní databázové role s konkrétními oprávněními, která jsou vhodná pro danou skupinu uživatelů.

* [Jak zobrazit přihlášení rizik Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Postup zprovoznění služby Azure Sentinel](../sentinel/connect-data-sources.md)

* [Pochopení přihlašovacích údajů a uživatelských účtů v Azure SQL](../azure-sql/database/logins-create-manage.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: ve scénářích podpory, kde Microsoft potřebuje získat přístup k datům souvisejícím s Azure SQL Database ve vašem vyhrazeném fondu SQL, Azure Customer Lockbox poskytuje rozhraní pro kontrolu a schválení nebo zamítnutí požadavků na přístup k datům.

* [Pochopení Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

Klasifikace zjišťování dat &amp; je integrovaná do Azure synapse SQL. Poskytuje pokročilé možnosti pro zjišťování, klasifikaci, označování a oznamování citlivých dat ve vašich databázích.

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

* [Vysvětlení klasifikace zjišťování dat &amp;](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Prostředky by měly být oddělené virtuální sítí nebo podsítí, vhodně označené a zabezpečené v rámci skupiny zabezpečení sítě nebo Azure Firewall. Prostředky, které ukládají nebo zpracovávají citlivá data, by měly být izolované. Použít privátní odkaz; Nasaďte Azure SQL Server do virtuální sítě a bezpečně se připojte pomocí privátního odkazu.

* [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

* [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

* [Jak nastavit privátní odkaz pro Azure SQL Database](../azure-sql/database/private-endpoint-overview.md#how-to-set-up-private-link-for-azure-sql-database)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: pro všechny Azure SQL Database ve vyhrazeném fondu SQL, které ukládají nebo zpracovávají citlivé informace, označte databázi a související prostředky jako citlivé pomocí značek. Nakonfigurujte privátní propojení ve spojení s značkami služby skupiny zabezpečení sítě (NSG) na vašich instancích Azure SQL Database, abyste zabránili exfiltrace citlivých informací.

Rozšířená ochrana před internetovými útoky pro Azure SQL Database, spravovaná instance Azure SQL a Azure synapse detekuje aktivity neobvyklé, které naznačují neobvyklé a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

* [Jak nakonfigurovat privátní linku a skupin zabezpečení sítě, aby se zabránilo exfiltraceí dat na instancích Azure SQL Database](../azure-sql/database/private-endpoint-overview.md)

* [Pochopení rozšířené ochrany před internetovými útoky pro Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

* [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: Azure SQL Database zabezpečení vašich dat šifrováním dat při pohybu pomocí Transport Layer Security. SQL Server pro všechna připojení vynutila šifrování (SSL/TLS). Tím se zajistí, že všechna data budou zašifrovaná mezi klientem a serverem, a to bez ohledu na nastavení šifrování nebo TrustServerCertificate v připojovacím řetězci.

* [Pochopení šifrování Azure SQL při přenosu](../azure-sql/database/security-overview.md#information-protection-and-encryption)

**Monitorování služby Azure Security Center:** Nelze použít

**Zodpovědnost**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Pokyny**: použijte funkci klasifikace SQL data Discovery pro Azure synapse &amp; . Klasifikace zjišťování dat &amp; poskytuje pokročilé funkce integrované v Azure SQL Database pro zjišťování, klasifikaci a označování &amp; ochrany citlivých dat ve vašich databázích.

Klasifikace zjišťování dat &amp; je součástí rozšířené nabídky zabezpečení dat, což je jednotný balíček pro pokročilé funkce zabezpečení SQL. Klasifikace zjišťování dat &amp; se dá použít a spravovat prostřednictvím portálu centrálních SQL ADS.

Kromě toho můžete v Azure Portal nastavit zásadu dynamického maskování dat (DDM). Modul doporučení DDM označuje určitá pole z vaší databáze jako potenciálně citlivá pole, která mohou být vhodná pro maskování.

* [Použití zjišťování a klasifikace dat pro Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

* [Principy dynamického maskování dat pro Azure synapse SQL](../azure-sql/database/dynamic-data-masking-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: pomocí řízení přístupu na základě role Azure (Azure RBAC) můžete spravovat přístup k databázím SQL Azure ve vyhrazeném fondu SQL.

Autorizaci řídí členství v databázové roli vašeho uživatelského účtu a oprávnění na úrovni objektů. Doporučený postup je udělit uživatelům co nejmenší možná oprávnění.

* [Jak integrovat SQL Server Azure s Azure Active Directory k ověřování](../azure-sql/database/authentication-aad-overview.md)

* [Jak řídit přístup v Azure SQL Server](../azure-sql/database/logins-create-manage.md)

* [Vysvětlení autorizace a ověřování v Azure SQL](../azure-sql/database/logins-create-manage.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: nepoužitelné; Microsoft spravuje základní infrastrukturu pro Azure synapse SQL a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

* [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost**: netýká se

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: transparentní šifrování dat (TDE) pomáhá chránit Azure synapse SQL před hrozbou škodlivosti offline aktivity šifrováním dat v klidovém stavu. Šifruje a dešifruje databáze, související zálohy a soubory transakčních protokolů v reálném čase, a přitom nevyžaduje změny v aplikaci. V Azure je výchozím nastavením pro TDE, že klíč DEK je chráněn integrovaným certifikátem serveru. Alternativně můžete použít TDE spravované zákazníky, označované také jako podpora služby Bring Your Own Key (BYOK) pro TDE. V tomto scénáři TDE ochrana šifrování klíč DEK je asymetrický klíč spravovaný zákazníkem, který je uložený ve Azure Key Vault ve vlastnictví a spravovaném zákazníkovi (cloudový externí systém správy klíčů Azure) a nikdy neopouští Trezor klíčů.

* [Pochopení transparentního šifrování dat spravovaného službou](../azure-sql/database/transparent-data-encryption-tde-overview.md?tabs=azure-portal)

* [Pochopení transparentního šifrování dat spravovaného zákazníkem](../azure-sql/database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

* [Jak zapnout TDE pomocí vlastního klíče](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají do produkčních instancí fondů SQL synapse a dalších důležitých nebo souvisejících prostředků.

Kromě toho můžete nastavit výstrahy pro databáze ve fondu SQL synapse pomocí Azure Portal. Když některá metrika (například velikost databáze nebo využití procesoru) dosáhne prahové hodnoty, můžou vám výstrahy poslat e-mail nebo zavolat webový Hook.

* [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/alerts/alerts-activity-log.md)

* [Vytvoření upozornění pro Azure SQL synapse](../azure-sql/database/alerts-insights-configure-portal.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](../security/benchmarks/security-control-vulnerability-management.md)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: povolení pokročilých zabezpečení dat a sledování doporučení od Azure Security Center při provádění posouzení ohrožení zabezpečení vašich databází SQL Azure.

* [Jak spustit posuzování ohrožení zabezpečení ve vašich databázích SQL Azure](../azure-sql/database/sql-vulnerability-assessment.md)

* [Jak povolit pokročilé zabezpečení dat](../azure-sql/database/azure-defender-for-sql.md)

* [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizovaného řešení pro správu oprav pro softwarové tituly třetích stran

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Pokyny**: posouzení ohrožení zabezpečení je skenovací služba integrovaná do Azure synapse SQL. Služba využívá znalostní bázi pravidel, která označují bezpečnostní hrozby. Zvýrazňuje odchylky od osvědčených postupů, jako jsou například nepřesné konfigurace, nadměrné oprávnění a nechráněná citlivá data. K posouzení ohrožení zabezpečení lze využít a spravovat prostřednictvím portálu centrálních zabezpečení SQL Advanced Data Security (ADS).

* [Správa a export kontrol posouzení ohrožení zabezpečení na portálu SQL ADS](../azure-sql/database/sql-vulnerability-assessment.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: použijte výchozí hodnocení rizik (zabezpečené skóre) poskytované Azure Security Center.

Klasifikace zjišťování dat &amp; je integrovaná do Azure synapse SQL. Poskytuje pokročilé možnosti pro zjišťování, klasifikaci, označování a oznamování citlivých dat ve vašich databázích.

* [Pochopení Azure Security Center zabezpečeného skóre](../security-center/secure-score-security-controls.md)

* [Vysvětlení klasifikace zjišťování dat &amp;](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny prostředky, které se vztahují k vašemu rezervovanému fondu SQL v rámci vašich předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

I když je možné zjistit klasické prostředky Azure prostřednictvím Azure Resource graphu, důrazně se doporučuje vytvořit a použít prostředky Azure Resource Manager, které budou předány.

* [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

* [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-3.0.0)

* [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

* [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

* [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

* [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Pokyny**: Definujte seznam schválených prostředků Azure, které souvisejí s vaším vyhrazeným fondem SQL.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat nebo zjišťovat prostředky v rámci předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: pomocí Azure Policy můžete umístit omezení typu prostředků, které se dají vytvořit v předplatných zákazníka, a to pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat nebo zjišťovat prostředky v rámci předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/index.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro aplikace běžící na výpočetních prostředcích.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací "blokování přístupu" pro aplikaci Microsoft Azure Management.

* [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: libovolný prostředek, který souvisí s vaším vyhrazeným fondem SQL, který je vyžadován pro obchodní operace, ale může být pro organizaci vyšší riziko, měl by být izolovaný ve svém vlastním virtuálním počítači nebo virtuální síti a dostatečně zabezpečený buď pomocí Azure firewall nebo skupiny zabezpečení sítě.

* [Jak vytvořit virtuální síť](../virtual-network/quick-create-portal.md)

* [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: použijte aliasy Azure Policy v oboru názvů Microsoft. SQL k vytvoření vlastních zásad pro auditování nebo vystavování konfigurace prostředků souvisejících s vaším vyhrazeným fondem SQL. Můžete také využít integrované definice zásad pro Azure Database/Server, například:
- Nasazení detekce hrozeb na SQL serverech
- SQL Server by měl používat koncový bod služby virtuální sítě

* [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-3.3.0)

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.

* [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Pokud používáte vlastní definice Azure Policy, použijte k bezpečnému ukládání a správě kódu službu Azure DevOps nebo Azure Repos.

* [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

* [Dokumentace k Azure Repos](/azure/devops/repos/index?preserve-view=true&view=azure-devops)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: nepoužitelné; Azure synapse SQL nemá konfigurovatelné nastavení zabezpečení.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: využijte Azure Security Center k provádění kontrol standardních hodnot pro všechny prostředky, které souvisejí s vaším vyhrazeným fondem SQL.

* [Jak opravit doporučení v Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Pokyny**: transparentní šifrování dat (TDE) pomocí klíčů spravovaných zákazníkem v Azure Key Vault umožňuje šifrování automaticky generovaného šifrovacího klíče databáze (klíč DEK) pomocí asymetrického klíče spravovaného zákazníkem, který se nazývá TDE Protector. To se také obecně označuje jako podpora Bring Your Own Key (BYOK) pro transparentní šifrování dat. Ve scénáři BYOK se ochrana TDE ukládá do Azure Key Vault ve vlastnictví a spravovaném zákazníkem. Kromě toho se ujistěte, že je v Azure Key Vault povolené obnovitelné odstranění.

* [Jak povolit TDE s klíčem spravovaným zákazníkem z Azure Key Vault](../azure-sql/database/transparent-data-encryption-byok-configure.md?tabs=azure-powershell)

* [Jak povolit obnovitelné odstranění v Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: použití spravovaných identit k poskytování služeb Azure s automaticky spravovanou identitou v Azure Active Directory (AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Azure Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

* [Kurz: Použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure SQL](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

* [Postup konfigurace spravovaných identit](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

* [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použijte centrálně spravovaný antimalwarový software

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky. Microsoft zpracovává anti-malware pro základní platformu.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure (například Azure synapse SQL); neběží ale na zákaznickém obsahu.

Předem prohledejte veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, jako jsou App Service, Data Lake Storage, Blob Storage, Azure SQL Server atd. Společnost Microsoft nemá přístup k vašim datům v těchto instancích.

* [Pochopení Microsoft antimalwaru pro Azure Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky. Microsoft zpracovává anti-malware pro základní platformu.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: snímky vyhrazeného fondu SQL se automaticky provedou během dne vytváření bodů obnovení, které jsou k dispozici po dobu sedmi dnů. Tuto dobu uchování nelze změnit. Vyhrazený fond SQL podporuje osm hodin cíl bodu obnovení (RPO). Datový sklad můžete obnovit v primární oblasti z libovolného snímku, který jste provedli během posledních sedmi dnů. Všimněte si, že v případě potřeby můžete také ručně aktivovat snímky.

* [Zálohování a obnovení ve vyhrazeném fondu SQL](./sql-data-warehouse/backup-and-restore.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné** materiály: snímky datového skladu se automaticky provedou během dne vytváření bodů obnovení, které jsou k dispozici po dobu sedmi dnů. Tuto dobu uchování nelze změnit. Vyhrazený fond SQL podporuje osm hodin cíl bodu obnovení (RPO). Datový sklad můžete obnovit v primární oblasti z libovolného snímku, který jste provedli během posledních sedmi dnů. Všimněte si, že v případě potřeby můžete také ručně aktivovat snímky.

Pokud k šifrování šifrovacího klíče databáze používáte klíč spravovaný zákazníkem, ujistěte se, že je váš klíč zálohovaný.

* [Zálohování a obnovení ve vyhrazeném fondu SQL](./sql-data-warehouse/backup-and-restore.md)

* [Postup zálohování klíčů Azure Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?preserve-view=true&view=azurermps-6.13.0)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: pravidelně testujte body obnovení, abyste měli jistotu, že jsou vaše snímky platné. Chcete-li obnovit existující vyhrazený fond SQL z bodu obnovení, můžete použít buď Azure Portal, nebo PowerShell. Test obnovení zálohovaných klíčů spravovaných zákazníkem

* [Postup obnovení klíčů Azure Key Vault](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?preserve-view=true&view=azurermps-6.13.0)

* [Zálohování a obnovení ve vyhrazeném fondu SQL](./sql-data-warehouse/backup-and-restore.md)

* [Postup obnovení existujícího vyhrazeného fondu SQL](./sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: v Azure SQL Database můžete nakonfigurovat jednu nebo sdruženou databázi s dlouhodobou zásadou uchovávání záloh (LTR) a automaticky tak uchovávat zálohy databáze v samostatných kontejnerech úložiště objektů BLOB v Azure po dobu až 10 let. Data v Azure Storage jsou šifrována a dešifrována transparentně pomocí 256 šifrování AES, je k dispozici jedna z nejúčinnějších šifrovacích šifr a je kompatibilní se standardem FIPS 140-2.

Ve výchozím nastavení se data v účtu úložiště šifrují pomocí klíčů spravovaných Microsoftem. Pro šifrování vašich dat můžete spoléhat na klíče spravované Microsoftem, nebo můžete šifrování spravovat pomocí vlastních klíčů. Pokud spravujete vlastní klíče pomocí Key Vault, ujistěte se, že je povolené obnovitelné odstranění.

* [Správa Azure SQL Database dlouhodobého uchovávání záloh](../azure-sql/database/long-term-backup-retention-configure.md)

* [Šifrování služby Azure Storage pro neaktivní uložená data](../storage/common/storage-service-encryption.md)

* [Jak povolit obnovitelné odstranění v Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorování Azure Security Center**: není k dispozici

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Doprovodné** materiály: Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují role pracovníků a také fáze zpracování a správy incidentů.

* [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Pokyny**: Security Center přiřadí závažnosti výstrahám, které vám pomůžou určit prioritu pořadí, ve kterém se zúčastníte jednotlivých výstrah, takže pokud dojde k ohrožení určitého prostředku, můžete se k němu hned dostat. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

* [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

* [Můžete se podívat na publikaci NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba.

* [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: * [prosím, postupujte podle pravidel Microsoft Engagement, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.) .

* [Další informace o strategii a provádění testování na základě červeného seskupování a testování průniku na webu společnosti Microsoft pro cloudovou infrastrukturu, služby a aplikace Microsoftu najdete tady.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)