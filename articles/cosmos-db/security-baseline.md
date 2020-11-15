---
title: Základní hodnoty zabezpečení Azure pro Cosmos DB
description: Základní hodnoty zabezpečení Azure pro Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 99cad2a4218995ad10488d97ce19eeef36b642ae
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636992"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Základní hodnoty zabezpečení Azure pro Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Základní plán zabezpečení Azure pro Cosmos DB obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Chraňte prostředky pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network

**Pokyny** : pomocí privátního odkazu Azure se můžete připojit k účtu Azure Cosmos prostřednictvím privátního koncového bodu. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Riziko exfiltrace dat můžete také snížit konfigurací striktní sady odchozích pravidel pro skupinu zabezpečení sítě (NSG) a přidružením tohoto NSG k vaší podsíti.

K zabezpečení účtu Azure Cosmos můžete použít také koncové body služby. Povolením koncového bodu služby můžete nakonfigurovat účty Azure Cosmos tak, aby povolovaly přístup jenom z konkrétní podsítě virtuální sítě Azure. Po povolení koncového bodu služby Azure Cosmos DB můžete omezit přístup k účtu Azure Cosmos pomocí připojení z podsítě ve virtuální síti.

Data uložená v účtu Azure Cosmos můžete také zabezpečit pomocí bran firewall protokolu IP. Azure Cosmos DB podporuje řízení přístupu na základě IP adres pro podporu příchozích bran firewall. Bránu firewall protokolu IP můžete nastavit na účtu Azure Cosmos pomocí Azure Portal, Azure Resource Manager šablon nebo prostřednictvím rozhraní příkazového řádku Azure CLI nebo Azure PowerShell.

Přehled privátních odkazů Azure: https://docs.microsoft.com/azure/private-link/private-link-overview

Postup konfigurace privátního koncového bodu pro Azure Cosmos DB:  https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Vytvoření skupiny zabezpečení sítě s konfigurací zabezpečení:  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Postup konfigurace brány firewall protokolu IP v Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuální sítě, podsítí a síťových karet.

**Doprovodné** materiály: použijte Azure Security Center a sledujte doporučení pro ochranu sítě, která vám pomůžou zabezpečit síťové prostředky týkající se vašeho účtu Azure Cosmos.

Když jsou virtuální počítače nasazené ve stejné virtuální síti jako účet Azure Cosmos, můžete použít skupinu zabezpečení sítě (NSG) a snížit tak riziko exfiltraceí dat. Povolte protokoly toku NSG a odešlete protokoly do účtu Azure Storage pro audity provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

Pochopení zabezpečení sítě, které poskytuje Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Jak povolit protokoly toku NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit a použít Analýza provozu: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Pokyny** : pomocí funkce sdílení prostředků mezi zdroji (CORS) Povolte webové aplikaci spuštěné v jedné doméně přístup k prostředkům v jiné doméně. Webové prohlížeče implementují omezení zabezpečení označované jako zásady stejného původu, které brání webové stránce v volání rozhraní API v jiné doméně. CORS ale poskytuje zabezpečený způsob, jak, aby zdrojová doména mohla volat rozhraní API v jiné doméně. Po povolení podpory CORS pro váš účet Azure Cosmos se vyhodnotí jenom ověřené požadavky, které určují, jestli jsou povolené podle pravidel, která jste zadali.

Konfigurace sdílení prostředků mezi zdroji: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: použití rozšířené ochrany před internetovými útoky (ATP) pro Azure Cosmos DB. ATP pro Azure Cosmos DB poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům Azure Cosmos nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby a integrovat je se systémy centrálního monitorování zabezpečení.

Povolte DDoS Protection Standard ve virtuálních sítích přidružených k vašim Azure Cosmos DB instancím, abyste se mohli chránit před útoky DDoS. K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

Jak nakonfigurovat Azure Cosmos DB rozšířené ochrany před internetovými útoky: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Jak nakonfigurovat DDoS Protection: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Pochopení Azure Security Center integrované analýzy hrozeb: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: záznam síťových paketů a protokolů toků

**Pokyny** : povolení protokolu Flow skupiny zabezpečení sítě (NSG) a odesílání protokolů do účtu úložiště pro audit provozu. Protokoly toku NSG můžete odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

