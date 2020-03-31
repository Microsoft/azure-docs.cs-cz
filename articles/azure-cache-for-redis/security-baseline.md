---
title: Směrný plán zabezpečení Azure pro azure mezipaměť pro Redis
description: Směrný plán zabezpečení Azure pro azure mezipaměť pro Redis
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 364b87e4d64b8cc65fdf293032f4340bddec957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479134"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Směrný plán zabezpečení Azure pro azure mezipaměť pro Redis

Směrný plán zabezpečení Azure pro Azure Cache for Redis obsahuje doporučení, která vám pomůžou zlepšit stav zabezpečení vašeho nasazení.

Směrný plán pro tuto službu vychází z [azure security benchmark verze 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure s našimi pokyny pro osvědčené postupy.

Další informace naleznete v [tématu Přehled směrného plánu zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace naleznete v [tématu Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrana prostředků pomocí skupin zabezpečení sítě nebo brány Azure Firewall ve virtuální síti

**Pokyny:** Nasazení instance Azure Cache for Redis v rámci virtuální sítě (VNet). Virtuální síť je privátní síť v cloudu. Když je instance Azure Cache for Redis nakonfigurovaná pomocí virtuální sítě, není veřejně adresovatelná a je přístupná jenom z virtuálních počítačů a aplikací v rámci virtuální sítě.

Můžete také zadat pravidla brány firewall s rozsahem počátečních a koncových adres IP. Při konfiguraci pravidel brány firewall se ke mezipaměti mohou připojit pouze klientská připojení ze zadaných rozsahů IP adres.

Jak nakonfigurovat podporu virtuálních sítí pro prémiovou mezipaměť Azure pro Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Jak nakonfigurovat pravidla brány firewall Azure Cache for Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Sledování a protokolování konfigurace a provozu sítí, podsítí a síťových rozhraní

**Pokyny:** Když virtuální počítače jsou nasazeny ve stejné virtuální síti jako vaše instance Azure Cache for Redis, můžete použít skupiny zabezpečení sítě (NSG) ke snížení rizika exfiltrace dat. Povolte protokoly toku nsg a odesílat protokoly do účtu úložiště Azure pro audit provozu. Protokoly toku nsg můžete také odesílat do pracovního prostoru Analýzy protokolů a pomocí traffic analytics poskytovat přehled o toku provozu ve vašem cloudu Azure. Některé výhody Traffic Analytics jsou schopnost vizualizovat aktivitu v síti a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzorce toku provozu a určit chybné konfigurace sítě.

Povolení protokolů toku nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit a používat Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrana kritických webových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odepřít komunikaci se známými škodlivými IP adresami

**Pokyny:** Nasazení virtuální sítě Azure (VNet) poskytuje rozšířené zabezpečení a izolaci pro vaši Mezipaměť Azure pro Redis, stejně jako podsítě, zásady řízení přístupu a další funkce pro další omezení přístupu. Při nasazení ve virtuální síti není azure cache pro Redis veřejně adresovatelná a je přístupná jenom z virtuálních počítačů a aplikací v rámci virtuální sítě.

Povolte standard ochrany DDoS na virtuálních sítích přidružených k vašim instancím Azure Cache for Redis, abyste se ochránili před distribuovanými útoky s cílem odepření služby (DDoS). Pomocí integrované analýzy hrozeb Centra zabezpečení Azure můžete odepřít komunikaci se známými škodlivými nebo nepoužívanými internetovými IP adresami.

Jak nakonfigurovat podporu virtuálních sítí pro prémiovou mezipaměť Azure pro Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Správa Azure DDoS Protection Standard pomocí portálu Azure:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Záznam síťových paketů a protokolů toku

**Pokyny:** Když jsou virtuální počítače nasazené ve stejné virtuální síti jako vaše instance Azure Cache for Redis, můžete použít skupiny zabezpečení sítě (NSG) ke snížení rizika exfiltrace dat. Povolte protokoly toku nsg a odesílat protokoly do účtu úložiště Azure pro audit provozu. Protokoly toku nsg můžete také odesílat do pracovního prostoru Analýzy protokolů a pomocí traffic analytics poskytovat přehled o toku provozu ve vašem cloudu Azure. Některé výhody Traffic Analytics jsou schopnost vizualizovat aktivitu v síti a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzorce toku provozu a určit chybné konfigurace sítě.

Povolení protokolů toku nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit a používat Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Nasazení síťových systémů detekce vniknutí/prevence vniknutí (IDS/IPS)

**Pokyny:** Při použití Azure Cache for Redis s webovými aplikacemi spuštěnými ve službě Azure App Service nebo výpočetních instancí nasaďte všechny prostředky v rámci virtuální sítě Azure (VNet) a zabezpečte pomocí brány Azure Web Application Firewall (WAF) na bráně webových aplikací. Nakonfigurujte waf pro spuštění v "Režimu prevence". Režim prevence blokuje vniknutí a útoky, které pravidla detekují. Útočník obdrží výjimku "403 neoprávněný přístup" a připojení je ukončeno. Režim prevence zaznamenává takové útoky v protokolech WAF.

Případně můžete vybrat nabídku z Azure Marketplace, která podporuje ids/ips funkce s možností kontroly datové části a nebo anomálie detekce.

Seznamte se s funkcemi Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

Jak nasadit Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Správa návštěvnosti webových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizace složitosti a administrativní režie pravidel zabezpečení sítě

**Pokyny:** Pomocí značek virtuálních síťových služeb definujte ovládací prvky přístupu k síti ve skupinách zabezpečení sítě (NSG) nebo Azure Firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. apimanagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo jako adresy změnit.

Můžete také použít skupiny zabezpečení aplikací (ASG) ke zjednodušení složité konfigurace zabezpečení. AsG umožňují konfigurovat zabezpečení sítě jako přirozené rozšíření struktury aplikace, což vám umožní seskupit virtuální počítače a definovat zásady zabezpečení sítě založené na těchto skupinách.

Značky virtuálních síťových služeb:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Skupiny zabezpečení aplikací:

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Udržovat standardní konfigurace zabezpečení pro síťová zařízení

**Pokyny:** Definujte a implementujte standardní konfigurace zabezpečení pro síťové prostředky související s instancemi Azure Cache for Redis pomocí zásad Azure. Pomocí aliasů zásad Azure v oborech názvů Microsoft.Cache a Microsoft.Network můžete vytvořit vlastní zásady pro auditování nebo vynucení síťové konfigurace vaší instance Azure Cache for Redis. Můžete také využít předdefinované definice zásad, jako jsou:

Měla by být povolena pouze zabezpečená připojení k mezipaměti Redis.

DDoS Protection Standard by měla být povolena

Azure Blueprints můžete také použít ke zjednodušení rozsáhlých nasazení Azure tím, že zabalíte artefakty prostředí klíčů, jako jsou šablony Azure Resource Manager (ARM), řízení přístupu na základě rolí (RBAC) a zásady v jediné definici podrobného plánu. Snadno použít podrobný plán pro nová předplatná a prostředí a doladit řízení a správu prostřednictvím správy verzí.

Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvořit Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentovat pravidla konfigurace provozu

**Pokyny:** Pomocí značek pro síťové prostředky přidružené k nasazení Azure Cache pro Redis, aby bylo možné logicky uspořádat je do taxonomie.

Jak vytvářet a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Použití automatizovaných nástrojů ke sledování konfigurace síťových prostředků a zjišťování změn

**Pokyny:** Pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků souvisejících s instancemi Azure Cache for Redis. Vytvořte výstrahy v rámci Azure Monitor, který se aktivuje, když dojde ke změnám kritických síťových prostředků.

Jak zobrazit a načíst události protokolu aktivit Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Jak vytvořit výstrahy ve službě Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace naleznete [v tématu Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Použití schválených zdrojů synchronizace času

**Pokyny**: Microsoft udržuje zdroj času používaný pro prostředky Azure, jako je Azure Cache for Redis pro časová razítka v protokolech.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurace správy centrálního protokolu zabezpečení

**Pokyny:** Povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do pracovního prostoru Log Analytics, centra událostí Azure nebo účtu úložiště Azure pro archiv. Protokoly aktivit poskytují přehled o operacích, které byly provedeny na vašich instancích Azure Cache for Redis na úrovni roviny ovládacího prvku. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni roviny ovládacího prvku pro vaše instance Azure Cache pro Redis.

Jak povolit diagnostická nastavení pro protokol aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Povolení protokolování auditování prostředků Azure

**Pokyny:** Povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do pracovního prostoru Log Analytics, centra událostí Azure nebo účtu úložiště Azure pro archiv. Protokoly aktivit poskytují přehled o operacích, které byly provedeny na vašich instancích Azure Cache for Redis na úrovni roviny ovládacího prvku. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni roviny ovládacího prvku pro vaše instance Azure Cache pro Redis.

Zatímco metriky jsou k dispozici povolením nastavení diagnostiky, protokolování auditování na rovině dat ještě není k dispozici pro Azure Cache pro Redis.

Jak povolit diagnostická nastavení pro protokol aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Shromažďování protokolů zabezpečení z operačních systémů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny:** Ve službě Azure Monitor nastavte dobu uchovávání protokolů pro pracovní prostory Log Analytics přidružené k vašim instancím Azure Cache for Redis podle předpisů vaší organizace o dodržování předpisů.

Všimněte si, že protokolování auditu v rovině dat ještě není k dispozici pro Azure Cache pro Redis.

Jak nastavit parametry uchovávání protokolů:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="26-monitor-and-review-logs"></a>2.6: Sledování a kontrola protokolů

**Pokyny:** Povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do pracovního prostoru Analýzy protokolů. Provádět dotazy v Log Analytics pro vyhledávání termínů, identifikovat trendy, analyzovat vzory a poskytnout mnoho dalších přehledů na základě dat protokolu aktivit, které mohly být shromážděny pro Azure Cache pro Redis.

Všimněte si, že protokolování auditu v rovině dat ještě není k dispozici pro Azure Cache pro Redis.

Jak povolit diagnostická nastavení pro protokol aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Jak shromažďovat a analyzovat protokoly aktivit Azure v pracovním prostoru Log Analytics v Azure Monitoru:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Povolit upozornění na anomální aktivitu

**Pokyny:** Můžete nakonfigurovat přijímat výstrahy na základě metrik a protokolů aktivit souvisejících s vaší azure cache pro redis instance. Azure Monitor umožňuje nakonfigurovat výstrahu pro odeslání e-mailovéoznámení, volání webhooku nebo vyvolání aplikace Azure Logic.

Zatímco metriky jsou k dispozici povolením nastavení diagnostiky, protokolování auditování na rovině dat ještě není k dispozici pro Azure Cache pro Redis.

Jak nakonfigurovat výstrahy pro Azure Cache pro Redis:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizovat protokolování antimalwaru

**Pokyny**: Nepoužije se; Azure Cache for Redis nezpracovává ani nevytváří protokoly související s antimalwarem.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="29-enable-dns-query-logging"></a>2.9: Povolení protokolování dotazů DNS

**Pokyny**: Nepoužije se; Azure Cache for Redis nezpracovává ani nevytváří protokoly související se službou DNS.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="210-enable-command-line-audit-logging"></a>2.10: Povolení protokolování auditu příkazového řádku

**Pokyny**: Nepoužije se; Tento pokyn je určen pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace naleznete [v tématu Zabezpečení: Identita a řízení přístupu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Vedení soupisu administrativních účtů

**Pokyny:** Služba Azure Active Directory (AD) má předdefinované role, které musí být explicitně přiřazeny a jsou dotazovatelné. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy ke zjišťování účtů, které jsou členy skupin pro správu.

Jak získat roli adresáře ve službě Azure AD s PowerShellem:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak získat členy role adresáře ve službě Azure AD s PowerShellem:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Změna výchozích hesel, pokud je to možné

**Navádění:** Řízení přístupu roviny k Azure Cache pro Redis se řídí prostřednictvím služby Azure Active Directory (AD). Azure AD nemá koncept výchozí hesla. 

Přístup k datové rovině azure cache pro Redis je řízen pomocí přístupových klíčů. Tyto klíče používají klienti, kteří se připojují k mezipaměti, a lze je kdykoli obnovit.

Není doporučeno vytvářet výchozí hesla do aplikace. Místo toho můžete uložit hesla v Azure Key Vault a pak pomocí Služby Azure Active Directory načíst.

Jak znovu vygenerovat Azure Cache pro redis přístupové klíče:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Použití vyhrazených administrativních účtů

**Pokyny**: Vytvořit standardní operační postupy týkající se používání vyhrazených administrativních účtů. Pomocí správy identit a přístupu Centra zabezpečení Azure můžete sledovat počet účtů pro správu.

Kromě toho můžete použít doporučení z Azure Security Center nebo integrované zásady Azure, jako jsou:

- K vašemu předplatnému by mělo být přiřazeno více než jednoho vlastníka.

- Zastaralé účty s oprávněními vlastníka by měly být odebrány z vašeho předplatného

- Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.

Jak pomocí Azure Security Center sledovat identitu a přístup (Preview):https://docs.microsoft.com/azure/security-center/security-center-identity-access

Jak používat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Použití jednotného přihlašování (SSO) s Azure Active Directory

**Pokyny**: Azure Cache for Redis používá přístupové klíče k ověření uživatelů a nepodporuje jednotné přihlašování (SSO) na úrovni roviny dat. Přístup k rovině ovládacího prvku pro Azure Cache pro Redis je k dispozici prostřednictvím rozhraní REST API a podporuje přistoupené zabezpečení. Chcete-li ověřit, nastavte hlavičku autorizace pro vaše požadavky na webový token JSON, který získáte ze služby Azure Active Directory.

Principy azure cache pro rozhraní REDIS REST API:https://docs.microsoft.com/rest/api/redis/

Principy přisátého řešení zabezpečení pomocí Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Použití vícefaktorového ověřování pro veškerý přístup založený na službě Azure Active Directory

**Pokyny:** Povolte vícefaktorové ověřování Azure Active Directory (AD) a postupujte podle doporučení Centra identit a správy přístupu centra zabezpečení Azure.

Jak povolit vícefaktorové povolení v Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak sledovat identitu a přístup v rámci Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Pro všechny administrativní úkoly používejte vyhrazené počítače (pracovní stanice s privilegovaným přístupem)

**Pokyny:** Používejte pracovní stanice s privilegovaným přístupem (PAW) s vícefaktorovým ověřováním (MFA) nakonfigurovaným pro přihlášení a konfiguraci prostředků Azure.

Další informace o pracovních stanicích s privilegovaným přístupem:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové povolení v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokolování a upozornění na podezřelou aktivitu z administrativních účtů

**Pokyny:** Použití Azure Active Directory (AD) Privileged Identity Management (PIM) pro generování protokolů a výstrah, když dojde k podezřelé nebo nebezpečné aktivity v prostředí.

Kromě toho pomocí zjišťování rizik Azure AD zobrazit výstrahy a sestavy o rizikové chování uživatelů.

Jak nasadit správu privilegovaných identit (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Seznamte se s detekcí rizik Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Správa prostředků Azure z jenom schválených umístění

**Pokyny:** Konfigurace pojmenovaných umístění v podmíněném přístupu služby Azure Active Directory (AD) umožňuje přístup pouze z konkrétních logických seskupení rozsahů IP adres nebo zemí nebo oblastí.

Jak nakonfigurovat pojmenovaná umístění v Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="39-use-azure-active-directory"></a>3.9: Použití služby Azure Active Directory

**Pokyny:** Jako centrální systém ověřování a autorizace použijte službu Azure Active Directory (AD). Azure AD chrání data pomocí silnéšifrování pro data v klidovém stavu a při přenosu. Azure AD také soli, hashy a bezpečně ukládá přihlašovací údaje uživatele.

Ověřování Azure AD nelze použít pro přímý přístup k Azure Cache pro redis 'rovina dat, ale přihlašovací údaje Azure AD lze použít pro správu na úrovni roviny řízení (tj. portál Azure) k řízení Azure Cache pro redis přístupové klíče.


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Pravidelně kontrolujte a slaďujte přístup uživatelů

**Pokyny:** Služba Azure Active Directory (AD) poskytuje protokoly, které vám pomohou zjistit zastaralé účty. Kromě toho můžete pomocí azure identity access reviews efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Přístup uživatelů lze pravidelně kontrolovat, aby se zajistilo, že pouze ti praví uživatelé mají trvalý přístup. 

Principy sestav Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak používat recenze přístupu k identitám Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Sledování pokusů o přístup k deaktivovaným účtům

**Pokyny:** Máte přístup k aktivitě přihlášení azure active directory (AD), auditace a zdroje protokolu rizikových událostí, které umožňují integraci s Azure Sentinel nebo siem třetí strany.

Tento proces můžete zjednodušit vytvořením diagnostických nastavení pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. Můžete nakonfigurovat požadované výstrahy protokolu v rámci Log Analytics.

Jak integrovat protokoly aktivit Azure do Azure Monitoru:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Jak napalubě Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Upozornění na odchylku chování přihlášení k účtu

**Pokyny:** Pro odchylku chování přihlášení k účtu v rovině ovládacího prvku použijte azure active directory (AD) ochrana identity a funkce detekce rizik a konfigurace automatické odpovědi na zjištěné podezřelé akce související s identitami uživatelů. Můžete také ingestovat data do Azure Sentinelu pro další šetření.

Jak zobrazit Azure AD riskantní přihlášení:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak nakonfigurovat a povolit zásady rizik a zásady ochrany identity:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak napalubě Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Poskytněte společnosti Microsoft přístup k relevantním zákaznickým datům během scénářů podpory

**Pokyny**: Zatím není k dispozici; Customer Lockbox ještě není podporován pro Azure Cache pro Redis.

Seznam služeb podporovaných customer lockboxem:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Nepoužije se

## <a name="data-protection"></a>Ochrana dat

*Další informace naleznete [v tématu Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Udržovat soupis citlivých informací

**Pokyny:** Pomocí značek můžete při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace,.

Jak vytvářet a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolovat systémy ukládající nebo zpracovávající citlivé informace

**Pokyny**: Implementujte samostatné odběry nebo skupiny pro správu pro vývoj, testování a výrobu. Instance Azure Cache for Redis by měly být odděleny virtuální sítí nebo podsítí a odpovídajícím způsobem označeny. Volitelně můžete pomocí brány firewall Azure Cache for Redis definovat pravidla tak, aby se ke mezipaměti mohla připojit pouze klientská připojení ze zadaných rozsahů IP adres.

Jak vytvořit další předplatná Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:

https://docs.microsoft.com/azure/governance/management-groups/create

Jak nasadit Azure Cache pro Redis do virtuální sítě:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Jak nakonfigurovat pravidla brány firewall Azure Cache for Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

Jak vytvářet a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Sledování a blokování neoprávněného přenosu citlivých informací

**Pokyny**: Zatím není k dispozici; Funkce identifikace, klasifikace a prevence ztrát ještě nejsou k dispozici pro Azure Cache pro Redis.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Cache for Redis a zavedla přísné ovládací prvky, aby zabránila ztrátě nebo vystavení zákaznických dat.

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Šifrování všech citlivých informací při přepravě

**Pokyny:** Azure Cache pro Redis vyžaduje ve výchozím nastavení komunikaci šifrovanou tls. V současné době jsou podporovány verze TLS 1.0, 1.1 a 1.2. TLS 1.0 a 1.1 jsou však na cestě k vyřazení v celém odvětví, takže pokud je to možné, použijte TLS 1.2. Pokud vaše klientská knihovna nebo nástroj nepodporuje TLS, pak povolení nešifrovaných připojení lze provést prostřednictvím portálu Azure nebo správu API. V takových případech, kdy šifrovaná připojení nejsou možná, umístění mezipaměti a klientské aplikace do virtuální sítě by se doporučuje.

Principy šifrování při přenosu pro Azure Cache pro Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

Seznamte se s požadovanými porty používanými ve scénářích mezipaměti virtuálnísítě:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Sdíleno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Použití aktivního nástroje pro zjišťování k identifikaci citlivých dat

**Pokyny:** Funkce identifikace, klasifikace a prevence ztrát dat ještě nejsou k dispozici pro Azure Cache for Redis. Označte instance obsahující citlivé informace jako takové a implementujte řešení třetí strany, pokud je to nutné pro účely dodržování předpisů.

Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.

Seznamte se s ochranou zákaznických dat v Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Použití Azure RBAC k řízení přístupu k prostředkům

**Pokyny:** Pomocí řízení přístupu na základě rolí Azure Active Directory (AAD) (RBAC) k řízení přístupu k rovině řízení Azure Cache for Redis (tj. portál Azure). 

Jak nakonfigurovat RBAC v Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Použití ochrany před ztrátou dat na hostiteli k vynucení kontroly přístupu

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Cache for Redis a zavedla přísné ovládací prvky, aby zabránila ztrátě nebo vystavení zákaznických dat.

Seznamte se s ochranou zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Šifrování citlivých informací v klidovém stavu

**Pokyny**: Azure Cache for Redis ukládá zákaznická data do paměti a přestože je silně chráněna mnoha ovládacími prvky implementovanými společností Microsoft, paměť není ve výchozím nastavení šifrovaná. Pokud to vaše organizace vyžaduje, šifrujte obsah před uložením do Azure Cache for Redis.

Pokud používáte funkci Azure Cache for Redis "Redis Data Persistence", data se odešlou do účtu Úložiště Azure, který vlastníte a spravujete. Perzistenci můžete nakonfigurovat z okna "Nová mezipaměť Azure pro Redis" během vytváření mezipaměti a v nabídce Prostředky pro existující mezipaměti premium.

Data ve Službě Azure Storage se šifrují a dešifrují transparentně pomocí 256bitového šifrování AES, což je jedna z nejsilnějších blokových šifer, která je k dispozici, a je kompatibilní s FIPS 140-2. Šifrování azure storage nelze zakázat. Můžete se spolehnout na klíče spravované společností Microsoft pro šifrování vašeho účtu úložiště nebo můžete spravovat šifrování pomocí vlastních klíčů.

Jak nakonfigurovat trvalost v Azure Cache pro Redis:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Principy šifrování pro účty Azure Storage:https://docs.microsoft.com/azure/storage/common/storage-service-encryption

Seznamte se s ochranou zákaznických dat Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokolování a upozorňování na změny kritických prostředků Azure

**Pokyny:** Pomocí Azure Monitor s protokolem aktivit Azure vytvořit výstrahy pro kdy dojde ke změnám v produkčních instancí Azure Cache pro Redis a dalších kritických nebo souvisejících prostředků.

Jak vytvořit výstrahy pro události protokolu aktivit Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace naleznete v [tématu Security Control: Vulnerability Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Spuštění automatizovaných nástrojů pro vyhledávání chyb zabezpečení

**Pokyny:** Postupujte podle doporučení z Azure Security Center na zabezpečení azure cache pro redis instance a související prostředky.

Společnost Microsoft provádí správu chyb zabezpečení v základních systémech, které podporují Azure Cache for Redis.

Seznamte se s doporučeními Azure Security Center:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Sdíleno

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Nasazení řešení pro správu automatických oprav operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Nasazení automatizovaného řešení pro správu softwarových oprav od jiných výrobců

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porovnání prohledávačů ohrožení zabezpečení zády k zadní straně

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Použití procesu hodnocení rizik a upřednostnění nápravy zjištěných zranitelných míst

**Pokyny:** Společnost Microsoft provádí správu chyb zabezpečení v základních systémech, které podporují azure cache pro redis.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

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

**Pokyny:** Použijte značky pro prostředky Azure, které poskytují metadata, aby je logicky uspořádali do taxonomie.

Jak vytvářet a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Odstranění neautorizovaných prostředků Azure

**Pokyny:** Použijte značkování, skupiny pro správu a samostatné předplatná, kde je to vhodné, k uspořádání a sledování Azure Cache pro instance Redis a souvisejících prostředků. Pravidelně odsouhlasete zásoby a zajistěte, aby byly z předplatného včas odstraněny neoprávněné prostředky.

Kromě toho pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

- Nepovolené typy prostředků

- Povolené typy prostředků

Jak vytvořit další předplatná Azure:https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:https://docs.microsoft.com/azure/governance/management-groups/create

Jak vytvořit a používat značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Udržovat soupis schválených prostředků Azure a softwarových titulů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorování neschválených prostředků Azure

**Pokyny**: Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

Nepovolené typy prostředků

Povolené typy prostředků

Kromě toho použijte Azure Resource Graph k dotazování/zjišťování prostředků v rámci předplatného.

Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvářet dotazy pomocí Azure Graphu:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Sledování neschválených softwarových aplikací v rámci výpočetních prostředků

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Odebrání neschválených prostředků Azure a softwarových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="68-use-only-approved-applications"></a>6.8: Používejte pouze schválené aplikace

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="69-use-only-approved-azure-services"></a>6.9: Používejte jenom schválené služby Azure

**Pokyny**: Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

Nepovolené typy prostředků

Povolené typy prostředků

Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odepřít konkrétní typ prostředku pomocí zásad Azure:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="610-implement-approved-application-list"></a>6.10: Implementace schváleného seznamu žádostí

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Omezte možnost uživatelů komunikovat se Správcem prostředků Azure prostřednictvím skriptů

**Pokyny:** Konfigurace podmíněného přístupu Azure omezit možnost uživatelů komunikovat s Azure Resource Manager (ARM) konfigurací "Blokovat přístup" pro "Microsoft Azure Management" App.

Jak nakonfigurovat podmíněný přístup pro blokování přístupu k ARM:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorování Azure Security Center**: Nelze použít

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

**Pokyny:** Definujte a implementujte standardní konfigurace zabezpečení pro vaše instance Azure Cache for Redis pomocí zásad Azure. Pomocí aliasů zásad Azure v oboru názvů Microsoft.Cache můžete vytvořit vlastní zásady pro auditování nebo vynucení konfigurace instancí Azure Cache for Redis. Můžete také využít předdefinované definice zásad související s vaší azure cache pro redis instance, jako jsou:

Měla by být povolena pouze zabezpečená připojení k mezipaměti Redis.

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

**Pokyny:** Pokud používáte vlastní definice zásad Azure nebo šablony Azure Resource Manager pro vaše instance Azure Cache pro Redis a související prostředky, použijte Azure Repos k bezpečnému ukládání a správě kódu.

Jak ukládat kód v Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentace k azure repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpečné ukládání vlastních bitových kopií operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Nasazení nástrojů pro správu konfigurace systému

**Pokyny**: Pomocí aliasů zásad Azure v oboru názvů "Microsoft.Cache" můžete vytvořit vlastní zásady pro výstrahu, audit ování a vynucení konfigurací systému. Kromě toho vytvořte proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Nasazení nástrojů pro správu konfigurace systému pro operační systémy

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementace automatického monitorování konfigurace pro služby Azure

**Pokyny**: Pomocí aliasů zásad Azure v oboru názvů "Microsoft.Cache" můžete vytvořit vlastní zásady pro výstrahu, audit ování a vynucení konfigurací systému. Pomocí zásad Azure [audit] [odepřít] a [nasadit, pokud neexistují] automaticky vynutit konfigurace pro vaše azure cache pro redis instance a související prostředky.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementace automatizovaného monitorování konfigurace operačních systémů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpečná správa tajných kódů Azure

**Pokyny:** Pro virtuální počítače Azure nebo webové aplikace spuštěné ve službě Azure App Service, které se používají k přístupu k vašim instancím Azure Cache for Redis, použijte identitu spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných služeb Azure Cache for Redis. Ujistěte se, že je povoleno měkké odstranění trezoru klíčů.

Jak integrovat s Azure Spravované identity:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak vytvořit trezor klíčů:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zajistit ověřování trezoru klíčů se spravovanou identitou:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpečná a automatická správa identit

**Pokyny:** Pro virtuální počítače Azure nebo webové aplikace spuštěné ve službě Azure App Service, které se používají k přístupu k vašim instancím Azure Cache for Redis, použijte identitu spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných služeb Azure Cache for Redis. Ujistěte se, že je povoleno měkké odstranění trezoru klíčů.

Spravované identity slouží k poskytování služeb Azure s automaticky spravovanou identitou ve službě Azure Active Directory. Spravované identity umožňují ověření na libovolné službě, která podporuje ověřování AAD, včetně Azure Key Vault, bez pověření ve vašem kódu.

Jak nakonfigurovat spravované identity:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Jak integrovat s Azure Spravované identity:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminujte nezamýšlenou expozici pověření

**Pokyny**: Implementujte skener pověření k identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů také podpoří přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je azure key vault.

Jak nastavit skener pověření:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace naleznete v [tématu Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Používejte centrálně spravovaný antimalwarový software

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

Microsoft anti-malware je povolena na podkladovém hostiteli, který podporuje služby Azure (například Azure App Service), ale neběží na obsah zákazníka.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Předprohledávací soubory, které se mají nahrát do nevýpočetních prostředků Azure

**Pokyny:** Microsoft anti-malware je povolena na podkladovém hostiteli, který podporuje služby Azure (například Azure Cache pro Redis), ale neběží na obsah zákazníka.

Předem proskente veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, jako je app service, storage datového jezera, úložiště objektů blob, databáze Azure pro PostgreSQL atd. Společnost Microsoft nemá v těchto případech přístup k vašim datům.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Zajistěte aktualizaci antimalwarového softwaru a podpisů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.

Microsoft anti-malware je povolena na podkladovém hostiteli, který podporuje služby Azure (například Azure Cache pro Redis), ale neběží na obsah zákazníka.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="data-recovery"></a>Obnovení dat

*Další informace naleznete [v tématu Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zajistěte pravidelné automatické zálohování

**Pokyny**: Povolit trvalost Redis. Redis perzis umožňuje zachovat data uložená v Redis. Můžete také pořizovat snímky a zálohovat data, která můžete načíst v případě selhání hardwaru. To je obrovská výhoda oproti základní nebo standardní úroveň, kde jsou všechna data uložena v paměti a může dojít ke ztrátě potenciálních dat v případě selhání, kde jsou uzly mezipaměti mimo provoz.

Můžete také použít Azure Cache pro Redis Export. Export umožňuje exportovat data uložená v mezipaměti Azure pro Redis do souborů RDB kompatibilních s Redisem. Pomocí této funkce můžete přesunout data z jedné mezipaměti Azure pro instanci Redis na jiný nebo na jiný server Redis. Během procesu exportu se vytvoří dočasný soubor na virtuálním počítači, který je hostitelem instance serveru Azure Cache for Redis, a soubor se nahraje do určeného účtu úložiště. Po dokončení operace exportu se stavem úspěchu nebo neúspěchu je dočasný soubor odstraněn.

Jak povolit trvalost Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Jak používat Azure Cache pro export Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Proveďte kompletní zálohy systému a zálohujte všechny klienty spravované klíče

**Pokyny**: Povolit trvalost Redis. Redis perzis umožňuje zachovat data uložená v Redis. Můžete také pořizovat snímky a zálohovat data, která můžete načíst v případě selhání hardwaru. To je obrovská výhoda oproti základní nebo standardní úroveň, kde jsou všechna data uložena v paměti a může dojít ke ztrátě potenciálních dat v případě selhání, kde jsou uzly mezipaměti mimo provoz.

Můžete také použít Azure Cache pro Redis Export. Export umožňuje exportovat data uložená v mezipaměti Azure pro Redis do souborů RDB kompatibilních s Redisem. Pomocí této funkce můžete přesunout data z jedné mezipaměti Azure pro instanci Redis na jiný nebo na jiný server Redis. Během procesu exportu se vytvoří dočasný soubor na virtuálním počítači, který je hostitelem instance serveru Azure Cache for Redis, a soubor se nahraje do určeného účtu úložiště. Po dokončení operace exportu se stavem úspěchu nebo neúspěchu je dočasný soubor odstraněn.

Pokud pomocí Azure Key Vault ukládat přihlašovací údaje pro vaše instance Azure Cache pro Redis, zajistit pravidelné automatické zálohování klíčů.

Jak povolit trvalost Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Jak používat Azure Cache pro export Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Jak zálohovat klíče trezoru klíčů:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny:** Použijte Azure Cache pro import Redis. Import lze použít k přenesení souborů RDB kompatibilních s Redisem z libovolného serveru Redis spuštěného v jakémkoli cloudu nebo prostředí, včetně redisu běžícího na Linuxu, Windows nebo libovolného poskytovatele cloudu, jako jsou Amazon Web Services a další. Import dat je snadný způsob, jak vytvořit mezipaměť s předem vyplněnými daty. Během procesu importu Azure Cache for Redis načte soubory RDB z úložiště Azure do paměti a pak vloží klíče do mezipaměti.

Pravidelně otestujte obnovení dat tajných kódů služby Azure Key Vault.

Jak používat Azure Cache pro import Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Jak obnovit tajné klíče trezoru klíčů:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zajištění ochrany záloh a klíčů spravovaných zákazníkem

**Pokyny:** Zálohy Azure Cache for Redis z redis exportu a trvalosti Redis uložených ve vybraném účtu Azure Storage. Data ve Službě Azure Storage se šifrují a dešifrují transparentně pomocí 256bitového šifrování AES, což je jedna z nejsilnějších blokových šifer, která je k dispozici, a je kompatibilní s FIPS 140-2. Šifrování azure storage nelze zakázat. Můžete se spolehnout na klíče spravované společností Microsoft pro šifrování vašeho účtu úložiště nebo můžete spravovat šifrování pomocí vlastních klíčů.

Principy šifrování pro účty Azure Storage:https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Microsoft

## <a name="incident-response"></a>Reakce na incidenty

*Další informace naleznete [v tématu Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Vytvoření průvodce odezvou na incidenty

**Pokyny:** Vytvořte průvodce odezvou na incidenty pro vaši organizaci. Ujistěte se, že existují písemné plány reakce na incidenty, které definují všechny role personálu, stejně jako fáze zpracování incidentů / správy od detekce až po kontrolu po incidentu.

Jak nakonfigurovat automatizaci pracovních postupů v Centru zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Pokyny pro vytváření vlastního procesu reakce na bezpečnostní incidenty:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomie incidentu v centru Microsoft Security Response Center:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Zákazník může také využít nist počítačové bezpečnosti Incident Handling Guide na pomoc při vytváření vlastního plánu reakce na incidenty:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Vytvoření postupu hodnocení incidentů a stanovení priorit

**Pokyny**: Centrum zabezpečení přiřazuje každé výstraze závažnost, která vám pomůže určit prioritu, které výstrahy by měly být nejprve prošetřeny. Závažnost je založena na tom, jak sebevědomý Security Center je v hledání nebo analytice slouží k vydání výstrahy, stejně jako úroveň spolehlivosti, že tam byl škodlivý záměr za aktivitu, která vedla k upozornění.

Navíc jasně označit odběry (pro ex. výroby, non-prod) a vytvořit systém pojmenování pro jasně identifikovat a kategorizovat prostředky Azure.

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="103-test-security-response-procedures"></a>10.3: Zkušební postupy odezvy zabezpečení

**Pokyny**: Proveďte cvičení k testování schopností reakce na incidenty vašich systémů na pravidelné kadenci. Identifikujte slabá místa a mezery a podle potřeby plán revidujte.

Podívejte se na publikaci NIST: Průvodce testovacími, školicími a cvičebními programy pro plány a schopnosti IT:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Poskytněte kontaktní údaje bezpečnostních incidentů a nakonfigurujte upozornění na bezpečnostní incidenty

**Pokyny**: Kontaktní informace o bezpečnostních incidentech společnost Microsoft použije k tomu, aby vás kontaktovala, pokud centrum MSRC (Microsoft Security Response Center) zjistí, že k datům zákazníka byla přístupná nezákonná nebo neoprávněná strana.  Zkontrolujte incidenty po faktu, abyste zajistili, že budou problémy vyřešeny.

Jak nastavit kontakt zabezpečení Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Začleňte výstrahy zabezpečení do systému reakce na incidenty

**Pokyny:** Exportujte výstrahy a doporučení Centra zabezpečení Azure pomocí funkce Nepřetržitý export. Nepřetržitý export umožňuje exportovat výstrahy a doporučení buď ručně, nebo průběžně. Datový konektor Azure Security Center můžete použít k streamování výstrah Sentinel.

Jak nakonfigurovat nepřetržitý export:

https://docs.microsoft.com/azure/security-center/continuous-export

Jak streamovat výstrahy do Azure Sentinelu:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizace reakce na výstrahy zabezpečení

**Pokyny:** Pomocí funkce Automatizace pracovních postupů v Centru zabezpečení Azure můžete automaticky aktivovat odpovědi prostřednictvím "Logic Apps" na výstrahy zabezpečení a doporučení.

Konfigurace automatizace pracovních postupů a aplikací logiky:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace naleznete [v tématu Bezpečnostní kontrola: Penetrační testy a cvičení červeného týmu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Proveďte pravidelné penetrační testování prostředků Azure a zajistěte nápravu všech kritických zjištění zabezpečení do 60 dnů

**Pokyny**: Postupujte podle pravidel společnosti Microsoft a ujistěte se, že vaše penetrační testy neporušují zásady společnosti Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Další informace o strategii společnosti Microsoft a provádění červených týmů a testování průniku živých webů proti cloudové infrastruktuře, službám a aplikacím spravovanému společností Microsoft naleznete zde: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

## <a name="next-steps"></a>Další kroky

- Podívejte se na [srovnávací test zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Další informace o [plánech zásad zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
