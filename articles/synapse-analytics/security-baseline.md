---
title: Základní plán zabezpečení Azure pro synapse Analytics
description: Základní hodnoty zabezpečení synapse Analytics poskytují pokyny a prostředky pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 323ddfc7d595bd0d2321660e3b4141444db20518
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586785"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Základní plán zabezpečení Azure pro synapse Analytics

Tato základní hodnota zabezpečení platí pro synapse Analytics pokyny od [verze Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah se seskupuje pomocí **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů týkajících se synapse Analytics. **Ovládací prvky** , které se nevztahují na synapse Analytics, se vyloučily.

 
Pokud chcete zjistit, jak synapse Analytics úplně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot zabezpečení synapse Analytics](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné** materiály: zabezpečení SQL Server Azure pro virtuální síť prostřednictvím privátního propojení. Privátní odkaz Azure umožňuje přístup ke službám Azure PaaS přes privátní koncový bod ve vaší virtuální síti. Provoz mezi vaší virtuální sítí a službou přenáší páteřní síť Microsoftu.

Případně můžete při připojování ke synapse fondu SQL zúžit rozsah odchozího připojení k databázi SQL pomocí skupiny zabezpečení sítě. Nastavením povolit službám Azure na OFF zakažte všechny přenosy služeb Azure do databáze SQL prostřednictvím veřejného koncového bodu. Ujistěte se, že nejsou v pravidlech brány firewall povolená žádná veřejná IP adresa.

- [Principy privátního odkazu Azure](../private-link/private-link-overview.md)

- [Principy privátního odkazu pro Azure synapse SQL](../azure-sql/database/private-endpoint-overview.md)

- [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) .

**Azure Policy předdefinované definice – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné** materiály: když se připojujete k vašemu rezervovanému fondu SQL a máte Povolené protokoly toku NSG (Network Security Group), protokoly se odesílají do účtu Azure Storage pro auditování provozu.

Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: použití rozšířené ochrany před internetovými útoky (ATP) pro Azure synapse SQL. ATP detekuje aktivity neobvyklé indikující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití a může aktivovat různé výstrahy, jako je například "potenciální injektáže SQL," a "přístup z neobvyklého umístění". ATP je součástí nabídky rozhraní ADS (Advanced Data Security) a dá se k nim přistupovat a spravovat prostřednictvím centrálního portálu SQL ADS.

Povolit DDoS Protection standard pro virtuální sítě přidružené k Azure synapse SQL pro ochranu před distribuovanými útoky DoS (Denial of Service). K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

- [Principy ATP pro Azure synapse SQL](../azure-sql/database/threat-detection-overview.md)

- [Jak povolit pokročilou zabezpečení dat pro Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Přehled reklam](../azure-sql/database/azure-defender-for-sql.md)

- [Jak nakonfigurovat DDoS Protection](../ddos-protection/manage-ddos-protection.md)

- [Pochopení Azure Security Center integrované analýzy hrozeb](../security-center/azure-defender.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Pokyny**: když se připojujete k vašemu rezervovanému fondu SQL a máte Povolené protokoly toku NSG (Network Security Group), odešlete protokoly do účtu Azure Storage pro auditování provozu. Protokoly toku můžete také odesílat do Log Analyticsho pracovního prostoru nebo je streamovat do Event Hubs.  Pokud je to potřeba pro prošetření aktivity neobvyklé, povolte zachytávání paketů Network Watcher.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení Network Watcher](../network-watcher/network-watcher-create.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: použití rozšířené ochrany před internetovými útoky (ATP) pro Azure synapse SQL. ATP detekuje aktivity neobvyklé indikující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití a může aktivovat různé výstrahy, jako je například "potenciální injektáže SQL," a "přístup z neobvyklého umístění". ATP je součástí nabídky rozhraní ADS (Advanced Data Security) a dá se k nim přistupovat a spravovat prostřednictvím centrálního portálu SQL ADS. ATP také integruje výstrahy s Azure Security Center.

- [Principy ATP pro Azure synapse SQL](../azure-sql/database/threat-detection-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: pomocí značek služby virtuální sítě můžete definovat řízení přístupu k síti pro skupiny zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Při použití koncového bodu služby pro vyhrazený fond SQL se vyžaduje odchozí IP adresy pro Azure SQL Database: musí být otevřené skupiny zabezpečení sítě (skupin zabezpečení sítě), aby se mohly Azure SQL Database IP adresy, aby bylo možné připojení. To můžete provést pomocí značek služby NSG pro Azure SQL Database.

- [Pochopení značek služeb pomocí koncových bodů služby pro Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/vnet-service-endpoint-rule-overview#limitations)

- [Pochopení a používání značek služeb](../virtual-network/service-tags-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace konfigurací zabezpečení sítě pro prostředky související s vaším vyhrazeným fondem SQL pomocí Azure Policy. Obor názvů Microsoft. SQL můžete použít k definování vlastních definic zásad nebo k používání kterékoli z vestavěných definic zásad, které jsou určené pro ochranu Azure SQL Database/Server Network. Příkladem příslušných integrovaných zásad zabezpečení sítě pro Azure SQL Database Server by byl: "SQL Server by měl používat koncový bod služby virtuální sítě".

Pomocí Azure modrotisky můžete zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou šablony pro správu prostředků Azure, řízení přístupu na základě role Azure (Azure RBAC) a zásady, a to v jediné definici podrobného plánu. Podrobné sestavování můžete snadno použít pro nová předplatná a prostředí a vyladit řízení a správu prostřednictvím správy verzí.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Pokyny**: používejte značky pro skupiny zabezpečení sítě (NSG) a další zdroje informací týkající se zabezpečení sítě a toku provozu. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vaším vyhrazeným fondem SQL. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: zásady auditu je možné definovat pro určitou databázi nebo jako výchozí zásady serveru v Azure (které hostují Azure synapse). Zásady serveru se vztahují na všechny existující a nově vytvořené databáze na serveru.

Pokud je auditování serveru povoleno, bude vždy použito pro databázi. Databáze bude auditována bez ohledu na nastavení auditování databáze.

Pokud povolíte auditování, můžete je zapsat do protokolu auditu v účtu Azure Storage, Log Analytics pracovním prostoru nebo Event Hubs.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

- [Nastavení auditování pro prostředky SQL Azure](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: Povolte auditování na úrovni serveru SQL Azure pro vyhrazený fond SQL a vyberte umístění úložiště pro protokoly auditu (Azure Storage, Log Analytics nebo Event Hubs).

Auditování lze povolit na úrovni databáze nebo serveru a je navrženo, aby bylo povoleno na úrovni serveru pouze v případě, že pro určitou databázi nevyžadujete konfiguraci samostatné datové jímky nebo uchování.

- [Postup povolení auditování pro Azure SQL Database](../azure-sql/database/auditing-overview.md)

- [Jak povolit auditování pro server](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

- [Rozdíly v zásadách auditování na úrovni serveru oproti databázi](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) .

**Azure Policy předdefinované definice – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: při ukládání protokolů souvisejících s vaším vyhrazeným fondem SQL v účtu úložiště, Log Analytics pracovním prostoru nebo centra událostí nastavte dobu uchování protokolu podle předpisů pro dodržování předpisů vaší organizace.

- [Správa životního cyklu úložiště objektů blob v Azure](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Postup nastavení parametrů uchovávání protokolů v pracovním prostoru Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Zachytávání událostí streamování v Event Hubs](../event-hubs/event-hubs-capture-overview.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) .

**Azure Policy předdefinované definice – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů pro chování neobvyklé a pravidelné kontroly výsledků. Pomocí rozšířené ochrany před internetovými útoky pro Azure SQL Database ve spojení s Azure Security Center upozorní na neobvyklé aktivity související s vaší databází SQL. Případně můžete nakonfigurovat výstrahy na základě hodnot metrik nebo položek protokolu aktivit Azure, které se týkají vaší databáze SQL.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

- [Pochopení rozšířené ochrany před internetovými útoky a upozorňování na Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Jak povolit pokročilou zabezpečení dat pro Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Jak nakonfigurovat vlastní výstrahy pro Azure SQL Database](../azure-sql/database/alerts-insights-configure-portal.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: použití rozšířené ochrany před internetovými útoky (ATP) pro Azure SQL Database ve spojení s Azure Security Center k monitorování a upozornění na aktivitu neobvyklé. ATP je součástí nabídky pokročilého zabezpečení dat (ADS) a dá se k nim přistupovat a spravovat prostřednictvím centrálních reklam SQL na portálu. Služby ADS obsahují funkce pro zjišťování a klasifikaci citlivých dat, zpřístupněníí a zmírnění potenciálních ohrožení zabezpečení databáze a zjišťují se neobvyklé aktivity, které by mohly znamenat hrozbu pro vaši databázi.

Alternativně můžete povolit a začlenit data do Azure Sentinel.

- [Pochopení rozšířené ochrany před internetovými útoky a upozorňování na Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Jak povolit pokročilou zabezpečení dat pro Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) .

**Azure Policy předdefinované definice – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Pokyny**: uživatelé se ověřují buď pomocí Azure Active Directory (Azure AD), nebo pomocí ověřování SQL.

Při prvním nasazení Azure SQL zadejte přihlašovací jméno správce a přidružené heslo pro toto přihlášení. Tento účet správce se nazývá správce serveru. Účty správců pro databázi můžete identifikovat tak, že otevřete Azure Portal a přejdete na kartu vlastnosti vašeho serveru nebo spravované instance. Můžete také nakonfigurovat účet správce Azure AD s úplnými oprávněními pro správu, který je nutný, pokud chcete povolit ověřování Azure AD.

Pro operace správy použijte předdefinované role Azure, které se musí explicitně přiřadit. Pomocí modulu Azure AD PowerShell můžete provádět dotazy ad hoc a zjišťovat účty, které jsou členy skupin pro správu.

- [Ověřování pro SQL Database](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

- [Vytváření účtů pro uživatele bez oprávnění správce](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

- [Použití účtu Azure AD k ověřování](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Správa stávajících přihlašovacích údajů a účtů správců v Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

- [Předdefinované role Azure](../role-based-access-control/built-in-roles.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné** materiály: Azure Active Directory (Azure AD) nemá koncept výchozích hesel. Při zřizování vyhrazeného fondu SQL doporučujeme, abyste se rozhodli pro integraci ověřování s Azure AD. Pomocí této metody ověřování uživatel odesílá název uživatelského účtu a požaduje, aby služba používala informace přihlašovacích údajů uložené v Azure AD.

- [Jak nakonfigurovat a spravovat ověřování Azure AD pomocí Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Principy ověřování v Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: Vytvořte zásady a postupy týkající se používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu, které se přihlásí prostřednictvím služby Azure Active Directory (Azure AD).

Chcete-li identifikovat účty správců pro databázi, otevřete Azure Portal a přejděte na kartu Vlastnosti serveru nebo spravované instance.

- [Pochopení Azure Security Center identity a přístupu](../security-center/security-center-identity-access.md)

- [Správa stávajících přihlašovacích údajů a účtů správců v Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Pokyny**: použijte registraci aplikace Azure (instanční objekt) k získání tokenu, který se dá použít k interakci s datovým skladem na řídicí rovině (Azure Portal) prostřednictvím volání rozhraní API.

- [Volání rozhraní Azure REST API](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Postup registrace klientské aplikace (instančního objektu) pomocí Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informace o REST API SQL pro Azure synapse](sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a sledujte Azure Security Center doporučení pro správu identit a přístupu.

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

- [Principy vícefaktorového ověřování v Azure SQL](../azure-sql/database/authentication-mfa-ssms-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Doprovodné** materiály: použití pracovní stanice s privilegovaným přístupem (privilegovaným přístupem) s nakonfigurovaným vícefaktorového ověřováním, které je nakonfigurované pro přihlášení a konfiguraci prostředků Azure.

- [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použití sestav zabezpečení Azure Active Directory (Azure AD) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě.

Použití rozšířené ochrany před internetovými útoky pro Azure SQL Database ve spojení s Azure Security Center k detekci a upozornění na aktivity neobvyklé indikující neobvyklé a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

SQL Server Audit vám umožní vytvořit audity serveru, které můžou obsahovat specifikace auditu serveru pro události na úrovni serveru a specifikace auditu databáze pro události na úrovni databáze. Auditované události lze zapsat do protokolů událostí nebo auditovat soubory.

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorovat aktivitu identity uživatelů a přístupu v Azure Security Center](../security-center/security-center-identity-access.md)

- [Kontrola rozšířené ochrany před internetovými útoky a potenciálních výstrah](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

- [Pochopení přihlašovacích údajů a uživatelských účtů v Azure SQL](../azure-sql/database/logins-create-manage.md)

- [Pochopení SQL Server auditování](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolte přístup k portálu a správě prostředků Azure jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: vytvořte správce Azure Active Directory (Azure AD) pro server Azure SQL Database ve vyhrazeném fondu SQL.

- [Jak nakonfigurovat a spravovat ověřování Azure AD pomocí Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) .

**Azure Policy předdefinované definice – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure AD efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

Při použití ověřování SQL vytvořte v databázi uživatele databáze s omezením. Zajistěte, aby jeden nebo více uživatelů databáze umístili do vlastní databázové role s konkrétními oprávněními, která jsou vhodná pro danou skupinu uživatelů.

- [Jak používat kontroly přístupu](../active-directory/governance/access-reviews-overview.md)

- [Pochopení přihlašovacích údajů a uživatelských účtů v Azure SQL](../azure-sql/database/logins-create-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Pokyny**: konfigurace ověřování Azure Active Directory (Azure AD) pomocí Azure SQL a povolení nastavení diagnostiky pro uživatelské účty Azure AD, odesílání protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. Nakonfigurujte požadované výstrahy v rámci Log Analytics.

Při použití ověřování SQL vytvořte v databázi uživatele databáze s omezením. Zajistěte, aby jeden nebo více uživatelů databáze umístili do vlastní databázové role s konkrétními oprávněními, která jsou vhodná pro danou skupinu uživatelů.

- [Jak používat kontroly přístupu](../active-directory/governance/access-reviews-overview.md)

- [Jak nakonfigurovat a spravovat ověřování Azure AD pomocí Azure SQL Database](../azure-sql/database/authentication-aad-configure.md)

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Pochopení přihlašovacích údajů a uživatelských účtů v Azure SQL](../azure-sql/database/logins-create-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Pokyny**: pomocí funkcí Azure Active Directory (Azure AD) Identity Protection a detekce rizik můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce související s identitami uživatelů. Další šetření navíc zapněte a ingestujte data do Azure Sentinel.

Při použití ověřování SQL vytvořte v databázi uživatele databáze s omezením. Zajistěte, aby jeden nebo více uživatelů databáze umístili do vlastní databázové role s konkrétními oprávněními, která jsou vhodná pro danou skupinu uživatelů.

- [Jak zobrazit přihlášení rizik Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Postup zprovoznění služby Azure Sentinel](../sentinel/connect-data-sources.md)

- [Pochopení přihlašovacích údajů a uživatelských účtů v Azure SQL](../azure-sql/database/logins-create-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: ve scénářích podpory, kde Microsoft potřebuje získat přístup k datům souvisejícím s Azure SQL Database ve vašem vyhrazeném fondu SQL, Azure Customer Lockbox poskytuje rozhraní pro kontrolu a schválení nebo zamítnutí požadavků na přístup k datům.

- [Pochopení Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

Klasifikace zjišťování dat &amp; je integrovaná do Azure synapse SQL. Poskytuje pokročilé možnosti pro zjišťování, klasifikaci, označování a oznamování citlivých dat ve vašich databázích.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Vysvětlení klasifikace zjišťování dat &amp;](../azure-sql/database/data-discovery-and-classification-overview.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) .

**Azure Policy předdefinované definice – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Prostředky by měly být oddělené virtuální sítí nebo podsítí, vhodně označené a zabezpečené v rámci skupiny zabezpečení sítě nebo Azure Firewall. Prostředky, které ukládají nebo zpracovávají citlivá data, by měly být izolované. Použít privátní odkaz; Nasaďte Azure SQL Server do virtuální sítě a bezpečně se připojte pomocí privátního odkazu.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Jak nastavit privátní odkaz pro Azure SQL Database](../azure-sql/database/private-endpoint-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: pro všechny Azure SQL Database ve vyhrazeném fondu SQL, které ukládají nebo zpracovávají citlivé informace, označte databázi a související prostředky jako citlivé pomocí značek. Nakonfigurujte privátní propojení ve spojení s značkami služby skupiny zabezpečení sítě (NSG) na vašich instancích Azure SQL Database, abyste zabránili exfiltrace citlivých informací.

Rozšířená ochrana před internetovými útoky pro Azure SQL Database, spravovaná instance Azure SQL a Azure synapse detekuje aktivity neobvyklé, které naznačují neobvyklé a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Jak nakonfigurovat privátní linku a skupin zabezpečení sítě, aby se zabránilo exfiltraceí dat na instancích Azure SQL Database](../azure-sql/database/private-endpoint-overview.md)

- [Pochopení rozšířené ochrany před internetovými útoky pro Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Pokyny**: použijte funkci klasifikace SQL data Discovery pro Azure synapse &amp; . Klasifikace zjišťování dat &amp; poskytuje pokročilé funkce integrované v Azure SQL Database pro zjišťování, klasifikaci a označování &amp; ochrany citlivých dat ve vašich databázích.

Klasifikace zjišťování dat &amp; je součástí rozšířené nabídky zabezpečení dat, což je jednotný balíček pro pokročilé funkce zabezpečení SQL. Klasifikace zjišťování dat &amp; se dá použít a spravovat prostřednictvím portálu centrálních SQL ADS.

Kromě toho můžete v Azure Portal nastavit zásadu dynamického maskování dat (DDM). Modul doporučení DDM označuje určitá pole z vaší databáze jako potenciálně citlivá pole, která mohou být vhodná pro maskování.

- [Použití zjišťování a klasifikace dat pro Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

- [Principy dynamického maskování dat pro Azure synapse SQL](../azure-sql/database/dynamic-data-masking-overview.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) .

**Azure Policy předdefinované definice – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-azure-rbac-access-control-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte řízení přístupu Azure RBAC 

**Pokyny**: pomocí řízení přístupu na základě role Azure (Azure RBAC) můžete spravovat přístup k databázím SQL Azure ve vyhrazeném fondu SQL.

Autorizaci řídí členství v databázové roli vašeho uživatelského účtu a oprávnění na úrovni objektů. Doporučený postup je udělit uživatelům co nejmenší možná oprávnění.

- [Jak integrovat Azure SQL Server s Azure Active Directory (Azure AD) pro ověřování](../azure-sql/database/authentication-aad-overview.md)

- [Jak řídit přístup v Azure SQL Server](../azure-sql/database/logins-create-manage.md)

- [Vysvětlení autorizace a ověřování v Azure SQL](../azure-sql/database/logins-create-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: transparentní šifrování dat (TDE) pomáhá chránit Azure synapse SQL před hrozbou škodlivosti offline aktivity šifrováním dat v klidovém stavu. Šifruje a dešifruje databáze, související zálohy a soubory transakčních protokolů v reálném čase, a přitom nevyžaduje změny v aplikaci. V Azure je výchozím nastavením pro TDE, že klíč DEK je chráněn integrovaným certifikátem serveru. Alternativně můžete použít TDE spravované zákazníky, označované také jako podpora služby Bring Your Own Key (BYOK) pro TDE. V tomto scénáři TDE ochrana šifrování klíč DEK je asymetrický klíč spravovaný zákazníkem, který je uložený ve Azure Key Vault ve vlastnictví a spravovaném zákazníkovi (cloudový externí systém správy klíčů Azure) a nikdy neopouští Trezor klíčů.

- [Pochopení transparentního šifrování dat spravovaného službou](../azure-sql/database/transparent-data-encryption-tde-overview.md)

- [Pochopení transparentního šifrování dat spravovaného zákazníkem](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview#customer-managed-transparent-data-encryption---bring-your-own-key)

- [Jak zapnout TDE pomocí vlastního klíče](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**Odpovědnost:** Sdílené

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) .

**Azure Policy předdefinované definice – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají do produkčních instancí fondů SQL synapse a dalších důležitých nebo souvisejících prostředků.

Kromě toho můžete nastavit výstrahy pro databáze ve fondu SQL synapse pomocí Azure Portal. Když některá metrika (například velikost databáze nebo využití procesoru) dosáhne prahové hodnoty, můžou vám výstrahy poslat e-mail nebo zavolat webový Hook.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Vytvoření upozornění pro Azure SQL synapse](../azure-sql/database/alerts-insights-configure-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: povolení pokročilých zabezpečení dat a použití doporučení od Azure Security Center při provádění posouzení ohrožení zabezpečení pro databáze SQL Azure.

- [Jak spustit posuzování ohrožení zabezpečení pro databáze SQL Azure](../azure-sql/database/sql-vulnerability-assessment.md)

- [Jak povolit pokročilé zabezpečení dat](../azure-sql/database/azure-defender-for-sql.md)

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) .

**Azure Policy předdefinované definice – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Pokyny**: posouzení ohrožení zabezpečení je skenovací služba integrovaná do Azure synapse SQL. Služba využívá znalostní bázi pravidel, která označují bezpečnostní hrozby. Zvýrazňuje odchylky od osvědčených postupů, jako jsou například nepřesné konfigurace, nadměrné oprávnění a nechráněná citlivá data.  K posouzení ohrožení zabezpečení lze využít a spravovat prostřednictvím portálu centrálních zabezpečení SQL Advanced Data Security (ADS).

- [Správa a export kontrol posouzení ohrožení zabezpečení na portálu SQL ADS](../azure-sql/database/sql-vulnerability-assessment.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: použijte výchozí hodnocení rizik (zabezpečené skóre) poskytované Azure Security Center.

Klasifikace zjišťování dat &amp; je integrovaná do Azure synapse SQL. Poskytuje pokročilé možnosti pro zjišťování, klasifikaci, označování a oznamování citlivých dat ve vašich databázích.

- [Pochopení Azure Security Center zabezpečeného skóre](../security-center/secure-score-security-controls.md)

- [Vysvětlení klasifikace zjišťování dat &amp;](../azure-sql/database/data-discovery-and-classification-overview.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) .

**Azure Policy předdefinované definice – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny prostředky, které se vztahují k vašemu rezervovanému fondu SQL v rámci vašich předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

I když je možné zjistit klasické prostředky Azure prostřednictvím Azure Resource graphu, důrazně se doporučuje vytvořit a použít prostředky Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription)

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

**Pokyny**: Definujte seznam schválených prostředků Azure, které souvisejí s vaším vyhrazeným fondem SQL.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat nebo zjišťovat prostředky v rámci předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: pomocí Azure Policy můžete umístit omezení typu prostředků, které se dají vytvořit v předplatných zákazníka, a to pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat nebo zjišťovat prostředky v rámci předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací "blokování přístupu" pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: použijte aliasy Azure Policy v oboru názvů Microsoft. SQL k vytvoření vlastních zásad pro auditování nebo vystavování konfigurace prostředků souvisejících s vaším vyhrazeným fondem SQL. Můžete také využít předdefinované definice zásad pro databáze nebo server Azure, například:

- Nasazení detekce hrozeb na SQL serverech
- SQL Server by měl používat koncový bod služby virtuální sítě

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Pokud používáte vlastní definice Azure Policy, použijte k bezpečnému ukládání a správě kódu službu Azure DevOps nebo Azure Repos.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

- [Dokumentace k Azure Repos](/azure/devops/repos/)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: nepoužitelné; Azure synapse SQL nemá konfigurovatelné nastavení zabezpečení.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: využijte Azure Security Center k provádění kontrol standardních hodnot pro všechny prostředky, které souvisejí s vaším vyhrazeným fondem SQL.

- [Jak opravit doporučení v Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Pokyny**: transparentní šifrování dat (TDE) s klíči spravovanými zákazníkem v Azure Key Vault umožňuje šifrování automaticky generovaného šifrovacího klíče databáze (klíč DEK) s asymetrickým klíčem spravovaným zákazníkem nazvaným TDE Protector. To se také obecně označuje jako podpora Bring Your Own Key (BYOK) pro transparentní šifrování dat. Ve scénáři BYOK se ochrana TDE ukládá do Azure Key Vault ve vlastnictví a spravovaném zákazníkem. Kromě toho se ujistěte, že je v Azure Key Vault povolené obnovitelné odstranění.

- [Jak povolit TDE s klíčem spravovaným zákazníkem z Azure Key Vault](../azure-sql/database/transparent-data-encryption-byok-configure.md)

- [Jak povolit obnovitelné odstranění v Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Pokyny**: použití spravovaných identit k poskytování služeb Azure s automaticky spravovanou identitou v Azure Active Directory (Azure AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Azure Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

- [Kurz: Použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure SQL](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Postup konfigurace spravovaných identit](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure (například Azure synapse SQL); neběží ale na zákaznickém obsahu.

Předem prohledejte veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, jako jsou App Service, Data Lake Storage, Blob Storage, Azure SQL Server atd. Společnost Microsoft nemá přístup k vašim datům v těchto instancích.

- [Pochopení Microsoft antimalwaru pro Azure Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: snímky vyhrazeného fondu SQL se automaticky provedou během dne vytváření bodů obnovení, které jsou k dispozici po dobu sedmi dnů. Tuto dobu uchování nelze změnit. Vyhrazený fond SQL podporuje osm hodin cíl bodu obnovení (RPO). Datový sklad můžete obnovit v primární oblasti z libovolného snímku, který jste provedli během posledních sedmi dnů. Všimněte si, že v případě potřeby můžete také ručně aktivovat snímky.

- [Zálohování a obnovení ve vyhrazeném fondu SQL](sql-data-warehouse/backup-and-restore.md)

**Odpovědnost:** Sdílené

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) .

**Azure Policy předdefinované definice – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné** materiály: snímky datového skladu se automaticky provedou během dne vytváření bodů obnovení, které jsou k dispozici po dobu sedmi dnů. Tuto dobu uchování nelze změnit. Vyhrazený fond SQL podporuje osm hodin cíl bodu obnovení (RPO). Datový sklad můžete obnovit v primární oblasti z libovolného snímku, který jste provedli během posledních sedmi dnů. Všimněte si, že v případě potřeby můžete také ručně aktivovat snímky.

Pokud k šifrování šifrovacího klíče databáze používáte klíč spravovaný zákazníkem, ujistěte se, že je váš klíč zálohovaný.

- [Zálohování a obnovení ve vyhrazeném fondu SQL](sql-data-warehouse/backup-and-restore.md)

- [Postup zálohování klíčů Azure Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Odpovědnost:** Sdílené

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) .

**Azure Policy předdefinované definice – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: pravidelně testujte body obnovení, abyste měli jistotu, že jsou vaše snímky platné. Chcete-li obnovit existující vyhrazený fond SQL z bodu obnovení, můžete použít buď Azure Portal, nebo PowerShell. Test obnovení zálohovaných klíčů spravovaných zákazníkem

- [Postup obnovení klíčů Azure Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Zálohování a obnovení ve vyhrazeném fondu SQL](sql-data-warehouse/backup-and-restore.md)

- [Postup obnovení existujícího vyhrazeného fondu SQL](sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: v Azure SQL Database můžete nakonfigurovat jednu nebo sdruženou databázi s dlouhodobou zásadou uchovávání záloh (LTR) a automaticky tak uchovávat zálohy databáze v samostatných kontejnerech úložiště objektů BLOB v Azure po dobu až 10 let. Data v Azure Storage jsou šifrována a dešifrována transparentně pomocí 256 šifrování AES, je k dispozici jedna z nejúčinnějších šifrovacích šifr a je kompatibilní se standardem FIPS 140-2.

Ve výchozím nastavení se data v účtu úložiště šifrují pomocí klíčů spravovaných Microsoftem. Pro šifrování vašich dat můžete spoléhat na klíče spravované Microsoftem, nebo můžete šifrování spravovat pomocí vlastních klíčů. Pokud spravujete vlastní klíče pomocí Key Vault, ujistěte se, že je povolené obnovitelné odstranění.

- [Správa Azure SQL Database dlouhodobého uchovávání záloh](../azure-sql/database/long-term-backup-retention-configure.md)

- [Šifrování služby Azure Storage pro neaktivní uložená data](../storage/common/storage-service-encryption.md)

- [Jak povolit obnovitelné odstranění v Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Doprovodné** materiály: Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují role pracovníků a také fáze zpracování a správy incidentů.

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Pokyny**: Security Center přiřadí závažnosti výstrahám, které vám pomůžou určit prioritu pořadí, ve kterém se zúčastníte jednotlivých výstrah, takže pokud dojde k ohrožení určitého prostředku, můžete se k němu hned dostat. Závažnost je založena na tom, jak je Security Center ve vyhledávání nebo v metrikě, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Můžete se podívat na publikaci NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba.

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

**Doprovodné** materiály: řiďte se prosím pravidly zapojení Microsoftu, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [Další informace o strategii a provádění testování na základě červeného seskupování a testování průniku na webu společnosti Microsoft pro cloudovou infrastrukturu, služby a aplikace Microsoftu najdete tady.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