Jak povolit protokoly toku NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit a použít Analýza provozu: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: použití rozšířené ochrany před internetovými útoky (ATP) pro Azure Cosmos DB. ATP pro Azure Cosmos DB poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům Azure Cosmos nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby a integrovat je se systémy centrálního monitorování zabezpečení. 

Jak nakonfigurovat Cosmos DB rozšířené ochrany před internetovými útoky: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: nepoužitelné; doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: u prostředků, které potřebují přístup k vašemu účtu Azure Cosmos, použijte značky služby Virtual Network k definování řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. AzureCosmosDB) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Další informace o použití značek služeb: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny** : definování a implementace standardních konfigurací zabezpečení pro síťové prostředky pomocí Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft.DocumentDB a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě instancí služby Azure Cosmos DB. Můžete také využít integrované definice zásad pro Azure Cosmos DB, například:

- Nasazení rozšířené ochrany před internetovými útoky pro účty Cosmos DB

- Cosmos DB by měl používat koncový bod služby virtuální sítě

Plány Azure můžete použít také ke zjednodušení rozsáhlých nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, řízení přístupu na základě role Azure (Azure RBAC) a zásady v jediné definici podrobného plánu. V rámci správy verzí můžete snadno použít podrobný plán na nová předplatná, prostředí a vyladit řízení a správu.

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Postup vytvoření Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro síťové prostředky přidružené k vašemu nasazení Azure Cosmos DB, aby je bylo možné logicky uspořádat do taxonomie.

Vytváření a používání značek: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny** : pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vašimi Azure Cosmos DB instancemi. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích. 

Jak zobrazit a načíst události protokolu aktivit Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Postup při vytváření výstrah v Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Pokyny** : Společnost Microsoft udržuje zdroj času používaný pro prostředky Azure, například Azure Cosmos DB pro časová razítka v protokolech.


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny** : ingestování protokolů prostřednictvím Azure monitor k agregaci dat zabezpečení vygenerovaných Azure Cosmos DB. V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty úložiště pro dlouhodobé a archivační úložiště. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo incidentu zabezpečení a řízení událostí zabezpečení (SIEM) třetí strany. 

Postup povolení diagnostických protokolů pro Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/logging

Jak připojit Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: Povolte nastavení diagnostiky pro Azure Cosmos DB a odešlete protokoly do pracovního prostoru Log Analytics nebo účtu úložiště. Nastavení diagnostiky v Azure Cosmos DB se používají ke shromažďování protokolů prostředků. Tyto protokoly jsou zachyceny na žádost a jsou také označovány jako "protokoly roviny dat". Mezi Příklady operací s rovinou dat patří odstranění, vložení a čtení. Můžete také povolit nastavení diagnostiky protokolu aktivit Azure a odeslat je do stejného Log Analytics pracovního prostoru.

Postup povolení nastavení diagnostiky pro Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/logging

Jak povolit nastavení diagnostiky pro protokol aktivit Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v Azure monitor nastavte dobu uchování protokolu pro Log Analytics pracovní prostory, které jsou přidružené k vašim Azure Cosmos DB instancí podle předpisů pro dodržování předpisů vaší organizace.

Postup nastavení parametrů uchovávání protokolů: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: můžete provádět dotazy v Log Analytics pracovní prostor pro hledání podmínek, identifikaci trendů, analýze vzorů a poskytování mnoha dalších přehledů na základě Azure Cosmos DB protokolů, které jste shromáždili.

Jak provádět dotazy pro Azure Cosmos DB v pracovních prostorech Log Analytics: https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé

**Doprovodné** materiály: v Azure Security Center povolit rozšířenou ochranu před internetovými útoky pro Azure Cosmos DB monitorovat aktivity neobvyklé v protokolech zabezpečení a událostech. Povolte nastavení diagnostiky v Azure Cosmos DB a odešlete protokoly do pracovního prostoru Log Analytics.

 

Pracovní prostor Log Analytics můžete také připojit do Azure Sentinel, protože poskytuje řešení pro automatizované odpovědi na orchestraci zabezpečení (společnosti). To umožňuje vytvořit playbooky (automatizovaná řešení) a použít je k nápravě problémů zabezpečení. Kromě toho můžete pomocí Azure Monitor vytvořit vlastní výstrahy protokolu v pracovním prostoru Log Analytics.

