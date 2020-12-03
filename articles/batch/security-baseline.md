---
title: Základní hodnoty zabezpečení Azure pro Batch
description: Základní hodnoty zabezpečení služby Batch poskytují pokyny a prostředky pro implementaci doporučení zabezpečení určených ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: batch
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e7be42b2a6e9f2cdc1aa0258f218fea9fd963093
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532023"
---
# <a name="azure-security-baseline-for-batch"></a>Základní hodnoty zabezpečení Azure pro Batch

Tato základní hodnota zabezpečení platí pro službu Batch s pokyny od [verze Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah se seskupuje pomocí **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů týkajících se služby Batch. **Ovládací prvky** , které se nevztahují na dávku, jsou vyloučené.

 
Pokud chcete zjistit, jak se Batch kompletně mapuje na test zabezpečení Azure, přečtěte si [úplný soubor mapování standardních hodnot zabezpečení dávky](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: nasazení Azure Batchch fondů v rámci virtuální sítě. Pokud chcete, aby výpočetní uzly fondu umožňovaly zabezpečeně komunikovat s jinými virtuálními počítači nebo s místní sítí, můžete fond zřídit v podsíti virtuální sítě Azure. Také nasazení fondu v rámci virtuální sítě vám umožní řídit skupinu zabezpečení sítě (NSG), která se používá k zabezpečení síťových rozhraní jednotlivých uzlů (NIC) a také podsíť. Nakonfigurujte NSG tak, aby povoloval provoz jenom z důvěryhodných IP adres/Locations na internetu.

Pokud je to možné, zakažte přístup k veřejné síti pomocí privátního propojení Azure a připojte se k účtu Azure Batch prostřednictvím privátního koncového bodu. Služba privátního propojení Azure je zabezpečená a přijímá připojení jenom z ověřených a autorizovaných privátních koncových bodů. Můžete taky omezit možnosti připojení a zjistitelnosti tím, že zakážete veřejně vystavené koncové body RDP/SSH pro výpočetní uzly ve fondu služby Batch.

- [Postup vytvoření fondu Azure Batch v rámci Virtual Network](batch-virtual-network.md)

- [Postup vytvoření účtu Azure Batch se zakázaným přístupem k síti](private-connectivity.md)

- [Postup vytvoření privátního koncového bodu](../private-link/create-private-endpoint-portal.md)

- [Jak odepřít přístup k provozu RDP/SSH](pool-endpoint-configuration.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné** materiály: použijte Azure Security Center a opravte doporučení k ochraně sítě týkající se skupiny zabezpečení virtuální sítě nebo sítě (NSG) přidružené k vašemu fondu služby Batch. Povolte protokoly toku na NSG, který se používá k ochraně fondu Batch, a odešlete protokoly do účtu Azure Storage pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Azure Log Analytics a používat Azure Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Azure Analýza provozu je schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a odhalit síťové opravy chyb.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: povolení služby Azure DDoS (Distributed Denial-of-Service) ve virtuální síti s ochranou fondu Azure Batch pro ochranu před útoky DDoS. K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

- [Jak nakonfigurovat DDoS Protection](/azure/virtual-network/manage-ddos-protection)

- [Pochopení Azure Security Center integrované analýzy hrozeb](/azure/security-center/security-center-alerts-service-layer)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné** materiály: Povolte protokoly toku ve skupině zabezpečení sítě (NSG), která se používá k ochraně fondu Azure Batch, a odešlete protokoly do Azure Storage účtu pro audit provozu.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: Pokud je to nutné pro účely dodržování předpisů, vyberte síťové virtuální zařízení z Azure Marketplace, které podporuje funkce pro zjišťování neoprávněných vniknutí (ID) a systémy ochrany před únikem informací (IP) s možnostmi kontroly zatížení.

Pokud zjišťování neoprávněných vniknutí nebo prevence na základě kontroly zatížení není požadavkem, je možné použít Azure Firewall s analýzou hrozeb. Azure Firewall filtrování na základě logiky hrozeb může upozorňovat a zamítnout provoz do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu.

Nasaďte Azure Firewall s veřejnou IP adresou ve stejné virtuální síti jako uzly fondu Azure Batch. Nakonfigurujte pravidla překladu adres (NAT) mezi důvěryhodnými umístěními na internetu a privátními IP adresami uzlů jednotlivých fondů. Na Azure Firewall v části Analýza hrozeb nakonfigurujte "Alert" a "deny" pro blokování výstrahy a zablokování provozu do a ze známých škodlivých IP adres a domén. IP adresy a domény se nacházejí z kanálu Microsoft Threat Intelligence a zahrnují se jenom ty nejvyšší důvěrné záznamy. 

- [Postup vytvoření fondu Azure Batch v rámci Virtual Network](batch-virtual-network.md)

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: pomocí značek služby virtuální sítě definujte řízení přístupu k síti pro skupiny zabezpečení sítě nebo brány firewall Azure, které jsou přidružené k vašim Azure Batch fondům. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Pochopení a používání značek služeb](../virtual-network/service-tags-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky přidružené k vašim Azure Batch fondům s Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft.Batch a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě vašich fondů Azure Batch.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro skupiny síťových služeb (skupin zabezpečení sítě) a další prostředky související se zabezpečením a tokem provozu sítě, které jsou spojené s vašimi fondy Azure Batch. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

- [Jak vytvořit virtuální síť](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vašimi fondy Azure Batch. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view) 

- [Vytváření výstrah v Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: účet Azure Batch, který se má Azure monitor k agregaci dat zabezpečení generovaných zařízeními clusteru. Využijte vlastní dotazy k detekci a reakci na hrozby v prostředí.  Pro Azure Batch monitorování na úrovni prostředků použijte rozhraní API služby Batch k monitorování nebo dotazování stavu vašich prostředků, včetně úloh, úkolů, uzlů a fondů.

- [Jak připojit účet Azure Batch k Azure Monitor](batch-diagnostics.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Návod**: pro Azure Batch monitorování na úrovni účtu sledujte jednotlivé účty Batch pomocí funkcí Azure monitor. Azure Monitor shromažďuje metriky a volitelně diagnostické protokoly pro prostředky s rozsahem na úrovni účtu Batch, jako jsou fondy, úlohy a úkoly. Shromažďovat a spotřebovávat tato data ručně nebo programově, abyste mohli monitorovat aktivity v účtu Batch a diagnostikovat problémy.

Pro Azure Batch monitorování na úrovni prostředků použijte rozhraní API Azure Batch k monitorování nebo dotazování stavu vašich prostředků, včetně úloh, úloh, uzlů a fondů.

- [Postup konfigurace Azure Batch monitorování a protokolování na úrovni účtu](monitoring-overview.md)

- [Principy monitorování na úrovni prostředků Batch](monitoring-overview.md#batch-resource-monitoring)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

#### <a name="azure-policy-built-in-definitions"></a>Azure Policy předdefinované definice

[!INCLUDE [microsoft.batch-2-3](../../includes/policy/standards/asb/rp-controls/microsoft.batch-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: Azure monitor shromažďuje metriky a protokoly diagnostiky pro prostředky v účtu Azure Batch. Shromažďovat a spotřebovávat tato data různými způsoby, jak monitorovat účet Azure Batch a diagnostikovat problémy. Můžete také nakonfigurovat upozornění na metriky, abyste obdrželi oznámení, když metrika dosáhne zadané hodnoty.

V případě potřeby se můžete ke svým uzlům fondu připojit pomocí protokolu SSH (Secure Shell) nebo protokol RDP (Remote Desktop Protocol) (RDP) a získat přístup k místním protokolům operačního systému.

- [Jak shromažďovat diagnostické protokoly z účtu Azure Batch](batch-diagnostics.md#batch-diagnostics)

- [Jak se vzdáleně připojit k uzlům Azure Batch fondu](/azure/batch/batch-api-basics#error-handling)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny**: Azure Batch účet Azure monitor. Ujistěte se, že se v pracovním prostoru Azure Log Analytics používala doba uchování protokolu nastavená v souladu s pravidly dodržování předpisů vaší organizace.

- [Postup konfigurace Azure Batch monitorování a protokolování](monitoring-overview.md)

- [Jak nakonfigurovat dobu uchování v pracovním prostoru Azure Log Analytics](../azure-monitor/platform/manage-cost-storage.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: vytváření Azure Batch výstrah metrik, které se aktivují, když hodnota zadané metriky přechází z dané prahové hodnoty.

- [Jak nakonfigurovat výstrahy Azure Batch metriky](batch-diagnostics.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: vytváření Azure Batch výstrah metrik, které se aktivují, když hodnota zadané metriky přechází z dané prahové hodnoty.

- [Jak nakonfigurovat výstrahy Azure Batch metriky](batch-diagnostics.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Pokyny**: použijte Windows Defender na jednotlivých uzlech služby Batch v případě operačních systémů Windows nebo Poskytněte vlastní antimalwarové řešení, pokud používáte Linux.

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Pokyny**: implementace řešení třetí strany pro protokolování DNS.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Pokyny**: ruční konfigurace protokolování konzoly a přepisu prostředí PowerShell v jednotlivých uzlech.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Udržujte si záznam místního účtu pro správu, který se vytvoří během zřizování fondu Azure Batch, i všechny další účty, které vytvoříte. Pokud se navíc používá Azure Active Directory integrace, Azure AD má předdefinované role, které se musí explicitně přiřadit a proto se Queryable. Pomocí modulu Azure AD PowerShell můžete provádět dotazy ad hoc a zjišťovat účty, které jsou členy skupin pro správu.

Kromě toho můžete použít Azure Security Center doporučení pro správu identit a přístupu.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Jak monitorovat identitu a přístup pomocí Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: při zřizování fondu Azure Batch máte možnost vytvářet účty místního počítače. Neexistují žádná výchozí hesla, která by bylo možné změnit, ale můžete zadat různá hesla pro přístup přes SSH (Secure Shell) a protokol RDP (Remote Desktop Protocol) (RDP). Po nakonfigurování Azure Batch fondu můžete vygenerovat náhodného uživatele pro jednotlivé uzly v rámci Azure Portal nebo prostřednictvím rozhraní API Azure Resource Manager.

- [Postup přidání uživatele ke konkrétnímu výpočetnímu uzlu](/rest/api/batchservice/computenode/adduser)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Pokyny**: integrace ověřování pro aplikace Azure Batch s Azure Active Directory. Vytvořte zásady a postupy týkající se používání vyhrazených účtů pro správu.

Kromě toho můžete použít Azure Security Center doporučení pro správu identit a přístupu.

- [Ověřování aplikací Batch pomocí Azure Active Directory](batch-aad-auth.md)

- [Jak monitorovat identitu a přístup pomocí Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Pokyny**: integrace ověřování pro aplikace Azure Batch s Azure Active Directory. Povolte vícefaktorové ověřování Azure AD a sledujte Azure Security Center doporučení pro správu identit a přístupu.

 

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Doprovodné** materiály: použití privilegovaným přístupem (privilegovaných pracovních stanic) s nakonfigurovaným vícefaktorového ověřováním, které je nakonfigurované pro přihlášení k a konfiguraci Azure Batchch prostředků.

- [Další informace o pracovních stanicích s privilegovaným přístupem](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: Pokud máte integrované ověřování pro aplikace Azure Batch s Azure Active Directory, použijte Azure Active Directory sestavy zabezpečení pro generování protokolů a výstrah, pokud dojde k podezřelé nebo nebezpečné aktivitě v prostředí. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu.

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Jak monitorovat aktivitu identity uživatelů a přístupu v Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: Pokud máte k dispozici integrované ověřování pro aplikace Azure Batch s Azure Active Directory, můžete pomocí pojmenovaných umístění podmíněného přístupu dovolit přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné** materiály: jako centrální ověřování a autorizační systém použijte Azure Active Directory a integrujte ověřování pro Azure Batch aplikace pomocí Azure AD. Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Jak ověřit aplikace Batch pomocí Azure AD](batch-aad-auth.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Kromě toho můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: Vytvoření nastavení diagnostiky pro Azure Active Directory uživatelských účtů, odesílání protokolů auditu a protokolů přihlášení do pracovního prostoru Azure Log Analytics. Nakonfigurujte požadované výstrahy v pracovním prostoru Azure Log Analytics.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Pokyny**: pomocí Azure Active Directory detekce rizik a Identity Protection můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce týkající se identit uživatelů. Kromě toho můžete ingestovat data do služby Azure Sentinel pro další šetření.

- [Zobrazení rizikových přihlášení Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.  

**Doprovodné** materiály: není k dispozici; Customer Lockbox ještě není pro Azure Batch podporované.
 
- [Seznam podporovaných služeb Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Fondy Azure Batch by se měly oddělit pomocí virtuální sítě/podsítě, vhodně označit a zabezpečit pomocí skupin zabezpečení sítě (NSG). Data Azure Batch by měla být obsažena v zabezpečeném Azure Storagem účtu.

- [Postup vytvoření fondu Azure Batch v rámci Virtual Network](batch-virtual-network.md)

- [Postup zabezpečení účtů Azure Storage](/azure/storage/common/storage-security-guide)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: u účtů Azure Storage přidružených k vašim Azure Batch fondům, které obsahují citlivé informace, je označit jako citlivé pomocí značek a zabezpečit je pomocí osvědčených postupů Azure.

Pro Azure Storage nebo výpočetní prostředky ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

- [Postup zabezpečení účtů Azure Storage](/azure/storage/common/storage-security-guide)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: šifrování všech citlivých informací během přenosu. Microsoft Azure prostředky vyjednávat TLS 1,2 ve výchozím nastavení. Ujistěte se, že všichni klienti, kteří se připojují ke svým fondům Azure Batch nebo úložiště dat (účty Azure Storage), můžou vyjednávat TLS 1,2 nebo vyšší.

Ujistěte se, že se pro přístup k účtu úložiště obsahujícímu data Azure Batch vyžaduje protokol HTTPS.

- [Principy šifrování Azure Storage účtu při přenosu](../storage/blobs/security-recommendations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: u účtů Azure Storage přidružených k vašim Azure Batch fondům, které obsahují citlivé informace, je označit jako citlivé pomocí značek a zabezpečit je pomocí osvědčených postupů Azure.

Pro Azure Storage nebo výpočetní prostředky ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

- [Postup zabezpečení účtů Azure Storage](/azure/storage/common/storage-security-guide)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte řízení přístupu na základě role

**Pokyny**: použijte řízení přístupu na základě role Azure (Azure RBAC) k řízení přístupu k rovině správy prostředků Azure, včetně účtu Batch, fondů služby Batch a účtů úložiště.

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Pokyny**: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Storage nebo výpočetní prostředky. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: u účtů úložiště přidružených k vašemu Azure Batch účtu doporučujeme, aby Microsoft mohl spravovat šifrovací klíče, ale v případě potřeby máte možnost spravovat vlastní klíče.

Azure Disk Encryption se dá využít k ochraně a ochraně vašich dat, aby splňovala závazky zabezpečení organizace a dodržování předpisů. Všechny spravované disky, snímky, image a data zapsaná na stávající disky se automaticky zašifrují bez použití klíčů spravovaných platformou.

- [Správa šifrovacích klíčů pro účty Azure Storage](/azure/storage/common/storage-encryption-keys-portal)

- [Postup konfigurace šifrovacích klíčů spravovaných zákazníkem](/azure/storage/common/storage-encryption-keys-portal)

- [Vytvoření fondu s povoleným šifrováním disku](disk-encryption.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet upozornění na důležité prostředky Azure související s nebo přidruženými k vašim Azure Batchm účtům a fondům.

Nakonfigurujte nastavení diagnostiky pro účty úložiště přidružené k fondu Azure Batch, abyste mohli monitorovat a protokolovat všechny operace CRUD pro data fondu.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/platform/alerts-activity-log.md)

- [Jak povolit další protokolování/auditování pro účet Azure Storage](../storage/common/storage-monitor-storage-account.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: u uzlů fondu Azure Batch zodpovídáte za správu řešení pro správu ohrožení zabezpečení.

Volitelně, pokud máte Rapid7, Qualys nebo jakékoli jiné předplatné platformy pro správu ohrožení zabezpečení, můžete ručně nainstalovat agenty pro posouzení ohrožení zabezpečení do uzlů fondu Batch a spravovat uzly prostřednictvím příslušného portálu.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Pokyny**: Společnost Microsoft zachovává a aktualizuje obrázky uzlů základních Azure Batch fondů. Zajistěte, aby operační systém uzlů Azure Batchového fondu zůstal opravený po dobu životnosti clusteru, což může vyžadovat povolení automatických aktualizací, monitorování uzlů nebo provádění pravidelných restartování.

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizovaného řešení pro správu oprav pro softwarové tituly třetích stran

**Pokyny**: Zajistěte, aby byly v uzlech fondu Azure Batch aplikace třetích stran opraveny po dobu životnosti clusteru, což může vyžadovat povolení automatických aktualizací, monitorování uzlů nebo provádění pravidelných restartování.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: Pokud máte Rapid7, Qualys nebo jakékoli jiné předplatné platformy pro správu ohrožení zabezpečení, můžete pomocí portálu tohoto dodavatele zobrazit a porovnat prověřování ohrožení zabezpečení back-.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: Využijte společný program pro vyhodnocování rizik (např. běžný systém vyhodnocování chyb zabezpečení) nebo výchozí hodnocení rizik poskytovaná skenovacím nástrojem třetí strany.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci vašich předplatných dotazovat nebo zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť atd.). Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

I když se v Azure Resource Graph Exploreru můžou zjistit klasické prostředky Azure, důrazně doporučujeme vytvářet a používat Azure Resource Manager prostředky.

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create)

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: definování seznamu schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat nebo zjišťovat prostředky v rámci předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné** materiály: u uzlů Azure Batch fondu implementujte řešení třetí strany pro monitorování uzlů clusteru pro neschválené softwarové aplikace.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: u uzlů Azure Batch fondu implementujte řešení třetí strany pro monitorování uzlů clusteru pro neschválené softwarové aplikace.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: u uzlů Azure Batch fondu implementujte řešení třetích stran, které zabraňuje neoprávněnému softwaru v provádění.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad: 
- Žádné povolené typy prostředků 
- Povolené typy prostředků 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné** materiály: pro uzly fondu Azure Batch implementujte řešení třetí strany, abyste zabránili spuštění neautorizovaných typů souborů.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací "blokování přístupu" pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: použijte aliasy Azure Policy v oboru názvů "Microsoft.Batch" k vytváření vlastních zásad pro auditování nebo prosazování konfigurace Azure Batch účtů a fondů.

- [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Pokyny**: Vytvoření zabezpečených konfigurací pro operační systém uzlů fondu Batch.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: použijte Azure Policy [Deny] a [nasazení, pokud neexistuje] pro vymáhání zabezpečených nastavení pro prostředky Azure související s účtem a fondy služby Batch (například virtuální sítě, podsítě, brány Azure firewall, účty Azure Storage atd.). Pomocí aliasů Azure Policy z následujících oborů názvů můžete vytvářet vlastní zásady:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: Azure Batch ve fondu image operačních systémů spravované a udržované Microsoftem. Zodpovídáte za implementaci konfigurace stavu na úrovni operačního systému.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Pokud používáte vlastní definice Azure Policy pro účty Azure Batch, fondy nebo související prostředky, použijte Azure Repos k bezpečnému ukládání a správě kódu.

- [Jak v Azure DevOps ukládat kód](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Dokumentace k Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Pokyny**: Pokud používáte pro fondy Azure Batch vlastní image, použijte řízení přístupu na základě role (RBAC), abyste zajistili, že k imagí budou mít přístup jenom autorizovaní uživatelé.

- [Princip RBAC v Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Konfigurace řízení přístupu na základě role Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: Používejte předdefinované definice Azure Policy pro upozornění, audit a prosazování konfigurací prostředků souvisejících s Azure Batch.  Použijte aliasy Azure Policy v oboru názvů "Microsoft.Batch" k vytváření vlastních zásad pro Azure Batch účty a fondy. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Pokyny**: Implementujte řešení třetí strany a udržujte požadovaný stav pro operační systémy uzlů Azure Batch fondu.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: použijte aliasy Azure Policy v oboru názvů "Microsoft.Batch" k vytvoření vlastních zásad pro auditování nebo prosazování konfigurace instance Azure Batch. Můžete použít také všechny předdefinované zásady vytvořené speciálně pro Azure Batch nebo prostředky používané Azure Batch, například:
- Podsítě by měly být přidružené ke skupině zabezpečení sítě – účty úložiště by měly používat koncový bod služby virtuální sítě.
- V účtech Batch by měly být povolené diagnostické protokoly.

- [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Pokyny**: Implementujte řešení třetí strany a sledujte stav operačních systémů uzlů Azure Batch fondu.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: Azure Key Vault lze použít s nasazením Azure Batch ke správě klíčů pro úložiště fondu v rámci účtů Azure Storage.

- [Integrace se spravovanými identitami Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Vytvoření Azure Key Vault](../key-vault/general/quick-create-portal.md)

- [Ověření Key Vault](../key-vault/general/authentication.md)
- [Postup přiřazení zásady přístupu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použijte centrálně spravovaný antimalwarový software

**Doprovodné** materiály: pomocí programu Windows Defender na jednotlivých uzlech fondu Azure Batch v případě operačních systémů Windows nebo Poskytněte vlastní řešení proti malwaru, pokud používáte Linux.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Batch), ale neběží na zákaznickém obsahu.

Předem Prohledejte všechny soubory nahrané do nevýpočetních prostředků Azure, například App Service, Data Lake Storage, Blob Storage atd. Společnost Microsoft nemá přístup k zákaznickým datům v těchto instancích.

- [Pochopení Microsoft antimalwaru pro Azure Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: v případě operačních systémů Windows použijte Windows Defender na jednotlivých uzlech Azure Batch fondu a ujistěte se, že je povolená Automatická aktualizace. Poskytněte vlastní řešení proti malwaru, pokud používáte Linux.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: při použití účtu Azure Storage pro úložiště dat fondu Azure Batch vyberte příslušnou možnost redundance (LRS, ZRS, GRS, RA-GRS). 

- [Jak nakonfigurovat redundanci úložiště pro účty Azure Storage](../storage/common/storage-redundancy.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: při použití účtu Azure Storage pro úložiště dat fondu Azure Batch vyberte příslušnou možnost redundance (LRS, ZRS, GRS, RA-GRS). Pokud používáte Azure Key Vault pro jakoukoli součást nasazení Azure Batch, ujistěte se, že jsou vaše klíče zálohovány.

- [Jak nakonfigurovat redundanci úložiště pro účty Azure Storage](../storage/common/storage-redundancy.md)

- [Postup zálohování klíčů trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny**: Pokud spravujete vlastní klíče pro účty Azure Storage nebo jakýkoli jiný prostředek, který souvisí s implementací Azure Batch, pravidelně otestujte obnovení zálohovaných klíčů.

- [Postup zálohování klíčů trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Postup obnovení klíče spravovaného zákazníkem pomocí PowerShellu](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: pokud se Azure Key Vault používá k ukládání klíčů souvisejících s účty úložiště Azure Batch fondu, povolte Soft-Delete v Azure Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění.

- [Jak povolit obnovitelné odstranění v Azure Key Vault](/azure/key-vault/key-vault-soft-delete-powershell)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Doprovodné** materiály: Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují role pracovníků a také fáze zpracování a správy incidentů.

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Pokyny**: Security Center přiřadí závažnosti výstrahám, které vám pomůžou určit prioritu pořadí, ve kterém se zúčastníte jednotlivých výstrah, takže pokud dojde k ohrožení určitého prostředku, můžete se k němu hned dostat. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní údaje incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že vaše data jsou přístupná z nezákonných nebo neoprávněných stran.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: – použijte [prosím pravidla zapojení Microsoftu, abyste zajistili, že testy průniku nebudou porušením zásad Microsoftu](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.) .

Další informace o strategii Microsoftu a provádění testování využití červeného týmu a testování průniku na základě spravované cloudové infrastruktury, služeb a aplikací Microsoftu najdete níže: 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
