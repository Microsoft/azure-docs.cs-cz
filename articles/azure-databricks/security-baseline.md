---
title: Základní hodnoty zabezpečení Azure pro Azure Databricks
description: Základní hodnoty zabezpečení Azure pro Azure Databricks
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 23057723d46ed5d9203741ab2eb1a15edb5bd510
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681692"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Základní hodnoty zabezpečení Azure pro Azure Databricks

Základní plán zabezpečení Azure pro Azure Databricks obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Chraňte prostředky pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network

**Pokyny**: nasazení Azure Databricks ve vaší vlastní službě Azure Virtual Network (VNET). Výchozím nasazením Azure Databricks je plně spravovaná služba v Azure: všechny prostředky roviny dat, včetně virtuální sítě, ke které budou přidružené všechny clustery, se nasadí do uzamčené skupiny prostředků. Pokud ale požadujete vlastní nastavení sítě, můžete nasazovat Azure Databricks prostředky roviny dat ve vlastní virtuální síti (vkládání virtuální sítě), což vám umožní implementovat vlastní síťové konfigurace. Můžete použít vlastní skupinu zabezpečení sítě (NSG) s vlastními pravidly pro konkrétní omezení odchozích přenosů dat.

Kromě toho můžete nakonfigurovat pravidla NSG, která určují omezení odchozích přenosů v podsíti, do které je vaše instance Azure Databricks nasazena. Azure Firewall lze nakonfigurovat pro pracovní prostory vložené do virtuální sítě.