Seznam výstrah ochrany před hrozbami pro Azure Cosmos DB: https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Jak připojit Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Vytváření, zobrazování a správa výstrah protokolu pomocí Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: nepoužitelné; Azure Cosmos DB nezpracovává ani nevytváří protokoly související s malwarem.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné** materiály: nepoužitelné; Azure Cosmos DB nezpracovává ani nevytváří protokoly související se službou DNS.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: podokno identita a řízení přístupu (IAM) můžete použít v Azure Portal ke konfiguraci řízení přístupu na základě role Azure (Azure RBAC) a udržování inventáře na Azure Cosmos DBch prostředcích. Role se aplikují na uživatele, skupiny, instanční objekty a spravované identity ve službě Active Directory. Pro jednotlivce a skupiny můžete použít předdefinované role nebo vlastní role.

Azure Cosmos DB poskytuje vestavěnou službu Azure RBAC pro běžné scénáře správy v Azure Cosmos DB. Jednotlivec, který má profil ve Azure Active Directory (AD), může tyto role Azure přiřadit uživatelům, skupinám, instančním objektům nebo spravovaným identitám, aby mohl udělit nebo odepřít přístup k prostředkům a operacím s prostředky Azure Cosmos DB.

K vyhledání účtů, které jsou členy skupin pro správu, můžete také použít modul Azure AD PowerShell k provádění dotazů ad hoc. 

Kromě toho mohou být některé akce v Azure Cosmos DB řízeny pomocí Azure Active Directory a primárních klíčů pro konkrétní účet.  K řízení přístupu ke klíčům použijte nastavení účtu disableKeyBasedMetadataWriteAccess.

Pochopení řízení přístupu na základě role v Azure v Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Sestavujte vlastní role pomocí Azure Cosmos DBch akcí (Microsoft.Docobor názvů umentDB): https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Vytvořte novou roli v Azure Active Directory: https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Postup získání role adresáře v Azure Active Directory pomocí prostředí PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true

Jak získat členy role adresáře v Azure Active Directory pomocí prostředí PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true

Omezení přístupu uživatelů k datovým operacím: https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny** : koncept výchozích nebo prázdných hesel neexistuje ve vztahu k Azure AD nebo Azure Cosmos DB. Místo toho Azure Cosmos DB používá dva typy klíčů k ověřování uživatelů a poskytování přístupu k jeho datům a prostředkům; primární klíče a tokeny prostředků. Klíče lze kdykoli znovu vygenerovat.

Princip zabezpečeného přístupu k datům v Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Postup opětovného generování Azure Cosmos DB klíčů: https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Postup při programovém přístupu ke klíčům pomocí Azure Active Directory: https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : sdílená

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: nepoužitelné; Azure Cosmos DB nepodporuje účty správců.  Veškerý přístup je integrovaný do Azure Active Directory a řízení přístupu na základě role Azure (Azure RBAC).



**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné** materiály: Azure Cosmos DB používá dva typy klíčů k autorizaci uživatelů a nepodporuje jednotné Sign-On (SSO) na úrovni datové roviny. Přístup k rovině ovládacího prvku pro Cosmos DB je k dispozici prostřednictvím REST API a podporuje jednotné přihlašování. Pro ověření nastavte hlavičku autorizace pro vaše požadavky na JSON Web Token, které získáte z Azure Active Directory.

Principy služby Azure Database for Cosmos DB REST API: https://docs.microsoft.com/rest/api/cosmos-db/

Vysvětlení jednotného přihlašování s Azure Active Directory: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: povolte Azure Active Directory Multi-Factor Authentication a sledujte Azure Security Center doporučení pro správu identit a přístupu.

Jak povolit vícefaktorové ověřování v Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorovat identitu a přístup v rámci Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny** : použití pracovních stanic s privilegovaným přístupem (privilegovaným přístupem) s Multi-Factor Authentication nakonfigurovaným pro přihlášení k prostředkům Azure a jejich konfigurace.

Další informace o pracovních stanicích s privilegovaným přístupem: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové ověřování v Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: protokolování a upozornění na podezřelou aktivitu z účtů pro správu

