---
title: Směrný plán zabezpečení Azure pro Průzkumníka dat
description: Směrný plán zabezpečení Azure pro Průzkumníka dat
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 92a607e4c17a16d07f07c97f626ed98a213d509b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289716"
---
# <a name="azure-security-baseline-for-data-explorer"></a>Směrný plán zabezpečení Azure pro Průzkumníka dat

Směrný plán zabezpečení Azure pro Průzkumníka dat obsahuje doporučení, která vám pomohou zlepšit stav zabezpečení vašeho nasazení.

Směrný plán pro tuto službu vychází z [azure security benchmark verze 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure s našimi pokyny pro osvědčené postupy.

Další informace naleznete v [tématu Přehled směrného plánu zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace naleznete v [tématu Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrana prostředků pomocí skupin zabezpečení sítě nebo brány Azure Firewall ve virtuální síti

**Pokyny:** Azure Data Explorer podporuje nasazení clusteru do podsítě ve vaší virtuální síti. Tato funkce umožňuje vynutit pravidla skupiny zabezpečení sítě (NSG) pro provoz clusteru Azure Data Explorer, připojit místní síť k podsíti clusteru Azure Data Explorer a zabezpečit zdroje datového připojení (Event Hub a Event Grid) pomocí servisních koncových bodů.

Jak nasadit cluster Průzkumníka dat Azure do virtuální sítě:https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Sledování a protokolování konfigurace a provozu vnech, podsítí a nics

**Pokyny:** Povolte protokoly toku skupiny zabezpečení sítě (NSG) a odesílejte protokoly do účtu úložiště pro audit provozu.

Povolení protokolů toku nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Principy zabezpečení sítě poskytované službou Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrana kritických webových aplikací

**Pokyny**: Nepoužije se; Doporučení je určené pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odepřít komunikaci se známými škodlivými IP adresami

**Pokyny:** Povolte Azure DDoS Protection Standard ve virtuální síti chrání vaše clustery Průzkumníka dat pro ochranu proti útokům DDoS. Pomocí integrované analýzy hrozeb Centra zabezpečení Azure můžete odepřít komunikaci se známými škodlivými nebo nepoužívanými internetovými IP adresami.

Jak nakonfigurovat ochranu Před sdos:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Seznamte se s integrovanou analýzou hrozeb Centra zabezpečení Azure:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Záznam síťových paketů a protokolů toku

**Pokyny:** Povolte protokoly toku na skupinách zabezpečení sítě (NSG), které se používají k ochraně clusteru Průzkumníka dat Azure, a odesílejte protokoly do účtu úložiště pro audit provozu.

Povolení protokolů toku nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Nasazení systémů detekce a prevence vniknutí založených na síti

**Pokyny**: Nepoužije se; Tento ovládací prvek se provádí v koncovém bodě nebo brány firewall.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Správa návštěvnosti webových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizace složitosti a administrativní režie pravidel zabezpečení sítě

**Pokyny:** Pomocí značek služby Virtual Network Service definujte ovládací prvky přístupu k síti ve skupinách zabezpečení sítě nebo v azure firewallech přidružených k clusterům Azure Data Explorer. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. apimanagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo jako adresy změnit.

Principy a používání značek služeb:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Požadavky na konfiguraci značek služeb pro Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Udržovat standardní konfigurace zabezpečení pro síťová zařízení

**Pokyny:** Zákazník definovat a implementovat standardní konfigurace zabezpečení pro síťové prostředky s Azure Policy.

Zákazník může také použít Azure Blueprints ke zjednodušení rozsáhlých nasazení Azure tím, že zabalí artefakty prostředí klíče, jako jsou šablony ARM, ovládací prvky RBAC a zásady, v jedné definici podrobného plánu. Snadno použít podrobný plán pro nová předplatná a prostředí a doladit řízení a správu prostřednictvím správy verzí.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvořit Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1.10: Pravidla konfigurace provozu dokumentu

**Pokyny:** Používejte značky pro skupiny zabezpečení sítě (NSG) a další prostředky související se zabezpečením sítě a tokem provozu pro clustery Průzkumníka dat. Pro jednotlivá pravidla sítě zabezpečení sítě použijte pole Popis k určení obchodní potřeby a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do nebo ze sítě.

Jak vytvářet a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Použití automatizovaných nástrojů ke sledování konfigurace síťových prostředků a zjišťování změn

**Pokyny:** Pomocí zásad Azure ověřte (nebo osvoňte) konfiguraci síťových prostředků.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace naleznete [v tématu Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Použití schválených zdrojů synchronizace času

**Pokyny**: Microsoft udržuje zdroje času pro prostředky Azure, zákazníci mohou aktualizovat synchronizaci času pro výpočetní nasazení vlastněná zákazníkem.

Jak nakonfigurovat synchronizaci času pro výpočetní prostředky Azure:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurace správy centrálního protokolu zabezpečení

**Pokyny:** Azure Data Explorer používá diagnostické protokoly pro přehled o úspěchy a selhání ingestování. Protokoly operací můžete exportovat do Azure Storage, Event Hub nebo Log Analytics a sledovat stav ingestování.

Jak sledovat operace ingestování Průzkumníka dat Azure:

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Jak ingestovat a dotazovat data monitorování v Azure Data Exploreru:

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Povolení protokolování auditování prostředků Azure

**Pokyny:** Povolte diagnostická nastavení pro Azure Data Explorer pro přístup a protokolování ke službám konkrétní operace a protokolování. Protokoly aktivit Azure v rámci Azure Monitoru, který zahrnuje protokolování na vysoké úrovni o prostředku jsou ve výchozím nastavení povolené.

Jak sledovat operace ingestování Průzkumníka dat Azure:https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Jak shromažďovat protokoly platformy a metriky pomocí Azure Monitoru:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Přehled protokolů platformy Azure:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Shromažďování protokolů zabezpečení z operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny:** V rámci Azure Monitor, nastavte dobu uchovávání pracovního prostoru Log Analytics podle předpisů vaší organizace dodržování předpisů. Účty úložiště Azure používejte pro dlouhodobé nebo archivní úložiště.

Jak nastavit parametry uchovávání protokolu pro pracovní prostory Analýzy protokolů:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="26-monitor-and-review-logs"></a>2.6: Sledování a revize protokolů

**Pokyny:** Analyzujte a monitorujte protokoly pro neobvyklé chování a pravidelně kontrolujte výsledky. Po povolení nastavení diagnostiky pro Azure Data Explorer, použijte Azure Monitor log Analytics Pracovní prostor pro kontrolu protokolů a provádět dotazy na data protokolu.

Principy pracovního prostoru analýzy protokolů:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak provádět vlastní dotazy v Azure Monitoru:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Povolit upozornění na anomální aktivitu

**Pokyny**: Nepoužije se; Azure Data Explorer nemá tuto možnost.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizovat protokolování antimalwaru

**Pokyny**: Nepoužije se; Azure Data Explorer nezpracovává protokolování antimalwaru.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="29-enable-dns-query-logging"></a>2.9: Povolení protokolování dotazů DNS

**Pokyny:** Nelze použít: Dotaz DNS není funkcí Průzkumníka dat Azure.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="210-enable-command-line-audit-logging"></a>2.10: Povolení protokolování auditu příkazového řádku

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace naleznete [v tématu Zabezpečení: Identita a řízení přístupu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Udržovat inventuru účtů pro správu

**Pokyny:** V Průzkumníku dat Azure role zabezpečení definují, které objekty zabezpečení (uživatelé a aplikace) mají oprávnění k provozu na zabezpečeném prostředku, jako je databáze nebo tabulka, a jaké operace jsou povoleny.  Kusto dotaz můžete využít k seznamu principů v roli správce pro clustery a databáze Azure Data Explorer.
Správa rolí zabezpečení v Průzkumníku dat Azure pomocí dotazu Kusto:https://docs.microsoft.com/azure/kusto/management/security-roles



**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: Azure AD nemá koncept výchozí hesla. Jiné prostředky Azure, které vyžadují heslo, vynutí vytvoření hesla s požadavky na složitost a minimální délkou hesla, která se liší v závislosti na službě. Nesete odpovědnost za aplikace třetích stran a služby marketplace, které mohou používat výchozí hesla.


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Zajistit používání vyhrazených administrativních účtů

**Pokyny**: Zákazník vytvořit standardní operační postupy týkající se používání vyhrazených administrativních účtů. Pomocí správy identit a přístupu Centra zabezpečení Azure můžete sledovat počet účtů pro správu.

Zákazníci mohou také povolit přístup just-in-time / just-enough-Access pomocí privilegovaných rolí správy privilegovaných identit Azure AD pro služby Microsoft a Azure ARM. 

Co je správa privilegovaných identit Azure AD?:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Využití jednotného přihlašování (SSO) pomocí služby Azure Active Directory

**Pokyny:** Pokud je to možné, zákazník používat přiřazování služeb při microsoftmuž a služeb ní ve službě Azure Active Directory (Azure AD) místo konfigurace jednotlivých samostatných přihlašovacích údajů pro jednotlivé služby. Používejte doporučení Centra zabezpečení Azure pro správu identit a přístupu.

Principy přisážacího zabezpečení pomocí Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Vícefaktorové ověřování použijte pro veškerý přístup založený na službě Azure Active Directory.

**Pokyny:** Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a postupujte podle doporučení Centra identit a správy přístupu Azure Security Center.

Jak povolit vícefaktorové povolení v Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

Jak sledovat identitu a přístup v rámci Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Použití vyhrazených počítačů (pracovní stanice s privilegovaným přístupem) pro všechny administrativní úkoly

**Pokyny:** Používejte paws (privilegovaný přístup pracovnístanice) s vícefaktorovým ověřováním (MFA) nakonfigurované pro přihlášení a konfiguraci prostředků Azure.

Další informace o pracovních stanicích s privilegovaným přístupem:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové povolení v Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3.7: Protokolování a záznam o podezřelé aktivitě na účtech pro správu

**Pokyny:** Použijte sestavy zabezpečení Azure Active Directory (Azure AD) pro generování protokolů a výstrah, když dojde k podezřelé nebo nebezpečné aktivity v prostředí. Sledování aktivity identity a přístupu pomocí Centra zabezpečení Azure

Jak identifikovat uživatele Azure AD označené pro rizikové aktivity:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak sledovat identitu uživatelů a aktivitu přístupu v Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Správa prostředků Azure z jenom schválených umístění

**Pokyny:** Zákazník má používat pojmenovaná umístění podmíněného přístupu, která umožňují přístup pouze z určitých logických seskupení oblastí IP adres nebo zemí nebo oblastí.

Jak nakonfigurovat pojmenovaná umístění v Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="39-utilize-azure-active-directory"></a>3.9: Využití služby Azure Active Directory

**Pokyny:** Azure Active Directory (Azure AD) je upřednostňovaná metoda pro ověřování na Azure Data Explorer. Podporuje řadu scénářů ověřování:

Ověřování uživatelů (interaktivní přihlášení): Používá se k ověření lidských objektů.

Ověřování aplikací (neinteraktivní přihlášení): Používá se k ověřování služeb a aplikací, které musí být spuštěny/ověřeny bez přítomnosti lidského uživatele.

Přehled řízení přístupu průzkumníka dat Azure:https://docs.microsoft.com/azure/kusto/management/access-control

Ověřování pomocí služby Azure Active Directory:https://docs.microsoft.com/azure/kusto/management/access-control/aad



**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Pravidelné přezkoumávání a slaďuje přístup uživatelů

**Pokyny:** Azure Active Directory (Azure AD) poskytuje protokoly, které pomáhají zjistit zastaralé účty. Kromě toho můžete pomocí azure identity access reviews efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Přístup uživatele lze pravidelně kontrolovat, aby se zajistilo, že pouze ti praví uživatelé mají trvalý přístup. 

Ověřování pomocí Azure AD pro Přístup k Průzkumníkům dat Azure:https://docs.microsoft.com/azure/kusto/management/access-control/how-to-authenticate-with-aad

Sestavy Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak používat recenze přístupu k identitám Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Sledování pokusů o přístup k deaktivovaným účtům

**Pokyny:** Můžete použít Azure Active Directory (Azure AD) Přihlásit aktivity, audit a zdroje protokolu rizikových událostí pro monitorování, které umožňuje integrovat s libovolným zabezpečení informací a správy událostí (SIEM) / monitorovací nástroj.

 Tento proces můžete zjednodušit vytvořením diagnostických nastavení pro uživatelské účty Azure Active Directory, odesláním protokolů auditu a protokolů přihlášení do pracovního prostoru Analýzy protokolů. Zákazník nakonfigurovat požadované výstrahy v rámci pracovního prostoru Analýzy protokolů.

Jak integrovat protokoly aktivit Azure do Azure Monitoru:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Upozornění na odchylku chování přihlášení k účtu

**Pokyny:** Pomocí funkce Azure Active Directory (Azure AD) detekce rizik a ochrana identity nakonfigurujte automatické odpovědi na zjištěné podezřelé akce související s identitami uživatelů. Kromě toho můžete ingestovat data do Azure Sentinelu pro další šetření.

Jak zobrazit Azure AD riskantní přihlášení:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak nakonfigurovat a povolit zásady rizik a zásady ochrany identity:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Poskytněte společnosti Microsoft přístup k relevantním zákaznickým datům během scénářů podpory

**Pokyny:** Ve scénářích podpory, kde společnost Microsoft potřebuje přístup k zákaznickým datům, poskytuje customer lockbox rozhraní, které zákazníkům umožňuje kontrolovat a schvalovat nebo zamítat požadavky na přístup k datům zákazníků.

Principy bezpečnostního schránce zákazníka:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

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

**Pokyny**: Implementujte samostatné odběry nebo skupiny pro správu pro vývoj, testování a výrobu. Clustery Azure Data Explorer by měly být odděleny od jiných prostředků virtuální sítí nebo podsítí, odpovídajícím způsobem označeny a zabezpečeny v rámci skupiny zabezpečení sítě (NSG) nebo Azure Firewall. Clustery Průzkumníka dat, které ukládají nebo zpracovávají citlivá data, by měly být dostatečně izolované.

Jak vytvořit další předplatná Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:https://docs.microsoft.com/azure/governance/management-groups/create

Jak vytvářet a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak nasadit cluster Průzkumníka dat Azure do virtuální sítě:https://docs.microsoft.com/azure/data-explorer/vnet-deployment

Jak vytvořit skupinu zabezpečení zabezpečení s konfigurací zabezpečení:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Sledování a blokování neoprávněného přenosu citlivých informací

**Pokyny**: Nepoužije se; Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Šifrování všech citlivých informací při přepravě

**Pokyny:** Cluster Průzkumník dat Azure ve výchozím nastavení vyjedná TLS 1.2. Ujistěte se, že všichni klienti připojení k prostředkům Azure jsou schopni vyjednat TLS 1.2 nebo vyšší.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4.5: <div>Použití nástroje active discovery k identifikaci citlivých dat</div>

**Pokyny:** Funkce identifikace, klasifikace a prevence ztrát nejsou pro Azure Data Explorer ještě dostupné. Implementujte řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.

Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4.6: <div>Použití Azure RBAC k řízení přístupu k prostředkům</div>

**Pokyny**: Azure Data Explorer umožňuje řídit přístup k databázím a tabulkám pomocí modelu řízení přístupu na základě rolí (RBAC). V rámci tohoto modelu objekty (uživatelé, skupiny a aplikace) jsou mapovány na role. Objekty zabezpečení mohou přistupovat k prostředkům podle rolí, které jsou přiřazeny.

Seznam rolí a oprávnění a pokyny ke konfiguraci RBAC pro Průzkumníka dat Azure:https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Použití funkce Zabránění ztrátám dat na hostiteli k vynucení řízení přístupu

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

Microsoft spravuje základní infrastrukturu pro Azure Data Explorer a zavedl přísné ovládací prvky, aby se zabránilo ztrátě nebo vystavení zákaznických dat.

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Šifrování citlivých informací v klidovém stavu

**Pokyny:** Azure Disk Encryption pomáhá chránit a chránit vaše data, aby splňovaly vaše závazky v oblasti zabezpečení a dodržování předpisů. Poskytuje šifrování svazku pro operační systém a datové disky virtuálních počítačů clusteru. Integruje se také s Azure Key Vault, který nám umožňuje řídit a spravovat šifrovací klíče a tajné klíče disku a zajistit, aby všechna data na discích virtuálních počítačů byla šifrovaná v klidovém stavu během azure storage.

Jak povolit šifrování v klidovém stavu pro clustery Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokolování a upozorňování na změny kritických prostředků Azure

**Pokyny:** Pomocí Azure Monitoru s protokolem aktivit Azure můžete vytvářet výstrahy, když dojde ke změnám na úrovni prostředků ve vašich clusterech Průzkumníka dat Azure.

Jak vytvořit výstrahy pro události protokolu aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Jak vytvořit výstrahy pro události protokolu aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace naleznete v [tématu Security Control: Vulnerability Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Spuštění automatizovaných nástrojů pro kontrolu zranitelnosti

**Pokyny:** Postupujte podle doporučení centra zabezpečení Azure k zabezpečení prostředků Azure Data Exploreru.

Microsoft také provádí správu chyb zabezpečení v základních systémech, které podporují Azure Data Explorer.

Seznamte se s doporučeními Azure Security Center:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Nasazení řešení automatické správy oprav operačních systémů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Nasazení automatizovaného řešení pro správu oprav softwaru třetích stran

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porovnání prohledávačů chyb zabezpečení zády k zadní straně

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Využijte proces hodnocení rizika k upřednostnění nápravy zjištěných chyb zabezpečení.

**Pokyny:** Použijte výchozí hodnocení rizik (Zabezpečené skóre) poskytované Centrem zabezpečení Azure.
Principy skóre zabezpečení Centra zabezpečení Azure:https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace naleznete v [tématu Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-utilize-azure-asset-discovery"></a>6.1: Využití Azure Asset Discovery

**Pokyny:** Pomocí Azure Resource Graph můžete dotazovat/zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, sítě, porty a protokoly atd.) v rámci vašeho předplatného. Zajistěte příslušná (čtecí) oprávnění ve vašem tenantovi a vyjmenovali všechna předplatná Azure a také prostředky v rámci vašich předplatných.

I když klasické prostředky Azure může být zjištěna prostřednictvím grafu prostředků, je vysoce doporučeno vytvářet a používat prostředky Azure Resource Manager do budoucna.

Jak vytvářet dotazy pomocí Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak zobrazit předplatná Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Principy Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="62-maintain-asset-metadata"></a>6.2: Udržovat metadata datových zdrojů

**Pokyny:** Použijte značky pro prostředky Azure, které poskytují metadata, aby je logicky uspořádali do taxonomie.

Jak vytvářet a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Odstranění neautorizovaných prostředků Azure

**Pokyny**: V případě potřeby můžete k uspořádání a sledování datových zdrojů použít příslušné konvence pojmenování, označování, skupiny pro správu nebo samostatná předplatná. Azure Resource Graph můžete použít k odsouhlasení inventáře v pravidelných intervalech a ujistěte se, že neoprávněné prostředky jsou odstraněny z předplatného včas. 

Jak vytvořit další předplatná Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:https://docs.microsoft.com/azure/governance/management-groups/create

Jak vytvořit a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak vytvářet dotazy pomocí Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Udržujte inventář schválených prostředků Azure a softwarových titulů.

**Pokyny:** Budete muset vytvořit inventář schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle vašich organizačních potřeb.  


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorování neschválených prostředků Azure

**Pokyny: Zásady**Azure můžete použít k omezení typu prostředků, které lze vytvořit v rámci předplatného zákazníků pomocí následujících předdefinovaných definic zásad:

    - Nepovolené typy prostředků

    - Povolené typy prostředků

Události generované zásadami budete moci sledovat pomocí 

Protokoly aktivit, které lze sledovat pomocí Azure Monitoru.

Kromě toho můžete použít Azure Resource Graph k dotazování/zjišťování prostředků v rámci předplatného.

Kurz: Vytvářejte a spravujte zásady pro vynucení dodržování předpisů:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Úvodní příručka: Spusťte první dotaz na graf prostředků pomocí Průzkumníka prostředků Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Vytvářejte, zobrazujte a spravujte výstrahy protokolu aktivit pomocí Azure Monitoru:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Sledování neschválených softwarových aplikací v rámci výpočetních prostředků

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Odebrání neschválených prostředků A Softwarových aplikací Azure

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="68-utilize-only-approved-applications"></a>6.8: Využijte pouze schválené aplikace

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="69-utilize-only-approved-azure-services"></a>6.9: Využití jenom schválených služeb Azure

**Pokyny: Zásady**Azure můžete použít k omezení typu prostředků, které lze vytvořit v rámci předplatného zákazníků pomocí následujících předdefinovaných definic zásad:

    - Nepovolené typy prostředků

    - Povolené typy prostředků

Kurz: Vytvářejte a spravujte zásady pro vynucení dodržování předpisů:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Ukázky zásad Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="610-implement-approved-application-list"></a>6.10: Implementace schváleného seznamu žádostí

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Omezte možnost uživatelů komunikovat se Správcem prostředků Azure prostřednictvím skriptů

**Pokyny:** Pomocí podmíněného přístupu Azure omezte možnost uživatelů pracovat se Správcem prostředků Azure konfigurací "Blokovat přístup" pro aplikaci Microsoft Azure Management. Tím zabráníte vytváření a změnám prostředků v rámci vašich předplatných Azure. 

Správa přístupu ke správě Azure pomocí podmíněného přístupu:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Omezení možnosti uživatelů spouštět skripty v rámci výpočetních prostředků

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fyzicky nebo logicky oddělit vysoce rizikové aplikace

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace naleznete v [tématu Zabezpečení: Zabezpečená konfigurace](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny:** Pomocí aliasů zásad Azure vytvořte vlastní zásady k auditování nebo vynucení konfigurace prostředků Azure. Můžete také použít předdefinované definice zásad Azure.

Azure Resource Manager má také možnost exportovat šablonu v JavaScriptu zápisu objektu (JSON), který by měl být přezkoumána, aby zajistily, že konfigurace splňují nebo překračují požadavky na zabezpečení pro vaši organizaci.

Můžete také použít doporučení z Azure Security Center jako výchozí hodnotu konfigurace pro vaše prostředky Azure.

Jak zobrazit dostupné aliasy zásad Azure:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Kurz: Vytvářejte a spravujte zásady pro vynucení dodržování předpisů:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Export jednoho a více prostředků do šablony na webu Azure Portal:https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

Bezpečnostní doporučení - referenční příručka:https://docs.microsoft.com/azure/security-center/recommendations-reference



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Vytvoření bezpečných konfigurací pro váš operační systém

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Udržovat zabezpečené konfigurace pro všechny prostředky Azure

**Pokyny:** Pomocí zásad Azure [odepřít] a [nasadit, pokud neexistují] vynutit zabezpečené nastavení napříč prostředky Azure.  Můžete použít řešení, jako je sledování změn, řídicí panel dodržování zásad nebo vlastní řešení snadno identifikovat změny zabezpečení ve vašem prostředí.

Seznamte se s efekty zásad Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

Vytvářejte a spravujte zásady pro vynucení dodržování předpisů:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Sledujte změny ve vašem prostředí pomocí řešení Sledování změn:https://docs.microsoft.com/azure/automation/change-tracking

Získejte data o dodržování předpisů o prostředcích Azure:https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Udržovat zabezpečené konfigurace pro operační systémy

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Konfigurace prostředků Azure bezpečně ukládat

**Pokyny:** Pomocí Azure Repos bezpečně ukládat a spravovat svůj kód, jako jsou vlastní zásady Azure, azure resource manager šablony, skripty konfigurace požadovaného stavu atd. Přístup k prostředkům, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, integrovaným skupinám zabezpečení nebo skupinám definovaným ve službě Azure Active Directory (Azure AD), pokud jsou integrovány s Azure DevOps, nebo službou Active Directory, pokud jsou integrovány s TFS.

Jak ukládat kód v Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Oprávnění a skupiny v Azure DevOps:https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpečně ukládat obrázky vlastního operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Nasazení nástrojů pro správu konfigurace systému

**Pokyny:** Definujte a implementujte standardní konfigurace zabezpečení pro prostředky Azure pomocí zásad Azure. Pomocí aliasů zásad Azure můžete vytvářet vlastní zásady k auditování nebo vynucení síťové konfigurace prostředků Azure. Můžete také použít předdefinované definice zásad související s konkrétními zdroji.  Kromě toho můžete použít Azure Automation k nasazení změny konfigurace.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak používat aliasy:https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Nasazení nástrojů pro správu konfigurace systému pro operační systémy

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementace monitorování automatické konfigurace pro služby Azure

**Pokyny:** Pomocí aliasů zásad Azure vytvořte vlastní zásady pro výstrahu, audit ování a vynucení konfigurací systému. Pomocí zásad Azure [audit] [odepřít] a [nasadit, pokud neexistují] automaticky vynutit konfigurace pro vaše prostředky Azure.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementace automatického monitorování konfigurace operačních systémů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="711-securely-manage-azure-secrets"></a>7.11: Bezpečná správa tajných kódů Azure

**Navádění:** Azure Disk Encryption poskytuje šifrování svazku pro operační systém a datové disky virtuálních počítačů clusteru Azure Data Explorer. Integruje se také s Azure Key Vault, který umožňuje řídit a spravovat šifrovací klíče a tajné klíče disku a zajistit, aby všechna data na discích virtuálních počítačů byla šifrovaná v klidovém stavu během azure storage.

Jak zabezpečit cluster v Azure Data Exploreru:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Microsoft

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Bezpečná a automatická správa identit

**Pokyny:** Pomocí spravovaných identit můžete poskytovat služby Azure s automaticky spravovanou identitou ve službě Azure AD. Spravované identity umožňuje ověření na libovolnou službu, která podporuje ověřování Azure AD, včetně trezoru klíčů, bez pověření ve vašem kódu. Jak nakonfigurovat spravované identity:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Konfigurace spravovaných identit pro cluster Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/managed-identities


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminujte nezamýšlenou expozici pověření

**Pokyny**: Implementujte skener pověření k identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů také podpoří přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je azure key vault. 

Jak nastavit skener pověření:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace naleznete v [tématu Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8.1: Využijte centrálně spravovaný antimalwarový software

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

Microsoft anti-malware je povolena na podkladovém hostiteli, který podporuje služby Azure (například Azure Data Explorer), ale neběží na obsah zákazníka.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Předprohledávací soubory, které se mají nahrát do nevýpočetních prostředků Azure

**Pokyny:** Microsoft anti-malware je povolena na podkladovém hostiteli, který podporuje služby Azure (například Azure Data Explorer), ale neběží na obsah zákazníků.

Předem proskente veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, jako je Azure Data Explorer, Storage data lake, Blob Storage, Azure Database for PostgreSQL atd. Společnost Microsoft nemá v těchto případech přístup k vašim datům.



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Zajistěte aktualizaci softwaru a podpisů ochrany proti malwaru

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

Microsoft anti-malware je povolena na podkladovém hostiteli, který podporuje služby Azure, ale neběží na obsah zákazníka.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="data-recovery"></a>Obnovení dat

*Další informace naleznete [v tématu Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zajistěte pravidelné automatické zálohování

**Pokyny:** Data v účtu úložiště Microsoft Azure používaná vaším clusterem Průzkumníků dat se vždy replikují, aby byla zajištěna odolnost a vysoká dostupnost. Azure Storage zkopíruje vaše data tak, aby byla chráněna před plánovanými a neplánovanými událostmi, včetně přechodných selhání hardwaru, výpadků sítě nebo napájení a rozsáhlých přírodních katastrof. Můžete se rozhodnout replikovat data ve stejném datovém centru, v zónových datových centrech ve stejné oblasti nebo v geograficky oddělených oblastech.

Principy redundance úložiště Azure a smluv o úrovni služeb:https://docs.microsoft.com/azure/storage/common/storage-redundancy

Export dat do úložiště :https://docs.microsoft.com/azure/kusto/management/data-export/export-data-to-storage



**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Proveďte kompletní zálohy systému a zálohujte všechny klienty spravované klíče

**Naváděcí**pokyny: Průzkumník dat Azure šifruje všechna data v účtu úložiště v klidovém stavu. Ve výchozím nastavení jsou data šifrována pomocí klíčů spravovaných společností Microsoft. Pro další kontrolu nad šifrovacími klíči můžete zadat klíče spravované zákazníkem, které se použijí pro šifrování dat. Klíče spravované zákazníkem musí být uloženy v trezoru klíčů Azure. 

Konfigurace klíčů spravovaných zákazníkem pomocí jazyka C# :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Konfigurace klíčů spravovaných zákazníky pomocí šablony Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

Jak zálohovat certifikáty Azure Key Vault:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny:** Pravidelně otestujte obnovení dat tajných kódů úložiště klíčů Azure.

Jak obnovit certifikáty Azure Key Vault:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

Konfigurace klíčů spravovaných zákazníkem pomocí jazyka C# :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Konfigurace klíčů spravovaných zákazníky pomocí šablony Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zajištění ochrany záloh a klíčů spravovaných zákazníkem

**Pokyny:** Zákazník povolit Soft-Delete v trezoru klíčů chránit klíče před náhodným nebo škodlivým odstraněním.  Můžete také povolit ochranu proti vymazání tak, aby pokud trezor nebo objekt v odstraněném stavu, nelze jej vymazat, dokud neuběhne doba uchování.  

Jak povolit ochranu proti softwarovému odstranění a vymazání v trezoru klíčů:https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

Konfigurace klíčů spravovaných zákazníkem pomocí jazyka C# :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Konfigurace klíčů spravovaných zákazníky pomocí šablony Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace naleznete [v tématu Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10.1: Vytvoření průvodce odezvou na incidenty

**Pokyny:** Vytvořte průvodce odezvou na incidenty pro vaši organizaci. Ujistěte se, že existují písemné plány reakce na incidenty, které definují všechny role personálu, stejně jako fáze zpracování incidentů / správy od detekce až po kontrolu po incidentu.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

    



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Vytvoření postupu hodnocení incidentů a stanovení priorit

**Pokyny**: Centrum zabezpečení přiřazuje každé výstraze závažnost, která vám pomůže určit prioritu, které výstrahy by měly být nejprve prošetřeny. Závažnost je založena na tom, jak sebevědomý Security Center je v hledání nebo analytice slouží k vydání výstrahy, stejně jako úroveň spolehlivosti, že tam byl škodlivý záměr za aktivitu, která vedla k upozornění. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

    Use tags to organize your Azure resources: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="103-test-security-response-procedures"></a>10.3: Zkušební postupy odezvy zabezpečení

**Pokyny:** Cvičení pro provádění testování možností reakce na incidenty vašich systémů na pravidelné kadence, které pomáhají chránit vaše prostředky Azure. Identifikujte slabá místa a mezery a podle potřeby plán revidujte.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Poskytněte kontaktní údaje &nbsp;bezpečnostních incidentů a nakonfigurujte upozornění pro bezpečnostní incidenty

**Pokyny**: Kontaktní informace o bezpečnostních incidentech společnost Microsoft použije k tomu, aby vás kontaktovala, pokud centrum MSRC (Microsoft Security Response Center) zjistí, že k vašim datům získala přístup nezákonná nebo neoprávněná strana. Zkontrolujte incidenty po faktu, abyste zajistili, že budou problémy vyřešeny.
    

Jak nastavit kontakt zabezpečení Centra zabezpečení Azure:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Začleňte výstrahy zabezpečení do systému reakce na incidenty

**Pokyny:** Exportujte výstrahy a doporučení Centra zabezpečení Azure pomocí funkce nepřetržitého exportu, která vám pomůže identifikovat rizika pro prostředky Azure. Nepřetržitý export umožňuje exportovat výstrahy a doporučení buď ručně, nebo průběžně. Datový konektor Azure Security Center můžete použít k streamování výstrah do Azure Sentinelu.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizace reakce na výstrahy zabezpečení

**Pokyny:** Pomocí funkce Automatizace pracovních postupů v Centru zabezpečení Azure můžete automaticky aktivovat odpovědi prostřednictvím "Logic Apps" na výstrahy zabezpečení a doporučení k ochraně prostředků Azure.
    

    How to configure Workflow Automation and Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation



**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace naleznete [v tématu Bezpečnostní kontrola: Penetrační testy a cvičení červeného týmu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Proveďte pravidelné penetrační testování vašich prostředků Azure a zajistěte nápravu všech kritických zjištění zabezpečení do 60 dnů

**Pokyny**: Postupujte podle pravidel společnosti Microsoft a ujistěte se, https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1že penetrační testy neporušují zásady společnosti Microsoft: .

Další informace o strategii společnosti Microsoft a provádění červených týmů a testování pronikání živých webů proti cloudové infrastruktuře, službám a aplikacím společnosti Microsoft naleznete zde:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

## <a name="next-steps"></a>Další kroky

- Podívejte se na [srovnávací test zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Další informace o [plánech zásad zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
