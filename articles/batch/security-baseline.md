---
title: Základní hodnoty zabezpečení Azure pro Batch
description: Základní hodnoty zabezpečení Azure pro Batch
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1eb24871817f365efe58b8e687563727df74493c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400972"
---
# <a name="azure-security-baseline-for-batch"></a>Základní hodnoty zabezpečení Azure pro Batch

Základní plán zabezpečení Azure pro službu Batch obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Chraňte prostředky pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network

**Pokyny**: nasazení Azure Batchch fondů v rámci virtuální sítě. Pokud chcete, aby výpočetní uzly fondu umožňovaly zabezpečeně komunikovat s jinými virtuálními počítači nebo s místní sítí, můžete fond zřídit v podsíti virtuální sítě Azure. Také nasazení fondu v rámci virtuální sítě vám umožní řídit skupinu zabezpečení sítě (NSG), která se používá k zabezpečení síťových rozhraní jednotlivých uzlů (NIC) a také podsíť. Nakonfigurujte NSG tak, aby povoloval provoz jenom z důvěryhodných IP adres/Locations na internetu.

Postup vytvoření fondu Azure Batch v rámci Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuální sítě, podsítí a síťových karet.

**Doprovodné**materiály: použijte Azure Security Center a opravte doporučení k ochraně sítě týkající se skupiny zabezpečení virtuální sítě nebo sítě (NSG) přidružené k vašemu fondu služby Batch. Povolte protokoly toku na NSG, který se používá k ochraně fondu Batch, a odešlete protokoly do účtu Azure Storage pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Azure Log Analytics a používat Azure Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Azure Analýza provozu je schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a odhalit síťové opravy chyb.

Jak povolit protokoly toku NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit a použít Analýza provozu:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Pochopení zabezpečení sítě, které poskytuje Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: povolení služby Azure DDoS (Distributed Denial-of-Service) ve virtuální síti s ochranou fondu Azure Batch pro ochranu před útoky DDoS. K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

Jak nakonfigurovat DDoS Protection:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Pochopení Azure Security Center integrované analýzy hrozeb:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: záznam síťových paketů a protokolů toků

**Doprovodné**materiály: Povolte protokoly toku ve skupině zabezpečení sítě (NSG), která se používá k ochraně fondu Azure Batch, a odešlete protokoly do Azure Storage účtu pro audit provozu.

Jak povolit protokoly toku NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: nasazení systémů ochrany před internetovými útoky na síť a prevence vniknutí

**Doprovodné**materiály: Pokud je to nutné pro účely dodržování předpisů, vyberte síťové virtuální zařízení z Azure Marketplace, které podporuje funkce pro zjišťování neoprávněných vniknutí (ID) a systémy ochrany před únikem informací (IP) s možnostmi kontroly zatížení.

Pokud zjišťování neoprávněných vniknutí nebo prevence na základě kontroly zatížení není požadavkem, je možné použít Azure Firewall s analýzou hrozeb. Azure Firewall filtrování na základě logiky hrozeb může upozorňovat a zamítnout provoz do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu.

Nasaďte Azure Firewall s veřejnou IP adresou ve stejné virtuální síti jako uzly fondu Azure Batch. Nakonfigurujte pravidla překladu adres (NAT) mezi důvěryhodnými umístěními na internetu a privátními IP adresami uzlů jednotlivých fondů. Na Azure Firewall v části Analýza hrozeb nakonfigurujte "Alert" a "deny" pro blokování výstrahy a zablokování provozu do a ze známých škodlivých IP adres a domén. IP adresy a domény se nacházejí z kanálu Microsoft Threat Intelligence a zahrnují se jenom ty nejvyšší důvěrné záznamy. 

