---
title: Základ zabezpečení Azure pro Cosmos DB
description: Základ zabezpečení Azure pro Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fd2706b6a6880d7c9454619e1315b2b5b5404561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244269"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Základ zabezpečení Azure pro Cosmos DB

Základ zabezpečení Azure pro Cosmos DB obsahuje doporučení, která vám pomůžou zlepšit stav zabezpečení vašeho nasazení.

Směrný plán pro tuto službu vychází z [azure security benchmark verze 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure s našimi pokyny pro osvědčené postupy.

Další informace naleznete v [tématu Přehled směrného plánu zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace naleznete v [tématu Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrana prostředků pomocí skupin zabezpečení sítě nebo brány Azure Firewall ve virtuální síti

**Pokyny:** Pomocí Azure Private Link se můžete připojit k účtu Azure Cosmos prostřednictvím privátního koncového bodu. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Riziko exfiltrace dat můžete také snížit konfigurací přísné sady odchozích pravidel pro skupinu zabezpečení sítě (NSG) a přidruženou skupinou zabezpečení sítě k podsíti.

Koncové body služby můžete také použít k zabezpečení účtu Azure Cosmos. Povolením koncového bodu služby můžete nakonfigurovat účty Azure Cosmos tak, aby umožňovaly přístup pouze z určité podsítě virtuální sítě Azure. Jakmile je koncový bod služby Azure Cosmos DB povolen, můžete omezit přístup k účtu Azure Cosmos pomocí připojení z podsítě ve virtuální síti.

Data uložená v účtu Azure Cosmos můžete také zabezpečit pomocí ip firewallů. Azure Cosmos DB podporuje ovládací prvky přístupu založené na PROTOKOLU IP pro příchozí podporu brány firewall. Bránu FIREWALL IP na účtu Azure Cosmos můžete nastavit pomocí portálu Azure, šablon Azure Resource Manager u Azure nebo pomocí azure cli nebo Azure PowerShellu.

Přehled privátního propojení Azure:https://docs.microsoft.com/azure/private-link/private-link-overview

Jak nakonfigurovat privátní koncový bod pro Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Jak vytvořit skupinu zabezpečení sítě s konfigurací zabezpečení:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Jak nakonfigurovat bránu firewall IP v Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Sledování a protokolování konfigurace a provozu sítí, podsítí a síťových rozhraní

**Pokyny:** Použijte Azure Security Center a postupujte podle doporučení ochrany sítě, které vám pomůžou zabezpečit síťové prostředky související s vaším účtem Azure Cosmos.

Když se virtuální počítače nasazují ve stejné virtuální síti jako váš účet Azure Cosmos, můžete použít skupinu zabezpečení sítě (NSG) ke snížení rizika exfiltrace dat. Povolte protokoly toku nsg a odesílat protokoly do účtu úložiště Azure pro audity provozu. Protokoly toku nsg můžete také odesílat do pracovního prostoru Analýzy protokolů a pomocí traffic analytics poskytovat přehled o toku provozu ve vašem cloudu Azure. Některé výhody Traffic Analytics jsou schopnost vizualizovat aktivitu v síti a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzorce toku provozu a určit chybné konfigurace sítě.

Seznamte se se zabezpečením sítě poskytovaným službou Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Povolení protokolů toku nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit a používat Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrana kritických webových aplikací

**Pokyny**: Pomocí funkce Sdílení prostředků mezi zdroji (CORS) povolte webové aplikaci spuštěné pod jednou doménou přístup k prostředkům v jiné doméně. Webové prohlížeče implementují omezení zabezpečení známé jako zásady stejného původu, které brání webové stránce volat api v jiné doméně. Cors však poskytuje bezpečný způsob, jak povolit původní doméně volat api v jiné doméně. Po povolení podpory CORS pro váš účet Azure Cosmos jsou vyhodnoceny pouze ověřené požadavky k určení, zda jsou povoleny podle pravidel, která jste zadali.

Konfigurace sdílení prostředků mezi zdroji:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odepřít komunikaci se známými škodlivými IP adresami

**Pokyny:** Použití rozšířené ochrany před internetovými technologiemi (ATP) pro Azure Cosmos DB . ATP pro Azure Cosmos DB poskytuje další vrstvu inteligence zabezpečení, která detekuje neobvyklé a potenciálně škodlivé pokusy o přístup nebo zneužití účtů Azure Cosmos. Tato vrstva ochrany umožňuje řešit hrozby a integrovat je s centrálními monitorovacími systémy zabezpečení.

Povolte standard ochrany DDoS ve virtuálních sítích přidružených k instancím Azure Cosmos DB, abyste se ochránili před útoky DDoS. Pomocí integrované analýzy hrozeb Centra zabezpečení Azure můžete odepřít komunikaci se známými škodlivými nebo nepoužívanými internetovými IP adresami.

Jak nakonfigurovat Azure Cosmos DB Advanced Threat Protection:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Jak nakonfigurovat ochranu Před sdos:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Seznamte se s integrovanou analýzou hrozeb Centra zabezpečení Azure:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Záznam síťových paketů a protokolů toku

**Pokyny:** Povolte protokoly toku skupiny zabezpečení sítě (NSG) a odesílejte protokoly do účtu úložiště pro audit provozu. Protokoly toku nsg můžete odesílat do pracovního prostoru Log Analytics a pomocí Traffic Analytics poskytnout přehled o toku provozu ve vašem cloudu Azure. Některé výhody Traffic Analytics jsou schopnost vizualizovat aktivitu v síti a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzorce toku provozu a určit chybné konfigurace sítě.

Povolení protokolů toku nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit a používat Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Nasazení síťových systémů detekce vniknutí/prevence vniknutí (IDS/IPS)

**Pokyny:** Použití rozšířené ochrany před internetovými technologiemi (ATP) pro Azure Cosmos DB. ATP pro Azure Cosmos DB poskytuje další vrstvu inteligence zabezpečení, která detekuje neobvyklé a potenciálně škodlivé pokusy o přístup nebo zneužití účtů Azure Cosmos. Tato vrstva ochrany umožňuje řešit hrozby a integrovat je s centrálními monitorovacími systémy zabezpečení. 

Jak nakonfigurovat pokročilou ochranu před hrozbami Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Správa návštěvnosti webových aplikací

**Pokyny**: Nepoužije se; Doporučení je určené pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizace složitosti a administrativní režie pravidel zabezpečení sítě

**Pokyny:** Pro prostředky, které potřebují přístup k vašemu účtu Azure Cosmos, použijte značky služby Virtuální síť k definování ovládacích prvků přístupu k síti v gGroups zabezpečení sítě nebo Azure Firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (například AzureCosmosDB) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo jako adresy změnit.

Další informace o používání značek služeb:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Udržovat standardní konfigurace zabezpečení pro síťová zařízení

**Pokyny**: Definujte a implementujte standardní konfigurace zabezpečení pro síťové prostředky pomocí zásad Azure. Pomocí aliasů zásad Azure v oborech názvů Microsoft.DocumentDB a Microsoft.Network vytvořte vlastní zásady pro auditování nebo vynucení síťové konfigurace instancí Azure Cosmos DB. Můžete také využít předdefinované definice zásad pro Azure Cosmos DB, například:

- Nasazení rozšířené ochrany před hrozbami pro účty Cosmos DB

- Cosmos DB by měl používat koncový bod služby virtuální sítě

Azure Blueprints můžete také použít ke zjednodušení rozsáhlých nasazení Azure tak, že zabalíte artefakty prostředí klíčů, jako jsou šablony Azure Resource Manager, řízení přístupu na základě rolí (RBAC) a zásady v jedné definici podrobného plánu. Podrobný plán můžete snadno použít na nová předplatná, prostředí a doladit řízení a správu prostřednictvím správy verzí.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvořit Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentovat pravidla konfigurace provozu

**Pokyny**: Pomocí značek pro síťové prostředky přidružené k nasazení Azure Cosmos DB je můžete logicky uspořádat do taxonomie.

Jak vytvářet a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Použití automatizovaných nástrojů ke sledování konfigurace síťových prostředků a zjišťování změn

**Pokyny:** Pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků souvisejících s instancemi Služby Azure Cosmos DB. Vytvořte výstrahy v rámci Azure Monitor, který se aktivuje, když dojde ke změnám kritických síťových prostředků. 

Jak zobrazit a načíst události protokolu aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak vytvořit výstrahy ve službě Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace naleznete [v tématu Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Použití schválených zdrojů synchronizace času

**Pokyny**: Microsoft udržuje zdroj času používaný pro prostředky Azure, jako je Azure Cosmos DB pro časová razítka v protokolech.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurace správy centrálního protokolu zabezpečení

**Pokyny:** Ingestování protokolů prostřednictvím Azure Monitor u agregovat data zabezpečení generované Azure Cosmos DB. V rámci Azure Monitoru můžete pomocí pracovních prostorů Log Analytics dotazovat a provádět analýzy a používat účty úložiště pro dlouhodobé nebo archivní úložiště. Případně můžete povolit a na palubě data do Azure Sentinelu nebo třetí strany zabezpečení incidentů a správě událostí (SIEM). 

Jak povolit diagnostické protokoly pro Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Jak napalubě Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Povolení protokolování auditování prostředků Azure

**Pokyny:** Povolte diagnostická nastavení pro Azure Cosmos DB a odešlete protokoly do pracovního prostoru analýzy protokolů nebo účtu úložiště. Diagnostická nastavení v Azure Cosmos DB se používají ke shromažďování protokolů prostředků. Tyto protokoly jsou zachyceny na požadavek a jsou také označovány jako "protokoly roviny dat". Některé příklady operací roviny dat patří odstranění, vložení a čtení. Můžete také povolit nastavení diagnostiky protokolu aktivit Azure a odeslat je do stejného pracovního prostoru Analýzy protokolů.

Jak povolit diagnostická nastavení pro Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Jak povolit diagnostická nastavení pro protokol aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Shromažďování protokolů zabezpečení z operačních systémů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny:** V Azure Monitor, nastavte dobu uchovávání protokolů pro pracovní prostory Log Analytics přidružené k vašim instancím Azure Cosmos DB v souladu s předpisy vaší organizace dodržování předpisů.

Jak nastavit parametry uchovávání protokolů:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="26-monitor-and-review-logs"></a>2.6: Sledování a kontrola protokolů

**Pokyny**: Můžete provádět dotazy v Log Analytics pracovního prostoru pro vyhledávání termínů, identifikovat trendy, analyzovat vzory a poskytnout mnoho dalších přehledů na základě protokolů Azure Cosmos DB, které jste shromáždili.

Jak provádět dotazy pro Azure Cosmos DB v pracovních prostorech Analýzy protokolů:https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Povolit upozornění na anomální aktivitu

**Pokyny:** V Centru zabezpečení Azure povolte pokročilou ochranu před internetovými technologiemi pro Azure Cosmos DB ke sledování neobvyklých aktivit v protokolech a událostech zabezpečení. Povolte diagnostická nastavení v Azure Cosmos DB a odesílejte protokoly do pracovního prostoru Log Analytics.

 

Můžete také napalubě pracovního prostoru Log Analytics do Azure Sentinelu, protože poskytuje řešení automatické odpovědi orchestrace zabezpečení (SOAR). To umožňuje vytvářet a používat playbooky (automatizovaná řešení) k nápravě problémů se zabezpečením. Kromě toho můžete vytvořit vlastní výstrahy protokolu v pracovním prostoru Log Analytics pomocí Azure Monitor.

Seznam výstrah ochrany před hrozbami pro Azure Cosmos DB:https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Jak napalubě Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Vytváření, zobrazení a správa výstrah protokolu pomocí Azure Monitoru:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizovat protokolování antimalwaru

**Pokyny**: Nepoužije se; Azure Cosmos DB nezpracovává ani nevytváří protokoly související s antimalwarem.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="29-enable-dns-query-logging"></a>2.9: Povolení protokolování dotazů DNS

**Pokyny**: Nepoužije se; Azure Cosmos DB nezpracovává ani nevytváří protokoly související s DNS.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="210-enable-command-line-audit-logging"></a>2.10: Povolení protokolování auditu příkazového řádku

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace naleznete [v tématu Zabezpečení: Identita a řízení přístupu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Vedení soupisu administrativních účtů

**Pokyny:** Podokno Řízení identit a přístupu (IAM) na webu Azure Portal můžete použít ke konfiguraci řízení přístupu na základě rolí (RBAC) a udržovat inventář na prostředcích Azure Cosmos DB. Role jsou použity pro uživatele, skupiny, instanční objekty a spravované identity ve službě Active Directory. Můžete použít předdefinované role nebo vlastní role pro jednotlivce a skupiny.

Azure Cosmos DB poskytuje integrované RBAC pro běžné scénáře správy v Azure Cosmos DB. Jednotlivec, který má profil ve službě Azure Active Directory (AD) můžete přiřadit tyto role RBAC uživatelům, skupinám, instanční objekty nebo spravované identity udělit nebo odepřít přístup k prostředkům a operacím na prostředky Azure Cosmos DB.

Modul Azure AD PowerShell můžete také použít k provádění adhoc dotazů ke zjišťování účtů, které jsou členy skupin pro správu. 

Kromě toho některé akce v Azure Cosmos DB lze řídit pomocí Služby Azure Active Directory a hlavní klíče specifické pro účet.  K řízení přístupu ke klíčům použijte nastavení účtu disableKeyBasedMetadataWriteAccess.

Principy řízení přístupu na základě rolí v Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Vytvořte si vlastní role pomocí akcí Azure Cosmos DB (obor názvů Microsoft.DocumentDB):https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Vytvořte novou roli ve službě Azure Active Directory:https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Jak získat roli adresáře ve službě Azure Active Directory pomocí PowerShellu:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak získat členy role adresáře ve službě Azure Active Directory pomocí PowerShellu:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Omezte přístup uživatelů pouze k datovým operacím:https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Změna výchozích hesel, pokud je to možné

**Pokyny:** Koncept výchozí nebo prázdná hesla neexistuje ve vztahu k Azure AD nebo Azure Cosmos DB. Místo toho Azure Cosmos DB používá dva typy klíčů k ověření uživatelů a poskytnout přístup k jeho data a prostředky; hlavní klíče a tokeny prostředků. Klíče lze kdykoli obnovit.

Principy zabezpečeného přístupu k datům v Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Jak znovu vygenerovat klíče Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Jak programově přistupovat ke klíčům pomocí služby Azure Active Directory:https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Použití vyhrazených administrativních účtů

**Pokyny**: Nepoužije se; Azure Cosmos DB nepodporuje účty správce.  Veškerý přístup je integrovaný s Azure Active Directory a řízení přístupu na základě rolí Azure (RBAC).



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Použití jednotného přihlašování (SSO) s Azure Active Directory

**Pokyny**: Azure Cosmos DB používá dva typy klíčů k autorizaci uživatelů a nepodporuje jednotné přihlašování (SSO) na úrovni roviny dat. Přístup k rovině ovládacího prvku pro Cosmos DB je k dispozici prostřednictvím rozhraní REST API a podporuje sso. Chcete-li ověřit, nastavte hlavičku autorizace pro vaše požadavky na webový token JSON, který získáte ze služby Azure Active Directory.

Principy rozhraní AZURE Database for Cosmos DB REST API:https://docs.microsoft.com/rest/api/cosmos-db/

Principy připojetých podle služby SSO pomocí služby Azure Active Directory:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Použití vícefaktorového ověřování pro veškerý přístup založený na službě Azure Active Directory

**Pokyny:** Povolte vícefaktorové ověřování Azure Active Directory a postupujte podle doporučení Centra identit a správy přístupu Služby Zabezpečení Azure.

Jak povolit vícefaktorové povolení v Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak sledovat identitu a přístup v rámci Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Pro všechny administrativní úkoly používejte vyhrazené počítače (pracovní stanice s privilegovaným přístupem)

**Pokyny:** Používejte pracovní stanice s privilegovaným přístupem (PAW) s vícefaktorovým ověřováním nakonfigurovaným pro přihlášení a konfiguraci prostředků Azure.

Další informace o pracovních stanicích s privilegovaným přístupem:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové povolení v Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokolování a upozornění na podezřelou aktivitu z administrativních účtů

**Pokyny:** Použití rozšířené ochrany před internetovými technologiemi (ATP) pro Azure Cosmos DB. ATP pro Azure Cosmos DB poskytuje další vrstvu inteligence zabezpečení, která detekuje neobvyklé a potenciálně škodlivé pokusy o přístup nebo zneužití účtů Azure Cosmos. Tato vrstva ochrany umožňuje řešit hrozby a integrovat je s centrálními monitorovacími systémy zabezpečení. 

Kromě toho můžete použít Azure Active Directory (AD) Privilegované správy identit (PIM) pro generování protokolů a výstrah, když dojde k podezřelé nebo nebezpečné aktivity v prostředí.

Pomocí azure ad detekce rizik pro zobrazení výstrah a sestav na rizikové chování uživatelů.

Jak nasadit správu privilegovaných identit (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Seznamte se s detekcí rizik Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Správa prostředků Azure z jenom schválených umístění

**Pokyny**: Nakonfigurujte podmínku umístění zásad podmíněného přístupu a spravujte pojmenovaná umístění. S pojmenovanými umístěními můžete vytvořit logická seskupení rozsahů IP adres nebo zemí a oblastí. Přístup k citlivým prostředkům, jako jsou instance Azure Cosmos DB, můžete omezit na nakonfigurovaná pojmenovaná umístění.

Jak nakonfigurovat pojmenovaná umístění v Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="39-use-azure-active-directory"></a>3.9: Použití služby Azure Active Directory

**Pokyny:** Jako centrální systém ověřování a autorizace použijte službu Azure Active Directory (AD). Azure AD chrání data pomocí silnéšifrování pro data v klidovém stavu a při přenosu. Azure AD také soli, hashy a bezpečně ukládá přihlašovací údaje uživatele.

Jak vytvořit a nakonfigurovat instanci služby Azure Active Directory:https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Jak nakonfigurovat a spravovat ověřování Azure Active Directory pomocí Azure SQL:https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Pravidelně kontrolujte a slaďujte přístup uživatelů

**Pokyny**: Služba Azure Active Directory poskytuje protokoly, které pomáhají zjistit zastaralé účty. Kromě toho můžete pomocí azure identity access reviews efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Přístup uživatele lze pravidelně kontrolovat, aby se zajistilo, že pouze ti praví uživatelé mají trvalý přístup.

Jak používat recenze přístupu k identitám Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Sledování pokusů o přístup k deaktivovaným účtům

**Pokyny:** Můžete vytvořit diagnostická nastavení pro uživatelské účty Služby Azure Active Directory, odesílat protokoly auditu a protokoly přihlášení do pracovního prostoru Log Analytics, kde můžete nakonfigurovat požadované výstrahy.

Jak integrovat protokoly aktivit Azure do Azure Monitoru:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Upozornění na odchylku chování přihlášení k účtu

**Pokyny:** Použití rozšířené ochrany před internetovými technologiemi (ATP) pro Azure Cosmos DB. ATP pro Azure Cosmos DB poskytuje další vrstvu inteligence zabezpečení, která detekuje neobvyklé a potenciálně škodlivé pokusy o přístup nebo zneužití účtů Azure Cosmos. Tato vrstva ochrany umožňuje řešit hrozby a integrovat je s centrálními monitorovacími systémy zabezpečení. 

Funkci Azure AD Identity Protection and Risk Detections můžete také použít ke konfiguraci automatických odpovědí na zjištěné podezřelé akce související s identitami uživatelů. Kromě toho můžete ingestovat protokoly do Azure Sentinelu pro další šetření.

Jak zobrazit riziková přihlášení služby Azure Active Directory:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak nakonfigurovat a povolit zásady rizik a zásady ochrany identity:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak napalubě Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Poskytněte společnosti Microsoft přístup k relevantním zákaznickým datům během scénářů podpory

**Pokyny**: V současné době není k dispozici; Customer Lockbox ještě není podporovánpro Azure Database for Cosmos DB.

Seznam služeb podporovaných customer lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Nepoužije se

## <a name="data-protection"></a>Ochrana dat

*Další informace naleznete [v tématu Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Udržovat soupis citlivých informací

**Pokyny:** Pomocí značek můžete při sledování instancí Azure Cosmos DB, které ukládají nebo zpracovávají citlivé informace.

Jak vytvářet a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolovat systémy ukládající nebo zpracovávající citlivé informace

**Pokyny**: Implementujte samostatné odběry nebo skupiny pro správu pro vývoj, testování a výrobu. Instance Azure Cosmos DB jsou odděleny virtuální sítí nebo podsítí, vhodně označeny a zabezpečeny v rámci skupiny zabezpečení sítě (NSG) nebo Azure Firewall. Instance Azure Cosmos DB, které ukládají citlivá data, by měly být izolované. Pomocí Azure Private Link se můžete připojit k účtu instance Azure Cosmos DB prostřednictvím privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v podsíti ve vaší virtuální síti. Poté můžete omezit přístup k vybraným soukromým IP adresám. 

Jak vytvořit další předplatná Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:https://docs.microsoft.com/azure/governance/management-groups/create

Jak vytvářet a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak nakonfigurovat privátní koncový bod pro Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Jak vytvořit skupinu zabezpečení sítě s konfigurací zabezpečení:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Sledování a blokování neoprávněného přenosu citlivých informací

**Pokyny:** Můžete nasadit pokročilou ochranu před internetovými technologiemi pro Azure Cosmos DB, která bude zjišťovat neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. V současné době může aktivovat následující výstrahy:

- Přístup z neobvyklých míst

- Neobvyklá extrakce dat

Navíc při použití virtuálních počítačů pro přístup k instancím Azure Cosmos DB, využijte private link, firewall, skupiny zabezpečení sítě a značky služeb ke zmírnění možnosti exfiltrace dat. Microsoft spravuje základní infrastrukturu pro Azure Cosmos DB a zavedl přísné ovládací prvky, aby zabránil ztrátě nebo vystavení zákaznických dat.

Jak nakonfigurovat pokročilou ochranu před hrozbami Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Sdíleno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Šifrování všech citlivých informací při přepravě

**Pokyny:** Všechna připojení k Azure Cosmos DB podporují https. Azure Cosmos DB také podporuje TLS1.2. Je možné vynutit minimální verzi TLS na straně serveru. Chcete-li tak [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)učinit, obraťte se na společnost .

Přehled zabezpečení Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-security

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Použití aktivního nástroje pro zjišťování k identifikaci citlivých dat

**Pokyny:** Funkce automatické identifikace, klasifikace a prevence ztrát ještě nejsou k dispozici pro Azure Cosmos DB. Můžete však použít azure cognitive search integrace pro klasifikaci a analýzu dat. Můžete také implementovat řešení jiného výrobce, pokud je to požadováno pro účely dodržování předpisů.

Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.

Index dat Azure Cosmos DB https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amppomocí Azure Cognitive Search: ;bc=/azure/cosmos-db/strouhanka/toc.json

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Použití Azure RBAC k řízení přístupu k prostředkům

**Pokyny:** Azure Cosmos DB poskytuje integrované řízení přístupu na základě rolí (RBAC) pro běžné scénáře správy v Azure Cosmos DB. Jednotlivec, který má profil ve službě Azure Active Directory můžete přiřadit tyto role RBAC uživatelům, skupinám, instanční objekty nebo spravované identity udělit nebo odepřít přístup k prostředkům a operacím na prostředky Azure Cosmos DB. Přiřazení rolí jsou vymezeny pouze pro přístup k rovině řízení, který zahrnuje přístup k účtům Azure Cosmos, databázím, kontejnerům a nabídkám (propustnost).

Jak implementovat RBAC v Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Použití ochrany před ztrátou dat na hostiteli k vynucení kontroly přístupu

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.

Společnost Microsoft spravuje základní infrastrukturu pro Cosmos DB a zavedla přísné kontroly, aby zabránila ztrátě nebo vystavení zákaznických dat.

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Šifrování citlivých informací v klidovém stavu

**Pokyny:** Všechna uživatelská data uložená v Cosmos DB jsou ve výchozím nastavení šifrována v klidovém stavu. Neexistují žádné ovládací prvky, které by jej vypnuly. Azure Cosmos DB používá šifrování AES-256 ve všech oblastech, kde je spuštěný účet.

Ve výchozím nastavení spravuje Microsoft klíče, které se používají k šifrování dat ve vašem účtu Azure Cosmos. Volitelně můžete přidat druhou vrstvu šifrování s vlastními klíči.

Principy šifrování v klidovém stavu pomocí Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Principy správy klíčů pro šifrování v klidovém stavu pomocí Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Jak nakonfigurovat klíče spravované zákazníkem pro váš účet Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokolování a upozorňování na změny kritických prostředků Azure

**Pokyny:** Pomocí Azure Monitor s protokolem aktivit Azure vytvořit výstrahy, kdy dojde ke změnám v produkčních instancích Azure Cosmos DB.

Jak vytvořit výstrahy pro události protokolu aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Jak vytvořit výstrahy pro události protokolu aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace naleznete v [tématu Security Control: Vulnerability Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Spuštění automatizovaných nástrojů pro vyhledávání chyb zabezpečení

**Pokyny:** Postupujte podle doporučení z Azure Security Center pro vaše instance Azure Cosmos DB. 

Společnost Microsoft provádí systémovou správu oprav a zranitelnosti na podkladových hostitelích, které podporují vaše instance Azure Cosmos DB. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.

Podporované funkce dostupné v Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Sdíleno

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Nasazení řešení pro správu automatických oprav operačního systému

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Nasazení automatizovaného řešení pro správu softwarových oprav od jiných výrobců

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porovnání prohledávačů ohrožení zabezpečení zády k zadní straně

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Použití procesu hodnocení rizik a upřednostnění nápravy zjištěných zranitelných míst

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace naleznete v [tématu Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Použití Azure Asset Discovery

**Pokyny:** Pomocí portálu Azure nebo Azure Resource Graph zjišťujete všechny prostředky (není omezen na Azure Cosmos DB, ale také včetně prostředků, jako jsou výpočetní prostředky, jiné úložiště, síť, porty a protokoly atd.) v rámci vašeho předplatného.  Ujistěte se, že máte příslušná oprávnění ve vašem tenantovi a jsou schopni vytvořit výčet všech předplatných Azure, stejně jako prostředky v rámci vašich předplatných.

I když klasické prostředky Azure může být zjištěna prostřednictvím grafu prostředků, je vysoce doporučeno vytvářet a používat prostředky Azure Resource Manager do budoucna.

Jak vytvářet dotazy pomocí Azure Graphu:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak zobrazit předplatná Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Principy řízení přístupu na základě rolí Azure:https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="62-maintain-asset-metadata"></a>6.2: Udržování metadat datových zdrojů

**Pokyny:** Použijte značky pro instance Azure Cosmos DB a související prostředky s metadaty logicky uspořádat do taxonomie.

Jak vytvářet a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Které prostředky Azure Cosmos DB podporují značky:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Odstranění neautorizovaných prostředků Azure

**Pokyny:** Použijte značkování, skupiny pro správu a samostatné předplatná, kde je to vhodné, k uspořádání a sledování prostředků, včetně prostředků Azure Cosmos DB. Pravidelně odsouhlasete zásoby a zajistěte, aby byly z předplatného včas odstraněny neoprávněné prostředky.

Jak vytvořit další předplatná Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:https://docs.microsoft.com/azure/governance/management-groups/create

Jak vytvořit a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Udržovat soupis schválených prostředků Azure a softwarových titulů

**Pokyny**: Nepoužije se; Tato příručka je určena pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

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

**Pokyny**: Nepoužije se; Tento směrný plán je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Odebrání neschválených prostředků Azure a softwarových aplikací

**Pokyny**: Nepoužije se; Tato příručka je určena pro výpočetní prostředky a Azure jako celek.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="68-use-only-approved-applications"></a>6.8: Používejte pouze schválené aplikace

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="69-use-only-approved-azure-services"></a>6.9: Používejte jenom schválené služby Azure

**Pokyny**: Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

- Nepovolené typy prostředků 

- Povolené typy prostředků

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odepřít konkrétní typ prostředku pomocí zásad Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="610-implement-approved-application-list"></a>6.10: Implementace schváleného seznamu žádostí

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Omezte možnost uživatelů komunikovat se Správcem azurezdrojů pomocí skriptů

**Pokyny:** Pomocí podmíněného přístupu Azure omezte možnost uživatelů pracovat se Správcem prostředků Azure konfigurací "Blokovat přístup" pro aplikaci Microsoft Azure Management. To může zabránit vytváření a změny prostředků v prostředí s vysokým zabezpečením.

Jak nakonfigurovat podmíněný přístup pro blokování přístupu ke Správci prostředků Azure:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Omezení možnosti uživatelů spouštět skripty v rámci výpočetních prostředků

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fyzicky nebo logicky oddělit vysoce rizikové aplikace

**Pokyny**: Nepoužije se; Tento pokyn je určený pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace naleznete v [tématu Zabezpečení: Zabezpečená konfigurace](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny:** Definujte a implementujte standardní konfigurace zabezpečení pro vaše instance Cosmos DB pomocí zásad Azure. Pomocí aliasů zásad Azure v oboru názvů Microsoft.DocumentDB můžete vytvořit vlastní zásady pro auditování nebo vynucení konfigurace instancí Cosmos DB. Můžete také využít předdefinované definice zásad pro Azure Cosmos DB, například:

- Nasazení rozšířené ochrany před hrozbami pro účty Cosmos DB

- Cosmos DB by měl používat koncový bod služby virtuální sítě

Jak zobrazit dostupné aliasy zásad Azure:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Vytvoření bezpečných konfigurací operačního systému

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Udržování zabezpečených konfigurací prostředků Azure

**Pokyny:** Pomocí zásad Azure [odepřít] a [nasadit, pokud neexistují] vynutit zabezpečené nastavení napříč prostředky Azure.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Principy efektů zásad Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Udržovat bezpečné konfigurace operačního systému

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Konfigurace prostředků Azure bezpečně ukládá

**Pokyny:** Pokud používáte vlastní definice zásad Azure pro cosmos DB nebo související prostředky, použijte Azure Repos bezpečně ukládat a spravovat váš kód.

Dokumentace k azure https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops repos:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpečné ukládání vlastních bitových kopií operačního systému

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Nasazení nástrojů pro správu konfigurace systému

**Pokyny**: Pomocí aliasů zásad Azure v oboru názvů Microsoft.DocumentDB vytvořte vlastní zásady pro výstrahu, audit ování a vynucení konfigurací systému. Kromě toho vytvořte proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Nasazení nástrojů pro správu konfigurace systému pro operační systémy

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementace automatického monitorování konfigurace pro služby Azure

**Pokyny**: Pomocí aliasů zásad Azure v oboru názvů Microsoft.DocumentDB vytvořte vlastní zásady pro výstrahu, audit ování a vynucení konfigurací systému. Pomocí zásad Azure [audit] [odepřít] a [nasadit, pokud neexistují] automaticky vynutit konfigurace pro vaše instance Azure Cosmos DB a související prostředky. 

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementace automatizovaného monitorování konfigurace operačních systémů

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpečná správa tajných kódů Azure

**Pokyny:** Pro virtuální počítače Azure nebo webové aplikace spuštěné ve službě Azure App Service, které se používají k přístupu k vašim instancím Azure Cosmos DB, použijte identitu spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných služeb Azure Cosmos DB. Ujistěte se, že je povoleno měkké odstranění trezoru klíčů.

Jak integrovat s Azure Spravované identity:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak vytvořit trezor klíčů:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zajistit ověřování trezoru klíčů se spravovanou identitou:https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpečná a automatická správa identit

**Pokyny:** Pro virtuální počítače Azure nebo webové aplikace spuštěné ve službě Azure App Service, které se používají k přístupu k vašim instancím Azure Cosmos DB, použijte identitu spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných služeb Azure Cosmos DB.

Pomocí spravovaných identit můžete poskytovat služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory (AD). Spravované identity umožňuje ověření na libovolnou službu, která podporuje ověřování Azure AD, včetně trezoru klíčů, bez pověření ve vašem kódu.

Jak nakonfigurovat spravované identity:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Jak integrovat s Azure Spravované identity:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminujte nezamýšlenou expozici pověření

**Pokyny**: Implementujte skener pověření k identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů také podpoří přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je azure key vault.

Jak nastavit skener přihlašovacích údajů:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace naleznete v [tématu Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Používejte centrálně spravovaný antimalwarový software

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky. Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure App Service), ale neběží na zákaznický obsah.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Předprohledávací soubory, které se mají nahrát do nevýpočetních prostředků Azure

**Pokyny**: Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure App Service), ale neběží na zákaznický obsah.

Je vaší odpovědností předem proskenovat všechny soubory nahrané do nevýpočetních prostředků Azure, včetně Azure Cosmos DB. Společnost Microsoft nemá přístup k zákaznickým datům, a proto nemůže vaším jménem provádět antimalwarové kontroly obsahu zákazníků.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Zajistěte aktualizaci antimalwarového softwaru a podpisů

**Pokyny**: Nepoužije se; referenční hodnota je určena pro výpočetní prostředky. Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure, ale neběží na zákaznický obsah.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="data-recovery"></a>Obnovení dat

*Další informace naleznete [v tématu Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zajistěte pravidelné automatické zálohování

**Pokyny:** Azure Cosmos DB pořizuje snímky vašich dat každé čtyři hodiny. Všechny zálohy jsou uloženy samostatně ve službě úložiště a tyto zálohy jsou globálně replikovány pro odolnost proti regionálním haváriím. V daném okamžiku jsou zachovány pouze poslední dva snímky. Pokud se však kontejner nebo databáze odstraní, Azure Cosmos DB zachová stávající snímky daného kontejneru nebo databáze po dobu 30 dnů. Chcete-li obnovit podporu Azure ze zálohy, obraťte se na podporu Azure.

Principy automatického zálohování Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Proveďte kompletní zálohy systému a zálohujte všechny klienty spravované klíče

**Pokyny**: Azure Cosmos DB automaticky přebírá zálohy vašich dat v pravidelných intervalech. Pokud je odstraněna databáze nebo kontejner, můžete soubor lístek podpory nebo volání podpory Azure obnovit data z automatického zálohování online. Podpora Azure je dostupná pro vybrané plány, jako je Standard, Developer a plány vyšší než oni. Chcete-li obnovit konkrétní snímek zálohy, Azure Cosmos DB vyžaduje, aby data je k dispozici po dobu trvání cyklu zálohování pro tento snímek. 

Pokud používáte trezor klíčů k ukládání přihlašovacích údajů pro instance Cosmos DB, zajistěte pravidelné automatické zálohování klíčů.

Seznamte se s automatickými zálohami Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Jak obnovit data v Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Jak zálohovat klíče trezoru klíčů:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny:** Pokud je odstraněna databáze nebo kontejner, můžete soubor lístek podpory nebo volání podpory Azure obnovit data z automatického zálohování online. Podpora Azure je dostupná pro vybrané plány, jako je Standard, Developer a plány vyšší než oni. Chcete-li obnovit konkrétní snímek zálohy, Azure Cosmos DB vyžaduje, aby data je k dispozici po dobu trvání cyklu zálohování pro tento snímek.

Otestujte obnovení tajných klíčů uložených v azure key vault pomocí PowerShellu. Rutina Restore-AzureKeyVaultKey vytvoří klíč v zadaném trezoru klíčů. Tento klíč je replikou zálohovaného klíče ve vstupním souboru a má stejný název jako původní klíč.

Seznamte se s automatickými zálohami Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Jak obnovit data v Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Jak obnovit tajné klíče úložiště klíčů Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zajištění ochrany záloh a klíčů spravovaných zákazníkem

**Pokyny:** Vzhledem k tomu, že všechna uživatelská data uložená v Cosmos DB je šifrována v klidovém stavu a v přenosu, není třeba provést žádnou akci. Dalším způsobem, jak to dát, je, že šifrování v klidovém stavu je ve výchozím nastavení "zapnuto". Neexistují žádné ovládací prvky, které by jej vypnuly nebo zapnuly. Azure Cosmos DB používá šifrování AES-256 ve všech oblastech, kde je spuštěný účet.

Povolte funkci Obnovitelné odstranění v trezoru klíčů, která chrání klíče před náhodným nebo škodlivým odstraněním.

Principy šifrování dat v Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Jak povolit obnovitelné odstranění v trezoru klíčů:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Sdíleno

## <a name="incident-response"></a>Reakce na incidenty

*Další informace naleznete [v tématu Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Vytvoření průvodce odezvou na incidenty

**Pokyny:** Vytvořte průvodce odezvou na incidenty pro vaši organizaci. Ujistěte se, že existují písemné plány reakce na incidenty, které definují všechny role personálu, stejně jako fáze zpracování incidentů / správy od detekce až po kontrolu po incidentu.

Můžete také využít NIST computer security incident handling guide na pomoc při vytváření vlastního plánu reakce na incidenty:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Jak nakonfigurovat automatizaci pracovních postupů v Centru zabezpečení Azure:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Pokyny pro vytváření vlastního procesu reakce na bezpečnostní incidenty:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomie incidentu v centru Microsoft Security Response Center:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Vytvoření postupu hodnocení incidentů a stanovení priorit

**Pokyny**: Centrum zabezpečení přiřazuje každé výstraze závažnost, která vám pomůže určit prioritu, které výstrahy by měly být nejprve prošetřeny. Závažnost je založena na tom, jak věří Centrum zabezpečení při hledání nebo analýzy použité k vydání výstrahy, stejně jako úroveň spolehlivosti, že za aktivitou, která vedla k výstraze, došlo ke škodlivému záměru.

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