**Doprovodné** materiály: použití rozšířené ochrany před internetovými útoky (ATP) pro Azure Cosmos DB. ATP pro Azure Cosmos DB poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům Azure Cosmos nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby a integrovat je se systémy centrálního monitorování zabezpečení. 

Kromě toho můžete použít Azure Active Directory (AD) Privileged Identity Management (PIM) pro generování protokolů a výstrah, když dojde k podezřelé nebo nebezpečné aktivitě v prostředí.

Pomocí zjišťování rizik Azure AD můžete zobrazit výstrahy a sestavy týkající se rizikového chování uživatelů.

Postup nasazení Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Vysvětlení zjišťování rizik Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny** : umožňuje nakonfigurovat podmínku umístění zásad podmíněného přístupu a spravovat pojmenovaná umístění. Pomocí pojmenovaných umístění můžete vytvořit logická seskupení rozsahů IP adres nebo zemí a oblastí. Můžete omezit přístup k citlivým prostředkům, jako jsou například instance Azure Cosmos DB, do nakonfigurovaných pojmenovaných umístění.

Jak nakonfigurovat pojmenovaná umístění v Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné** materiály: jako centrální ověřování a systém autorizací použijte Azure Active Directory (AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Postup vytvoření a konfigurace Azure Active Directory instance: https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Jak nakonfigurovat a spravovat Azure Active Directory ověřování pomocí Azure SQL: https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Kromě toho můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatele se může pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

Jak používat kontroly přístupu Azure identity: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorování pokusů o přístup k deaktivovaným účtům

**Doprovodné** materiály: můžete vytvořit nastavení diagnostiky pro Azure Active Directory uživatelské účty, Odeslat protokoly auditu a protokoly přihlášení do pracovního prostoru Log Analytics, kde můžete nakonfigurovat požadované výstrahy.

Jak integrovat protokoly aktivit Azure do Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Doprovodné** materiály: použití rozšířené ochrany před internetovými útoky (ATP) pro Azure Cosmos DB. ATP pro Azure Cosmos DB poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům Azure Cosmos nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby a integrovat je se systémy centrálního monitorování zabezpečení. 

Pomocí funkce Azure AD Identity Protection a detekce rizik můžete také nakonfigurovat automatizované odezvy na zjištěné podezřelé akce týkající se identit uživatelů. Kromě toho můžete protokoly ingestovat do služby Azure Sentinel pro další šetření.

Postup zobrazení Azure Active Directory rizikových přihlášení: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak nakonfigurovat a povolit zásady pro rizika ochrany identity: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak připojit Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: aktuálně není k dispozici; Customer Lockbox ještě není pro službu Azure Database for Cosmos DB podporované.

Seznam podporovaných služeb Customer Lockbox: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Odpovědnost** : netýká se

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: pomocí značek pomáhají při sledování Azure Cosmos DB instancí, které ukládají nebo zpracovávají citlivé informace.

Vytváření a používání značek: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny** : implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Instance Azure Cosmos DB jsou odděleny virtuální sítí nebo podsítí, vhodně označené a zabezpečené v rámci skupiny zabezpečení sítě (NSG) nebo Azure Firewall. Instance Azure Cosmos DB ukládající citlivé údaje by měly být izolované. Pomocí privátního odkazu Azure se můžete připojit k účtu Azure Cosmos DB instance prostřednictvím privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v podsíti v rámci vaší virtuální sítě. Pak můžete omezit přístup k vybraným privátním IP adresám. 

Jak vytvořit další předplatná Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Postup vytvoření skupin pro správu: https://docs.microsoft.com/azure/governance/management-groups/create

Vytváření a používání značek: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Postup konfigurace privátního koncového bodu pro Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Vytvoření skupiny zabezpečení sítě s konfigurací zabezpečení: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Pokyny** : můžete nasadit rozšířenou ochranu před internetovými útoky pro Azure Cosmos DB, která detekuje aktivity neobvyklé s neobvyklými a potenciálně škodlivými pokusy o přístup k databázím nebo jejich zneužití. V současné době může aktivovat následující výstrahy:

- Přístup z neobvyklých umístění

- Neobvyklé extrakce dat

Kromě toho při použití virtuálních počítačů pro přístup k instancím Azure Cosmos DB použijte privátní linku, bránu firewall, skupiny zabezpečení sítě a značky služeb, aby se snížila možnost exfiltraceí dat. Společnost Microsoft spravuje základní infrastrukturu pro Azure Cosmos DB a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

Jak nakonfigurovat Cosmos DB rozšířené ochrany před internetovými útoky: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Pochopení ochrany zákaznických dat v Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center** : Ano

**Odpovědnost** : sdílená

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: všechna připojení k Azure Cosmos DB podporují protokol HTTPS. Azure Cosmos DB podporuje také protokol TLS 1.2. Je možné vyhovět minimální verzi TLS na straně serveru. Pokud to chcete udělat, kontaktujte prosím [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) .

Přehled zabezpečení Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-security

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: automatické funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Cosmos DB. Pro klasifikaci a analýzu dat ale můžete použít integraci služby Azure Kognitivní hledání. Pokud je to nutné pro účely dodržování předpisů, můžete také implementovat řešení třetí strany.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

Index Azure Cosmos DB data pomocí Azure Kognitivní hledání: https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp ; BC =/azure/cosmos-db/breadcrumb/toc.jszapnuto

Pochopení ochrany zákaznických dat v Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Odpovědnost** : sdílená

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Doprovodné** materiály: Azure Cosmos DB poskytuje integrované řízení přístupu na základě role Azure (Azure RBAC) pro běžné scénáře správy v Azure Cosmos DB. Jednotlivec, který má profil v Azure Active Directory může přiřadit tyto role Azure uživatelům, skupinám, instančním objektům nebo spravovaným identitám pro udělení nebo odepření přístupu k prostředkům a operacím na Azure Cosmos DBch prostředcích. Přiřazování rolí je vymezené jenom na úrovni řízení a přístup, což zahrnuje přístup k účtům Azure Cosmos, databázím, kontejnerům a nabídkám (propustnost).

Jak implementovat službu Azure RBAC v Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

Společnost Microsoft spravuje základní infrastrukturu pro Cosmos DB a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

Pochopení ochrany zákaznických dat v Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: všechna uživatelská data uložená v Cosmos DB jsou ve výchozím nastavení šifrována v klidovém stavu. Neexistují žádné ovládací prvky, které by bylo možné vypnout. Azure Cosmos DB používá šifrování AES-256 ve všech oblastech, kde je účet spuštěný.

Ve výchozím nastavení spravuje Microsoft klíče, které slouží k šifrování dat v účtu Azure Cosmos. Volitelně můžete zvolit, že se má přidat druhá vrstva šifrování s vlastními klíči.

Principy šifrování v klidovém umístění pomocí Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Princip správy klíčů pro šifrování v klidovém umístění pomocí Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Postup konfigurace klíčů spravovaných zákazníkem pro váš účet Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : sdílená

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají v produkčních instancích Azure Cosmos DB.

Vytváření upozornění pro události protokolu aktivit Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Vytváření upozornění pro události protokolu aktivit Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](../security/benchmarks/security-control-vulnerability-management.md)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: postupujte podle doporučení Azure Security Center pro vaše Azure Cosmos DB instance. 