Postup vytvoření fondu Azure Batch v rámci Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Postup nasazení Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné**materiály: nejde použít, srovnávací test je určený pro webové aplikace běžící na Azure App Service nebo IaaS instancích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: pomocí značek služby virtuální sítě definujte řízení přístupu k síti pro skupiny zabezpečení sítě nebo brány firewall Azure, které jsou přidružené k vašim Azure Batch fondům. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Pochopení a používání značek služeb:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky přidružené k vašim Azure Batch fondům s Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft.Batch a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě vašich fondů Azure Batch.

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné**materiály: používejte značky pro skupiny síťových služeb (skupin zabezpečení sítě) a další prostředky související se zabezpečením a tokem provozu sítě, které jsou spojené s vašimi fondy Azure Batch. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak vytvořit virtuální síť:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Vytvoření NSG:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vašimi fondy Azure Batch. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

Jak zobrazit a načíst události protokolu aktivit Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Postup při vytváření výstrah v Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Doprovodné**materiály: u Azure Batch ve výchozím nastavení poskytuje společnost Microsoft časovou synchronizaci. Pokud ale máte specifické požadavky na synchronizaci času, můžete tyto změny implementovat.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné**materiály: účet Azure Batch, který se má Azure monitor k agregaci dat zabezpečení generovaných zařízeními clusteru. Využijte vlastní dotazy k detekci a reakci na hrozby v prostředí.  Pro Azure Batch monitorování na úrovni prostředků použijte rozhraní API služby Batch k monitorování nebo dotazování stavu vašich prostředků, včetně úloh, úkolů, uzlů a fondů.

Jak připojit účet Azure Batch k Azure Monitor:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Návod**: pro Azure Batch monitorování na úrovni účtu sledujte jednotlivé účty Batch pomocí funkcí Azure monitor. Azure Monitor shromažďuje metriky a volitelně diagnostické protokoly pro prostředky s rozsahem na úrovni účtu Batch, jako jsou fondy, úlohy a úkoly. Shromažďovat a spotřebovávat tato data ručně nebo programově, abyste mohli monitorovat aktivity v účtu Batch a diagnostikovat problémy.

Pro Azure Batch monitorování na úrovni prostředků použijte rozhraní API Azure Batch k monitorování nebo dotazování stavu vašich prostředků, včetně úloh, úloh, uzlů a fondů.

Jak nakonfigurovat Azure Batch monitorování a protokolování na úrovni účtu:

https://docs.microsoft.com/azure/batch/monitoring-overview

Principy sledování na úrovni prostředků Batch:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="24-collect-security-logs-from-operating-system"></a>2,4: shromáždění protokolů zabezpečení z operačního systému

**Doprovodné**materiály: Azure monitor shromažďuje metriky a protokoly diagnostiky pro prostředky v účtu Azure Batch. Shromažďovat a spotřebovávat tato data různými způsoby, jak monitorovat účet Azure Batch a diagnostikovat problémy. Můžete také nakonfigurovat upozornění na metriky, abyste obdrželi oznámení, když metrika dosáhne zadané hodnoty.

V případě potřeby se můžete ke svým uzlům fondu připojit pomocí protokolu SSH (Secure Shell) nebo protokol RDP (Remote Desktop Protocol) (RDP) a získat přístup k místním protokolům operačního systému.

Jak shromažďovat diagnostické protokoly z účtu Azure Batch:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

Jak se vzdáleně připojit k uzlům Azure Batch fondu:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny**: Azure Batch účet Azure monitor. Ujistěte se, že se v pracovním prostoru Azure Log Analytics používala doba uchování protokolu nastavená v souladu s pravidly dodržování předpisů vaší organizace.

Postup konfigurace Azure Batch monitorování a protokolování:

https://docs.microsoft.com/azure/batch/monitoring-overview

Jak nakonfigurovat dobu uchování v pracovním prostoru Azure Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné**materiály: vytváření Azure Batch výstrah metrik, které se aktivují, když hodnota zadané metriky přechází z dané prahové hodnoty.

Jak nakonfigurovat Azure Batch výstrahy metriky:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé

