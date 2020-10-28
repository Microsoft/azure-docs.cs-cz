---
title: Základní hodnoty zabezpečení Azure
description: Základní hodnoty zabezpečení Azure pro Azure SQL Database a Azure SQL Managed instance
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9db0562e7d1c994c93b5a2dde9a3589c3d97ee9b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792612"
---
# <a name="azure-security-baseline-for-azure-sql-database--sql-managed-instance"></a>Základní hodnota zabezpečení Azure pro Azure SQL Database & SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Základní plán zabezpečení Azure pro Azure SQL Database obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Chraňte prostředky pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network

**Pokyny** : můžete povolit privátní propojení Azure a povolit přístup ke službám Azure PaaS (například SQL Database) a hostovaným zákaznickým a partnerským službám Azure prostřednictvím privátního koncového bodu ve vaší virtuální síti. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu.

Aby bylo možné provozovat Azure SQL Database, pomocí značek služby SQL povolte odchozí přenosy přes skupiny zabezpečení sítě.

Pravidla virtuální sítě umožňují Azure SQL Database přijímat jenom komunikace, které se odesílají z vybraných podsítí v rámci virtuální sítě.

Jak nastavit privátní odkaz pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

Použití koncových bodů a pravidel služby virtuální sítě pro servery:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuální sítě, podsítí a síťových karet.

**Doprovodné** materiály: použijte Azure Security Center a opravte doporučení k ochraně sítě pro podsíť, do které jste Azure SQL Database nasadili.

Pro Azure Virtual Machines (VM), který se bude připojovat k vašemu Azure SQL Database, povolte protokoly toku NSG (Network Security Group) pro skupin zabezpečení sítě ochranu těchto virtuálních počítačů a odeslání protokolů do účtu Azure Storage pro auditování provozu.

Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

Jak povolit protokoly toku NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Pochopení zabezpečení sítě, které poskytuje Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Jak povolit a použít Analýza provozu:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Pochopení zabezpečení sítě, které poskytuje Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro služby Azure apps nebo výpočetní prostředky hostující webové aplikace.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: ve virtuálních sítích, které jsou přidružené k vašemu Azure SQL Database pro ochranu před distribuovanými útoky DoS (Denial of Service), povolte DDoS Protection Standard. K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

Jak nakonfigurovat DDoS Protection:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Pochopení Azure Security Center integrované analýzy hrozeb:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: záznam síťových paketů a protokolů toků

**Pokyny** : u Azure Virtual Machines (virtuálních počítačů), které se budou připojovat k vaší instanci Azure SQL Database, povolte protokoly toku NSG (Network Security Group) pro skupin zabezpečení sítě, které chrání tyto virtuální počítače, a odešlete protokoly do účtu Azure Storage pro audit provozu. Pokud je to potřeba pro prošetření aktivity neobvyklé, povolte zachytávání paketů Network Watcher.

Jak povolit protokoly toku NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Postup povolení Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny** : povolení rozšířené ochrany před internetovými útoky (ATP) pro Azure SQL Database.  Uživatelům se zobrazí výstraha o podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení a útocích prostřednictvím injektáže SQL a také o vzorcích dotazů neobvyklé Database. Rozšířená ochrana před internetovými útoky také integruje výstrahy s Azure Security Center.

Pochopení a použití rozšířené ochrany před internetovými útoky pro Azure SQL Database: https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro služby Azure apps nebo výpočetní prostředky hostující webové aplikace.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny** : pomocí značek služby virtuální sítě můžete definovat řízení přístupu k síti pro skupiny zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Při použití koncových bodů služby pro Azure SQL Database se vyžaduje odchozí připojení k Azure SQL Database veřejné IP adresy: aby se Azure SQL Database IP adresy povolovaly, musí se otevřít skupiny zabezpečení sítě (skupin zabezpečení sítě). To můžete provést pomocí značek služby NSG pro Azure SQL Database.

Principy značek služeb pomocí koncových bodů služby pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

Pochopení a používání značek služeb:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny** : definování a implementace konfigurací zabezpečení sítě pro váš Azure SQL Database pomocí Azure Policy. Pomocí oboru názvů "Microsoft. SQL" můžete definovat vlastní definice zásad nebo použít některou z vestavěných definic zásad navržených pro ochranu sítě serveru. Příkladem příslušných integrovaných zásad zabezpečení sítě pro server bude: "SQL Database by měl používat koncový bod služby virtuální sítě".