Microsoft provádí systémovou opravu a správu ohrožení zabezpečení na podkladových hostitelích, kteří podporují vaše Azure Cosmos DB instance. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

Podporované funkce dostupné v Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Monitorování Azure Security Center** : Ano

**Odpovědnost** : sdílená

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: nasazení automatizovaného řešení pro správu oprav softwaru třetí strany

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: použijte Azure Asset Discovery.

**Pokyny** : pomocí Azure Portal nebo grafu prostředků Azure můžete v rámci svých předplatných zjistit všechny prostředky (bez omezení Azure Cosmos DB, ale taky včetně prostředků, jako jsou výpočetní prostředky, jiné úložiště, sítě, porty a protokoly atd.).  Ujistěte se, že máte ve svém tenantovi příslušná oprávnění a že máte v rámci předplatných všechny odběry Azure i prostředky.

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

Jak vytvářet dotazy pomocí Azure Resource graphu: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak zobrazit vaše předplatná Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0&preserve-view=true

Principy řízení přístupu na základě role v Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek u instancí Azure Cosmos DB a souvisejících prostředků s metadaty k logickému uspořádání do taxonomie.

Vytváření a používání značek: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Které Azure Cosmos DB prostředky podporují značky: https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků, mimo jiné Azure Cosmos DB prostředky. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Jak vytvořit další předplatná Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Postup vytvoření Skupiny pro správu: https://docs.microsoft.com/azure/governance/management-groups/create