**Doprovodné**materiály: vytváření Azure Batch výstrah metrik, které se aktivují, když hodnota zadané metriky přechází z dané prahové hodnoty.

Jak nakonfigurovat Azure Batch výstrahy metriky:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Pokyny**: použijte Windows Defender na jednotlivých uzlech služby Batch v případě operačních systémů Windows nebo Poskytněte vlastní antimalwarové řešení, pokud používáte Linux.

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Pokyny**: implementace řešení třetí strany pro protokolování DNS

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Pokyny**: ruční konfigurace protokolování konzoly a přepisu prostředí PowerShell v jednotlivých uzlech.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné**materiály: Udržujte si záznam místního účtu pro správu, který se vytvoří během zřizování fondu Azure Batch, i všechny další účty, které vytvoříte. Kromě toho, pokud je použita Integrace Azure Active Directory (AAD), má AAD předdefinované role, které se musí explicitně přiřadit a proto se Queryable. Pomocí modulu PowerShellu pro AAD můžete provádět dotazy ad hoc a zjišťovat účty, které jsou členy skupin pro správu.

Kromě toho můžete použít Azure Security Center doporučení pro správu identit a přístupu.

Jak získat roli adresáře v AAD pomocí PowerShellu:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak získat členy role adresáře v AAD pomocí PowerShellu:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Jak monitorovat identitu a přístup pomocí Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: při zřizování fondu Azure Batch máte možnost vytvářet účty místního počítače. Neexistují žádná výchozí hesla, která by bylo možné změnit, ale můžete zadat různá hesla pro přístup přes SSH (Secure Shell) a protokol RDP (Remote Desktop Protocol) (RDP). Po nakonfigurování Azure Batch fondu můžete vygenerovat náhodného uživatele pro jednotlivé uzly v rámci Azure Portal nebo prostřednictvím rozhraní API Azure Resource Manager.

Postup přidání uživatele ke konkrétnímu výpočetnímu uzlu:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3,3: Ujistěte se, že použití vyhrazených účtů pro správu

**Pokyny**: integrace ověřování pro aplikace Azure Batch s Azure Active Directory. Vytvořte zásady a postupy týkající se používání vyhrazených účtů pro správu.

Kromě toho můžete použít Azure Security Center doporučení pro správu identit a přístupu.

Ověřování aplikací Batch pomocí Azure Active Directory:

https://docs.microsoft.com/azure/batch/batch-aad-auth

Jak monitorovat identitu a přístup pomocí Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3,4: Využijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné**materiály: nejde použít, ale Azure Batch podporuje ověřování Azure AD, jednotné přihlašování se nepodporuje.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory.

**Pokyny**: integrace ověřování pro aplikace Azure Batch s využitím Azure Active Directory (AAD). Povolte službu AAD Multi-Factor Authentication (MFA) a sledujte Azure Security Center doporučení pro správu identit a přístupu.

 

Jak povolit vícefaktorové ověřování v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorovat identitu a přístup v rámci Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Doprovodné**materiály: použití privilegovaným přístupem (privilegovaných pracovních stanic) se službou Multi-Factor Authentication (MFA) nakonfigurovaným pro přihlášení a konfiguraci vašich Azure Batchch prostředků.

Další informace o pracovních stanicích s privilegovaným přístupem:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové ověřování v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: protokolování a upozornění na podezřelou aktivitu z účtů pro správu

**Doprovodné**materiály: Pokud máte integrované ověřování pro aplikace Azure Batch s Azure Active Directory (AAD), použijte sestavy Azure Active Directory zabezpečení pro generování protokolů a výstrah, pokud dojde k podezřelé nebo nebezpečné aktivitě v prostředí. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu.

Jak identifikovat uživatele Azure AD označené příznakem rizika pro rizikové aktivity:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak monitorovat aktivitu identity uživatelů a přístupu v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: Pokud máte k dispozici integrované ověřování pro aplikace Azure Batch s Azure Active Directory, můžete pomocí pojmenovaných umístění podmíněného přístupu dovolit přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