Pomocí Azure modrotisky můžete zjednodušit rozsáhlá nasazení Azure tím, že zabalíte artefakty klíčových prostředí, jako jsou šablony pro správu prostředků Azure, řízení přístupu na základě role (RBAC) a zásady, v jediné definici podrobného plánu. Podrobné sestavování můžete snadno použít pro nová předplatná a prostředí a vyladit řízení a správu prostřednictvím správy verzí.

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Postup vytvoření Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Pokyny** : používejte značky pro skupiny zabezpečení sítě (NSG) a další zdroje informací týkající se zabezpečení sítě a toku provozu. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny** : pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které se vztahují k vašemu serveru. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

Jak zobrazit a načíst události protokolu aktivit Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Postup při vytváření výstrah v Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Doprovodné** materiály: Microsoft udržuje časové zdroje pro prostředky Azure. Můžete aktualizovat synchronizaci času pro vaše nasazení výpočtů.

Jak nakonfigurovat časovou synchronizaci pro výpočetní prostředky Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: Povolte auditování pro Azure SQL Database ke sledování událostí databáze a jejich zápis do protokolu auditu ve vašem účtu Azure Storage, Log Analytics pracovní prostor nebo Event Hubs.

Navíc můžete zasílat telemetrii diagnostiky Azure SQL do Azure SQL Analytics, což je cloudové řešení, které monitoruje výkon Azure SQL Database a spravované instance SQL Azure ve velkém měřítku a v různých předplatných. Může vám to usnadnit shromažďování a vizualizace Azure SQL Database metriky výkonu a obsahuje integrované inteligentní funkce pro řešení potíží s výkonem.

Postup nastavení auditování pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

Postup při streamování diagnostiky do Azure SQL Analytics:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: Povolte auditování na serveru a vyberte umístění úložiště pro protokoly auditu (Azure Storage, Log Analytics nebo centrum událostí).

Postup povolení auditování pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: nepoužitelné; Tento srovnávací test je určený pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: při ukládání protokolů Azure SQL Database v pracovním prostoru Log Analytics nastavte dobu uchování protokolu v souladu s předpisy pro dodržování předpisů vaší organizace.

Postup nastavení parametrů uchovávání protokolů:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny** : analýza a sledování protokolů pro chování neobvyklé a pravidelné kontroly výsledků. Pomocí rozšířené ochrany před internetovými útoky v Azure Security Center můžete upozorňovat na neobvyklou aktivitu související s vaší instancí Azure SQL Database. Případně můžete nakonfigurovat výstrahy na základě hodnot metrik nebo položek protokolu aktivit Azure souvisejících s vašimi instancemi Azure SQL Database.

Pochopení rozšířené ochrany před internetovými útoky a upozorňování na Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Jak nakonfigurovat vlastní výstrahy pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé

**Pokyny** : použití Azure Security Center rozšířené ochrany před internetovými útoky pro Azure SQL Database monitorování a upozorňování na aktivitu neobvyklé. Pro databáze SQL povolte Azure Defender pro SQL. Azure Defender pro SQL zahrnuje funkce pro zpřístupnění a zmírnění potenciálních ohrožení zabezpečení databáze a zjišťování aktivit neobvyklé, které by mohly znamenat hrozbu pro vaši databázi.

Pochopení rozšířené ochrany před internetovými útoky a upozorňování na Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Jak povolit Azure Defender pro SQL pro Azure SQL Database:

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Správa výstrah v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: nepoužitelné; pro Azure SQL Database je řešení proti malwaru spravované Microsoftem na podkladové platformě.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné** materiály: nepoužitelné; Protokolování DNS se nedá použít pro Azure SQL Database.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné** materiály: nepoužitelné; auditování příkazového řádku nelze použít pro Azure SQL Database.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Azure Active Directory (Azure AD) mají předdefinované role, které se musí explicitně přiřadit a které jsou Queryable. Pomocí modulu Azure AD PowerShell můžete provádět dotazy ad hoc a zjišťovat účty, které jsou členy skupin pro správu.

Jak získat roli adresáře ve službě Azure AD pomocí prostředí PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak získat členy role adresáře ve službě Azure AD pomocí prostředí PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné** materiály: Azure Active Directory nemá koncept výchozích hesel. Při zřizování instance Azure SQL Database doporučujeme, abyste se rozhodli pro integraci ověřování s Azure Active Directory.

Jak nakonfigurovat a spravovat Azure Active Directory ověřování pomocí Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: Vytvořte zásady a postupy týkající se používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Pochopení Azure Security Center identity a přístupu:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné** materiály: nepoužitelné; i když můžete nakonfigurovat ověřování Azure Active Directory pro integraci s Azure SQL Database, jednotné přihlašování se nepodporuje.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: povolte službu Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) a sledujte Azure Security Center doporučení pro správu identit a přístupu.

