---
title: Směrný plán zabezpečení Azure pro registr kontejnerů Azure
description: Směrný plán zabezpečení Azure pro registr kontejnerů Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2970ebf42acb87e93f8b827b3687b219da5867c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244286"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Směrný plán zabezpečení Azure pro registr kontejnerů Azure

Azure Baseline zabezpečení pro Azure Container Registry obsahuje doporučení, která vám pomohou zlepšit stav zabezpečení vašeho nasazení.

Směrný plán pro tuto službu vychází z [azure security benchmark verze 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure s našimi pokyny pro osvědčené postupy.

Další informace naleznete v [tématu Přehled směrného plánu zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace naleznete v [tématu Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrana prostředků pomocí skupin zabezpečení sítě nebo brány Azure Firewall ve virtuální síti

**Pokyny:** Virtuální síť Azure poskytuje zabezpečené privátní sítě pro vaše azure a místní prostředky. Omezením přístupu k privátnímu registru kontejnerů Azure z virtuální sítě Azure zajistíte, že k registru přistupují jenom prostředky ve virtuální síti. U scénářů mezi místními prostory můžete také nakonfigurovat pravidla brány firewall tak, aby umožňovala přístup k registru pouze z určitých adres IP. Zpoza brány firewall nakonfigurujte pravidla přístupu brány firewall a značky služeb pro přístup k registru kontejnerů.

Omezte přístup k registru kontejnerů Azure pomocí virtuální sítě Azure nebo pravidel brány firewall:https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

Konfigurace pravidel pro přístup k registru kontejnerů Azure za bránou firewall:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Sledování a protokolování konfigurace a provozu sítí, podsítí a síťových rozhraní

**Pokyny:** Použijte Azure Security Center a navažte doporučení pro ochranu sítě k ochraně síťových prostředků v Azure. Povolte protokoly toku nsg a odesílat protokoly do účtu úložiště pro audit provozu.

Povolení protokolů toku nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Chraňte své síťové prostředky:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrana kritických webových aplikací

**Pokyny**: Nepoužije se. Benchmark je určený pro Azure App Service nebo výpočetní prostředky hostování webových aplikací.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odepřít komunikaci se známými škodlivými IP adresami

**Pokyny**: Povolte standardní ochranu DDoS ve virtuálních sítích pro ochranu před útoky DDoS. Pomocí integrované analýzy hrozeb Centra zabezpečení Azure můžete odepřít komunikaci se známými škodlivými nebo nepoužívanými internetovými IP adresami.  Nasaďte bránu Azure Firewall na každé hranici sítě organizace s povolenou službou Threat Intelligence a nakonfigurovanou na "Výstrahy a odepření" pro škodlivý síťový provoz.

Azure Security Center Just In Time Network přístup ke konfiguraci skupin y nsg omezit vystavení koncových bodů na schválené IP adresy po omezenou dobu. Pomocí azure security center adaptivní sítě k posílení zabezpečení můžete také doporučit konfigurace skupiny zabezpečení sítě, které omezují porty a zdrojové IP adresy na základě skutečného provozu a analýzy hrozeb.

Jak nakonfigurovat ochranu Před sdos:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Jak nasadit Bránu Azure Firewall:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Seznamte se s integrovanou analýzou hrozeb Centra zabezpečení Azure:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Principy adaptivního posílení sítě Centra zabezpečení Azure:https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure Security Center právě v řízení přístupu k časové síti:https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Záznam síťových paketů a protokolů toku

**Pokyny:** Povolte protokoly toku skupiny zabezpečení sítě (NSG) pro skupinu zabezpečení sítě připojenou k podsíti, která se používá k ochraně registru kontejnerů Azure. Můžete zaznamenat protokoly toku NSG do účtu úložiště Azure pro generování záznamů toku. Pokud je to nutné pro zkoumání neobvyklé aktivity, povolte zachytávání paketů Azure Network Watcher.

Povolení protokolů toku nsg:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit sledování sítě:https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Nasazení síťových systémů detekce vniknutí/prevence vniknutí (IDS/IPS)

**Pokyny:** Vyberte nabídku z Azure Marketplace, která podporuje funkce IDS/IPS s možnostmi kontroly datové části. Pokud není vyžadováno zjišťování nebo prevence narušení na základě kontroly datové části, lze použít azure bránu firewall se službou Threat Intelligence. Filtrování založené na inteligenci hrozeb Azure Firewall může upozorňovat a odmítat provoz na známé škodlivé IP adresy a domény a z nich. IP adresy a domény jsou získávány z informačního kanálu Microsoft Threat Intelligence.

Nasadit řešení brány firewall podle vašeho výběru na každé z hranic sítě vaší organizace ke zjištění nebo odepření škodlivého provozu.

Azure Marketplace:https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Jak nasadit Bránu Azure Firewall:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Jak nakonfigurovat výstrahy pomocí Azure Firewall:https://docs.microsoft.com/azure/firewall/threat-intel


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Správa návštěvnosti webových aplikací

**Pokyny**: Nepoužije se. Benchmark je určený pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizace složitosti a administrativní režie pravidel zabezpečení sítě

**Pokyny:** Pro prostředky, které potřebují přístup k registru kontejnerů, použijte značky virtuálních síťových služeb pro službu Azure Container Registry k definování ovládacích prvků přístupu k síti ve skupinách zabezpečení sítě nebo azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby "AzureContainerRegistry" v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo jako adresy změnit.

Povolit přístup podle výrobníznačky:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Udržovat standardní konfigurace zabezpečení pro síťová zařízení

**Pokyny:** Definujte a implementujte standardní konfigurace zabezpečení pro síťové prostředky přidružené k vašim registrům kontejnerů Azure pomocí Zásad Azure. Pomocí aliasů zásad Azure v oborech názvů Microsoft.ContainerRegistry a Microsoft.Network vytvořte vlastní zásady pro auditování nebo vynucení síťové konfigurace registrů kontejnerů. 

Azure Blueprints můžete použít ke zjednodušení rozsáhlých nasazení Azure tak, že zabalíte artefakty prostředí klíčů, jako jsou šablony Azure Resource Manager, ovládací prvky RBAC a zásady, v jediné definici podrobného plánu. Snadno použít podrobný plán pro nová předplatná a doladit řízení a správu prostřednictvím správy verzí.

Auditujte dodržování předpisů registrů kontejnerů Azure pomocí Zásad Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak vytvořit Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentovat pravidla konfigurace provozu

**Pokyny:** Zákazník může používat Azure Blueprints ke zjednodušení rozsáhlých nasazení Azure zabalením artefakty prostředí klíčů, jako jsou šablony Azure Resource Manager, ovládací prvky RBAC a zásady, v jedné definici podrobného plánu. Snadno použít podrobný plán pro nová předplatná a doladit řízení a správu prostřednictvím správy verzí.

Jak vytvořit Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Použití automatizovaných nástrojů ke sledování konfigurace síťových prostředků a zjišťování změn

**Pokyny:** Pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků souvisejících s registry kontejnerů. Vytvořte výstrahy v rámci Azure Monitor, který se aktivuje, když dojde ke změnám kritických síťových prostředků.

Jak zobrazit a načíst události protokolu aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak vytvořit výstrahy ve službě Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace naleznete [v tématu Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Použití schválených zdrojů synchronizace času

**Pokyny**: Microsoft udržuje zdroje času pro prostředky Azure, ale máte možnost spravovat nastavení synchronizace času pro vaše výpočetní prostředky.

Jak nakonfigurovat synchronizaci času pro výpočetní prostředky Azure:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurace správy centrálního protokolu zabezpečení

**Pokyny:** Ingestování protokolů prostřednictvím Azure Monitor agregovat data zabezpečení generované registru kontejneru Azure. V rámci Azure Monitoru můžete pomocí pracovního prostoru Log Analytics dotazovat a provádět analýzy a používat účty úložiště Azure pro dlouhodobé nebo archivní úložiště.

Protokoly registru kontejnerů Azure pro diagnostické vyhodnocení a auditování:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Povolení protokolování auditování prostředků Azure

**Pokyny:** Azure Monitor shromažďuje protokoly prostředků (dříve nazývané diagnostické protokoly) pro události řízené uživatelem ve vašem registru. Shromažďujte a konzumujte tato data k auditování událostí ověřování registru a poskytují úplnou stopu aktivity na artefakty registru, jako jsou události vyžádat a nabízená hlášení, abyste mohli diagnostikovat problémy se zabezpečením registru.

Protokoly registru kontejnerů Azure pro diagnostické vyhodnocení a auditování:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Shromažďování protokolů zabezpečení z operačních systémů

**Pokyny**: Nepoužije se. Benchmark je určen pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny:** V rámci Azure Monitor, nastavte dobu uchovávání pracovního prostoru Log Analytics podle předpisů vaší organizace dodržování předpisů. Účty úložiště Azure používejte pro dlouhodobé nebo archivní úložiště.

Jak nastavit parametry uchovávání protokolu pro pracovní prostory Analýzy protokolů:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="26-monitor-and-review-logs"></a>2.6: Sledování a kontrola protokolů

**Pokyny:** Analyzujte a monitorujte protokoly registru kontejnerů Azure pro neobvyklé chování a pravidelně kontrolujte výsledky. Pracovní prostor Služby Analýzy protokolů azure monitoru slouží ke kontrole protokolů a provádění dotazů na data protokolu.

Protokoly registru kontejnerů Azure pro diagnostické vyhodnocení a auditování:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Pochopit pracovní prostor analýzy protokolů:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak provádět vlastní dotazy v Azure Monitoru:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Povolit upozornění na anomální aktivitu

**Pokyny:** Použijte pracovní prostor Azure Log Analytics pro monitorování a upozorňování na neobvyklé aktivity v protokolech zabezpečení a událostech souvisejících s registrem kontejnerů Azure.

Protokoly registru kontejnerů Azure pro diagnostické vyhodnocení a auditování:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Jak upozornit na data protokolu analytics protokolu:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizovat protokolování antimalwaru

**Pokyny**: Nepoužije se. Azure Container Registry nezpracovává ani nevytváří protokoly související s antimalwarem.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="29-enable-dns-query-logging"></a>2.9: Povolení protokolování dotazů DNS

**Pokyny**: Nepoužije se. Azure Container Registry je koncový bod a neinicializuje komunikaci a služba nedotazuje DNS.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="210-enable-command-line-audit-logging"></a>2.10: Povolení protokolování auditu příkazového řádku

**Pokyny**: Nepoužije se. Benchmark je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace naleznete [v tématu Zabezpečení: Identita a řízení přístupu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Vedení soupisu administrativních účtů

**Pokyny:** Azure Active Directory (Azure AD) má předdefinované role, které musí být explicitně přiřazeny a jsou dotazovatelné. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy ke zjišťování účtů, které jsou členy skupin pro správu.

Pro každý registr kontejnerů Azure sledujte, jestli je předdefinovaný účet správce povolený nebo zakázaný. Zakažte účet, když se nepoužívá.

Jak získat roli adresáře ve službě Azure AD s PowerShellem:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak získat členy role adresáře ve službě Azure AD s PowerShellem:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Účet správce registru kontejnerů Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Změna výchozích hesel, pokud je to možné

**Pokyny**: Azure Active Directory (Azure AD) nemá koncept výchozí hesla. Jiné prostředky Azure, které vyžadují heslo, vynucují vytvoření hesla s požadavky na složitost a minimální délkou hesla, které se liší v závislosti na službě. Nesete odpovědnost za aplikace třetích stran a služby Marketplace, které mohou používat výchozí hesla.

Pokud je povolen výchozí účet správce registru kontejneru Azure, komplexní hesla se vytvoří automaticky a mají být otočena. Zakažte účet, když se nepoužívá.

Účet správce registru kontejnerů Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Použití vyhrazených administrativních účtů

**Pokyny**: Vytvořit standardní operační postupy týkající se používání vyhrazených administrativních účtů. Pomocí správy identit a přístupu Centra zabezpečení Azure můžete sledovat počet účtů pro správu.

Vytvořte také postupy, které povolí předdefinovaný účet správce registru kontejneru. Zakažte účet, když se nepoužívá.

Principy identity a přístupu Centra zabezpečení Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Účet správce registru kontejnerů Azure:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Použití jednotného přihlašování (SSO) s Azure Active Directory

**Naváděcí**pokyny: Kdykoli je to možné, použijte azure active directory sso namísto konfigurace jednotlivých samostatných přihlašovacích údajů pro jednotlivé služby. Používejte doporučení Centra zabezpečení Azure pro správu identit a přístupu.

Pro individuální přístup k registru kontejnerů použijte individuální přihlášení integrované s Azure Active Directory.

Principy přisátého řešení zabezpečení pomocí Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

Individuální přihlášení do registru kontejnerů:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Použití vícefaktorového ověřování pro veškerý přístup založený na službě Azure Active Directory

**Pokyny:** Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a postupujte podle doporučení Centra identit a správy přístupu Azure Security Center.

Jak povolit vícefaktorové povolení v Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak sledovat identitu a přístup v rámci Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Pro všechny administrativní úkoly používejte vyhrazené počítače (pracovní stanice s privilegovaným přístupem)

**Pokyny:** Používejte paws (privilegovaný přístup pracovnístanice) s MFA nakonfigurované pro přihlášení a konfiguraci prostředků Azure.

Další informace o pracovních stanicích s privilegovaným přístupem:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové povolení v Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Monitorování Centra zabezpečení Azure:** Není k msti

**Odpovědnost**: Zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokolování a upozornění na podezřelou aktivitu z administrativních účtů

**Pokyny:** Použijte sestavy zabezpečení Azure Active Directory (Azure AD) pro generování protokolů a výstrah, když dojde k podezřelé nebo nebezpečné aktivity v prostředí. Azure Security Center slouží ke sledování aktivity identity a přístupu.

Jak identifikovat uživatele Azure AD označené pro rizikové aktivity:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak sledovat identitu uživatelů a aktivitu přístupu v Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Správa prostředků Azure z jenom schválených umístění

**Pokyny:** Pomocí pojmenovaných umístění podmíněného přístupu můžete povolit přístup pouze z určitých logických seskupení oblastí IP adres nebo zemí nebo oblastí.

Jak nakonfigurovat pojmenovaná umístění v Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="39-use-azure-active-directory"></a>3.9: Použití služby Azure Active Directory

**Pokyny:** Použijte Azure Active Directory (Azure AD) jako centrální ověřovací a autorizační systém. Azure AD chrání data pomocí silnéšifrování pro data v klidovém stavu a při přenosu. Azure AD také soli, hashy a bezpečně ukládá přihlašovací údaje uživatele.

Jak vytvořit a nakonfigurovat instanci Azure AD:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Pravidelně kontrolujte a slaďujte přístup uživatelů

**Pokyny:** Azure Active Directory (Azure AD) poskytuje protokoly, které pomáhají zjistit zastaralé účty. Kromě toho můžete pomocí azure identity access reviews efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Přístup uživatelů lze pravidelně kontrolovat, aby se zajistilo, že pouze ti praví uživatelé mají trvalý přístup.

Principy sestav Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak používat revize přístupu k identitám Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Sledování pokusů o přístup k deaktivovaným účtům

**Pokyny:** Máte přístup ke zdrojům protokolu přihlašovací aktivity služby Azure Active Directory (Azure AD), auditování a řízení událostí rizik, které umožňují integraci s libovolným nástrojem pro správu informací o zabezpečení a událostí (SIEM) /monitorování.

Tento proces můžete zjednodušit vytvořením diagnostických nastavení pro uživatelské účty Služby Azure Active Directory a odesláním protokolů auditování a protokolů přihlášení do pracovního prostoru Analýzy protokolů. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Analýzy protokolů.

Jak integrovat protokoly aktivit Azure do Azure Monitoru:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Upozornění na odchylku chování přihlášení k účtu

**Pokyny:** Pomocí funkcí Azure Active Directory (Azure AD) Risk and Identity Protection nakonfigurujte automatické odpovědi na zjištěné podezřelé akce související s identitami uživatelů. 

Jak zobrazit Azure AD riskantní přihlášení:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak nakonfigurovat a povolit zásady rizik a zásady ochrany identity:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Poskytněte společnosti Microsoft přístup k relevantním zákaznickým datům během scénářů podpory

**Pokyny**: Není k dispozici; Customer Lockbox není aktuálně podporován pro Azure Container Registry.

Seznam služeb podporovaných customer lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace naleznete [v tématu Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Udržovat soupis citlivých informací

**Pokyny:** Pomocí značek prostředků můžete při sledování registrů kontejnerů Azure, které ukládají nebo zpracovávají citlivé informace.

Tag a verze kontejneru image nebo jiné artefakty v registru a uzamknout image nebo úložiště, na pomoc při sledování bitových kopií, které ukládají nebo zpracovávají citlivé informace.

Jak vytvářet a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Doporučení pro označování a správu verzí imitací kontejnerů:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Uzamkněte image kontejneru v registru kontejneru Azure:https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolovat systémy ukládající nebo zpracovávající citlivé informace

**Pokyny:** Implementujte samostatné registry kontejnerů, předplatná nebo skupiny pro správu pro vývoj, testování a výrobu. Zdroje, které ukládají nebo zpracovávají citlivé údaje, by měly být dostatečně izolovány.

Prostředky by měly být odděleny virtuální sítí nebo podsítí, odpovídajícím způsobem označeny a zabezpečeny skupinou zabezpečení sítě (NSG) nebo bránou Azure Firewall.

Jak vytvořit další předplatná Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:https://docs.microsoft.com/azure/governance/management-groups/create

Jak vytvářet a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Omezte přístup k registru kontejnerů Azure pomocí virtuální sítě Azure nebo pravidel brány firewall:https://docs.microsoft.com/azure/container-registry/container-registry-vnet

Jak vytvořit skupinu zabezpečení zabezpečení s konfigurací zabezpečení:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Jak nasadit Bránu Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Jak nakonfigurovat výstrahu nebo výstrahu a odmítnout pomocí Azure Firewall:

https://docs.microsoft.com/azure/firewall/threat-intel



**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Sledování a blokování neoprávněného přenosu citlivých informací

**Pokyny**: Nasaďte automatizovaný nástroj na obvody sítě, který monitoruje neoprávněný přenos citlivých informací a blokuje takové přenosy a zároveň upozorňuje odborníky na zabezpečení informací.

Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Šifrování všech citlivých informací při přepravě

**Pokyny:** Ujistěte se, že všichni klienti připojení k registru kontejnerů Azure jsou schopni vyjednat TLS 1.2 nebo vyšší. Prostředky Microsoft Azure ve výchozím nastavení vyjednávají TLS 1.2.

Postupujte podle doporučení Azure Security Center pro šifrování v klidovém stavu a šifrování při přenosu, pokud je to možné.

Pochopte šifrování při přenosu s Azure:https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Sdíleno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Použití aktivního nástroje pro zjišťování k identifikaci citlivých dat

**Pokyny:** Funkce identifikace, klasifikace a prevence ztrát dat ještě nejsou k dispozici pro Azure Container Registry. Implementujte řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.

Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Použití Azure RBAC k řízení přístupu k prostředkům

**Pokyny:** Použijte Azure Active Directory (Azure AD) RBAC k řízení přístupu k datům a prostředkům v registru kontejnerů Azure. 

Jak nakonfigurovat RBAC v Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Role a oprávnění registru kontejnerů Azure:https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Použití ochrany před ztrátou dat na hostiteli k vynucení kontroly přístupu

**Pokyny:** Pokud je to nutné pro dodržování předpisů výpočetních prostředků, implementujte nástroj třetí strany, jako je například řešení pro prevenci ztráty dat založené na hostiteli, k vynucení řízení přístupu k datům i v případě, že jsou data zkopírována ze systému.

Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Šifrování citlivých informací v klidovém stavu

**Navádění:** Použijte šifrování v klidovém stavu na všech prostředcích Azure. Ve výchozím nastavení jsou všechna data v registru kontejneru Azure šifrována v klidovém stavu pomocí klíčů spravovaných microsoftem.

Principy šifrování v klidovém stavu v Azure:https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Klíče spravované zákazníkem v registru kontejnerů Azure:https://aka.ms/acr/cmk



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokolování a upozorňování na změny kritických prostředků Azure

**Pokyny:** Azure Monitor shromažďuje protokoly prostředků (dříve nazývané diagnostické protokoly) pro události řízené uživatelem ve vašem registru. Shromažďovat a využívat tato data k auditování událostí ověřování registru a poskytnout úplnou stopu aktivity na artefakty registru, jako je například vyžádat a vyžádat události, takže můžete diagnostikovat provozní problémy s registrem.

Protokoly registru kontejnerů Azure pro diagnostické vyhodnocení a auditování:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace naleznete v [tématu Security Control: Vulnerability Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Spuštění automatizovaných nástrojů pro vyhledávání chyb zabezpečení

**Pokyny:** Postupujte podle doporučení Centra zabezpečení Azure k provádění posouzení ohrožení zabezpečení na ibi kontejnerů. Volitelně nasazujte řešení třetích stran z Azure Marketplace k provádění hodnocení ohrožení zabezpečení bitových obrázků.

Jak implementovat doporučení k vyhodnocení ohrožení zabezpečení Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Integrace registru kontejnerů Azure s Centrem zabezpečení (preview):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Nasazení řešení pro správu automatických oprav operačního systému

**Pokyny:** Společnost Microsoft provádí správu oprav v základních systémech, které podporují Azure Container Registry.

Automatizujte aktualizace bitových kopií kontejneru při zjištění aktualizací základních bitových kopií z operačního systému a dalších oprav.

Aktualizace základních bitových bitových bitových bitových žev pro úlohy registru kontejnerů Azure:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Nasazení automatizovaného řešení pro správu softwarových oprav od jiných výrobců

**Pokyny:** K opravě bitových kopií aplikací můžete použít řešení třetí strany.  Můžete také spustit úlohy registru kontejnerů Azure a automatizovat aktualizace ibi aplikací v registru kontejnerů na základě oprav zabezpečení nebo jiných aktualizací v základních ibi.

Aktualizace základních bitových obrázků pro úlohy ACR:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porovnání prohledávačů ohrožení zabezpečení zády k zadní straně

**Pokyny:** Integrujte Azure Container Registry (ACR) s Azure Security Center a povolte pravidelné prohledávání ibi kontejnerů pro chyby zabezpečení. Volitelně nasazujte řešení třetích stran z Azure Marketplace, abyste mohli provádět pravidelné kontroly chyb zabezpečení bitových obrázků.

Integrace registru kontejnerů Azure s Centrem zabezpečení (preview):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Použití procesu hodnocení rizik a upřednostnění nápravy zjištěných zranitelných míst

**Pokyny:** Integrujte Azure Container Registry (ACR) s Azure Security Center, abyste povolili pravidelné prohledávání ibi kontejnerů pro chyby zabezpečení a klasifikovali rizika. Volitelně nasazujte řešení třetích stran z Azure Marketplace, abyste mohli provádět pravidelné kontroly zranitelnosti bitových obrázků a klasifikaci rizik.

Integrace registru kontejnerů Azure s Centrem zabezpečení (preview):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Monitorování Centra zabezpečení Azure:** Není k msti

**Odpovědnost**: Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace naleznete v [tématu Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Použití Azure Asset Discovery

**Pokyny:** Pomocí Azure Resource Graph můžete dotazovat/zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, sítě, porty a protokoly atd.) v rámci vašeho předplatného.  Zajistěte příslušná (čtecí) oprávnění ve vašem tenantovi a vyjmenovali všechna předplatná Azure a také prostředky v rámci vašich předplatných.

I když klasické prostředky Azure může být zjištěna prostřednictvím grafu prostředků, je vysoce doporučeno vytvářet a používat prostředky Azure Resource Manager do budoucna.

Jak vytvářet dotazy pomocí Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak zobrazit předplatná Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Principy Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="62-maintain-asset-metadata"></a>6.2: Udržování metadat datových zdrojů

**Pokyny**: Azure Container Registry udržuje metadata včetně značek a manifestů pro image v registru. Postupujte podle doporučených postupů pro označování artefaktů.

O registrech, repozitářích a obrázcích:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Doporučení pro označování a správu verzí imitací kontejnerů:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Odstranění neautorizovaných prostředků Azure

**Pokyny**: Azure Container Registry udržuje metadata včetně značek a manifestů pro image v registru. Postupujte podle doporučených postupů pro označování artefaktů.

O registrech, repozitářích a obrázcích:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Doporučení pro označování a správu verzí imitací kontejnerů:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Udržovat soupis schválených prostředků Azure a softwarových titulů

**Pokyny:** Budete muset vytvořit inventář schválených prostředků Azure podle vašich organizačních potřeb.  

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorování neschválených prostředků Azure

**Pokyny:** Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit ve vašich předplatných.

Pomocí Azure Resource Graph můžete dotazovat/zjišťovat prostředky v rámci jejich předplatného.  Ujistěte se, že jsou schváleny všechny prostředky Azure v prostředí.

Auditujte dodržování předpisů registrů kontejnerů Azure pomocí Zásad Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvářet dotazy pomocí Azure Graphu:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Sledování neschválených softwarových aplikací v rámci výpočetních prostředků

**Pokyny:** Analyzujte a monitorujte protokoly registru kontejnerů Azure pro neobvyklé chování a pravidelně kontrolujte výsledky. Pracovní prostor Služby Analýzy protokolů azure monitoru slouží ke kontrole protokolů a provádění dotazů na data protokolu.

Protokoly registru kontejnerů Azure pro diagnostické vyhodnocení a auditování:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Pochopit pracovní prostor analýzy protokolů:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Jak provádět vlastní dotazy v Azure Monitoru:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Odebrání neschválených prostředků Azure a softwarových aplikací

**Pokyny:** Azure Automation poskytuje úplnou kontrolu při nasazení, provozu a vyřazení úloh a prostředků z provozu.  Můžete implementovat vlastní řešení pro odebrání neautorizovaných prostředků Azure. Úvod do Azure Automation:https://docs.microsoft.com/azure/automation/automation-intro


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="68-use-only-approved-applications"></a>6.8: Používejte pouze schválené aplikace

**Pokyny**: Nepoužije se. Benchmark je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="69-use-only-approved-azure-services"></a>6.9: Používejte jenom schválené služby Azure

**Pokyny:** Využijte zásady Azure k omezení služeb, které můžete poskytovat ve vašem prostředí.

Auditujte dodržování předpisů registrů kontejnerů Azure pomocí Zásad Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odepřít konkrétní typ prostředku pomocí zásad Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="610-implement-approved-application-list"></a>6.10: Implementace schváleného seznamu žádostí

**Pokyny**: Nepoužije se. Benchmark je určen pro výpočetní prostředky.



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Omezte možnost uživatelů komunikovat se Správcem azurezdrojů pomocí skriptů

**Pokyny:** Pomocí konfigurace specifické pro operační systém nebo prostředky třetích stran omezit schopnost uživatelů spouštět skripty v rámci výpočetních prostředků Azure.

Jak nakonfigurovat podmíněný přístup pro blokování přístupu ke Správci prostředků Azure:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Omezení možnosti uživatelů spouštět skripty v rámci výpočetních prostředků

**Pokyny:** Pomocí konfigurace specifické pro operační systém nebo prostředky třetích stran omezit schopnost uživatelů spouštět skripty v rámci výpočetních prostředků Azure.

Například jak řídit spuštění skriptu prostředí PowerShell v prostředí ch od Windows:https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fyzicky nebo logicky oddělit vysoce rizikové aplikace

**Pokyny**: Software, který je vyžadován pro obchodní operace, ale může být pro organizaci ohrožen vyšším rizikem, by měl být izolován v rámci vlastního virtuálního počítače nebo virtuální sítě a dostatečně zabezpečen pomocí brány Azure Firewall nebo skupiny zabezpečení sítě.

Jak vytvořit virtuální síť:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak vytvořit skupinu zabezpečení zabezpečení s konfigurací zabezpečení:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace naleznete v [tématu Zabezpečení: Zabezpečená konfigurace](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny:** Pomocí Azure Policy nebo Azure Security Center udržovat konfigurace zabezpečení pro všechny prostředky Azure.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Auditujte dodržování předpisů registrů kontejnerů Azure pomocí Zásad Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Vytvoření bezpečných konfigurací operačního systému

**Pokyny:** K udržování konfigurací zabezpečení ve všech výpočetních prostředcích použijte doporučení Azure Security Center "Náprava chyb zabezpečení v konfiguracích zabezpečení na virtuálních počítačích".

Jak sledovat doporučení Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-recommendations

Jak napravit doporučení Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Udržování zabezpečených konfigurací prostředků Azure

**Pokyny:** Pomocí zásad Azure [odepřít] a [nasadit, pokud neexistují] vynutit zabezpečené nastavení napříč prostředky Azure.

Auditujte dodržování předpisů registrů kontejnerů Azure pomocí Zásad Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Seznamte se s efekty zásad Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Udržovat bezpečné konfigurace operačního systému

**Pokyny**: Nepoužije se. Benchmark je určen pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Konfigurace prostředků Azure bezpečně ukládá

**Pokyny:** Pokud používáte vlastní definice zásad Azure, použijte Azure Repos bezpečně ukládat a spravovat svůj kód.

Jak ukládat kód v Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentace k azure repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpečné ukládání vlastních bitových kopií operačního systému

**Pokyny**: Nepoužije se. Benchmark se vztahuje na výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Nasazení nástrojů pro správu konfigurace systému

**Pokyny:** Pomocí zásad Azure můžete upozorňovat, auditovat a vynucovat konfigurace systému. Kromě toho vytvořte proces a kanál pro správu výjimek zásad.

Auditujte dodržování předpisů registrů kontejnerů Azure pomocí Zásad Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Nasazení nástrojů pro správu konfigurace systému pro operační systémy

**Pokyny**: Nepoužije se. Benchmark se vztahuje na výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementace automatického monitorování konfigurace pro služby Azure

**Pokyny:** Pomocí Azure Security Center můžete provádět prohledávací plány pro vaše prostředky Azure.

Pomocí zásad Azure můžete omezit typ prostředků, které se dají vytvořit ve vašich předplatných.

Jak napravit doporučení v Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Auditujte dodržování předpisů registrů kontejnerů Azure pomocí Zásad Azure:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementace automatizovaného monitorování konfigurace operačních systémů

**Pokyny**: Nepoužije se. Benchmark se vztahuje na výpočetní prostředky.


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpečná správa tajných kódů Azure

**Pokyny:** Pomocí identity spravované služby ve spojení s Azure Key Vault zjednodušit a zabezpečit správu tajných služeb pro vaše cloudové aplikace.

Jak integrovat s Azure Spravované identity:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak vytvořit trezor klíčů:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zajistit ověřování trezoru klíčů se spravovanou identitou:https://docs.microsoft.com/azure/key-vault/managed-identity

Použijte identitu spravovanou Azure v úlohách registru kontejnerů Azure:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpečná a automatická správa identit

**Pokyny:** Pomocí spravovaných identit můžete poskytovat služby Azure s automaticky spravovanou identitou ve službě Azure AD. Spravované identity umožňuje ověření na libovolnou službu, která podporuje ověřování Azure AD, včetně trezoru klíčů, bez pověření ve vašem kódu.

Jak nakonfigurovat spravované identity:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

K ověření v registru kontejnerů Azure použijte spravovanou identitu:https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


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

**Pokyny:** Pomocí microsoftu Antimalware pro Azure Cloud Services a virtuální počítače průběžně sledovat a bránit své prostředky. Pro Linux použijte antimalwarové řešení třetích stran.

Konfigurace antimalwaru Microsoftu pro cloudové služby a virtuální počítače:https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Předprohledávací soubory, které se mají nahrát do nevýpočetních prostředků Azure

**Pokyny**: Microsoft Antimalware je povolena na podkladovém hostiteli, který podporuje služby Azure (například Azure Container Registry), ale neběží na obsah zákazníka.

Předem proskenujte všechny soubory nahrané do nevýpočetních prostředků Azure, jako je app service, úložiště datových jezer, úložiště objektů blob atd.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Zajistěte aktualizaci antimalwarového softwaru a podpisů

**Pokyny**: Nepoužije se. Benchmark je určen pro výpočetní prostředky. Společnost Microsoft zpracovává antimalwarovou platformu.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace naleznete [v tématu Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zajistěte pravidelné automatické zálohování

**Pokyny:** Data v registru kontejnerů Microsoft Azure se vždy automaticky replikují, aby byla zajištěna odolnost a vysoká dostupnost. Azure Container Registry zkopíruje vaše data tak, aby byla chráněna před plánovanými a neplánovanými událostmi.

Volitelně geograficky replikovat registr kontejnerů pro údržbu replik registru ve více oblastech Azure. 

Geografická replikace v registru kontejnerů Azure:https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Proveďte kompletní zálohy systému a zálohujte všechny klienty spravované klíče

**Pokyny:** Volitelně zálohovat image kontejneru importem z jednoho registru do druhého.

Zálohujte klíče spravované zákazníky v Azure Key Vault pomocí nástrojů příkazového řádku Azure nebo sad SDK.

Import image kontejneru do registru kontejnerů:https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Jak zálohovat klíče trezoru klíčů v Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny:** Otestujte obnovení zálohovaných klíčů spravovaných zákazníky v azure key vaultu pomocí nástrojů příkazového řádku Azure nebo sad SDK.

Jak obnovit klíče Azure Key Vault v Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zajištění ochrany záloh a klíčů spravovaných zákazníkem

**Pokyny:** Můžete povolit obnovitelné odstranění v trezoru klíčů Azure chránit klíče před náhodným nebo škodlivým odstraněním.

Jak povolit obnovitelné odstranění v trezoru klíčů:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Monitorování Azure Security Center**: Nelze použít

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

**Pokyny**: Azure Security Center přiřadí závažnost každé výstrahy, které vám pomohou určit prioritu, které výstrahy by měly být nejprve prozkoumány. Závažnost je založena na tom, jak sebevědomý Security Center je v hledání nebo analytice slouží k vydání výstrahy, stejně jako úroveň spolehlivosti, že tam byl škodlivý záměr za aktivitu, která vedla k upozornění.

Navíc jasně označit odběry (pro ex. výroby, non-prod) a vytvořit systém pojmenování pro jasně identifikovat a kategorizovat prostředky Azure.


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="103-test-security-response-procedures"></a>10.3: Zkušební postupy odezvy zabezpečení

**Pokyny**: Proveďte cvičení k testování schopností reakce na incidenty vašich systémů na pravidelné kadenci. Identifikujte slabá místa a mezery a podle potřeby plán revidujte.

Podívejte se na publikaci NIST: Průvodce testovacími, školicími a cvičebními programy pro plány a schopnosti IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Poskytněte kontaktní údaje bezpečnostních incidentů a nakonfigurujte upozornění na bezpečnostní incidenty

**Pokyny**: Kontaktní informace o bezpečnostních incidentech společnost Microsoft použije k tomu, aby vás kontaktovala, pokud centrum MSRC (Microsoft Security Response Center) zjistí, že k datům zákazníka byla přístupná nezákonná nebo neoprávněná strana.  Zkontrolujte incidenty po faktu, abyste zajistili, že budou problémy vyřešeny.

Jak nastavit kontakt zabezpečení Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Monitorování Azure Security Center**: Nelze použít

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
