---
title: Směrný plán zabezpečení Azure pro dávku
description: Směrný plán zabezpečení Azure pro dávku
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: a9569c1f5de797c77f447b5df15e85a57e8be84b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472924"
---
# <a name="azure-security-baseline-for-batch"></a>Směrný plán zabezpečení Azure pro dávku

Směrný plán zabezpečení Azure pro dávku obsahuje doporučení, která vám pomohou zlepšit stav zabezpečení vašeho nasazení.

Základní informace pro tyto služby jsou z azure [security benchmark verze 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure s našimi pokyny pro osvědčené postupy.

Další informace naleznete v [tématu Přehled směrného plánu zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace naleznete v [tématu Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrana prostředků pomocí skupin zabezpečení sítě nebo brány Azure Firewall ve virtuální síti

**Pokyny:** Nasazení fondu batch Azure v rámci virtuální sítě. Chcete-li povolit výpočetní uzly fondu bezpečně komunikovat s jinými virtuálními počítači nebo s místní sítí, můžete zřídit fond v podsíti virtuální sítě Azure. Nasazení fondu v rámci virtuální sítě také umožňuje kontrolu nad skupinou zabezpečení sítě (NSG) slouží k zabezpečení síťových rozhraní jednotlivých uzlů (NIC), stejně jako podsítě. Nakonfigurujte soubor zabezpečení sítě tak, aby umožňoval přenosy pouze z důvěryhodných adres IP/umístění v Internetu.


Jak vytvořit fond dávek Azure v rámci virtuální sítě:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Sledování a protokolování konfigurace a provozu vnech, podsítí a nics

**Pokyny:** Použijte Azure Security Center a napravit doporučení ochrany sítě související s virtuální sítě / skupiny zabezpečení sítě (NSG) přidružené k fondu Batch. Povolte protokoly toku na souboru zabezpečení sítě, který se používá k ochraně fondu dávek, a odesílejte protokoly do účtu úložiště Azure pro audit provozu. Protokoly toku nsg můžete také odesílat do pracovního prostoru Azure Log Analytics a používat Azure Traffic Analytics k poskytování přehledů o toku provozu ve vašem cloudu Azure. Některé výhody Azure Traffic Analytics jsou možnost vizualizovat aktivitu v síti a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzorce toku provozu a určit chybné konfigurace sítě.


Povolení protokolů toku nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Jak povolit a používat Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


Seznamte se se zabezpečením sítě poskytovaným službou Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrana kritických webových aplikací

**Pokyny:** Není použitelné, benchmark je určen pro webové aplikace spuštěné v azure app service nebo IaaS instance.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odepřít komunikaci se známými škodlivými IP adresami

**Pokyny:** Povolte Azure DDoS (distribuované odmítnutí služby) Standardní ochrana ve virtuální síti chrání váš fond Azure Batch pro ochranu proti útokům DDoS. Pomocí integrované analýzy hrozeb Centra zabezpečení Azure můžete odepřít komunikaci se známými škodlivými nebo nepoužívanými internetovými IP adresami.


Jak nakonfigurovat ochranu Před sdos:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Seznamte se s integrovanou analýzou hrozeb Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Záznam síťových paketů a protokolů toku

**Pokyny:** Povolte protokoly toku ve skupině zabezpečení sítě (NSG), která se používá k ochraně fondu Azure Batch, a odesílejte protokoly do účtu úložiště Azure pro audit provozu.


Povolení protokolů toku nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Nasazení systémů detekce a prevence vniknutí založených na síti

**Pokyny:** Pokud je to nutné pro účely dodržování předpisů, vyberte síťové virtuální zařízení z Azure Marketplace, které podporuje systémy detekce vniknutí (IDS) a systémy prevence narušení (IPS) s možnostmi kontroly užitečného zatížení.


Pokud není vyžadováno zjišťování nebo prevence narušení na základě kontroly datové části, lze použít Azure Firewall se inteligentními hrozbami. Filtrování založené na analýzách hrozeb azure firewall může upozorňovat a odpírat provoz na známé škodlivé IP adresy a domény a z nich. IP adresy a domény jsou získávány z informačního kanálu Microsoft Threat Intelligence.


Nasaďte Azure Firewall s veřejnou IP adresou ve stejné virtuální síti jako uzly fondu dávek Azure. Nakonfigurujte pravidla překladu síťových adres (NAT) mezi důvěryhodnými umístěními v Internetu a privátními IP adresami jednotlivých uzlů fondu. Na Azure Firewall, v rámci Threat Intelligence, nakonfigurujte "Výstrahy a odepřít" blokovat upozornění a blokovat provoz na nebo ze známých škodlivých IP adres a domén. IP adresy a domény jsou získávány z informačního kanálu Microsoft Threat Intelligence a jsou zahrnuty pouze záznamy s nejvyšší spolehlivostí. 


Jak vytvořit fond dávek Azure v rámci virtuální sítě:

https://docs.microsoft.com/azure/batch/batch-virtual-network


Jak nasadit Bránu Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal


Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Správa návštěvnosti webových aplikací

**Pokyny:** Není použitelné, benchmark je určen pro webové aplikace spuštěné v azure app service nebo IaaS instance.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizace složitosti a administrativní režie pravidel zabezpečení sítě

**Pokyny:** Pomocí značek virtuálních síťových služeb definujte ovládací prvky přístupu k síti ve skupinách zabezpečení sítě nebo azure firewallech přidružených k vašemu fondu azure batch. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. apimanagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo jako adresy změnit.


Porozumět servisním značkám a používat je:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Udržovat standardní konfigurace zabezpečení pro síťová zařízení

**Pokyny:** Definujte a implementujte standardní konfigurace zabezpečení pro síťové prostředky přidružené k vašemu fondu Azure Batch s Azure Policy. Pomocí aliasů zásad Azure v oborech názvů Microsoft.Batch a Microsoft.Network vytvořte vlastní zásady pro auditování nebo vynucení síťové konfigurace fondů Azure Batch.



Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1.10: Pravidla konfigurace provozu dokumentu

**Pokyny:** Použití značek pro skupiny síťových služeb (NSG) a další prostředky související se zabezpečením sítě a tokem provozu, které jsou přidruženy k dávkovým fondům Azure. Pro jednotlivá pravidla sítě zabezpečení sítě použijte pole Popis k určení obchodní potřeby a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do nebo ze sítě.


Pomocí některé z předdefinovaných definic zásad Azure souvisejících s označováním, jako je například Vyžadovat značku a její hodnotu, můžete zajistit, aby se všechny prostředky vytvořily pomocí značek, a upozornit vás na existující neoznačené prostředky.


Azure PowerShell nebo Azure CLI můžete použít k vyhledávání nebo provádění akcí na prostředky na základě jejich značek.


Jak vytvářet a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


Jak vytvořit virtuální síť:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal


Jak vytvořit nsg:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Použití automatizovaných nástrojů ke sledování konfigurace síťových prostředků a zjišťování změn

**Pokyny:** Pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků souvisejících s fondy Azure Batch. Vytvořte výstrahy v rámci Azure Monitor, který se aktivuje, když dojde ke změnám kritických síťových prostředků.

Jak zobrazit a načíst události protokolu aktivit Azure:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Jak vytvořit výstrahy ve službě Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace naleznete [v tématu Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Použití schválených zdrojů synchronizace času

**Pokyny:** Pro Azure Batch, ve výchozím nastavení Microsoft poskytuje synchronizaci času. Pokud však máte konkrétní požadavky na synchronizaci času, můžete tyto změny implementovat.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurace správy centrálního protokolu zabezpečení

**Pokyny:** Palubní účet Azure Batch do Azure Monitoru pro agregaci dat zabezpečení generovaných zařízeními clusteru. Využijte vlastní dotazy ke zjišťování a reakci na hrozby v prostředí.  Pro monitorování na úrovni prostředků Azure Batch použijte dávková api ke sledování nebo dotazování na stav prostředků, včetně úloh, úkolů, uzlů a fondů.



Jak napalubě účtu Azure Batch do Azure Monitoru:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Povolení protokolování auditování prostředků Azure

**Pokyny:** Pro monitorování úrovně účtu Azure Batch sledujte každý účet Batch pomocí funkcí Azure Monitoru. Azure Monitor shromažďuje metriky a volitelně diagnostické protokoly pro prostředky vymezené na úrovni účtu Batch, jako jsou fondy, úlohy a úkoly. Shromažďujte a konzumujte tato data ručně nebo programově, abyste sledovali aktivity ve vašem účtu Batch a diagnostikovali problémy.


Pro monitorování úrovně prostředků Azure Batch použijte azure dávková api ke sledování nebo dotazování na stav vašich prostředků, včetně úloh, úkolů, uzlů a fondů.


Jak nakonfigurovat monitorování a protokolování na úrovni účtu Azure Batch:

https://docs.microsoft.com/azure/batch/monitoring-overview


Principy monitorování na úrovni prostředků dávky:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Shromažďování protokolů zabezpečení z operačního systému

**Pokyny:** Azure Monitor shromažďuje metriky a diagnostické protokoly pro prostředky ve vašem účtu Azure Batch. Shromažďujte a spotřebovávejte tato data různými způsoby, abyste monitorovali svůj účet Azure Batch a diagnostikovali problémy. Můžete také nakonfigurovat upozornění na metriky, abyste obdrželi oznámení, když metrika dosáhne zadané hodnoty.


V případě potřeby se můžete připojit k jednotlivým uzlům fondu prostřednictvím zabezpečeného prostředí (SSH) nebo protokolu RDP (Remote Desktop Protocol) pro přístup k protokolům místního operačního systému.


Jak shromažďovat diagnostické protokoly z účtu Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics


Jak se vzdáleně připojit k uzlům fondu Azure Batch:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny:** Palubní účet Azure Batch do Azure Monitoru. Ujistěte se, že použitý pracovní prostor Azure Log Analytics má nastavenou dobu uchovávání protokolů podle předpisů pro dodržování předpisů vaší organizace.


Jak nakonfigurovat monitorování a protokolování azure batch:

https://docs.microsoft.com/azure/batch/monitoring-overview


Jak nakonfigurovat dobu uchovávání pracovního prostoru Azure Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="26-monitor-and-review-logs"></a>2.6: Sledování a revize protokolů

**Pokyny:** Vytvořte výstrahy metriky Azure Batch, které se aktivují, když hodnota zadané metriky překročí danou prahovou hodnotu.


Jak nakonfigurovat upozornění na metriky Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Povolit upozornění na anomální aktivitu

**Pokyny:** Vytvořte výstrahy metriky Azure Batch, které se aktivují, když hodnota zadané metriky překročí danou prahovou hodnotu.


Jak nakonfigurovat upozornění na metriky Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizovat protokolování antimalwaru

**Pokyny**: Použijte program Windows Defender na jednotlivých dávkových uzlech v případě operačních systémů Windows nebo poskytněte vlastní řešení ochrany proti malwaru, pokud používáte Linux.

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="29-enable-dns-query-logging"></a>2.9: Povolení protokolování dotazů DNS

**Pokyny**: Implementace řešení třetí strany pro protokolování DNS

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="210-enable-command-line-audit-logging"></a>2.10: Povolení protokolování auditu příkazového řádku

**Pokyny:** Ručně nakonfigurujte protokolování konzoly a přepis prostředí PowerShell na základě počtu uzlů.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace naleznete [v tématu Zabezpečení: Identita a řízení přístupu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Udržovat inventuru účtů pro správu

**Pokyny:** Udržovat záznam místního účtu pro správu, který se vytvoří během zřizování fondu Azure Batch, a také všechny ostatní účty, které vytvoříte. Kromě toho pokud azure active directory (AAD) integrace se používá, AAD má předdefinované role, které musí být explicitně přiřazeny a jsou proto dotazovatelné. Pomocí modulu AAD PowerShell můžete provádět adhoc dotazy ke zjišťování účtů, které jsou členy skupin pro správu.


Kromě toho můžete použít azure security center identity a řízení přístupu doporučení.


Jak získat roli adresáře ve službě AAD pomocí prostředí PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Jak získat členy role adresáře v AAD s PowerShellem:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0


Jak sledovat identitu a přístup pomocí Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Změna výchozích hesel tam, kde je to možné

**Pokyny:** Při zřizování fondu Azure Batch máte možnost vytvořit účty místního počítače. Neexistují žádná výchozí hesla, která by bylo možné změnit, ale můžete zadat různá hesla pro přístup k zabezpečenému prostředí (SSH) a protokolu RDP (RdP). Po nakonfigurovaném fondu dávek Azure můžete vygenerovat náhodného uživatele pro jednotlivé uzly v rámci portálu Azure nebo prostřednictvím rozhraní API Azure Resource Manager.


Jak přidat uživatele do konkrétního výpočetního uzlu:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Zajistit používání vyhrazených administrativních účtů

**Pokyny:** Integrace ověřování pro dávkové aplikace Azure s Azure Active Directory. Vytvořte zásady a postupy týkající se používání vyhrazených účtů pro správu.


Kromě toho můžete použít azure security center identity a řízení přístupu doporučení.


Jak ověřit dávkové aplikace pomocí služby Azure Active Directory:

https://docs.microsoft.com/azure/batch/batch-aad-auth


Jak sledovat identitu a přístup pomocí Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Využití jednotného přihlašování (SSO) pomocí služby Azure Active Directory

**Pokyny:** Nelze použít, zatímco Azure Batch podporuje ověřování Azure AD, jednotné přihlašování není podporováno.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Vícefaktorové ověřování použijte pro veškerý přístup založený na službě Azure Active Directory.

**Pokyny:** Integrace ověřování pro dávkové aplikace Azure s Azure Active Directory (AAD). Povolte vícefaktorové ověřování AAD (MFA) a postupujte podle doporučení Centra identit a správy přístupu Azure Security Center.
 


Jak povolit vícefaktorové povolení v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Jak sledovat identitu a přístup v rámci Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Používejte vyhrazené počítače (pracovní stanice s privilegovaným přístupem) pro všechny úkoly správy

**Pokyny:** Používejte paws (privilegovaný přístup pracovnístanice) s vícefaktorové ověřování (MFA) nakonfigurované pro přihlášení a konfiguraci prostředků Azure Batch.


Další informace o pracovních stanicích s privilegovaným přístupem:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Jak povolit vícefaktorové povolení v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokolování a záznam podezřelé aktivity z účtů pro správu

**Pokyny:** Pokud máte integrované ověřování pro dávkové aplikace Azure s Azure Active Directory (AAD), použijte sestavy zabezpečení Azure Active Directory pro generování protokolů a výstrah, když dojde k podezřelé nebo nebezpečné aktivity v prostředí. Azure Security Center slouží ke sledování aktivity identity a přístupu.


Jak identifikovat uživatele Azure AD označené pro rizikové aktivity:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk


Jak sledovat identitu uživatelů a aktivitu přístupu v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Správa prostředků Azure z jenom schválených umístění

**Pokyny:** Pokud máte integrované ověřování pro dávkové aplikace Azure s Azure Active Directory, můžete použít podmíněný přístup pojmenovaná umístění povolit přístup pouze z konkrétnílogické seskupení rozsahů IP adres nebo zemí nebo oblastí.



Jak nakonfigurovat pojmenovaná umístění v Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="39-use-azure-active-directory"></a>3.9: Použití služby Azure Active Directory

**Pokyny:** Použijte Azure Active Directory (AAD) jako centrální ověřovací a autorizační systém a integrujte ověřování pro dávkové aplikace Azure s AAD. AAD chrání data pomocí silného šifrování pro data v klidovém stavu a při přenosu. AAD také soli, hasha a bezpečně ukládá přihlašovací údaje uživatele.


Jak vytvořit a nakonfigurovat instanci AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance


Jak ověřit dávkové aplikace pomocí služby AAD:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Pravidelné přezkoumávání a slaďuje přístup uživatelů

**Pokyny:** Azure Active Directory (AAD) poskytuje protokoly, které pomáhají zjistit zastaralé účty. Kromě toho můžete pomocí azure identity access reviews efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Přístup uživatelů lze pravidelně kontrolovat, aby bylo zajištěno, že pouze ti praví uživatelé mají trvalý přístup.


Jak používat recenze přístupu k identitám Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Sledování pokusů o přístup k deaktivovaným účtům

**Pokyny:** Vytvořte nastavení diagnostiky pro uživatelské účty Služby Azure Active Directory, odesílat protokoly auditu a protokoly přihlášení do pracovního prostoru Azure Log Analytics. Nakonfigurujte požadované výstrahy v pracovním prostoru Azure Log Analytics.


Jak integrovat protokoly aktivit Azure do Azure Monitoru:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Upozornění na odchylku chování přihlášení k účtu

**Pokyny:** Pomocí funkce Azure Active Directory (AAD) detekce rizik a ochrana identity nakonfigurujte automatické odpovědi na zjištěné podezřelé akce související s identitami uživatelů. Kromě toho můžete ingestovat data do Azure Sentinelu pro další šetření.


Jak zobrazit aad riskantní přihlášení:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Jak nakonfigurovat a povolit zásady rizik a zásady ochrany identity:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


Jak napalubě Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13: <div>Poskytněte společnosti Microsoft přístup k relevantním zákaznickým datům během scénářů podpory<br></div>

**Pokyny**: Není k dispozici; Customer Lockbox ještě není podporovánpro Azure Batch. Seznam služeb podporovaných customer lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

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

**Pokyny**: Implementujte samostatné odběry nebo skupiny pro správu pro vývoj, testování a výrobu. Fondy dávek Azure by měly být odděleny virtuální sítí nebo podsítí, odpovídajícím způsobem označeny a zabezpečeny skupinou zabezpečení sítě (NSG). Azure Batch data by měla být obsažena v rámci zabezpečeného účtu úložiště Azure.


Jak vytvořit fond dávek Azure v rámci virtuální sítě:

https://docs.microsoft.com/azure/batch/batch-virtual-network


Jak zabezpečit účty úložiště Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Sledování a blokování neoprávněného přenosu citlivých informací.

**Pokyny:** Pro účty úložiště Azure přidružené k vašemu fondu dávek Azure, které obsahují citlivé informace, označte je jako citlivé pomocí značek a zabezpečte je pomocí osvědčených postupů Azure.


Funkce identifikace, klasifikace a prevence ztrát ještě nejsou dostupné pro Azure Storage nebo výpočetní prostředky. Implementujte řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.


Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.


Seznamte se s ochranou zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


Jak zabezpečit účty úložiště Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Šifrování všech citlivých informací při přepravě

**Pokyny**: Šifrování všech citlivých informací při přenosu. Prostředky Microsoft Azure budou ve výchozím nastavení vyjednávat tls 1.2. Ujistěte se, že všichni klienti, kteří se připojují k vašim fondům dávek Azure nebo úložištím dat (účty úložiště Azure), jsou schopni vyjednat TLS 1.2 nebo vyšší.


Ujistěte se, že protokol HTTPS je vyžadován pro přístup k účtu úložiště obsahujícím data Azure Batch.


Principy šifrování účtu úložiště Azure při přenosu:

https://docs.microsoft.com/azure/storage/blobs/security-recommendations

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Sdíleno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Použití aktivního nástroje pro zjišťování k identifikaci citlivých dat

**Pokyny:** Pro účty úložiště Azure přidružené k vašemu fondu dávek Azure, které obsahují citlivé informace, označte je jako citlivé pomocí značek a zabezpečte je pomocí osvědčených postupů Azure.


Funkce identifikace, klasifikace a prevence ztrát ještě nejsou dostupné pro Azure Storage nebo výpočetní prostředky. Implementujte řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.


Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.


Seznamte se s ochranou zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


Jak zabezpečit účty úložiště Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Použití Azure RBAC k řízení přístupu k prostředkům

**Pokyny:** Pomocí řízení přístupu na základě rolí Azure Active Directory (AAD) (RBAC) můžete řídit přístup k rovině správy prostředků Azure, včetně dávkového účtu, dávkového fondu a účtů úložiště.


Principy Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview


Jak nakonfigurovat RBAC v Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Použití funkce Zabránění ztrátám dat na hostiteli k vynucení řízení přístupu

**Pokyny:** Funkce identifikace, klasifikace a prevence ztrát dat ještě nejsou k dispozici pro Azure Storage nebo výpočetní prostředky. Implementujte řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.



Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.



Seznamte se s ochranou zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Šifrování citlivých informací v klidovém stavu

**Pokyny:** Pro účty úložiště přidružené k vašemu účtu Azure Batch se doporučuje povolit Microsoftu spravovat šifrovací klíče, ale v případě potřeby máte možnost spravovat vlastní klíče.



Jak spravovat šifrovací klíče pro účty úložiště Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokolování a upozorňování na změny kritických prostředků Azure

**Pokyny:** Pomocí Azure Monitoru s protokolem aktivit Azure vytvořte výstrahy, kdy dojde ke změnám u důležitých prostředků Azure souvisejících s vašimi účty/fondy Azure Batch nebo s nimi přidruženými.



Nakonfigurujte diagnostická nastavení pro účty úložiště přidružené k fondu dávek Azure, abyste monitorovali a zaznamenávali všechny operace CRUD proti datům fondu.



Jak vytvořit výstrahy pro události protokolu aktivit Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



Jak povolit další protokolování/auditování pro účet úložiště Azure:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace naleznete v [tématu Security Control: Vulnerability Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Spuštění automatizovaných nástrojů pro kontrolu zranitelnosti

**Pokyny:** Pro uzly fondu dávek Azure jste zodpovědní za správu řešení správy chyb zabezpečení.


Volitelně, pokud máte předplatné Rapid7, Qualys nebo jiné platformy pro správu chyb zabezpečení, můžete ručně nainstalovat agenty pro posouzení chyb zabezpečení na uzly fondu dávek a spravovat uzly prostřednictvím příslušného portálu.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Nasazení řešení automatické správy oprav operačních systémů

**Pokyny:** Microsoft udržovat a aktualizovat základní azure batch pool image uzlu. Ujistěte se, že operační systém uzlů fondu dávek Azure zůstane opravený po dobu životnosti clusteru, což může vyžadovat povolení automatických aktualizací, sledování uzlů nebo provádění pravidelných restartování.

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Sdíleno

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Nasazení automatizovaného řešení pro správu oprav softwaru třetích stran

**Pokyny:** Ujistěte se, že aplikace uzlů azure batch pool u jiných výrobců zůstávají opraveny po dobu životnosti clusteru, což může vyžadovat povolení automatických aktualizací, sledování uzlů nebo provádění pravidelných restartování.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porovnání prohledávačů chyb zabezpečení zády k zadní straně

**Pokyny:** Pokud máte předplatné rapid7, Qualys nebo jiné platformy pro správu zranitelnosti, můžete použít portál tohoto dodavatele k zobrazení a porovnání prohledávačů chyb zabezpečení zády k sobě.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Pomocí procesu hodnocení rizik upřednostněte nápravu zjištěných chyb zabezpečení.

**Pokyny**: Používejte běžný program hodnocení rizik (např. společný systém hodnocení zranitelnosti) nebo výchozí hodnocení rizik poskytovaného vaším nástrojem pro skenování třetí stranou.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace naleznete v [tématu Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Použití Azure Asset Discovery

**Pokyny:** Pomocí Azure Resource Graph můžete dotazovat/zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť atd.) v rámci předplatného. Ujistěte se, že máte příslušná (číst) oprávnění ve vašem tenantovi a jsou schopni vytvořit výčet všech předplatných Azure, stejně jako prostředky v rámci vašich předplatných.


I když klasické prostředky Azure může být zjištěna prostřednictvím Azure Resource Graph Explorer, je vysoce doporučeno vytvářet a používat prostředky Azure Resource Manager (ARM) do budoucna.


Jak vytvářet dotazy pomocí Průzkumníka zdrojů Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Jak zobrazit předplatná Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Principy Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="62-maintain-asset-metadata"></a>6.2: Udržovat metadata datových zdrojů

**Pokyny:** Použijte značky pro prostředky Azure, které poskytují metadata, aby je logicky uspořádali do taxonomie.


Jak vytvářet a používat značky:

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

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Udržujte inventář schválených prostředků Azure a softwarových titulů.

**Pokyny:** Definování seznamu schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorování neschválených prostředků Azure

**Pokyny**: Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

- Nepovolené typy prostředků
- Povolené typy prostředků


Pomocí Azure Resource Graph můžete dotazovat/zjišťovat prostředky v rámci vašeho předplatného. Ujistěte se, že jsou schváleny všechny prostředky Azure v prostředí.


Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Jak vytvářet dotazy pomocí Průzkumníka zdrojů Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Sledování neschválených softwarových aplikací v rámci výpočetních prostředků

**Pokyny:** Pro uzly fondu dávek Azure implementujte řešení jiného výrobce pro monitorování uzlů clusteru pro neschválené softwarové aplikace.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Odebrání neschválených prostředků A Softwarových aplikací Azure

**Pokyny:** Pro uzly fondu dávek Azure implementujte řešení jiného výrobce pro monitorování uzlů clusteru pro neschválené softwarové aplikace.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="68-use-only-approved-applications"></a>6.8: Používejte pouze schválené aplikace

**Pokyny:** Pro uzly fondu dávek Azure implementujte řešení jiného výrobce, abyste zabránili spuštění neoprávněného softwaru.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="69-use-only-approved-azure-services"></a>6.9: Použití jenom schválených služeb Azure

**Pokyny**: Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:


- Nepovolené typy prostředků
- Povolené typy prostředků


Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Jak odepřít konkrétní typ prostředku pomocí zásad Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="610-implement-approved-application-list"></a>6.10: Implementace schváleného seznamu žádostí

**Pokyny:** Pro uzly fondu dávek Azure implementujte řešení jiného výrobce, abyste zabránili spuštění neoprávněných typů souborů.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Omezení možnosti uživatelů komunikovat se Správcem prostředků Azure pomocí skriptů</div>

**Pokyny:** Pomocí podmíněného přístupu Azure omezte uživatelům možnost pracovat se Správcem prostředků Azure konfigurací "Blokovat přístup" pro aplikaci Microsoft Azure Management.


Jak nakonfigurovat podmíněný přístup pro blokování přístupu ke Správci prostředků Azure:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Omezení možnosti uživatelů spouštět skripty v rámci výpočetních prostředků

**Pokyny**: Nepoužije se,

To se nevztahuje na Azure Batch, protože uživatelé (nesprávci) fondů Azure Batch nepotřebují přístup k jednotlivým uzlům ke spuštění úloh. Správce clusteru již má root přístup ke všem uzlům.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fyzicky nebo logicky oddělit vysoce rizikové aplikace

**Pokyny:** Není použitelné, benchmark je určen pro webové aplikace spuštěné v azure app service nebo IaaS instance.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace naleznete v [tématu Zabezpečení: Zabezpečená konfigurace](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny:** Pomocí aliasů zásad Azure v oboru názvů "Microsoft.Batch" můžete vytvořit vlastní zásady pro auditování nebo vynucení konfigurace účtů a fondů Azure Batch.


Jak zobrazit dostupné aliasy zásad Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Vytvoření bezpečných konfigurací pro váš operační systém

**Pokyny:** Vytvořte zabezpečené konfigurace operačního systému uzlů fondu dávek.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Udržovat zabezpečené konfigurace pro všechny prostředky Azure

**Pokyny:** Pomocí zásad Azure [odepřít] a [nasadit, pokud neexistují] vynutit zabezpečené nastavení pro prostředky Azure související s vaším dávkovým účtem a fondy (jako jsou virtuální sítě, podsítě, Azure Firewalls, Účty azure storage atd.). Aliasy zásad Azure můžete použít z následujících oborů názvů k vytvoření vlastních zásad:

- Microsoft.Batch
- Microsoft.Storage
- Microsoft.Network


Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Principy efektů zásad Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Udržovat zabezpečené konfigurace pro operační systémy

**Pokyny:** Image operačního systému Azure Batch Pool, které spravuje a spravuje Microsoft. Jste zodpovědní za implementaci konfigurace stavu na úrovni operačního systému.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Konfigurace prostředků Azure bezpečně ukládat

**Pokyny:** Pokud používáte vlastní definice zásad Azure pro vaše účty Azure Batch, fondy nebo související prostředky, použijte Azure Repos k bezpečnému ukládání a správě kódu.


Jak ukládat kód v Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops


Dokumentace k azure repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpečně ukládat obrázky vlastního operačního systému

**Pokyny:** Pokud používáte vlastní image pro fondy Azure Batch, použijte řízení přístupu na základě rolí (RBAC) a ujistěte se, že k bitovým kopiím mají přístup pouze oprávnění uživatelé.


Principy RBAC v Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles


Jak nakonfigurovat RBAC v Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Nasazení nástrojů pro správu konfigurace systému

**Pokyny:** Pomocí předdefinovaných definic zásad Azure můžete upozorňovat, auditovat a vynucovat konfigurace prostředků související s azure batch.  Pomocí aliasů zásad Azure v oboru názvů Microsoft.Batch vytvořte vlastní zásady pro účty a fondy Azure Batch. Kromě toho vytvořte proces a kanál pro správu výjimek zásad.



Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Nasazení nástrojů pro správu konfigurace systému pro operační systémy

**Pokyny:** Implementujte řešení jiného výrobce k udržení požadovaného stavu pro operační systémy uzlů azure batch pool.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementace monitorování automatické konfigurace pro služby Azure

**Pokyny:** Pomocí aliasů zásad Azure v oboru názvů "Microsoft.Batch" můžete vytvořit vlastní zásady pro auditování nebo vynucení konfigurace instance Azure Batch. Můžete také použít všechny integrované zásady vytvořené speciálně pro Azure Batch nebo prostředky používané Azure Batch, jako jsou:

- Podsítě by měly být přidruženy ke skupině zabezpečení sítě - účty úložiště by měly používat koncový bod služby virtuální sítě.
- Diagnostické protokoly v dávkových účtech by měly být povoleny.

Jak zobrazit dostupné aliasy zásad Azure:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementace automatického monitorování konfigurace operačních systémů

**Pokyny:** Implementujte řešení jiného výrobce pro sledování stavu operačních systémů uzlů uzly fondu dávek Azure.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="711-securely-manage-azure-secrets"></a>7.11: Bezpečná správa tajných kódů Azure

**Pokyny:** Azure Key Vault se dá použít s nasazením Azure Batch ke správě klíčů pro úložiště fondu v rámci účtů úložiště Azure.


Jak integrovat s Azure Spravované identity:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity


Jak vytvořit Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/quick-create-portal


Jak zajistit ověřování trezoru klíčů se spravovanou identitou:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Bezpečná a automatická správa identit

**Pokyny:** Není k dispozici, identita spravované služby není podporována Azure Batch

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminujte nezamýšlenou expozici pověření

**Pokyny**: Implementujte skener pověření k identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů také podpoří přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je azure key vault. 

Jak nastavit skener pověření:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace naleznete v [tématu Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Použití centrálně spravovaného antimalwarového softwaru

**Pokyny:** Použijte Windows Defender na jednotlivých uzlech fondu Azure Batch v případě operačních systémů Windows nebo poskytněte vlastní řešení ochrany proti malwaru, pokud používáte Linux.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Předprohledávací soubory, které se mají nahrát do nevýpočetních prostředků Azure

**Pokyny**: Microsoft Antimalware je povolena na podkladovém hostiteli, který podporuje služby Azure (například Azure Batch), ale neběží na obsah zákazníka.


Předem proskenujte všechny soubory nahrané do nevýpočetních prostředků Azure, jako je app service, úložiště datových jezer, úložiště objektů blob atd. Společnost Microsoft nemá v těchto případech přístup k zákaznickým datům.


Seznamte se s antimalwarovým programem Microsoft pro cloudové služby Azure a virtuální počítače:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Zajistěte aktualizaci softwaru a podpisů ochrany proti malwaru

**Pokyny:** V případě operačních systémů Windows použijte program Windows Defender na jednotlivých uzlech fondu Azure Batch a zajistěte, aby byla povolena automatická aktualizace. Pokud používáte Linux, poskytněte své vlastní řešení proti malwaru.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace naleznete [v tématu Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zajistěte pravidelné automatické zálohování

**Pokyny:** Při použití účtu úložiště Azure pro úložiště dat fondu dávek Azure zvolte příslušnou možnost redundance (LRS,ZRS, GRS, RA-GRS). 


Jak nakonfigurovat redundanci úložiště pro účty úložiště Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Proveďte kompletní zálohy systému a zálohujte všechny klienty spravované klíče

**Pokyny:** Při použití účtu úložiště Azure pro úložiště dat fondu dávek Azure zvolte příslušnou možnost redundance (LRS,ZRS, GRS, RA-GRS).  Pokud používáte Azure Key Vault pro jakoukoli část nasazení Azure Batch, ujistěte se, že vaše klíče jsou zálohovány.


Jak nakonfigurovat redundanci úložiště pro účty úložiště Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy


Jak zálohovat klíče trezoru klíčů v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny:** Pokud spravujete vlastní klíče pro účty úložiště Azure nebo jakýkoli jiný prostředek související s implementací Azure Batch, pravidelně otestujte obnovení zálohovaných klíčů.


Jak zálohovat klíče trezoru klíčů v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


Jak obnovit klíč spravovaný zákazníkem pomocí Prostředí PowerShell:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zajištění ochrany záloh a klíčů spravovaných zákazníkem

**Pokyny:** Pokud azure key vault se používá k uložení klíče související s účty úložiště dávkového fondu Azure, povolit obnovitelné odstranění v trezoru klíčů Azure chránit klíče před náhodným nebo škodlivým odstraněním.


Jak povolit obnovitelné odstranění v azure key vault:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace naleznete [v tématu Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10.1: Vytvoření průvodce odezvou na incidenty

**Pokyny**: Ujistěte se, že existují písemné plány reakce na incidenty, které definují role personálu, jakož i fáze zpracování incidentů / správy.



Jak nakonfigurovat automatizaci pracovních postupů v Centru zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Vytvoření postupu hodnocení incidentů a stanovení priorit

**Pokyny**: Centrum zabezpečení přiřazuje výstrahám závažnost, která vám pomůže určit pořadí, ve kterém se účastníte jednotlivých výstrah, takže když dojde k ohrožení zabezpečení prostředku, můžete se k němu hned dostat. Závažnost je založena na tom, jak sebevědomý Security Center je v hledání nebo analytice slouží k vydání výstrahy, stejně jako úroveň spolehlivosti, že tam byl škodlivý záměr za aktivitu, která vedla k upozornění.

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="103-test-security-response-procedures"></a>10.3: Zkušební postupy odezvy zabezpečení

**Pokyny**: Proveďte cvičení k testování schopností reakce na incidenty vašich systémů na pravidelné kadenci. Identifikujte slabá místa a mezery a podle potřeby plán revidujte.

Podívejte se na publikaci NIST: Průvodce testovacími, školicími a cvičebními programy pro plány a schopnosti IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Poskytněte kontaktní údaje &nbsp;bezpečnostních incidentů a nakonfigurujte upozornění pro bezpečnostní incidenty

**Pokyny**: Kontaktní informace o bezpečnostních incidentech společnost Microsoft použije k tomu, aby vás kontaktovala, pokud centrum MSRC (Microsoft Security Response Center) zjistí, že k vašim datům získala přístup nezákonná nebo neoprávněná strana.



Jak nastavit kontakt zabezpečení Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Začleňte výstrahy zabezpečení do systému reakce na incidenty

**Pokyny:** Exportujte výstrahy a doporučení Centra zabezpečení Azure pomocí funkce Nepřetržitý export. Nepřetržitý export umožňuje exportovat výstrahy a doporučení buď ručně, nebo průběžně. Datový konektor Azure Security Center můžete použít k streamování výstrah do Azure Sentinelu.


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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Proveďte pravidelné penetrační testování vašich prostředků Azure a zajistěte, aby byly všechny důležité výsledky zabezpečení opraveny do 60 dnů.

**Pokyny**: Postupujte podle pravidel společnosti Microsoft a ujistěte se, že penetrační testy neporušují zásady společnosti Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



Další informace o strategii společnosti Microsoft a provádění červených týmů a testování pronikání živých webů proti cloudové infrastruktuře, službám a aplikacím společnosti Microsoft naleznete zde: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

## <a name="next-steps"></a>Další kroky

- Podívejte se na [srovnávací test zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Další informace o [plánech zásad zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