Jak nakonfigurovat pojmenovaná umístění v Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné**materiály: jako centrální ověřování a autorizační systém použijte Azure Active Directory (AAD) a integrujte ověřování pro Azure Batch aplikace pomocí AAD. AAD chrání data pomocí silného šifrování pro data v klidovém umístění a při přenosu. AAD taky soli, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Jak vytvořit a nakonfigurovat instanci AAD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Jak ověřit aplikace Batch pomocí AAD:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné**materiály: Azure Active Directory (AAD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Kromě toho můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

Jak používat kontroly přístupu Azure identity:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorování pokusů o přístup k deaktivovaným účtům

**Doprovodné**materiály: Vytvoření nastavení diagnostiky pro Azure Active Directory uživatelských účtů, odesílání protokolů auditu a protokolů přihlášení do pracovního prostoru Azure Log Analytics. Nakonfigurujte požadované výstrahy v pracovním prostoru Azure Log Analytics.

Jak integrovat protokoly aktivit Azure do Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Doprovodné**materiály: Využijte možnost detekce rizik v Azure Active Directory (AAD) a funkce Ochrana identit ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů. Kromě toho můžete ingestovat data do služby Azure Sentinel pro další šetření.

Postup zobrazení rizikových přihlášení AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak nakonfigurovat a povolit zásady pro rizika ochrany identity:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak připojit Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3,13: <div>Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.<br></div>

**Doprovodné**materiály: není k dispozici; Customer Lockbox ještě není pro Azure Batch podporované.
 
Seznam podporovaných služeb Customer Lockbox: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné**materiály: používejte značky, které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Fondy Azure Batch by se měly oddělit pomocí virtuální sítě/podsítě, vhodně označit a zabezpečit pomocí skupin zabezpečení sítě (NSG). Data Azure Batch by měla být obsažena v zabezpečeném Azure Storagem účtu.

Postup vytvoření fondu Azure Batch v rámci Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Postup zabezpečení Azure Storage účtů:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací.

**Doprovodné**materiály: u účtů Azure Storage přidružených k vašim Azure Batch fondům, které obsahují citlivé informace, je označit jako citlivé pomocí značek a zabezpečit je pomocí osvědčených postupů Azure.

Pro Azure Storage nebo výpočetní prostředky ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

Pochopení ochrany zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Postup zabezpečení Azure Storage účtů:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné**materiály: šifrování všech citlivých informací během přenosu. Microsoft Azure prostředky vyjednávat TLS 1,2 ve výchozím nastavení. Ujistěte se, že všichni klienti, kteří se připojují ke svým fondům Azure Batch nebo úložiště dat (účty Azure Storage), můžou vyjednávat TLS 1,2 nebo vyšší.

Ujistěte se, že se pro přístup k účtu úložiště obsahujícímu data Azure Batch vyžaduje protokol HTTPS.

Principy šifrování Azure Storage účtu při přenosu:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné**materiály: u účtů Azure Storage přidružených k vašim Azure Batch fondům, které obsahují citlivé informace, je označit jako citlivé pomocí značek a zabezpečit je pomocí osvědčených postupů Azure.

Pro Azure Storage nebo výpočetní prostředky ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

Pochopení ochrany zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Postup zabezpečení Azure Storage účtů:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: použijte řízení přístupu na základě role Azure (Azure RBAC) k řízení přístupu k rovině správy prostředků Azure, včetně účtu Batch, fondů služby Batch a účtů úložiště.

Porozumění službě Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

Jak nakonfigurovat službu Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Pokyny**: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Storage nebo výpočetní prostředky. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

Pochopení ochrany zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné**materiály: u účtů úložiště přidružených k vašemu Azure Batch účtu doporučujeme, aby Microsoft mohl spravovat šifrovací klíče, ale v případě potřeby máte možnost spravovat vlastní klíče.

Správa šifrovacích klíčů pro účty Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet upozornění na důležité prostředky Azure související s nebo přidruženými k vašim Azure Batchm účtům a fondům.

Nakonfigurujte nastavení diagnostiky pro účty úložiště přidružené k fondu Azure Batch, abyste mohli monitorovat a protokolovat všechny operace CRUD pro data fondu.

Vytváření upozornění pro události protokolu aktivit Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Jak povolit další protokolování/auditování pro účet Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](../security/benchmarks/security-control-vulnerability-management.md)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné**materiály: u uzlů fondu Azure Batch zodpovídáte za správu řešení pro správu ohrožení zabezpečení.