* [Postup nasazení Azure Databricks do vlastního Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Jak spravovat skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuální sítě, podsítí a síťových karet.

**Pokyny**: nasazení Azure Databricks ve vaší vlastní službě Azure Virtual Network (VNET). Skupina zabezpečení sítě (NSG) není vytvořena automaticky. Je potřeba, abyste vytvořili základní NSG s výchozími pravidly Azure. Když nasadíte pracovní prostor, přidají se pravidla, která vyžadují datacihly. Můžete také začít s prázdným NSG a automaticky se přidají odpovídající pravidla. Povolte protokoly toku NSG a odešlete protokoly do účtu úložiště pro audity provozu. Protokoly toku můžete také odesílat do Log Analyticsho pracovního prostoru a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

* [Postup nasazení Azure Databricks do vlastního Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Jak povolit protokoly toku NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Postup povolení a použití Analýza provozu](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Postup povolení Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: povolení Azure DDoS Protection Standard ve virtuálních sítích Azure přidružených k vašim Azure Databricks instancím pro ochranu před distribuovanými útoky DoS (Denial-of-Service). K odepření komunikace se známými škodlivými nebo nepoužívanými veřejnými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

* [Správa Azure DDoS Protection Standard pomocí Azure Portal](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Pochopení ochrany před hrozbami pro síťovou vrstvu Azure v Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: záznam síťových paketů a protokolů toků

**Pokyny**: nasazení Azure Databricks ve vaší vlastní službě Azure Virtual Network (VNET). Skupina zabezpečení sítě (NSG) není vytvořena automaticky. Je potřeba, abyste vytvořili základní NSG s výchozími pravidly Azure. Když nasadíte pracovní prostor, přidají se pravidla, která vyžadují datacihly. Povolte protokoly toku NSG a odešlete protokoly do účtu úložiště pro audit provozu. Protokoly toku můžete také odesílat do Log Analyticsho pracovního prostoru a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

* [Postup nasazení Azure Databricks do vlastního Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Jak povolit protokoly toku NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Postup povolení a použití Analýza provozu](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Postup povolení Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny**: implementace síťového virtuálního zařízení s podporou IDP/IP (síťové virtuální zařízení) z Azure Marketplace k vytvoření pracovního prostoru integrovaného ve virtuální síti, ve kterém mají všechny Azure Databricks clustery jednu odchozí IP adresu. Jedna IP adresa se dá použít jako další vrstva zabezpečení s dalšími službami a aplikacemi Azure, které umožňují přístup na základě konkrétních IP adres. Ke správě příchozího a odchozího provozu můžete použít bránu Azure firewall nebo jiné nástroje třetích stran, jako je síťové virtuální zařízení.

Pokud zjišťování neoprávněných vniknutí nebo prevence na základě kontroly zatížení není požadavkem, je možné použít Azure Firewall s analýzou hrozeb. Azure Firewall filtrování na základě logiky hrozeb může upozorňovat a zamítnout provoz do a ze známých škodlivých IP adres a domén. IP adresy a domény se naúčtují z informačního kanálu Microsoft Threat Intelligence.

* [Přiřazování jedné veřejné IP adresy pracovním prostorům vloženým do virtuální sítě s využitím služby Azure Firewall](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [Vytvoření síťové virtuální zařízení](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: pomocí značek služeb definujte řízení přístupu k síti u skupin zabezpečení sítě, které jsou připojené k podsítím, ke které je vaše instance Azure Databricks přidružená. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny. Značky služeb nejsou podporované v pracovních prostorech virtuální sítě, které nejsou vloženy.

* [Principy značek služeb](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace konfigurací zabezpečení sítě pro instance Azure Databricks s využitím Azure Policy. Pomocí aliasů Azure Policy v oboru názvů "Microsoft. datacihly" můžete definovat vlastní definice zásad. Zásady se nevztahují na prostředky v rámci spravované skupiny prostředků.

Pomocí Azure modrotisky můžete také zjednodušit rozsáhlá nasazení Azure tím, že zabalíte artefakty klíčových prostředí, jako jsou šablony pro správu prostředků Azure, řízení přístupu na základě role (RBAC) a zásady, a to v jediné definici podrobného plánu. Podrobné sestavování můžete snadno použít pro nová předplatná a prostředí a vyladit řízení a správu prostřednictvím správy verzí.

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Pochopení Azure Policych aliasů a struktury definice](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Vytvoření Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné**materiály: používejte značky pro skupin zabezpečení sítě a další prostředky související se zabezpečením sítě a tokem provozu, které jsou přidružené k vaší instanci Azure Databricks. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

* [Vytváření a používání značek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vašimi Azure Databricks instancemi. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

* [Jak zobrazit a načíst události protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Vytváření výstrah v Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Pokyny**: Microsoft udržuje časové zdroje pro prostředky Azure, ale máte možnost spravovat nastavení synchronizace času pro výpočetní prostředky. Vzhledem k tomu, že Azure Databricks je služba PaaS, nemáte přímý přístup k virtuálním počítačům v clusteru Azure Databricks a nelze nastavit nastavení synchronizace času.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: ingestování protokolů prostřednictvím Azure monitor k agregaci dat zabezpečení vygenerovaných Azure Databricks. V rámci Azure Monitor se můžete dotazovat na Log Analytics pracovním prostoru, který je nakonfigurovaný tak, aby přijímal vaše datacihly a diagnostické protokoly. Použijte účty Azure Storage pro dlouhodobé nebo archivní úložiště protokolů nebo centra událostí pro export dat do jiných systémů. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo incidentu zabezpečení a řízení událostí zabezpečení (SIEM) třetí strany.

Poznámka: Azure Databricks diagnostické protokoly vyžadují plán Azure Databricks Premium

* [Postup konfigurace nastavení diagnostiky](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Jak připojit Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné**materiály: Povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu úložiště Azure pro archivaci. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni ovládacího prvku pro vaše prostředky Azure.

Pro protokolování auditu Azure Databricks poskytuje ucelené a komplexní diagnostické protokoly aktivit prováděné Azure Databricks uživateli, což vašemu podniku umožní monitorovat podrobné způsoby používání Azure Databricks.

Poznámka: Azure Databricks diagnostické protokoly vyžadují plán Azure Databricks Premium

* [Jak povolit nastavení diagnostiky pro protokol aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy))

* [Postup povolení nastavení diagnostiky pro Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné**materiály: Azure Databricks poskytuje ucelené a komplexní diagnostické protokoly aktivit prováděné Azure Databricks uživateli, což umožňuje vašemu podniku monitorovat podrobné způsoby používání Azure Databricks.

Poznámka: Azure Databricks diagnostické protokoly vyžadují plán Azure Databricks Premium. Protokolování zabezpečení operačního systému není k dispozici.

* [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Postup povolení nastavení diagnostiky pro Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné**materiály: povolení nastavení diagnostiky pro Azure Databricks. Pokud se rozhodnete ukládat protokoly v pracovním prostoru Log Analytics, nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště. Aktivity související se zabezpečením jsou sledovány v protokolech auditu datacihly.

Poznámka: Azure Databricks diagnostické protokoly vyžadují plán Azure Databricks Premium

* [Postup povolení nastavení diagnostiky v Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Postup nastavení parametrů uchovávání protokolů pro Log Analytics pracovní prostory](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné**materiály: Povolte nastavení diagnostiky pro Azure Databricks a odešlete protokoly do pracovního prostoru Log Analytics. Pomocí pracovního prostoru Log Analytics můžete analyzovat a monitorovat protokoly Azure Databricks pro chování neobvyklé a pravidelně kontrolovat výsledky.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo jiného dodavatele SIEM.

Poznámka: Azure Databricks diagnostické protokoly vyžadují plán Azure Databricks Premium

* [Postup povolení nastavení diagnostiky v Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Postup dotazování protokolů Azure Databricks odeslaných do pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Jak připojit Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé

**Pokyny**: Konfigurace nastavení diagnostiky pro instanci Azure Databricks a odesílání protokolů do pracovního prostoru Log Analytics V rámci pracovního prostoru Log Analytics nakonfigurujte výstrahy, které se mají provést, když dojde k předdefinované sadě podmínek.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo jiného dodavatele SIEM.

Poznámka: Azure Databricks diagnostické protokoly vyžadují plán Azure Databricks Premium

* [Postup odesílání protokolů Azure Databricks do pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery)

* [Postup konfigurace výstrah v pracovním prostoru Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné**materiály: nelze použít.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné**materiály: nepoužitelné; Azure Databricks nezpracovává ani nevytváří protokoly související s DNS, které jsou přístupné pro uživatele.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné**materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a řízení přístupu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Pokyny**: Azure Databricks rozhraní API SCIM můžete použít ke správě uživatelů v pracovním prostoru Azure Databricks a udělení oprávnění pro správu určeným uživatelům. Můžete je také spravovat prostřednictvím Azure Databricks uživatelského rozhraní.

* [Jak používat rozhraní API pro SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Postup přidání a odebrání uživatelů v Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: Azure Databricks používá Azure Active Directory (AD) k poskytnutí přístupu k Azure Portal a také ke konzole pro správu Azure Databricks. Azure AD nemá koncept výchozích hesel, ale zodpovídáte za změnu nebo povolení výchozích hesel pro jakékoli vlastní aplikace nebo aplikace třetích stran.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Pokyny**: Azure Databricks rozhraní API SCIM můžete použít k přidání uživatelů s oprávněními správce v Azure Databricks. Můžete je také spravovat prostřednictvím Azure Databricks uživatelského rozhraní.

* [Jak používat rozhraní API pro SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Postup přidání a odebrání uživatelů v Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Pokyny**: Azure Databricks je automaticky nastaven na použití Azure Active Directory jednotného přihlašování k ověřování uživatelů. Uživatelé mimo vaši organizaci musí dokončit proces pozvání a přidat ho do tenanta služby Active Directory, aby se mohli přihlásit k Azure Databricks pomocí jednotného přihlašování. SCIM můžete implementovat pro automatizaci zřizování a rušení zřizování uživatelů z pracovních prostorů.

* [Jak používat rozhraní API pro SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Princip jednotného přihlašování pro Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Jak pozvat uživatele do tenanta Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: použijte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory.

**Doprovodné**materiály: Povolte Azure AD MFA a sledujte Azure Security Center doporučení pro správu identit a přístupu.

* [Jak povolit vícefaktorové ověřování v Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Jak monitorovat identitu a přístup v rámci Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Doprovodné**materiály: použití privilegovaným přístupem (privilegovaných pracovních stanic) s MFA nakonfigurovaným pro přihlášení a konfiguraci prostředků Azure.

* [Další informace o pracovních stanicích s privilegovaným přístupem](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Jak povolit vícefaktorové ověřování v Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: protokolování a upozornění na podezřelou aktivitu z účtů pro správu

**Doprovodné**materiály: použití sestav zabezpečení Azure Active Directory pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu. Kromě toho můžete využít protokoly diagnostiky Azure Databricks.

* [Jak identifikovat uživatele Azure AD označené příznakem rizika pro rizikové aktivity](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Jak monitorovat identitu uživatelů a aktivity přístupu v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

* [Postup konfigurace pojmenovaných umístění v Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: Azure Databricks je automaticky nastaven na použití Azure Active Directory jednotného přihlašování k ověřování uživatelů. Uživatelé mimo vaši organizaci musí dokončit proces pozvání a přidat ho do tenanta služby Active Directory, aby se mohli přihlásit k Azure Databricks pomocí jednotného přihlašování.

* [Princip jednotného přihlašování pro Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Jak pozvat uživatele do tenanta Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné**materiály: Azure AD poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé. Můžete také implementovat rozhraní API SCIM a diagnostické protokoly Azure Databricks pro kontrolu přístupu uživatelů. Ke kontrole přístupu uživatelů můžete použít také rozhraní API SCIM a diagnostické protokoly Azure Databricks.

Kromě toho je pravidelně kontrolovat a spravovat přístup uživatelů v konzole pro správu Azure Databricks.

* [Vytváření sestav Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Jak používat recenze Azure identity Access](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Správa přístupu uživatelů v konzole pro správu Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorování pokusů o přístup k deaktivovaným účtům

**Doprovodné**materiály: máte přístup k aktivitám přihlášení ke službě Azure AD, k auditu a rizikovým zdrojům protokolu událostí, které vám umožní integraci s jakýmkoli nástrojem Siem/monitoring. Kromě toho můžete použít diagnostické protokoly Azure Databricks ke kontrole aktivity přístupu uživatele.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro Azure Active Directory uživatelských účtů a odesláním protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics.

* [Jak používat rozhraní API pro SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Jak integrovat protokoly aktivit Azure do Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Doprovodné**materiály: použití funkcí rizika a ochrany identity v Azure AD ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření. Kromě toho můžete použít diagnostické protokoly Azure Databricks ke kontrole aktivity přístupu uživatele.

* [Jak zobrazit rizikové přihlašování Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Jak nakonfigurovat a povolit zásady rizik ochrany identity](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Jak připojit Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné**materiály: když jsou lístky podpory otevřené, technici zákaznických služeb a podpory budou požádat o souhlas s přístupem k relevantním zákaznickým datům.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: aktuálně není k dispozici.

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné**materiály: pomocí značek pomáhají při sledování Azure Databricks instancí, které zpracovávají citlivé informace.

* [Vytváření a používání značek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Výchozím nasazením Azure Databricks je plně spravovaná služba, která je nasazená v rámci své vlastní virtuální sítě. Pokud požadujete vlastní nastavení sítě, můžete nasadit Azure Databricks ve své vlastní virtuální síti. Osvědčeným postupem je vytvořit samostatné pracovní prostory Azure Databricks pro různé obchodní týmy nebo oddělení.

* [Postup nasazení Azure Databricks do vlastního Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Vytvoření dalších předplatných Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Postup vytvoření Skupiny pro správu](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Vytvoření Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací.

**Pokyny**: pomocí architektury exfiltrace Protection pro datacihly můžete zmírnit možnost datových exfiltrace.

* [Ochrana exfiltrace dat pomocí Azure Databricks](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

Společnost Microsoft spravuje základní infrastrukturu pro Azure Databricks a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

* [Pochopení ochrany zákaznických dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: aktuálně není k dispozici.

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: Společnost Microsoft bude při správě Azure Databricks instance prostřednictvím konzoly Azure Portal nebo Azure Databricks vyjednávat TLS 1,2 standardně. Webová aplikace datacihly podporuje TLS 1,3.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné**materiály: aktuálně není k dispozici; funkce pro identifikaci, klasifikaci a ochranu před únikem informací nejsou aktuálně k dispozici pro Azure Databricks. Označte Azure Databricks instance a související prostředky, které mohou zpracovávat citlivé informace jako takové a implementovat řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.

Platforma datacihly je jenom výpočetní a všechna data jsou uložená v jiných datových službách Azure. Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

* [Pochopení ochrany zákaznických dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: aktuálně není k dispozici.

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Doprovodné**materiály: v Azure Databricks můžete pomocí seznamů řízení přístupu (ACL) nakonfigurovat oprávnění k přístupu k datovým tabulkám, clusterům, fondům, úlohám a objektům pracovního prostoru, jako jsou poznámkové bloky, experimenty a složky. Všichni uživatelé s právy pro správu můžou spravovat seznamy řízení přístupu, protože můžou uživatelům, kteří mají udělená delegovaná oprávnění ke správě seznamů řízení přístupu. K nastavení oprávnění v pracovním prostoru Azure Databricks můžete použít službu Azure RBAC.

Poznámka: řízení přístupu k tabulkám, clusterům, úlohám a pracovním prostorům je dostupné jenom v plánu Azure Databricks Premium.

* [Správa řízení přístupu v Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Databricks a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

* [Pochopení ochrany zákaznických dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: Azure Databricks pracovní prostor zahrnuje rovinu správy, která je hostována ve Azure Databricks spravované virtuální síti a rovině dat nasazené ve virtuální síti spravované zákazníkem. Rovina ovládacího prvku ukládá poznámkové bloky všech uživatelů a související výsledky poznámkových bloků do databáze. Ve výchozím nastavení jsou všechny poznámkové bloky a výsledky zašifrované v klidovém stavu pomocí jiného šifrovacího klíče.

Systém souborů datacihly (DBFS) je distribuovaný systém souborů připojený k Azure Databricksmu pracovnímu prostoru a dostupný v clusterech Azure Databricks. DBFS se implementuje jako účet úložiště ve spravované skupině prostředků v pracovním prostoru Azure Databricks. Ve výchozím nastavení je účet úložiště zašifrovaný pomocí klíčů spravovaných Microsoftem. Vaše data se ukládají do datových služeb Azure, které vlastníte, a máte možnost je zašifrovat. Použití DBFS k ukládání produkčních dat se nedoporučuje.

Poznámka: tyto funkce nejsou k dispozici pro všechna předplatná Azure Databricks. Požádejte o přístup zástupce Microsoftu nebo datacihly.

* [Postup povolení klíčů spravovaných zákazníkem pro Azure Databricks poznámkových blocích](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Postup povolení klíčů spravovaných zákazníkem pro Azure Databricks systému souborů](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné**materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet upozornění, když se změny projeví u důležitých Azure Databricks pracovních prostorů.

* [Vytvoření upozornění pro události protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Pokyny**: implementace řešení pro správu ohrožení zabezpečení třetí strany.

Pokud máte předplatné platformy pro správu ohrožení zabezpečení, můžete použít inicializační skripty Azure Databricks k instalaci agentů pro posouzení ohrožení zabezpečení na uzlech clusteru Azure Databricks a ke správě uzlů prostřednictvím příslušného portálu. Všimněte si, že každé řešení třetí strany funguje jinak.

* [Ruční instalace agenta Rapid7](https://insightagent.help.rapid7.com/docs/install)

* [Ruční instalace agenta Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Skripty pro inicializaci Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné**materiály: Společnost Microsoft uchovává Azure Databricks základní image uzlů clusteru, ale zodpovídá za to, že jsou zachovány opravy těchto uzlů clusteru. Chcete-li přidat aktualizaci údržby do existujícího spuštěného clusteru, je nutné restartovat cluster.

* [Vysvětlení aktualizací údržby modulu runtime pro Azure Databricks](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: nasazení automatizovaného řešení pro správu oprav softwaru třetí strany

**Doprovodné**materiály: Společnost Microsoft udržuje Azure Databricks základní image uzlů clusteru, ale zodpovídáte za zajištění, že všechny aplikace třetích stran, které nainstalujete, zůstanou opraveny.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné**materiály: implementace řešení pro správu ohrožení zabezpečení třetí strany, které má možnost porovnat prověřování ohrožení zabezpečení v průběhu času. Pokud máte předplatné správy ohrožení zabezpečení, můžete na portálu tohoto dodavatele zobrazit a porovnat kontroly ohrožení zabezpečení zpětného vyhledávání. Všimněte si, že každé řešení třetí strany funguje jinak.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných chyb zabezpečení.

**Doprovodné**materiály: Využijte společný program pro vyhodnocování rizik (např. běžný systém vyhodnocování chyb zabezpečení) nebo výchozí hodnocení rizik poskytovaná skenovacím nástrojem třetí strany.

**Monitorování Azure Security Center**: není k dispozici

**Zodpovědnost**: zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: použijte Azure Asset Discovery.

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci vašich předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.). Ujistěte se, že ve vašem tenantovi existují příslušná oprávnění (čtení) a že máte v rámci předplatných všechna předplatná Azure i prostředky.

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

* [Jak vytvářet dotazy pomocí Azure Resource graphu](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Jak zobrazit vaše předplatná Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Principy služby Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné**materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

* [Vytváření a používání značek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné**materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků Azure. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Kromě toho použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných zákazníka, pomocí následujících integrovaných definic zásad:
- Nepovolené typy prostředků
- Povolené typy prostředků

* [Vytvoření dalších předplatných Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Postup vytvoření Skupiny pro správu](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Vytváření a používání značek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: Udržujte inventarizaci schválených prostředků Azure a softwarových titulů.

**Pokyny**: definování schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné**materiály: pomocí Azure Policy můžete klást omezení typů prostředků, které se dají vytvořit v zákaznických předplatných, a to pomocí těchto integrovaných definic zásad:
- Nepovolené typy prostředků
- Povolené typy prostředků

Pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat prostředky. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak vytvářet dotazy pomocí Azure graphu](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné**materiály: nepoužitelné; Azure Databricks je služba PaaS, zákazníci nemají přímý přístup k virtuálním počítačům v clusteru Azure Databricks.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.), včetně instancí Azure Databricks. Odeberte všechny neschválené prostředky Azure, které zjistíte. Pro Azure Databricks uzly clusteru implementujte řešení třetí strany, které vám umožní odebrat nebo upozornit na neschválený software.

* [Jak vytvářet dotazy pomocí Azure graphu](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné**materiály: nepoužitelné; Azure Databricks je služba PaaS, nemáte přímý přístup k virtuálním počítačům v clusteru Azure Databricks.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:
- Nepovolené typy prostředků
- Povolené typy prostředků

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="610-implement-approved-application-list"></a>6,10: Implementujte schválený seznam aplikací

**Doprovodné**materiály: nepoužitelné; Azure Databricks je služba PaaS, nemáte přímý přístup k virtuálním počítačům v clusteru Azure Databricks.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Omezení schopnosti uživatelů pracovat s Azure Resource Manager prostřednictvím skriptů</div>

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací "blokování přístupu" pro aplikaci Microsoft Azure Management.

* [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné**materiály: nepoužitelné; neplatí to Azure Databricks, že uživatelé (bez správců) clusteru nepotřebují přístup k jednotlivým uzlům ke spouštění úloh. Správce clusteru má ve výchozím nastavení rootový přístup ke všem uzlům clusteru.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné**materiály: nepoužitelné; Srovnávací test je určený pro služby Azure apps nebo výpočetní prostředky hostující webové aplikace.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure Databricks instance pomocí Azure Policy. Použijte aliasy Azure Policy v oboru názvů Microsoft. datacihly k vytvoření vlastních zásad pro auditování nebo prosazování konfigurace vašich Azure Databricksch instancí. Všimněte si, že všechny použité zásady nefungují ve skupině prostředků spravované datacihly.

* [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné**materiály: nepoužitelné; Azure Databricks je služba PaaS, nemáte přímý přístup k virtuálním počítačům v clusteru Azure Databricks.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure Databricks instance pomocí Azure Policy. Použijte aliasy Azure Policy v oboru názvů Microsoft. datacihly k vytvoření vlastních zásad pro auditování nebo prosazování konfigurace vašich Azure Databricksch instancí. Použijte zásady Azure [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení napříč prostředky Azure.

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Pochopení Azure Policych efektů](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Pokyny**: Azure Databricks bitových kopií operačního systému spravovaných a spravovaných Microsoftem. Zodpovídáte za implementaci konfigurace stavu na úrovni operačního systému.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné**materiály: Pokud používáte vlastní definice zásad Azure, použijte k bezpečnému ukládání a správě kódu službu Azure DevOps nebo Azure Repos.

* [Jak v Azure DevOps ukládat kód](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentace k Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Pokyny**: Pokud používáte vlastní image pro uzly Azure Databricks clusteru, nahrajte vlastní základní image do registru Docker, jako je například Azure Container Registry (ACR).

* [Jak přizpůsobit kontejnery pomocí kontejnerových služeb datacihly](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Pochopení Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: nasazení nástrojů pro správu konfigurace systému

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure Databricks instance pomocí Azure Policy. Použijte aliasy Azure Policy v oboru názvů Microsoft. datacihly k vytvoření vlastních zásad pro auditování nebo prosazování konfigurace vašich Azure Databricksch instancí.

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: nasaďte nástroje pro správu konfigurace systému pro operační systémy.

**Doprovodné**materiály: nepoužitelné; Azure Databricks je služba PaaS, nemáte přímý přístup k virtuálním počítačům v clusteru Azure Databricks.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Implementujte automatizované monitorování konfigurace pro služby Azure.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure Databricks instance pomocí Azure Policy. Použijte aliasy Azure Policy v oboru názvů Microsoft. datacihly k vytvoření vlastních zásad pro auditování nebo prosazování konfigurace vašich Azure Databricksch instancí.

* [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné**materiály: Implementujte řešení třetí strany a sledujte stav operačního systému uzlu Azure Databricks clusteru.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné**materiály: k bezpečné správě a používání tajných kódů používejte Azure Key Vault s oborem Azure Databricks tajného klíče.

* [Použití Azure Key Vault s Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné**materiály: aktuálně není k dispozici; Spravované identity nejsou aktuálně k dispozici pro Azure Databricks

* [Služby, které podporují spravované identity prostředků Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: aktuálně není k dispozici.

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

* [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné**materiály: Pokud máte předplatné platformy pro správu ohrožení zabezpečení, můžete použít inicializační skripty Azure Databricks k instalaci agentů pro posouzení ohrožení zabezpečení na Azure Databricks uzlech clusteru a ke správě uzlů prostřednictvím příslušného portálu. Všimněte si, že každé řešení třetí strany funguje jinak.

* [Ruční instalace agenta Rapid7](https://insightagent.help.rapid7.com/docs/install)

* [Ruční instalace agenta Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Skripty pro inicializaci Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure App Service), ale neběží na vašem obsahu.

Předem Prohledejte všechny soubory, které jsou odesílány do Azure Databricks uzlů clusteru nebo souvisejících prostředků.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné**materiály: Pokud máte předplatné platformy pro správu ohrožení zabezpečení, můžete použít inicializační skripty Azure Databricks k instalaci agentů pro posouzení ohrožení zabezpečení na Azure Databricks uzlech clusteru a ke správě uzlů prostřednictvím příslušného portálu. Všimněte si, že každé řešení třetí strany funguje jinak.

* [Ruční instalace agenta Rapid7](https://insightagent.help.rapid7.com/docs/install)

* [Ruční instalace agenta Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Skripty pro inicializaci Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné**materiály: u vašich Azure Databricks zdrojů dat se ujistěte, že jste nakonfigurovali odpovídající úroveň redundance dat pro váš případ použití. Pokud například používáte účet Azure Storage pro úložiště dat Azure Databricks, vyberte odpovídající možnost redundance (LRS, ZRS, GRS, RA-GRS).

* [Zdroje dat pro Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Místní zotavení po havárii pro clustery Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

**Pokyny**: zálohování všech spravovaných zákaznických klíčů souvisejících s implementacemi Azure Databricks v rámci Azure Key Vault. Pomocí REST API a CLI můžete také vytvořit každodenní zálohu konfigurací datacihly. Pomocí REST API/CLI můžete také vytvořit každodenní zálohu konfigurací datacihly.

* [Postup zálohování klíčů trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

**Doprovodné**materiály: testování obnovení zálohovaných zákaznických klíčů souvisejících s implementacemi Azure Databricks.

* [Postup obnovení klíčů trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

**Doprovodné**materiály: Ujistěte se, že je v Key Vault povolené obnovitelné odstranění, aby se chránily klíče proti náhodnému nebo škodlivému odstranění.

* [Jak povolit obnovitelné odstranění v Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: Vytvoření Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů z detekce až po přezkoumání po jednotlivých událostech.

* [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [K podpoře při vytváření vlastního plánu odpovědí na incidenty můžete také využít příručku pro zpracování incidentů v počítači s NIST.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné**materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné**materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

* [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné**materiály: kontaktní údaje incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k zákaznickým datům došlo přes protiprávní nebo neoprávněnou stranu. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

* [Jak nastavit kontakt zabezpečení Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné**materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat ověřovací data výstrah.

* [Postup konfigurace průběžného exportu](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Jak streamovat výstrahy do Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné**materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

* [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech důležitých zjištění zabezpečení do 60 dnů.

**Pokyny**: * [použijte pravidla zapojení Microsoftu a ujistěte se, že testy průniku nejsou v rozporu s zásadami Microsoftu](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) .

* [V této části najdete další informace o strategii Microsoftu a provádění testování v rámci červeného seskupování a testování průniku na cloudové infrastruktuře, služby a aplikace spravované Microsoftem.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Další informace o [plánech zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