Vytváření a používání značek: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: udržování inventáře schválených prostředků Azure a softwarových titulů

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Kromě toho můžete pomocí grafu prostředků Azure dotazovat nebo zjišťovat prostředky v rámci předplatných.

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvářet dotazy pomocí Azure graphu: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné** materiály: nepoužitelné; Tato standardní hodnota je určená pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky a Azure jako celek.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků 

- Povolené typy prostředků

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odepřít konkrétní typ prostředku s Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="610-implement-approved-application-list"></a>6,10: Implementujte schválený seznam aplikací

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: Omezte schopnost uživatelů pracovat se AzureResources Managerem prostřednictvím skriptů.

**Pokyny** : pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management. To může zabránit vytváření a změnám prostředků v rámci vysoce zabezpečeného prostředí.

Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny** : definování a implementace standardních konfigurací zabezpečení pro Cosmos DB instance pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft.DocumentDB můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace instancí Cosmos DB. Můžete také využít integrované definice zásad pro Azure Cosmos DB, například:

- Nasazení rozšířené ochrany před internetovými útoky pro účty Cosmos DB

- Cosmos DB by měl používat koncový bod služby virtuální sítě

Jak zobrazit dostupné aliasy Azure Policy: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0&preserve-view=true

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Pochopení Azure Policych účinků: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny** : Pokud používáte vlastní definice Azure Policy pro Cosmos DB nebo související prostředky, použijte Azure Repos k bezpečnému ukládání a správě kódu.

Azure Repos dokumentaci: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops&preserve-view=truehttps://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: nasazení nástrojů pro správu konfigurace systému

**Pokyny** : použijte aliasy Azure Policy v oboru názvů "Microsoft.DocumentDB" k vytvoření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: nasaďte nástroje pro správu konfigurace systému pro operační systémy.

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Implementujte automatizované monitorování konfigurace pro služby Azure.

**Pokyny** : použijte aliasy Azure Policy v oboru názvů "Microsoft.DocumentDB" k vytvoření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. K automatickému vymáhání konfigurací pro vaše Azure Cosmos DB instance a související prostředky použijte Azure Policy [audit], [Deny] a [nasazení, pokud neexistuje]. 

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: u virtuálních počítačů Azure nebo webových aplikací, které běží na Azure App Service slouží pro přístup k instancím Azure Cosmos DB, použijte identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů Azure Cosmos DB. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

Jak integrovat se spravovanými identitami Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Postup vytvoření Key Vault: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Ověření Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Jak přiřadit zásadu přístupu Key Vault: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: u virtuálních počítačů Azure nebo webových aplikací, které běží na Azure App Service slouží pro přístup k instancím Azure Cosmos DB, použijte identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů Azure Cosmos DB.

Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou v Azure Active Directory (AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

Postup konfigurace spravovaných identit: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Jak integrovat se spravovanými identitami Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny** : implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

Jak nastavit skener přihlašovacích údajů: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky. Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure App Service), ale neběží na zákaznickém obsahu.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny** : Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure App Service), ale neběží na zákaznickém obsahu.

Vaše zodpovědnost za všechny soubory nahrané do nevýpočetních prostředků Azure, včetně Azure Cosmos DB, je vaše zodpovědnost. Microsoft nemůže získat přístup k zákaznickým datům, a proto nemůže za vás provádět kontrolu proti malwarovým kontrolám zákaznického obsahu.


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: nepoužitelné; Srovnávací test je určený pro výpočetní prostředky. Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure, ale neběží na zákaznickém obsahu.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: Azure Cosmos DB přebírá snímky vašich dat každé čtyři hodiny. Všechny zálohy se ukládají samostatně ve službě úložiště a tyto zálohy se globálně replikují z hlediska odolnosti proti regionálním katastrofám. V jednom okamžiku se zachová jenom poslední dva snímky. Pokud se však kontejner nebo databáze odstraní, Azure Cosmos DB zachová stávající snímky daného kontejneru nebo databáze po dobu 30 dnů. Kontaktujte podporu Azure a požádejte o obnovení ze zálohy.