Volitelně, pokud máte Rapid7, Qualys nebo jakékoli jiné předplatné platformy pro správu ohrožení zabezpečení, můžete ručně nainstalovat agenty pro posouzení ohrožení zabezpečení do uzlů fondu Batch a spravovat uzly prostřednictvím příslušného portálu.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Pokyny**: Společnost Microsoft zachovává a aktualizuje obrázky uzlů základních Azure Batch fondů. Zajistěte, aby operační systém uzlů Azure Batchového fondu zůstal opravený po dobu životnosti clusteru, což může vyžadovat povolení automatických aktualizací, monitorování uzlů nebo provádění pravidelných restartování.


**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: nasazení automatizovaného řešení pro správu oprav Third Party Software

**Pokyny**: Zajistěte, aby byly v uzlech fondu Azure Batch aplikace třetích stran opraveny po dobu životnosti clusteru, což může vyžadovat povolení automatických aktualizací, monitorování uzlů nebo provádění pravidelných restartování.


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné**materiály: Pokud máte Rapid7, Qualys nebo jakékoli jiné předplatné platformy pro správu ohrožení zabezpečení, můžete pomocí portálu tohoto dodavatele zobrazit a porovnat prověřování ohrožení zabezpečení back-.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných chyb zabezpečení.

**Doprovodné**materiály: Využijte společný program pro vyhodnocování rizik (např. běžný systém vyhodnocování chyb zabezpečení) nebo výchozí hodnocení rizik poskytovaná skenovacím nástrojem třetí strany.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: použijte Azure Asset Discovery.

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci vašich předplatných dotazovat nebo zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť atd.). Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager (ARM), které budou předány dál.

Jak vytvářet dotazy pomocí Azure Resource graphu:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak zobrazit vaše předplatná Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Porozumění službě Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat Assetu

**Doprovodné**materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné**materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Jak vytvořit další předplatná Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Postup vytvoření Skupiny pro správu:

https://docs.microsoft.com/azure/governance/management-groups/create

Vytváření značek a uživatelských značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: Udržujte inventarizaci schválených prostředků Azure a softwarových titulů.

**Doprovodné**materiály: definování seznamu schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky


**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat nebo zjišťovat prostředky v rámci předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvářet dotazy pomocí Azure graphu: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné**materiály: u uzlů Azure Batch fondu implementujte řešení třetí strany pro monitorování uzlů clusteru pro neschválené softwarové aplikace.


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné**materiály: u uzlů Azure Batch fondu implementujte řešení třetí strany pro monitorování uzlů clusteru pro neschválené softwarové aplikace.


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné**materiály: u uzlů Azure Batch fondu implementujte řešení třetích stran, které zabraňuje neoprávněnému softwaru v provádění.


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odepřít konkrétní typ prostředku s Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="610-implement-approved-application-list"></a>6,10: Implementujte schválený seznam aplikací

**Doprovodné**materiály: pro uzly fondu Azure Batch implementujte řešení třetí strany, abyste zabránili spuštění neautorizovaných typů souborů.


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Omezení schopnosti uživatelů pracovat s Azure Resource Manager prostřednictvím skriptů</div>

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací "blokování přístupu" pro aplikaci Microsoft Azure Management.

Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné**materiály: nelze použít,