Jak povolit vícefaktorové ověřování v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorovat identitu a přístup v rámci Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Doprovodné** materiály: použití pracovní stanice s privilegovaným přístupem (privilegovaným přístupem) se službou Multi-Factor Authentication MFA nakonfigurovanou pro přihlášení a konfiguraci prostředků Azure.

Další informace o pracovních stanicích s privilegovaným přístupem:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové ověřování v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: protokolování a upozornění na podezřelou aktivitu z účtů pro správu

**Doprovodné** materiály: použití sestav zabezpečení Azure Active Directory pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě.

Pomocí rozšířené ochrany před internetovými útoky pro Azure SQL Database můžete detekovat aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Jak identifikovat uživatele Azure AD označené příznakem rizika pro rizikové aktivity:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak monitorovat aktivitu identity uživatelů a přístupu v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Kontrola rozšířené ochrany před internetovými útoky a potenciálních výstrah:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny** : pomocí pojmenovaných umístění podmíněného přístupu povolte přístup k portálu a správě prostředků Azure jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

Jak nakonfigurovat pojmenovaná umístění v Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny** : Vytvoření správce služby Azure Active Directory (Azure AD) pro váš server.

Jak nakonfigurovat a spravovat Azure Active Directory ověřování pomocí Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Jak vytvořit a nakonfigurovat instanci Azure AD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

Jak používat kontroly přístupu Azure identity:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorování pokusů o přístup k deaktivovaným účtům

**Pokyny** : konfigurace ověřování Azure Active Directory (Azure AD) pomocí Azure SQL a vytvoření nastavení diagnostiky pro Azure Active Directory uživatelských účtů, odesílání protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. Nakonfigurujte požadované výstrahy v rámci Log Analytics pracovního prostoru.

Jak nakonfigurovat a spravovat Azure Active Directory ověřování pomocí Azure SQL:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Jak integrovat protokoly aktivit Azure do Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Pokyny** : pomocí Azure Active Directory (Azure AD) Identity Protection a detekce rizik můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce týkající se identit uživatelů. Kromě toho můžete ingestovat data do služby Azure Sentinel pro další šetření.

Postup zobrazení přihlášení rizik Azure AD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak nakonfigurovat a povolit zásady pro rizika ochrany identity:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: ve scénářích podpory, kde Microsoft potřebuje získat přístup k zákaznickým datům, Azure Customer Lockbox poskytuje rozhraní pro zákazníky, kteří budou kontrolovat a schvalovat nebo odmítat žádosti o přístup k datům zákazníků.

Pochopení Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny** : implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Prostředky by měly být oddělené podle virtuální sítě a podsítě, musí být správně označené a zabezpečené v rámci NSG nebo Azure Firewall. Prostředky, které ukládají nebo zpracovávají citlivá data, by měly být izolované. Použít privátní odkaz; Nasaďte Azure SQL Database do vaší virtuální sítě a soukromě se připojte pomocí soukromých koncových bodů.

Jak vytvořit další předplatná Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Postup vytvoření Skupiny pro správu:

https://docs.microsoft.com/azure/governance/management-groups/create

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak nastavit privátní odkaz pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: u databází v Azure SQL Database ukládání a zpracování citlivých informací, označte databázi a související prostředky jako citlivé pomocí značek. Nakonfigurujte privátní propojení ve spojení s značkami služby skupin zabezpečení sítě na vašich instancích Azure SQL Database, abyste zabránili exfiltrace citlivých informací.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

Jak nakonfigurovat privátní linku a skupin zabezpečení sítě, aby se zabránilo exfiltraceí dat na vašich Azure SQL Databasech instancích:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Pochopení ochrany zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: Azure SQL Database zabezpečení vašich dat šifrováním dat při pohybu pomocí Transport Layer Security. SQL Database pro všechna připojení vynutila šifrování (SSL/TLS). Tím se zajistí, že všechna data budou zašifrovaná mezi klientem a serverem, a to bez ohledu na nastavení šifrování nebo TrustServerCertificate v připojovacím řetězci.

Pochopení šifrování Azure SQL při přenosu:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: použijte funkci zjišťování a klasifikace dat Azure SQL Database. Zjišťování a klasifikace dat poskytuje pokročilé funkce, které jsou integrované v Azure SQL Database pro zjišťování, klasifikaci a označování &amp; ochrany citlivých dat ve vašich databázích.

