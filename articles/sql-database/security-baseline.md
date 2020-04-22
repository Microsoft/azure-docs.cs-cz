---
title: Základ zabezpečení Azure pro azure sql databázi
description: Základ zabezpečení Azure pro azure sql databázi
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8c0e7c6dfb1275e1fafbab1dd9e89cb2fe4376ad
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759153"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Základ zabezpečení Azure pro azure sql databázi

Azure Security Baseline pro Azure SQL Database obsahuje doporučení, která vám pomůžou zlepšit stav zabezpečení vašeho nasazení.

Směrný plán pro tuto službu vychází z [azure security benchmark verze 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure s našimi pokyny pro osvědčené postupy.

Další informace naleznete v [tématu Přehled směrného plánu zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace naleznete v [tématu Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrana prostředků pomocí skupin zabezpečení sítě nebo brány Azure Firewall ve virtuální síti

**Pokyny:** Můžete povolit Azure Private Link povolit přístup k službám Azure PaaS (například SQL Database) a službám hostovaných zákazníkům/partnerů azure přes privátní koncový bod ve vaší virtuální síti. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. 

Chcete-li povolit provoz k dosažení Azure SQL Database, použijte značky služby SQL k povolení odchozího provozu prostřednictvím skupin zabezpečení sítě.

Pravidla virtuální sítě umožňují Azure SQL Database přijímat jenom komunikaci, která se odesílá z vybraných podsítí uvnitř virtuální sítě.

Jak nastavit privátní propojení pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

Použití koncových bodů a pravidel služby virtuální sítě pro databázové servery:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Sledování a protokolování konfigurace a provozu sítí, podsítí a síťových rozhraní

**Pokyny:** Použijte Azure Security Center a navažte doporučení ochrany sítě pro podsíť, do které se nasazuje váš databázový server Azure SQL. 

Pro virtuální počítače Azure (VM), které se budou připojovat k instanci azure SQL databázového serveru, povolte protokoly toku skupiny zabezpečení sítě (NSG) pro skupiny zabezpečení sítě, které chrání tyto virtuální počítače, a odesílat protokoly do účtu úložiště Azure pro auditování provozu. 

Protokoly toku nsg můžete také odesílat do pracovního prostoru Analýzy protokolů a pomocí traffic analytics poskytovat přehled o toku provozu ve vašem cloudu Azure. Některé výhody Traffic Analytics jsou schopnost vizualizovat aktivitu v síti a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzorce toku provozu a určit chybné konfigurace sítě.

Povolení protokolů toku nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Seznamte se se zabezpečením sítě poskytovaným službou Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Jak povolit a používat Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Seznamte se se zabezpečením sítě poskytovaným službou Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrana kritických webových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určené pro službu Azure Apps Service nebo výpočetní prostředky hostující webové aplikace.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odepřít komunikaci se známými škodlivými IP adresami

**Pokyny:** Povolte standard ochrany DDoS ve virtuálních sítích přidružených k instancím serveru SQL Server pro ochranu před distribuovanými útoky odmítnutí služby. Pomocí integrované analýzy hrozeb Centra zabezpečení Azure můžete odepřít komunikaci se známými škodlivými nebo nepoužívanými internetovými IP adresami.

Jak nakonfigurovat ochranu Před sdos:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Seznamte se s integrovanou analýzou hrozeb Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Záznam síťových paketů a protokolů toku

**Pokyny:** Pro virtuální počítače Azure (VM), které se připojí k instanci Azure SQL Database, povolit protokoly toku skupiny zabezpečení sítě (NSG) pro skupiny nsg chrání tyto virtuální počítače a odesílat protokoly do účtu úložiště Azure pro audit provozu. Pokud je to nutné pro zkoumání neobvyklé aktivity, povolte zachytávání paketů Sledování sítě.

Povolení protokolů toku nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit sledování sítě:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Nasazení síťových systémů detekce vniknutí/prevence vniknutí (IDS/IPS)

**Pokyny:** Povolit pokročilou ochranu před internetovými technologiemi (ATP) pro azure SQL database.  Uživatelé obdrží upozornění na podezřelé databázové aktivity, potenciální chyby zabezpečení a útoky injektáže SQL, stejně jako neobvyklé přístup k databázi a dotazy vzory. Pokročilá ochrana před internetovými hrozbami také integruje výstrahy s Azure Security Center. 

Seznamte se s pokročilou ochranou před internetovými hrozbami pro Azure SQL Database a používání ní:https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Správa návštěvnosti webových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určené pro službu Azure Apps Service nebo výpočetní prostředky hostující webové aplikace.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizace složitosti a administrativní režie pravidel zabezpečení sítě

**Pokyny:** Pomocí značek virtuálních síťových služeb definujte ovládací prvky přístupu k síti ve skupinách zabezpečení sítě nebo azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. apimanagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo jako adresy změnit.

Při použití koncových bodů služby pro Azure SQL Database je vyžadováno odchozí na veřejné IP adresy Azure SQL Database: Skupiny zabezpečení sítě (NSG) musí být otevřeny pro IP adresy Azure SQL Database, aby bylo možné připojení. Můžete to provést pomocí značek služby NSG pro Azure SQL Database.

Principy značek služeb s koncovými body služby pro databázi Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

Porozumět značkám služeb a používat je:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Udržovat standardní konfigurace zabezpečení pro síťová zařízení

**Pokyny:** Definujte a implementujte konfigurace zabezpečení sítě pro instance serveru Azure SQL Database pomocí zásad Azure. Obor názvů "Microsoft.Sql" můžete použít k definování vlastních definic zásad nebo můžete použít některou z předdefinovaných definic zásad navržených pro ochranu sítě serveru Azure SQL Database. Příkladem příslušné zásady integrovaného zabezpečení sítě pro server Azure SQL Database by bylo: "SQL Server by měl používat koncový bod služby virtuální sítě".

 

Pomocí plánů Azure můžete zjednodušit rozsáhlá nasazení Azure tak, že zabalením artefaktů prostředí klíčů, jako jsou šablony Azure Resource Management, řízení přístupu na základě rolí (RBAC) a zásady, můžete v jedné definici podrobného plánu zjednodušit. Snadno použít podrobný plán pro nová předplatná a prostředí a doladit řízení a správu prostřednictvím správy verzí.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvořit Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentovat pravidla konfigurace provozu

**Pokyny:** Používejte značky pro skupiny zabezpečení sítě (NSG) a další prostředky související se zabezpečením sítě a tokem provozu. Pro jednotlivá pravidla sítě zabezpečení sítě použijte pole Popis k určení obchodní potřeby a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do nebo ze sítě.

Pomocí některé z předdefinovaných definic zásad Azure souvisejících s označováním, jako je například Vyžadovat značku a její hodnotu, můžete zajistit, aby se všechny prostředky vytvořily pomocí značek, a upozornit vás na existující neoznačené prostředky.

Azure PowerShell nebo Azure CLI můžete použít k vyhledávání nebo provádění akcí na prostředky na základě jejich značek.

Jak vytvářet a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Použití automatizovaných nástrojů ke sledování konfigurace síťových prostředků a zjišťování změn

**Pokyny:** Pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků souvisejících s instancemi serveru Azure SQL Database. Vytvořte výstrahy v rámci Azure Monitor, který se aktivuje, když dojde ke změnám kritických síťových prostředků.

Jak zobrazit a načíst události protokolu aktivit Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak vytvořit výstrahy ve službě Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace naleznete [v tématu Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Použití schválených zdrojů synchronizace času

**Pokyny**: Microsoft udržuje zdroje času pro prostředky Azure. Můžete aktualizovat synchronizaci času pro vaše výpočetní nasazení.

Jak nakonfigurovat synchronizaci času pro výpočetní prostředky Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurace správy centrálního protokolu zabezpečení

**Pokyny:** Povolte auditování pro Azure SQL Database ke sledování událostí databáze a jejich zápis do protokolu auditu v účtu úložiště Azure, v pracovním prostoru Log Analytics nebo v centru událostí.

Kromě toho můžete streamovat telemetrii diagnostiky Azure SQL do Azure SQL Analytics, cloudového řešení, které monitoruje výkon databází Azure SQL, elastických fondů a spravovaných instancí ve velkém měřítku a napříč několika předplatnými. Může vám pomoci shromažďovat a vizualizovat metriky výkonu Azure SQL Database a má integrovanou inteligenci pro řešení potíží s výkonem.

Jak nastavit auditování pro azure sql database:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Jak shromažďovat protokoly platformy a metriky pomocí Azure Monitoru:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

Jak streamovat diagnostiku do Azure SQL Analytics:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Povolení protokolování auditování prostředků Azure

**Pokyny:** Povolte auditování na instanci serveru Azure SQL Database a zvolte umístění úložiště pro protokoly auditování (Azure Storage, Log Analytics nebo Event Hub).

Jak povolit auditování pro Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Shromažďování protokolů zabezpečení z operačních systémů

**Pokyny**: Nepoužije se; tento benchmark je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny:** Při ukládání protokolů Azure SQL Database v pracovním prostoru Analýzy protokolů nastavte dobu uchovávání protokolů podle předpisů vaší organizace pro dodržování předpisů.

Jak nastavit parametry uchovávání protokolů:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="26-monitor-and-review-logs"></a>2.6: Sledování a kontrola protokolů

**Pokyny:** Analyzujte a monitorujte protokoly pro neobvyklé chování a pravidelně kontrolujte výsledky. Pomocí rozšířené ochrany před internetovými hrozbami centra zabezpečení Azure můžete upozornit na neobvyklou aktivitu související s instancí Azure SQL Database. Případně nakonfigurujte výstrahy na základě hodnot metrik nebo položek protokolu aktivit Azure souvisejících s instancemi databáze Azure SQL Database.

Seznamte se s pokročilou ochranou před internetovými hrozbami a s výstrahami pro Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Jak nakonfigurovat vlastní výstrahy pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Povolit upozornění na anomální aktivitu

**Pokyny:** Pomocí Azure Security Center Advanced Threat Protection pro Azure SQL databases pro monitorování a upozorňování na neobvyklé aktivity. Povolte rozšířené zabezpečení dat pro databáze SQL. Rozšířené zabezpečení dat zahrnuje funkce pro zjišťování a klasifikaci citlivých dat, zjišťování a zmírňování potenciálních chyb zabezpečení databáze a zjišťování neobvyklých aktivit, které by mohly znamenat ohrožení databáze.

Seznamte se s pokročilou ochranou před internetovými hrozbami a s výstrahami pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Jak povolit rozšířené zabezpečení dat pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Jak spravovat výstrahy v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizovat protokolování antimalwaru

**Pokyny**: Nepoužije se; pro Azure SQL Server je řešení ochrany proti malwaru spravované společností Microsoft na základní platformě.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="29-enable-dns-query-logging"></a>2.9: Povolení protokolování dotazů DNS

**Pokyny**: Nepoužije se; Protokolování DNS se nevztahuje na Azure SQL Server.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="210-enable-command-line-audit-logging"></a>2.10: Povolení protokolování auditu příkazového řádku

**Pokyny**: Nepoužije se; Auditování příkazového řádku se nevztahuje na Azure SQL Server.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace naleznete [v tématu Zabezpečení: Identita a řízení přístupu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Vedení soupisu administrativních účtů

**Pokyny:** Azure Active Directory (AAD) má předdefinované role, které musí být explicitně přiřazeny a jsou dotazovatelné. Pomocí modulu AAD PowerShell můžete provádět dotazy ad hoc ke zjišťování účtů, které jsou členy skupin pro správu.

Jak získat roli adresáře ve službě Azure AD s PowerShellem:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak získat členy role adresáře ve službě Azure AD s PowerShellem:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Změna výchozích hesel, pokud je to možné

**Pokyny**: Služba Azure Active Directory nemá koncept výchozích hesel. Při zřizování instance Azure SQL Database se doporučuje, abyste se rozhodli integrovat ověřování s Azure Active Directory.

Jak nakonfigurovat a spravovat ověřování Azure Active Directory pomocí Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Použití vyhrazených administrativních účtů

**Pokyny**: Vytvořte zásady a postupy týkající se používání vyhrazených účtů pro správu. Pomocí správy identit a přístupu Centra zabezpečení Azure můžete sledovat počet účtů pro správu.

Principy identity a přístupu Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Použití jednotného přihlašování (SSO) s Azure Active Directory

**Pokyny**: Nepoužije se; zatímco můžete nakonfigurovat azure active directory ověřování pro integraci s Azure SQL Server, jednotné přihlašování není podporováno.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Použití vícefaktorového ověřování pro veškerý přístup založený na službě Azure Active Directory

**Pokyny:** Povolte vícefaktorové ověřování Azure Active Directory (AAD) a postupujte podle doporučení Centra identit a správy přístupu centra zabezpečení Azure.

Jak povolit vícefaktorové povolení v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak sledovat identitu a přístup v rámci Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Pro všechny administrativní úkoly používejte vyhrazené počítače (pracovní stanice s privilegovaným přístupem)

**Pokyny:** Použijte pracovní stanici s privilegovaným přístupem (PAW) s vícefaktorovým ověřováním MFA nakonfigurovaným pro přihlášení a konfiguraci prostředků Azure.

Další informace o pracovních stanicích s privilegovaným přístupem:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové povolení v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokolování a upozornění na podezřelou aktivitu z administrativních účtů

**Pokyny:** Pomocí sestav zabezpečení služby Azure Active Directory pro generování protokolů a výstrah, když dojde k podezřelé nebo nebezpečné aktivity v prostředí.

Pomocí rozšířené ochrany před internetovými hrozbami pro Azure SQL Database můžete zjistit neobvyklé aktivity označující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Jak identifikovat uživatele Azure AD označené pro rizikové aktivity:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak sledovat identitu uživatelů a aktivitu přístupu v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Projděte si rozšířenou ochranu před internetovými hrozbami a potenciální výstrahy:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Správa prostředků Azure z jenom schválených umístění

**Pokyny:** Pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup portálu a azure resource managementu pouze z konkrétních logických seskupení rozsahů IP adres nebo zemí nebo oblastí.

Jak nakonfigurovat pojmenovaná umístění v Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="39-use-azure-active-directory"></a>3.9: Použití služby Azure Active Directory

**Pokyny:** Vytvořte správce služby Azure Active Directory (AAD) pro instance serveru Azure SQL Database.

Jak nakonfigurovat a spravovat ověřování Azure Active Directory pomocí Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

Jak vytvořit a nakonfigurovat instanci AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Pravidelně kontrolujte a slaďujte přístup uživatelů

**Pokyny:** Azure Active Directory (AAD) poskytuje protokoly, které pomáhají zjistit zastaralé účty. Kromě toho můžete pomocí kontrol přístupu azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Přístup uživatelů lze pravidelně kontrolovat, aby bylo zajištěno, že pouze ti praví uživatelé mají trvalý přístup.

Jak používat recenze přístupu k identitám Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Sledování pokusů o přístup k deaktivovaným účtům

**Pokyny:** Konfigurace ověřování Služby Azure Active Directory (AAD) pomocí Azure SQL a vytvoření diagnostických nastavení pro uživatelské účty Služby Azure Active Directory, odesílání protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. Nakonfigurujte požadované výstrahy v pracovním prostoru Log Analytics.

Jak nakonfigurovat a spravovat ověřování Azure Active Directory pomocí Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

Jak integrovat protokoly aktivit Azure do Azure Monitoru:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Upozornění na odchylku chování přihlášení k účtu

**Pokyny:** Pomocí ochrany identity Azure Active Directory (AAD) a detekce rizik nakonfigurujte automatické odpovědi na zjištěné podezřelé akce související s identitami uživatelů. Kromě toho můžete ingestovat data do Azure Sentinelu pro další šetření.

Jak zobrazit přihlášení rizik azure ad:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak nakonfigurovat a povolit zásady rizik a zásady ochrany identity:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Poskytněte společnosti Microsoft přístup k relevantním zákaznickým datům během scénářů podpory

**Pokyny:** Ve scénářích podpory, kde Microsoft potřebuje přístup k zákaznickým datům, poskytuje Azure Customer Lockbox zákazníkům rozhraní ke kontrole a schvalování nebo zamítání požadavků na přístup k datům zákazníků.

Pochopit bezpečnostní okno zákazníka:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace naleznete [v tématu Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Udržovat soupis citlivých informací

**Pokyny:** Pomocí značek můžete při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace,.

Jak vytvářet a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolovat systémy ukládající nebo zpracovávající citlivé informace

**Pokyny**: Implementujte samostatné odběry nebo skupiny pro správu pro vývoj, testování a výrobu. Prostředky by měly být odděleny vnet/podsítě, odpovídajícím způsobem označeny a zabezpečeny v rámci brány zabezpečení sítě nebo brány Azure Firewall. Prostředky, které ukládají nebo zpracovávají citlivá data, by měly být izolovány. Použít soukromý odkaz; nasaďte Azure SQL Server ve virtuální síti a připojujte se soukromě pomocí privátních koncových bodů.

Jak vytvořit další předplatná Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:

https://docs.microsoft.com/azure/governance/management-groups/create

Jak vytvořit a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak nastavit privátní propojení pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Sledování a blokování neoprávněného přenosu citlivých informací

**Pokyny:** Pro Azure SQL databases ukládání nebo zpracování citlivých informací, označte databázi a související prostředky jako citlivé pomocí značek. Nakonfigurujte privátní propojení ve spojení se značkami služby Group sítě na instancích azure sql database, abyste zabránili exfiltraci citlivých informací.

Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.

Jak nakonfigurovat privátní připojení a sítě zabezpečení sítě, aby se zabránilo exfiltraci dat v instancích Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Seznamte se s ochranou zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Šifrování všech citlivých informací při přepravě

**Navádění**: Azure SQL Database zabezpečuje vaše data šifrováním dat v pohybu pomocí zabezpečení transportní vrstvy. SQL Server vynucuje šifrování (SSL/TLS) za všech okolností pro všechna připojení. Tím je zajištěno, že všechna data jsou šifrována "na cestě" mezi klientem a serverem bez ohledu na nastavení Encrypt nebo TrustServerCertificate v připojovacím řetězci.

Principy šifrování Azure SQL při přenosu:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Použití aktivního nástroje pro zjišťování k identifikaci citlivých dat

**Pokyny:** Použijte funkci zjišťování a klasifikace dat databáze Azure SQL Database. Zjišťování a klasifikace dat poskytuje pokročilé funkce integrované do Azure SQL &amp; Database pro zjišťování, klasifikaci a označování chrání citlivá data ve vašich databázích.

Jak používat zjišťování dat a klasifikaci pro Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Použití Azure RBAC k řízení přístupu k prostředkům

**Pokyny:** Použijte Azure Active Directory (AAD) pro ověřování a řízení přístupu k instancím Azure SQL Database.

Jak integrovat Azure SQL Server s Azure Active Directory pro ověřování:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Jak řídit přístup na Azure SQL Serveru:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Použití ochrany před ztrátou dat na hostiteli k vynucení kontroly přístupu

**Pokyny**: Microsoft spravuje základní infrastrukturu pro Azure SQL Database a zavedl přísné ovládací prvky, aby se zabránilo ztrátě nebo vystavení zákaznických dat.

Seznamte se s ochranou zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Šifrování citlivých informací v klidovém stavu

**Pokyny:** Transparentní šifrování dat (TDE) pomáhá chránit Azure SQL Database, Azure SQL spravované instance a Azure Data Warehouse před hrozbou škodlivé offline aktivity šifrováním dat v klidovém stavu. Šifruje a dešifruje databáze, související zálohy a soubory transakčních protokolů v reálném čase, a přitom nevyžaduje změny v aplikaci. Ve výchozím nastavení je transparentní šifrování dat povolené pro všechny nově nasazené databáze Azure SQL. Šifrovací klíč TDE může spravovat společnost Microsoft nebo zákazník.

Jak spravovat transparentní šifrování dat a používat vlastní šifrovací klíče:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokolování a upozorňování na změny kritických prostředků Azure

**Pokyny:** Pomocí Azure Monitor s protokolem aktivit Azure vytvořit výstrahy pro při provádění změn na produkční instance Azure SQL databases a dalších důležitých nebo souvisejících prostředků.

Jak vytvořit výstrahy pro události protokolu aktivit Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace naleznete v [tématu Security Control: Vulnerability Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Spuštění automatizovaných nástrojů pro vyhledávání chyb zabezpečení

**Pokyny:** Povolte rozšířené zabezpečení dat pro Azure SQL Database a postupujte podle doporučení Centra zabezpečení Azure k provádění hodnocení ohrožení zabezpečení na vašich serverech Azure SQL.

Jak spustit hodnocení ohrožení zabezpečení v databázích Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

Jak povolit rozšířené zabezpečení dat:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Jak implementovat doporučení k vyhodnocení ohrožení zabezpečení Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Nasazení řešení pro správu automatických oprav operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Nasazení automatizovaného řešení pro správu softwarových oprav od jiných výrobců

**Pokyny**: Nepoužije se; tento benchmark je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porovnání prohledávačů ohrožení zabezpečení zády k zadní straně

**Pokyny:** Povolte pravidelné opakované kontroly pro instance azure sql database; Tím nakonfigurujete posouzení ohrožení zabezpečení tak, aby jednou týdně automaticky spouštěli prohledávací zařízení v databázi. Souhrn výsledků skenování bude odeslán na vámi uvedenou e-mailovou adresu. Porovnejte výsledky a ověřte, zda byly opraveny chyby zabezpečení.

Jak exportovat zprávu o posouzení chyby zabezpečení v Azure Security Center:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Použití procesu hodnocení rizik a upřednostnění nápravy zjištěných zranitelných míst

**Pokyny:** Použijte výchozí hodnocení rizik (Zabezpečené skóre) poskytované Centrem zabezpečení Azure.

Principy skóre zabezpečení Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace naleznete v [tématu Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Použití Azure Asset Discovery

**Pokyny:** Pomocí Azure Resource Graph dotaz a zjistit všechny prostředky (včetně instancí Azure SQL Server) v rámci předplatného.  Ujistěte se, že máte příslušná (číst) oprávnění ve vašem tenantovi a jsou schopni vytvořit výčet všech předplatných Azure, stejně jako prostředky v rámci vašich předplatných.

I když klasické prostředky Azure může být zjištěna prostřednictvím grafu prostředků, je vysoce doporučeno vytvářet a používat prostředky Azure Resource Manager do budoucna.

Jak vytvářet dotazy pomocí Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak zobrazit předplatná Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Principy Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="62-maintain-asset-metadata"></a>6.2: Udržování metadat datových zdrojů

**Pokyny:** Použijte značky pro prostředky Azure, které poskytují metadata, aby je logicky uspořádali do taxonomie.

Jak vytvořit a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Odstranění neautorizovaných prostředků Azure

**Pokyny:** V případě potřeby použijte značkování, skupiny pro správu a samostatná předplatná k uspořádání a sledování datových zdrojů. Pravidelně odsouhlasete zásoby a zajistěte, aby byly z předplatného včas odstraněny neoprávněné prostředky.

Jak vytvořit další předplatná Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:

https://docs.microsoft.com/azure/governance/management-groups/create

Jak vytvořit a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Udržovat soupis schválených prostředků Azure a softwarových titulů

**Pokyny:** Definování seznamu schválených prostředků Azure a schváleného softwaru pro vaše výpočetní prostředky

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorování neschválených prostředků Azure

**Pokyny**: Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

- Nepovolené typy prostředků

- Povolené typy prostředků

Pomocí Azure Resource Graph můžete dotazovat/zjišťovat prostředky v rámci vašeho předplatného. Ujistěte se, že jsou schváleny všechny prostředky Azure v prostředí.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvářet dotazy pomocí Azure Graphu:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Sledování neschválených softwarových aplikací v rámci výpočetních prostředků

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Odebrání neschválených prostředků Azure a softwarových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="68-use-only-approved-applications"></a>6.8: Používejte pouze schválené aplikace

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="69-use-only-approved-azure-services"></a>6.9: Používejte jenom schválené služby Azure

**Pokyny**: Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

- Nepovolené typy prostředků

- Povolené typy prostředků

Pomocí Azure Resource Graph můžete dotazovat/zjišťovat prostředky v rámci vašeho předplatného. Ujistěte se, že jsou schváleny všechny prostředky Azure v prostředí.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odepřít konkrétní typ prostředku pomocí zásad Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="610-implement-approved-application-list"></a>6.10: Implementace schváleného seznamu žádostí

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro aplikace spuštěné na výpočetních prostředcích.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Omezte možnost uživatelů komunikovat se Správcem prostředků Azure prostřednictvím skriptů

**Pokyny:** Pomocí podmíněného přístupu Azure omezte uživatelům možnost pracovat se Správcem prostředků Azure konfigurací "Blokovat přístup" pro aplikaci Microsoft Azure Management.

Jak nakonfigurovat podmíněný přístup pro blokování přístupu ke Správci prostředků Azure:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Omezení možnosti uživatelů spouštět skripty v rámci výpočetních prostředků

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fyzicky nebo logicky oddělit vysoce rizikové aplikace

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro službu App Service nebo výpočetní prostředky hostující desktopové nebo webové aplikace.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace naleznete v [tématu Zabezpečení: Zabezpečená konfigurace](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny:** Pomocí doporučení Azure Policy nebo Azure Security Center pro servery a databáze Azure SQL můžete udržovat konfigurace zabezpečení pro všechny prostředky Azure.

Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Vytvoření bezpečných konfigurací operačního systému

**Pokyny**: Nepoužije se; toto doporučení určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Udržování zabezpečených konfigurací prostředků Azure

**Pokyny:** Pomocí zásad Azure [odepřít] a [nasadit, pokud neexistují] vynutit zabezpečené nastavení napříč prostředky Azure.

Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Principy efektů zásad Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Udržovat bezpečné konfigurace operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Konfigurace prostředků Azure bezpečně ukládá

**Pokyny:** Pokud používáte vlastní definice zásad Azure, použijte Azure DevOps nebo Azure Repos bezpečně ukládat a spravovat svůj kód.

Jak ukládat kód v Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentace k azure repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpečné ukládání vlastních bitových kopií operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Nasazení nástrojů pro správu konfigurace systému

**Pokyny**: Pomocí aliasů zásad Azure v oboru názvů "Microsoft.SQL" můžete vytvořit vlastní zásady pro výstrahu, audit ování a vynucení konfigurací systému. Kromě toho vytvořte proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Nasazení nástrojů pro správu konfigurace systému pro operační systémy

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementace automatického monitorování konfigurace pro služby Azure

**Pokyny:** Využijte Azure Security Center k provádění základních prohledávese pro vaše servery Azure SQL a databáze.

Jak napravit doporučení v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementace automatizovaného monitorování konfigurace operačních systémů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpečná správa tajných kódů Azure

**Navádění:** Použití Trezoru klíčů Azure k ukládání šifrovacích klíčů pro transparentní šifrování dat Azure SQL Database (TDE).

Jak chránit citlivá data uložená na Azure SQL Serveru a ukládat šifrovací klíče v Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpečná a automatická správa identit

**Pokyny:** Pomocí spravovaných identit můžete poskytovat služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory (AAD). Spravované identity umožňují ověření na libovolné službě, která podporuje ověřování AAD, včetně Azure Key Vault, bez pověření ve vašem kódu.

Kurz: Přístup k Azure SQL použijte spravovanou identitu přiřazenou systémovým virtuálním jám windows:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

Jak nakonfigurovat spravované identity:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminujte nezamýšlenou expozici pověření

**Pokyny:** Implementujte skener pověření k identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů také podpoří přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je azure key vault. 

Jak nastavit skener pověření:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace naleznete v [tématu Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Používejte centrálně spravovaný antimalwarový software

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky. Společnost Microsoft zpracovává antimalwarovou platformu.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Předprohledávací soubory, které se mají nahrát do nevýpočetních prostředků Azure

**Pokyny:** Microsoft anti-malware je povolena na podkladovém hostiteli, který podporuje služby Azure (například Azure App Service), ale neběží na obsah zákazníků.

Předem proskente veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, jako je app service, storage datového jezera, úložiště objektů blob, Azure SQL Server atd. Společnost Microsoft nemá v těchto případech přístup k vašim datům.

Seznamte se s antimalwarovým programem Microsoft pro cloudové služby Azure a virtuální počítače:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Zajistěte aktualizaci antimalwarového softwaru a podpisů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky. Společnost Microsoft zpracovává antimalwarovou platformu.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="data-recovery"></a>Obnovení dat

*Další informace naleznete [v tématu Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zajistěte pravidelné automatické zálohování

**Pokyny:** Chcete-li chránit vaše podnikání před ztrátou dat, Azure SQL Database automaticky vytvoří úplné zálohování databáze týdně, rozdílové zálohování databáze každých 12 hodin a zálohování transakčníprotokol každých 5 – 10 minut. Zálohy jsou uloženy v úložišti RA-GRS po dobu nejméně 7 dnů pro všechny úrovně služeb. Všechny úrovně služeb s výjimkou základní podpory konfigurovatelné doby uchovávání záloh pro obnovení v čase, až 35 dní.

Chcete-li splnit různé požadavky na dodržování předpisů, můžete vybrat různé doby uchovávání pro týdenní, měsíční a/nebo roční zálohy. Spotřeba úložiště závisí na zvolené frekvenci zálohování a dobu uchování.

Seznamte se se zálohami a kontinuitou podnikání pomocí Azure SQL Serveru:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Sdíleno

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Proveďte kompletní zálohy systému a zálohujte všechny klienty spravované klíče

**Navádění**: Azure SQL Database automaticky vytvoří zálohy databáze, které jsou uchovávány mezi 7 a 35 dny a používá Azure pro čtení geograficky redundantní úložiště (RA-GRS) k zajištění, že jsou zachovány i v případě, že datové centrum není k dispozici. Tyto zálohy jsou vytvářeny automaticky. V případě potřeby povolte dlouhodobé geograficky redundantní zálohy pro vaše azure SQL databáze.

Pokud používáte klíče spravované zákazníkem pro transparentní šifrování dat, ujistěte se, že jsou vaše klíče zálohovány.

Principy zálohování na Azure SQL Serveru:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Jak zálohovat klíče trezoru klíčů v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny:** Zajistěte možnost pravidelně provádět obnovení dat obsahu v rámci služby Azure Backup. V případě potřeby otestujte obsah obnovení do izolované sítě VLAN. Otestujte obnovení zálohovaných klíčů spravovaných zákazníkem.

Jak obnovit klíče trezoru klíčů v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

Jak obnovit zálohy Azure SQL Database pomocí obnovení v čase:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zajištění ochrany záloh a klíčů spravovaných zákazníkem

**Pokyny:** Povolit obnovitelné odstranění v trezoru klíčů Azure chránit klíče před náhodným nebo škodlivým odstraněním.

Jak povolit obnovitelné odstranění v trezoru klíčů:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace naleznete [v tématu Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Vytvoření průvodce odezvou na incidenty

**Pokyny**: Ujistěte se, že existují písemné plány reakce na incidenty, které definují role personálu, jakož i fáze zpracování incidentů / správy.

Jak nakonfigurovat automatizaci pracovních postupů v Centru zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Vytvoření postupu hodnocení incidentů a stanovení priorit

**Pokyny**: Centrum zabezpečení přiřazuje výstrahám závažnost, která vám pomůže určit pořadí, ve kterém se účastníte jednotlivých výstrah, takže když dojde k ohrožení zabezpečení prostředku, můžete se k němu hned dostat. Závažnost je založena na tom, jak sebevědomý Security Center je v hledání nebo analytice slouží k vydání výstrahy, stejně jako úroveň spolehlivosti, že tam byl škodlivý záměr za aktivitu, která vedla k upozornění.

Výstrahy zabezpečení v Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-alerts-overview



**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="103-test-security-response-procedures"></a>10.3: Zkušební postupy odezvy zabezpečení

**Pokyny**: Proveďte cvičení k testování schopností reakce na incidenty vašich systémů na pravidelné kadenci. Identifikujte slabá místa a mezery a podle potřeby plán revidujte.

Můžete odkazovat na publikaci NIST: Průvodce testováním, školením a cvičením pro plány a možnosti IT:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Poskytněte kontaktní údaje bezpečnostních incidentů a nakonfigurujte upozornění na bezpečnostní incidenty

**Pokyny**: Kontaktní informace o bezpečnostních incidentech společnost Microsoft použije k tomu, aby vás kontaktovala, pokud centrum MSRC (Microsoft Security Response Center) zjistí, že k vašim datům získala přístup nezákonná nebo neoprávněná strana.

Jak nastavit kontakt zabezpečení Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Začleňte výstrahy zabezpečení do systému reakce na incidenty

**Pokyny:** Exportujte výstrahy a doporučení Centra zabezpečení Azure pomocí funkce Nepřetržitý export. Nepřetržitý export umožňuje exportovat výstrahy a doporučení buď ručně, nebo průběžně. Datový konektor Azure Security Center můžete použít k streamování výstrah do Sentinelu.

Jak nakonfigurovat nepřetržitý export:

https://docs.microsoft.com/azure/security-center/continuous-export

Jak streamovat výstrahy do Azure Sentinelu:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizace reakce na výstrahy zabezpečení

**Pokyny:** Pomocí funkce Automatizace pracovních postupů v Centru zabezpečení Azure můžete automaticky aktivovat odpovědi prostřednictvím "Logic Apps" na výstrahy zabezpečení a doporučení.

Konfigurace automatizace pracovních postupů a aplikací logiky:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace naleznete [v tématu Bezpečnostní kontrola: Penetrační testy a cvičení červeného týmu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Proveďte pravidelné penetrační testování prostředků Azure a zajistěte nápravu všech kritických zjištění zabezpečení do 60 dnů

**Pokyny**: Postupujte podle pravidel společnosti Microsoft a ujistěte se, že penetrační testy neporušují zásady společnosti Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Další informace o strategii společnosti Microsoft a provádění červených týmů a testování pronikání živých webů proti cloudové infrastruktuře, službám a aplikacím společnosti Microsoft naleznete zde:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

## <a name="next-steps"></a>Další kroky

- Podívejte se na [srovnávací test zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Další informace o [plánech zásad zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