Neplatí to Azure Batch, protože uživatelé (bez správců) fondů Azure Batch nepotřebují přístup k jednotlivým uzlům ke spouštění úloh. Správce clusteru již má kořenový přístup ke všem uzlům.


**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné**materiály: nejde použít, srovnávací test je určený pro webové aplikace běžící na Azure App Service nebo IaaS instancích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: použijte aliasy Azure Policy v oboru názvů "Microsoft.Batch" k vytváření vlastních zásad pro auditování nebo prosazování konfigurace Azure Batch účtů a fondů.

Jak zobrazit dostupné aliasy Azure Policy:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: Vytvoření zabezpečených konfigurací pro váš operační systém

**Pokyny**: Vytvoření zabezpečených konfigurací pro operační systém uzlů fondu Batch.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: Údržba zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: použijte Azure Policy [Deny] a [nasazení, pokud neexistuje] pro vymáhání zabezpečených nastavení pro prostředky Azure související s účtem a fondy služby Batch (například virtuální sítě, podsítě, brány Azure firewall, účty Azure Storage atd.). Pomocí aliasů Azure Policy z následujících oborů názvů můžete vytvářet vlastní zásady:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Pochopení Azure Policych účinků: https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: udržování zabezpečených konfigurací pro operační systémy

**Doprovodné**materiály: Azure Batch ve fondu image operačních systémů spravované a udržované Microsoftem. Zodpovídáte za implementaci konfigurace stavu na úrovni operačního systému.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné**materiály: Pokud používáte vlastní definice Azure Policy pro účty Azure Batch, fondy nebo související prostředky, použijte Azure Repos k bezpečnému ukládání a správě kódu.

Jak ukládat kód v Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos dokumentaci:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Pokyny**: Pokud používáte pro fondy Azure Batch vlastní image, použijte řízení přístupu na základě role Azure (Azure RBAC) a zajistěte, aby k nim měli přístup jenom autorizovaní uživatelé.

Porozumění službě Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Jak nakonfigurovat službu Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: nasazení nástrojů pro správu konfigurace systému

**Doprovodné**materiály: Používejte předdefinované definice Azure Policy pro upozornění, audit a prosazování konfigurací prostředků souvisejících s Azure Batch.  Použijte aliasy Azure Policy v oboru názvů "Microsoft.Batch" k vytváření vlastních zásad pro Azure Batch účty a fondy. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: nasaďte nástroje pro správu konfigurace systému pro operační systémy.

**Pokyny**: Implementujte řešení třetí strany a udržujte požadovaný stav pro operační systémy uzlů Azure Batch fondu.


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Implementujte automatizované monitorování konfigurace pro služby Azure.

**Pokyny**: použijte aliasy Azure Policy v oboru názvů "Microsoft.Batch" k vytvoření vlastních zásad pro auditování nebo prosazování konfigurace instance Azure Batch. Můžete použít také všechny předdefinované zásady vytvořené speciálně pro Azure Batch nebo prostředky používané Azure Batch, například:

- Podsítě by měly být přidružené ke skupině zabezpečení sítě.

– Účty úložiště by měly používat koncový bod služby virtuální sítě.

- V účtech Batch by měly být povolené diagnostické protokoly.

Jak zobrazit dostupné aliasy Azure Policy: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Pokyny**: Implementujte řešení třetí strany a sledujte stav operačních systémů uzlů Azure Batch fondu.


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="711-securely-manage-azure-secrets"></a>7,11: zabezpečená Správa tajných klíčů Azure

**Doprovodné**materiály: Azure Key Vault lze použít s nasazením Azure Batch ke správě klíčů pro úložiště fondu v rámci účtů Azure Storage.

Jak integrovat se spravovanými identitami Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Postup vytvoření Key Vault:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Ověření Key Vault:

https://docs.microsoft.com/azure/key-vault/general/authentication