Jak používat zjišťování a klasifikaci dat pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Doprovodné** materiály: použijte Azure Active Directory (Azure AD) pro ověřování a řízení přístupu k instancím Azure SQL Database.

Jak integrovat Azure SQL Database s Azure Active Directory pro ověřování:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Řízení přístupu v Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: Společnost Microsoft spravuje základní infrastrukturu pro Azure SQL Database a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

Pochopení ochrany zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: transparentní šifrování dat (TDE) pomáhá chránit Azure SQL Database, Azure SQL Managed instance a Azure Data Warehouse před hrozbou nebezpečných offline aktivit šifrováním dat v klidovém stavu. Provádí šifrování a dešifrování neaktivní uložené databáze, souvisejících záloh a souborů transakčních protokolů v reálném čase a nevyžaduje žádné změny v aplikaci. Ve výchozím nastavení je TDE povolený pro všechny nově nasazené databáze v SQL Database a na spravované instanci SQL. Šifrovací klíč TDE může spravovat buď společnost Microsoft, nebo zákazník.

Správa transparentního šifrování dat a používání vlastních šifrovacích klíčů:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají v produkčních instancích Azure SQL Database a dalších důležitých nebo souvisejících prostředcích.

Vytváření upozornění pro události protokolu aktivit Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](../../security/benchmarks/security-control-vulnerability-management.md)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: Povolte Azure Defender pro SQL pro Azure SQL Database a použijte doporučení od Azure Security Center při provádění posouzení ohrožení zabezpečení na serverech.

Jak spustit posuzování ohrožení zabezpečení na Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

Jak povolit Azure Defender pro SQL:

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: nasazení automatizovaného řešení pro správu oprav softwaru třetí strany

**Doprovodné** materiály: nepoužitelné; Tento srovnávací test je určený pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: povolení pravidelného opakovaného prohledávání pro instance Azure SQL Database; Tím se nakonfiguruje posouzení ohrožení zabezpečení pro automatické spuštění prohledávání databáze jednou za týden. Na e-mailové adresy, které poskytnete, se pošle souhrn výsledků kontroly. Porovnáním výsledků ověřte, že chyby zabezpečení byly opraveny.

Export sestavy posouzení ohrožení zabezpečení v Azure Security Center:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: použijte výchozí hodnocení rizik (zabezpečené skóre) poskytované Azure Security Center.

Pochopení Azure Security Center zabezpečeného skóre:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: použijte Azure Asset Discovery.

**Pokyny** : pomocí grafu prostředků Azure můžete v rámci vašich předplatných dotazovat a zjišťovat všechny prostředky (včetně Azure SQL Database).  Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

Jak vytvářet dotazy pomocí Azure Resource graphu: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak zobrazit vaše předplatná Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Porozumění službě Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Jak vytvořit další předplatná Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Postup vytvoření Skupiny pro správu:

https://docs.microsoft.com/azure/governance/management-groups/create

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: udržování inventáře schválených prostředků Azure a softwarových titulů

**Doprovodné** materiály: definování seznamu schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat nebo zjišťovat prostředky v rámci předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvářet dotazy pomocí Azure graphu: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: pomocí Azure Policy můžete umístit omezení typu prostředků, které se dají vytvořit v předplatných zákazníka, a to pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat nebo zjišťovat prostředky v rámci předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odepřít konkrétní typ prostředku s Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="610-implement-approved-application-list"></a>6,10: Implementujte schválený seznam aplikací

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro aplikace běžící na výpočetních prostředcích.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: Omezte schopnost uživatelů pracovat se správcem prostředků Azure prostřednictvím skriptů

**Pokyny** : pomocí podmíněného přístupu Azure omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací "blokování přístupu" pro aplikaci Microsoft Azure Management.

Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro App Service nebo výpočetní prostředky hostující Desktop nebo webové aplikace.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny** : použijte Azure Policy nebo Azure Security Center doporučení pro Azure SQL Database k údržbě konfigurací zabezpečení pro všechny prostředky Azure.

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Pochopení Azure Policych účinků:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Pokud používáte vlastní definice Azure Policy, použijte k bezpečnému ukládání a správě kódu službu Azure DevOps nebo Azure Repos.

Jak ukládat kód v Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos dokumentaci:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: nasazení nástrojů pro správu konfigurace systému

**Doprovodné** materiály: použijte aliasy Azure Policy v oboru názvů Microsoft. SQL k vytvoření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: nasaďte nástroje pro správu konfigurace systému pro operační systémy.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Implementujte automatizované monitorování konfigurace pro služby Azure.

**Doprovodné** materiály: využijte Azure Security Center k provádění kontrol standardních hodnot pro Azure SQL Database.