Principy Azure Cosmos DB automatizovaného zálohování: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

**Doprovodné** materiály: Azure Cosmos DB automaticky provede zálohování vašich dat v pravidelných intervalech. Pokud dojde k odstranění databáze nebo kontejneru, můžete odeslat lístek podpory nebo zavolat podporu Azure, která obnoví data z automatických online zálohování. Podpora Azure je dostupná jenom pro vybrané plány, jako je standard, vývojář a plány vyšší než. Aby bylo možné obnovit konkrétní snímek zálohy, Azure Cosmos DB vyžaduje, aby data byla k dispozici po dobu trvání cyklu zálohování pro daný snímek. 

Pokud chcete ukládat přihlašovací údaje pro instance Cosmos DB pomocí Key Vault, zajistěte pravidelné automatizované zálohování vašich klíčů.

Vysvětlení Azure Cosmos DB automatizovaného zálohování: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Postup obnovení dat v Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Postup zálohování klíčů Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Odpovědnost** : sdílená

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

**Doprovodné** materiály: Pokud se databáze nebo kontejner odstraní, můžete vytvořit lístek podpory nebo zavolat podporu Azure, která obnoví data z automatických online zálohování. Podpora Azure je dostupná jenom pro vybrané plány, jako je standard, vývojář a plány vyšší než. Aby bylo možné obnovit konkrétní snímek zálohy, Azure Cosmos DB vyžaduje, aby data byla k dispozici po dobu trvání cyklu zálohování pro daný snímek.

Testovací obnovení tajných kódů uložených v Azure Key Vault pomocí prostředí PowerShell. Rutina Restore-AzureKeyVaultKey vytvoří klíč v zadaném trezoru klíčů. Tento klíč je replikou záložního klíče ve vstupním souboru a má stejný název jako původní klíč.

Vysvětlení Azure Cosmos DB automatizovaného zálohování:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Postup obnovení dat v Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Postup obnovení Azure Key Vault tajných kódů:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0&preserve-view=true

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : sdílená

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

**Doprovodné** materiály: vzhledem k tomu, že všechna uživatelská data uložená v Cosmos DB jsou šifrována v klidovém stavu a v přenosu, není nutné provádět žádnou akci. Dalším způsobem, jak to dát, je toto šifrování v klidovém stavu "zapnuto". Neexistují žádné ovládací prvky, které by bylo možné vypnout nebo zapnout. Azure Cosmos DB používá šifrování AES-256 ve všech oblastech, kde je účet spuštěný.

Povolí Soft-Delete v Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění.

Pochopení šifrování dat v Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Postup povolení Soft-Delete v Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorování Azure Security Center** : Ano

**Odpovědnost** : sdílená

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny** : Vytvoření Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů z detekce až po přezkoumání po jednotlivých událostech.

K podpoře při vytváření vlastního plánu odpovědí na incidenty můžete také využít příručku pro zpracování incidentů v počítači s NIST. https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Postup konfigurace automatizace pracovních postupů v rámci Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomie centra Microsoft Security Response Center: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak si nejste jistí, jak si Security Center ve vyhledávání, nebo na analýzu, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, že při aktivitě, která vedla k výstraze, byl škodlivý záměr.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

Přečtěte si téma publikace NIST: Průvodce pro testování, školení a cvičení programů pro plány a možnosti IT: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana.  Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

Jak nastavit Azure Security Center kontakt zabezpečení: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat ověřovací data výstrah.

Postup konfigurace průběžného exportu: https://docs.microsoft.com/azure/security-center/continuous-export

Jak streamovat výstrahy do Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech důležitých zjištění zabezpečení do 60 dnů.

**Doprovodné** materiály: Využijte pravidla zapojení Microsoftu, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

V této části najdete další informace o strategii a provádění testování na základě červeného týmu a testování průniku na cloudové infrastruktuře, služby a aplikace spravované Microsoftem:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : sdílená

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [plánech zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)