Jak přiřadit zásadu přístupu Key Vault:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: bezpečné a automaticky spravované identity

**Doprovodné**materiály: není k dispozici, identita spravované služby Azure Batch nepodporuje.


**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

Jak nastavit skener přihlašovacích údajů: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné**materiály: pomocí programu Windows Defender na jednotlivých uzlech fondu Azure Batch v případě operačních systémů Windows nebo Poskytněte vlastní řešení proti malwaru, pokud používáte Linux.


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Batch), ale neběží na zákaznickém obsahu.

Předem Prohledejte všechny soubory nahrané do nevýpočetních prostředků Azure, například App Service, Data Lake Storage, Blob Storage atd. Společnost Microsoft nemá přístup k zákaznickým datům v těchto instancích.

Pochopení ochrany proti malwaru Microsoftu pro Azure Cloud Services a Virtual Machines:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné**materiály: v případě operačních systémů Windows použijte Windows Defender na jednotlivých uzlech Azure Batch fondu a ujistěte se, že je povolená Automatická aktualizace. Poskytněte vlastní řešení proti malwaru, pokud používáte Linux.


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: při použití účtu Azure Storage pro úložiště dat fondu Azure Batch vyberte příslušnou možnost redundance (LRS, ZRS, GRS, RA-GRS). 

Jak nakonfigurovat redundanci úložiště pro účty Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

**Pokyny**: při použití účtu Azure Storage pro úložiště dat fondu Azure Batch vyberte příslušnou možnost redundance (LRS, ZRS, GRS, RA-GRS).  Pokud používáte Azure Key Vault pro jakoukoli součást nasazení Azure Batch, ujistěte se, že jsou vaše klíče zálohovány.

Jak nakonfigurovat redundanci úložiště pro účty Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Postup zálohování klíčů trezoru klíčů v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

**Pokyny**: Pokud spravujete vlastní klíče pro účty Azure Storage nebo jakýkoli jiný prostředek, který souvisí s implementací Azure Batch, pravidelně otestujte obnovení zálohovaných klíčů.

Postup zálohování klíčů trezoru klíčů v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

Postup obnovení klíče spravovaného zákazníkem pomocí PowerShellu:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

**Doprovodné**materiály: pokud se Azure Key Vault používá k ukládání klíčů souvisejících s účty úložiště Azure Batch fondu, povolte v Azure Key Vault obnovitelné odstranění, abyste chránili klíče před náhodným nebo úmyslným odstraněním.

Jak povolit obnovitelné odstranění v Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Doprovodné**materiály: Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují role pracovníků a také fáze zpracování a správy incidentů.

Postup konfigurace automatizace pracovních postupů v rámci Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Pokyny**: Security Center přiřadí závažnosti výstrahám, které vám pomůžou určit prioritu pořadí, ve kterém se zúčastníte jednotlivých výstrah, takže pokud dojde k ohrožení určitého prostředku, můžete se k němu hned dostat. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné**materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

Přečtěte si téma publikace NIST: Průvodce pro testování, školení a cvičení programů pro plány a možnosti IT: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách &nbsp; pro incidenty zabezpečení

**Doprovodné**materiály: kontaktní údaje incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že vaše data jsou přístupná z nezákonných nebo neoprávněných stran.

Jak nastavit Azure Security Center kontakt zabezpečení:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné**materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

Postup konfigurace průběžného exportu:

https://docs.microsoft.com/azure/security-center/continuous-export

Jak streamovat výstrahy do Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné**materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a ujistěte se, že máte v 60 dnech opravit všechny důležité výsledky zabezpečení.

**Doprovodné**materiály: řiďte se prosím pravidly zapojení Microsoftu, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Další informace o strategii a provádění testování na základě červeného seskupování a testování průniku na webu pomocí spravované cloudové infrastruktury, služeb a aplikací Microsoftu najdete tady: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [plánech zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)