Postup nápravy doporučení v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: použijte Azure Key Vault k uložení šifrovacích klíčů pro Azure SQL Database transparentní šifrování dat (TDE).

Jak chránit citlivá data uložená v Azure SQL Database a ukládat šifrovací klíče v Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Pokyny** : použití spravovaných identit k poskytování služeb Azure s automaticky spravovanou identitou v Azure Active Directory (Azure AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Azure Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

Kurz: pro přístup k Azure SQL použijte spravovanou identitu přiřazenou systémem Windows VM:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

Postup konfigurace spravovaných identit:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny** : implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

Jak nastavit skener přihlašovacích údajů: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky. Microsoft zpracovává anti-malware pro základní platformu.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny** : ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure (například Azure App Service), ale neběží na zákaznickém obsahu.

Předem prohledejte veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, jako jsou App Service, Data Lake Storage, Blob Storage, Azure SQL Database atd. Společnost Microsoft nemá přístup k vašim datům v těchto instancích.

Pochopení ochrany proti malwaru Microsoftu pro Azure Cloud Services a Virtual Machines: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky. Microsoft zpracovává anti-malware pro základní platformu.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: k ochraně vaší firmy před ztrátou dat Azure SQL Database automaticky vytvoří úplné zálohy databáze každý týden, odrozdílové zálohy databáze každých 12 hodin a zálohování protokolů transakcí každé 5-10 minut. Zálohy se ukládají do úložiště RA-GRS po dobu alespoň 7 dnů pro všechny úrovně služeb. Všechny úrovně služeb s výjimkou podpora Basic konfigurovatelný doba uchovávání záloh pro obnovení k určitému bodu v čase, až 35 dní.

Pro splnění různých požadavků na dodržování předpisů můžete pro týdenní, měsíční nebo roční zálohy vybrat jinou dobu uchování. Spotřeba úložiště závisí na zvolené četnosti zálohování a na dobu uchování (e).

Principy zálohování a kontinuity podnikových aplikací pomocí Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Monitorování Azure Security Center** : Ano

**Odpovědnost** : sdílená

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

**Doprovodné** materiály: Azure SQL Database automaticky vytvoří zálohy databáze, které jsou zachované mezi 7 a 35 dny, a používá geograficky redundantní úložiště Azure s přístupem pro čtení (RA-GRS), aby se zajistilo, že jsou zachována i v případě, že datové centrum není k dispozici. Tyto zálohy jsou vytvořeny automaticky. V případě potřeby povolte pro databáze SQL Azure dlouhodobé geografické redundantní zálohy.

Pokud používáte klíče spravované zákazníkem pro transparentní šifrování dat, ujistěte se, že se klíče zálohují.

Principy zálohování v Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Postup zálohování klíčů trezoru klíčů v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

**Doprovodné** materiály: Zajistěte, aby bylo možné pravidelně provádět obnovování dat v rámci Azure Backup. V případě potřeby proveďte test obnovení obsahu na izolovanou síť VLAN. Test obnovení zálohovaných klíčů spravovaných zákazníkem

Postup obnovení klíčů trezoru klíčů v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

Postup obnovení zálohy Azure SQL Database pomocí obnovení k bodu v čase:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

**Pokyny** : povolení obnovitelného odstranění v Azure Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění.

Jak povolit obnovitelné odstranění v Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Doprovodné** materiály: Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují role pracovníků a také fáze zpracování a správy incidentů.

Postup konfigurace automatizace pracovních postupů v rámci Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Pokyny** : Security Center přiřadí závažnosti výstrahám, které vám pomůžou určit prioritu pořadí, ve kterém se zúčastníte jednotlivých výstrah, takže pokud dojde k ohrožení určitého prostředku, můžete se k němu hned dostat. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Výstrahy zabezpečení v Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

Můžete se podívat na publikaci NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba.

Jak nastavit Azure Security Center kontakt zabezpečení:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy na Sentinel.

Postup konfigurace průběžného exportu:

https://docs.microsoft.com/azure/security-center/continuous-export

Jak streamovat výstrahy do Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech důležitých zjištění zabezpečení do 60 dnů.

**Doprovodné** materiály: řiďte se prosím pravidly zapojení Microsoftu, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Další informace o strategii a provádění testování na základě červeného seskupování a testování průniku na webu pomocí spravované cloudové infrastruktury, služeb a aplikací Microsoftu najdete tady: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : sdílená

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../../security/benchmarks/overview.md)
- Další informace o [plánech zabezpečení Azure](../../security/benchmarks/security-baselines-overview.md)