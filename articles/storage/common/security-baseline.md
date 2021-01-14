---
title: Základní hodnoty zabezpečení Azure pro Azure Storage
description: Základní hodnoty zabezpečení Azure pro Azure Storage
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f6825fee4cfb78ab54d782cd4d942bb994bedae9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98195717"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Základní hodnoty zabezpečení Azure pro Azure Storage

Základní plán zabezpečení Azure pro Azure Storage obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Chraňte prostředky pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network

**Pokyny**: Nakonfigurujte bránu firewall účtu úložiště tak, že omezíte přístup na klienty z konkrétních rozsahů veřejných IP adres, vyberete virtuální sítě (virtuální sítě) v Azure nebo konkrétní prostředky Azure. Můžete také nakonfigurovat privátní koncové body, aby provoz do služby úložiště z vašeho podniku prochází výhradně v privátních sítích.

Poznámka: klasické účty úložiště nepodporují brány firewall a virtuální sítě.

- [Jak nakonfigurovat bránu Azure Storage firewall](./storage-network-security.md#change-the-default-network-access-rule)

- [Postup konfigurace privátních koncových bodů pro Azure Storage](./storage-private-endpoints.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1,2: Sledujte a protokolujte konfiguraci virtuální sítě, podsítě a síťové karty a provoz

**Doprovodné** materiály: Azure Storage poskytuje vrstvený model zabezpečení. Přístup k účtu úložiště můžete omezit na požadavky pocházející ze zadaných IP adres, rozsahů IP adres nebo ze seznamu podsítí v Azure Virtual Network (virtuální síť). K zabezpečení síťových prostředků v Azure můžete použít Azure Security Center a postupovat podle doporučení ochrany sítě. Povolte také protokoly toku NSG pro virtuální sítě/podsíť nakonfigurovanou pro účty úložiště pomocí brány firewall účtu úložiště a odešlete protokoly do účtu úložiště pro audit provozu. 

Všimněte si, že pokud máte k účtu úložiště připojené privátní koncové body, nemůžete pro podsítě nakonfigurovat pravidla skupiny zabezpečení sítě (NSG). 

- [Konfigurace bran firewall Azure Storage a virtuálních sítí](./storage-network-security.md)

- [Jak povolit protokoly toku NSG](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Principy zabezpečení sítě, které poskytuje Azure Security Center](../../security-center/security-center-network-recommendations.md)

- [Principy privátních koncových bodů pro Azure Storage](./storage-private-endpoints.md#known-issues)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: nepoužitelné; doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: povolení rozšířené ochrany před internetovými útoky pro váš účet Azure Storage. Rozšířená ochrana před internetovými útoky pro Azure Storage poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům úložiště nebo jejich zneužití. Azure Security Center integrované výstrahy jsou založené na aktivitách, pro které byla síťová komunikace přidružena k IP adrese, která byla úspěšně vyřešena, bez ohledu na to, zda je IP adresa známou nebo zda se jedná o známou IP adresu (například známý cryptominer) nebo IP adresu, která nebyla dříve rozpoznána jako riziková. Výstrahy zabezpečení se spouštějí při výskytu anomálií v aktivitě. 

- [Jak povolit rozšířenou ochranu před internetovými útoky](./azure-defender-storage-configure.md?tabs=azure-portal)

- [Pochopení Azure Security Center integrované analýzy hrozeb](../../security-center/azure-defender.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: záznam síťových paketů a protokolů toků

**Pokyny**: Network Watcher Capture paketů umožňuje vytvořit relace zachycení pro sledování provozu mezi účtem úložiště a virtuálním počítačem. K dispozici jsou filtry pro relaci zachycení, aby bylo možné zachytit pouze požadovaný provoz. Zachytávání paketů pomáhá diagnostikovat anomálie sítě, jak proaktivní, tak i proaktivní. Mezi další použití patří shromažďování statistik sítě, získávání informací o neoprávněných vniknutích k síti, ladění komunikace mezi klientem a serverem a mnoho dalšího. Schopnost vzdáleně aktivovat zachycení paketů, usnadňuje zatížení ručního zachycení paketů na požadovaném virtuálním počítači, který šetří cenné časy. 

- [Správa zachytávání paketů pomocí Azure Network Watcher pomocí portálu](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: nasazení systémů ochrany před internetovými útoky na síť a prevence vniknutí

**Pokyny**: Rozšířená ochrana před internetovými útoky pro Azure Storage poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům úložiště nebo jejich zneužití. Výstrahy zabezpečení se spouštějí při výskytu anomálií v aktivitě. Tyto výstrahy zabezpečení jsou integrovány do centra Azure Security Center a jsou také odesílány e-mailem správcům předplatného s podrobnostmi o podezřelé aktivitě a doporučeními, jak hrozby prozkoumat a opravit. 

- [Konfigurace rozšířené ochrany před internetovými útoky pro Azure Storage](./azure-defender-storage-configure.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: nepoužitelné; doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: u prostředků ve virtuálních sítích, které potřebují přístup k vašemu účtu úložiště, použijte značky Virtual Network služby pro nakonfigurovanou virtuální síť a definujte řízení přístupu k síti pro skupiny zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. úložiště) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny. 

Pokud je třeba přístup k síti určit na konkrétní účty úložiště, použijte Virtual Network zásady koncového bodu služby.

- [Další informace o použití značek služeb](../../virtual-network/service-tags-overview.md)

- [Další informace o zásadách koncového bodu služby virtuální sítě pro Azure Storage](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost**: služba

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky přidružené k vašemu Azure Storage účtu pomocí Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. Storage a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě vašich prostředků účtu úložiště. 

Můžete také využít integrované definice zásad související s účtem úložiště, například: účty úložiště by měly používat koncový bod služby virtuální sítě. 

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Ukázky Azure Policy pro úložiště](../../governance/policy/samples/built-in-policies.md#storage)

- [Ukázky Azure Policy pro síť](../../governance/policy/samples/built-in-policies.md#network)

- [Vytvoření Azure Blueprint](../../governance/blueprints/create-blueprint-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Pokyny**: používejte značky pro skupiny zabezpečení sítě (NSG) a další zdroje informací týkající se zabezpečení sítě a toku provozu. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě. Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky. Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek. 

- [Vytváření a používání značek](../../azure-resource-manager/management/tag-resources.md)

- [Vytvoření Virtual Network](../../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: k protokolování změn konfigurace síťových prostředků použijte Azure Policy. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích. 

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Vytváření výstrah v Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-resource"></a>2,1: použijte schválený prostředek synchronizace času

**Doprovodné** materiály: nepoužitelné; Microsoft udržuje časové zdroje pro účty Azure Storage.

**Monitorování služby Azure Security Center:** Nelze použít

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: ingestování protokolů prostřednictvím Azure monitor k agregaci dat zabezpečení generovaných zařízeními koncových bodů, síťovými prostředky a dalšími systémy zabezpečení. V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště, a to v případě funkcí zabezpečení, jako je například neměnné úložiště a zajištěné uchování.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: analýza úložiště Azure poskytuje protokoly objektů blob, front a tabulek. Pomocí Azure Portal můžete nakonfigurovat, které protokoly se budou zaznamenávat pro váš účet. 

- [Jak nakonfigurovat monitorování pro účet Azure Storage](./storage-monitor-storage-account.md#configure-monitoring-for-a-storage-account)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: při ukládání protokolů událostí zabezpečení do účtu Azure Storage nebo v pracovním prostoru Log Analytics můžete zásady uchovávání informací nastavit podle požadavků vaší organizace. 

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](./storage-monitor-storage-account.md#configure-logging)

- [Změnit dobu uchovávání dat v Log Analytics](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: Chcete-li zkontrolovat protokoly Azure Storage, jsou k dispozici běžné možnosti, jako jsou například dotazy prostřednictvím nabídky Log Analytics a také jedinečná možnost zobrazení souborů protokolu přímo. V Azure Storage se protokoly ukládají v objektech blob, ke kterým se musí `http://accountname.blob.core.windows.net/$logs` přihlašovat přímo v (ve výchozím nastavení je složka protokolování skrytá, takže budete muset přejít přímo. V příkazech seznamu se nezobrazí. 

Také pro svůj účet Azure Storage povolit rozšířenou ochranu před internetovými útoky. Rozšířená ochrana před internetovými útoky pro Azure Storage poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům úložiště nebo jejich zneužití. Výstrahy zabezpečení se spouštějí při výskytu anomálií v aktivitě. Tyto výstrahy zabezpečení jsou integrovány do centra Azure Security Center a jsou také odesílány e-mailem správcům předplatného s podrobnostmi o podezřelé aktivitě a doporučeními, jak hrozby prozkoumat a opravit. 

- [Protokolovat a kontrolovat data](./storage-analytics-logging.md#how-logs-are-stored)

- [Jak povolit rozšířenou ochranu před internetovými útoky](./azure-defender-storage-configure.md?tabs=azure-portal)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé

**Doprovodné** materiály: v Azure Security Center povolit rozšířenou ochranu před internetovými útoky pro účet úložiště. Povolte nastavení diagnostiky pro účet úložiště a odešlete protokoly do pracovního prostoru Log Analytics. Připojte pracovní prostor Log Analytics do Azure Sentinel, protože poskytuje řešení pro automatizované odpovědi na orchestraci zabezpečení (společnosti). To umožňuje vytvořit playbooky (automatizovaná řešení) a použít je k nápravě problémů zabezpečení. 

- [Jak připojit Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Správa výstrah v Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

- [Upozornění na data protokolu Log Analytics](../../azure-monitor/learn/tutorial-response.md)

- [Protokolování analýz služby Azure Storage](./storage-analytics-logging.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: použijte Azure Security Center a povolte ochranu před internetovými útoky pro Azure Storage zjišťování malwaru pro Azure Storage pomocí analýzy reputace hodnoty hash a podezřelého přístupu z aktivního uzlu pro ukončení práce (proxy server anonymizace). 

- [Konfigurace rozšířené ochrany před internetovými útoky pro Azure Storage](./azure-defender-storage-configure.md?tabs=azure-portal)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné** materiály: řešení Azure DNS Analytics (Preview) v Azure monitor shromažďuje přehledy o infrastruktuře DNS v oblasti zabezpečení, výkonu a provozu. V současné době nepodporuje Azure Storage účty, ale můžete použít řešení protokolování DNS třetích stran. 

- [Získejte přehled o vaší infrastruktuře DNS pomocí řešení DNS Analytics Preview.](../../azure-monitor/insights/dns-analytics.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné** materiály: nepoužitelné; Srovnávací test je určený pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Azure AD má předdefinované role, které se musí explicitně přiřadit a jsou Queryable. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu. 

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné** materiály: účty Azure Storage ani Azure Active Directory nemají koncept výchozích nebo prázdných hesel. Azure Storage implementuje model řízení přístupu, který podporuje řízení přístupu na základě role Azure (Azure RBAC), stejně jako sdílené klíče a signatury sdíleného přístupu (SAS). Charakteristika sdíleného klíče a ověřování SAS je, že k volajícímu není přidružena žádná identita, a proto nelze provést autorizaci na základě oprávnění zabezpečení. 

- [Autorizace přístupu k datům v Azure Storage](./storage-auth.md)

- [Porozumění objektům zabezpečení a řízení přístupu pro účet Azure Storage](./storage-introduction.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu, které mají přístup k vašemu účtu úložiště. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu. 

Pomocí Azure AD Privileged Identity Management privilegovaných rolí pro služby společnosti Microsoft a ARM Azure můžete také povolit přístup za běhu nebo jen tolik. 

- [Pochopení Azure Security Center identity a přístupu](../../security-center/security-center-identity-access.md)

- [Přehled Privileged Identity Management](../../active-directory/privileged-identity-management/index.yml)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory Single Sign-On (SSO).

**Pokyny**: kdykoli je to možné, použijte Azure Active Directory jednotného přihlašování (SSO) místo konfigurace jednotlivých samostatných přihlašovacích údajů pro každou službu. Použijte Azure Security Center doporučení pro správu identit a přístupu. 

- [Princip jednotného přihlašování s Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

- [Autorizace přístupu k datům v Azure Storage](./storage-auth.md)

- [Autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory](./storage-auth-aad.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory.

**Doprovodné** materiály: Povolte Azure Active Directory vícefaktorového ověřování a sledujte Azure Security Center doporučení pro správu identit a přístupu, která vám pomůžou ochránit prostředky účtu úložiště. 

- [Jak povolit vícefaktorové ověřování v Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Doprovodné** materiály: použití privilegovaným přístupem (privilegovaných pracovních stanic) s MFA nakonfigurovaným pro přihlášení a konfiguraci prostředků účtu úložiště. 

- [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak povolit vícefaktorové ověřování v Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: protokolování a upozornění na podezřelou aktivitu z účtů pro správu

**Doprovodné** materiály: odeslání výstrah detekce rizik Azure Security Center do Azure monitor a konfigurace vlastního upozorňování/oznámení pomocí skupin akcí. Povolit rozšířenou ochranu před internetovými útoky pro účet Azure Storage pro generování výstrah pro podezřelou aktivitu. Navíc můžete pomocí zjišťování rizik Azure AD zobrazovat výstrahy a sestavy týkající se rizikového chování uživatelů. 

- [Postup nastavení rozšířené ochrany před internetovými útoky pro účet Azure Storage](./azure-defender-storage-configure.md)

- [Vysvětlení zjišťování rizik Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat skupiny akcí pro vlastní výstrahy a oznámení](../../azure-monitor/platform/action-groups.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí. 

- [Postup konfigurace pojmenovaných umístění v Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure poskytuje řízení přístupu na základě role Azure (Azure RBAC) pro jemně odstupňovanou kontrolu nad přístupem klienta k prostředkům v účtu úložiště.  Použijte přihlašovací údaje Azure AD, pokud je to možné, místo použití klíče účtu, který může být snáze ohrožen. Když návrh aplikace vyžaduje pro přístup k úložišti objektů BLOB sdílené přístupové podpisy, pomocí přihlašovacích údajů Azure AD vytvořte delegovaný přístup uživatelů (SAS), pokud je to možné, pro zajištění vysokého zabezpečení.

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Použití poskytovatele prostředků Azure Storage k přístupu k prostředkům správy](./authorization-resource-provider.md)

- [Jak nakonfigurovat přístup k objektům blob Azure a zařadit data do fronty pomocí Azure RBAC v Azure Portal](./storage-auth-aad-rbac-portal.md)

- [Autorizace přístupu k datům v Azure Storage](./storage-auth.md)

- [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](./storage-sas-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Pokyny**: Přečtěte si protokoly Azure Active Directory, abyste mohli zjistit zastaralé účty, které můžou obsahovat role pro správu účtu úložiště. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím, které se dají použít pro přístup k prostředkům účtu úložiště a přiřazování rolí. Přístup uživatelů by měl být pravidelně přezkoumán, aby se zajistilo, že pouze přípravní uživatelé budou mít přístup i nadále.  

Pomocí sdíleného přístupového podpisu (SAS) taky můžete zajistit zabezpečený delegovaný přístup k prostředkům ve vašem účtu úložiště, aniž byste tak ohrozili zabezpečení vašich dat. Můžete řídit, k jakým prostředkům má klient přístup, jaká oprávnění jsou na těchto prostředcích a jak dlouho je SAS platné, mezi další parametry.

Přečtěte si také anonymní přístup pro čtení kontejnerů a objektů BLOB. Ve výchozím nastavení může ke kontejneru a ke všem v něm obsaženým objektům blob získat přístup jen uživatel, kterému byla udělena příslušná oprávnění. Azure Monitor můžete použít k upozornění na anonymní přístup k účtům úložiště pomocí podmínky anonymního ověřování.

Jedním z efektivních způsobů, jak snížit riziko přístupu k nepodezřelému uživatelskému účtu, je omezit dobu trvání přístupu, který uživatelům udělíte. Časově omezené identifikátory URI SAS představují jeden efektivní způsob, jak automaticky vypršet přístup uživatelů k účtu úložiště. Časté střídání klíčů účtu úložiště je navíc způsob, jak zajistit, že neočekávaný přístup prostřednictvím klíčů účtu úložiště je omezené trvání.

- [Pochopení sestav Azure AD](../../active-directory/reports-monitoring/index.yml)

- [Postup zobrazení a změny přístupu na úrovni účtu Azure Storage](./storage-auth-aad-rbac-portal.md)

- [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](./storage-sas-overview.md)

- [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/anonymous-read-access-configure.md)

- [Monitorování účtu úložiště na webu Azure Portal](./storage-monitor-storage-account.md)

- [Správa přístupových klíčů účtu úložiště](./storage-account-keys-manage.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorování pokusů o přístup k deaktivovaným účtům

**Pokyny**: pomocí analýza úložiště Zaprotokolujte podrobné informace o úspěšných a neúspěšných požadavcích na službu úložiště. Všechny protokoly se ukládají v objektech blob bloku v kontejneru s názvem $logs, který se automaticky vytvoří, když je Analýza úložiště povolený pro účet úložiště.

Vytvořte nastavení diagnostiky pro Azure Active Directory uživatelských účtů a odešlete protokoly auditu a protokoly přihlášení do pracovního prostoru Log Analytics. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics. Pokud chcete monitorovat selhání ověřování u účtů Azure Storage, můžete vytvořit upozornění, která vás upozorní, když se dosáhne určitých prahových hodnot pro metriky prostředků úložiště. Kromě toho použijte Azure Monitor k upozornění na anonymní přístup k účtům úložiště pomocí podmínky anonymního ověřování.

- [Protokolování analýz služby Azure Storage](./storage-analytics-logging.md)

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak nakonfigurovat výstrahy metrik pro účty Azure Storage](./storage-monitor-storage-account.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Doprovodné** materiály: použití funkcí rizika a ochrany Identity v Azure Active Directory ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce související s prostředky vašeho účtu úložiště. Pomocí služby Azure Sentinel byste měli povolit automatizované odezvy pro implementaci reakcí zabezpečení vaší organizace. 

- [Zobrazení rizikových přihlášení Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: ve scénářích podpory, kde Microsoft potřebuje získat přístup k zákaznickým datům, Customer Lockbox (Preview pro účet úložiště) poskytuje rozhraní pro zákazníky, kteří budou kontrolovat a schvalovat nebo odmítat žádosti o přístup k datům zákazníků. Microsoft nepožaduje ani nepožaduje přístup k tajným klíčům vaší organizace uloženým v účtu úložiště.

- [Pochopení Customer Lockbox](../../security/fundamentals/customer-lockbox-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které pomáhají při sledování prostředků účtu úložiště, které ukládají nebo zpracovávají citlivé informace. 

- [Vytváření a používání značek](../../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace izolace pomocí samostatného předplatného, skupin pro správu a účtů úložiště pro jednotlivé domény zabezpečení, jako je prostředí, citlivost na data.  Můžete omezit účet úložiště pro řízení úrovně přístupu k účtům úložiště, které vaše aplikace a podniková prostředí vyžadují, na základě typu a podmnožiny používaných sítí. Při konfiguraci síťových pravidel mají přístup k účtu úložiště jenom aplikace požadující data přes zadanou sadu sítí. Přístup k Azure Storage můžete řídit přes Azure RBAC. Můžete také nakonfigurovat privátní koncové body, aby se zlepšily zabezpečení jako provoz mezi vaší virtuální sítí a službou průchod přes páteřní síť Microsoftu, což eliminuje expozici veřejného Internetu. 

- [Vytvoření dalších předplatných Azure](../../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../../azure-resource-manager/management/tag-resources.md)

- [Konfigurace bran firewall Azure Storage a virtuálních sítí](./storage-network-security.md)

- [Koncové body služby Virtual Network](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací.

**Pokyny**: u prostředků účtu úložiště, které ukládají nebo zpracovávají citlivé informace, označte prostředky jako citlivé pomocí značek. Chcete-li snížit riziko ztráty dat prostřednictvím exfiltrace, omezte odchozí síťový provoz pro účty Azure Storage pomocí Azure Firewall. 

Kromě toho můžete pomocí zásad koncového bodu služby virtuální sítě vyfiltrovat odchozí přenosy virtuální sítě na účty Azure Storage prostřednictvím koncového bodu služby a exfiltraceit data pouze na konkrétní Azure Storage účty.

- [Konfigurace bran firewall Azure Storage a virtuálních sítí](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Zásady koncového bodu služby pro virtuální síť pro službu Azure Storage](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Principy ochrany zákaznických dat v Azure](../../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: můžete vynutili použití protokolu HTTPS povolením zabezpečeného přenosu vyžadovaného pro účet úložiště. Po povolení této možnosti budou připojení pomocí protokolu HTTP zamítnuta. Navíc můžete pomocí Azure Security Center a Azure Policy vynutili zabezpečený přenos pro váš účet úložiště.

- [Postup při vyžadování zabezpečeného přenosu v Azure Storage](./storage-require-secure-transfer.md)

- [Zásady zabezpečení Azure monitorované pomocí Security Center](../../security-center/policy-reference.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: funkce pro identifikaci dat ještě nejsou k dispozici pro Azure Storage účet a související prostředky. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů. 

- [Principy ochrany zákaznických dat v Azure](../../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím řízení přístupu na základě role Azure (Azure RBAC). Azure Storage definuje sadu předdefinovaných rolí Azure, které zahrnují společné sady oprávnění používané pro přístup k datům objektů BLOB nebo front. 

- [Přiřazení rolí Azure pro účet Azure Storage](./storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal)

- [Použití poskytovatele prostředků Azure Storage k přístupu k prostředkům správy](./authorization-resource-provider.md)

- [Jak nakonfigurovat přístup k objektům blob Azure a zařadit data do fronty pomocí Azure RBAC v Azure Portal](./storage-auth-aad-rbac-portal.md)

- [Postup vytvoření a konfigurace instance AAD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autorizace přístupu k datům v Azure Storage](./storage-auth.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: Azure Storage šifrování je povolené pro všechny účty úložiště a nedá se zakázat. Azure Storage automaticky šifruje vaše data, když se trvale uloží do cloudu. Při čtení dat ze služby Azure Storage jsou data před vrácením dešifrována službou Azure Storage. Azure Storage šifrování vám umožní zabezpečit data v klidovém prostředí, aniž byste museli upravovat kód nebo přidávat kód do žádné aplikace. 

- [Principy šifrování Azure Storage v klidovém umístění](./storage-service-encryption.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro změny prostředků účtů úložiště, ke kterým dochází. Můžete také povolit protokolování Azure Storage, abyste mohli sledovat, jak byl každý požadavek vytvořený proti Azure Storage autorizovaný. Protokoly označují, zda byl požadavek anonymně vytvořen pomocí tokenu OAuth 2,0, pomocí sdíleného klíče nebo pomocí sdíleného přístupového podpisu (SAS). Kromě toho použijte Azure Monitor k upozornění na anonymní přístup k účtům úložiště pomocí podmínky anonymního ověřování.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../../azure-monitor/platform/alerts-activity-log.md)

- [Protokolování analýz služby Azure Storage](./storage-analytics-logging.md)

- [Jak nakonfigurovat výstrahy metrik pro účty Azure Storage](./storage-monitor-storage-account.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](../../security/benchmarks/security-control-vulnerability-management.md)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: dodržujte doporučení Azure Security Center a průběžně auditujte a sledujte konfiguraci účtů úložiště. 

- [Doporučení k zabezpečení – Referenční příručka](../../security-center/recommendations-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: nasazení automatizovaného řešení pro správu oprav Third Party Software

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: nepoužitelné; Microsoft provádí správu ohrožení zabezpečení pro základní systémy, které podporují účty úložiště.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných chyb zabezpečení.

**Doprovodné** materiály: použijte výchozí hodnocení rizik (zabezpečené skóre) poskytované Azure Security Center. 

- [Pochopení Azure Security Center zabezpečeného skóre](../../security-center/secure-score-security-controls.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: použijte Azure Asset Discovery.

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny prostředky (včetně účtů úložiště) v rámci vašich předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků. 

- [Jak vytvářet dotazy pomocí Azure graphu](../../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription)

- [Pochopení Azure RBAC](../../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat Assetu

**Doprovodné** materiály: použití značek k prostředkům účtu úložiště, které dávají metadata k logickému uspořádání do taxonomie. 

- [Vytváření a používání značek](../../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování účtů úložiště a souvisejících prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného. 

K detekci neautorizovaných prostředků Azure taky použijte rozšířenou ochranu před internetovými útoky pro Azure Storage. 

- [Vytvoření dalších předplatných Azure](../../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../../azure-resource-manager/management/tag-resources.md)

- [Konfigurace rozšířené ochrany před internetovými útoky pro Azure Storage](./azure-defender-storage-configure.md?tabs=azure-portal)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: Udržujte inventarizaci schválených prostředků Azure a softwarových titulů.

**Pokyny**: budete muset vytvořit inventarizaci schválených prostředků Azure podle potřeb vaší organizace. 


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad: 

 - Žádné povolené typy prostředků 
 - Povolené typy prostředků 

Kromě toho můžete pomocí grafu prostředků Azure dotazovat nebo zjišťovat prostředky v rámci předplatných. To může pomáhat v prostředích s vysokým zabezpečením, jako jsou ta s účty úložiště. 

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Pokyny**: zákazník může zabránit vytváření nebo používání prostředků pomocí Azure Policy podle požadavků zásad společnosti zákazníka. 

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad: 

- Žádné povolené typy prostředků 
- Povolené typy prostředků 

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../../governance/policy/samples/index.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="610-implement-approved-application-list"></a>6,10: Implementujte schválený seznam aplikací

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager prostřednictvím skriptů

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management. To může zabránit vytváření a změnám prostředků v prostředí s vysokým zabezpečením, jako jsou ty, které mají účty úložiště. 

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k ARM](../../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. Storage vytvořit vlastní zásady pro auditování nebo prosazování konfigurace instancí účtů úložiště. Můžete také použít předdefinované definice Azure Policy pro Azure Storage účet, například: 

Auditování neomezeného síťového přístupu k účtům úložiště  
Nasazení rozšířené ochrany před internetovými útoky na účty úložiště  
Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.  
Měl by se povolit zabezpečený přenos do účtů úložiště  

Použijte doporučení z Azure Security Center jako standardní hodnoty konfigurace pro vaše účty úložiště. 

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: Vytvoření zabezpečených konfigurací pro váš operační systém

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: Údržba zabezpečených konfigurací pro všechny prostředky Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] k vymáhání zabezpečených nastavení napříč prostředky účtu úložiště. 

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Porozumění efektům Azure Policy](../../governance/policy/concepts/effects.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: udržování zabezpečených konfigurací pro operační systémy

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Používejte Azure Repos pro bezpečné ukládání a správu kódu, jako jsou vlastní zásady Azure, šablony Azure Resource Manager, požadované konfigurační skripty stavu atd. Pokud chcete získat přístup k prostředkům, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps, nebo Active Directory, pokud je integrovaná se sadou TFS.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: nasazení nástrojů pro správu konfigurace systému

**Doprovodné** materiály: využijte Azure Policy k oznamování, auditu a prosazování systémových konfigurací pro účet úložiště. Dále můžete vyvinout proces a kanál pro správu výjimek zásad. 

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: nasaďte nástroje pro správu konfigurace systému pro operační systémy.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Implementujte automatizované monitorování konfigurace pro služby Azure.

**Doprovodné** materiály: využijte Azure Security Center k provádění kontrol standardních hodnot vašich účtů Azure Storage. 

- [Jak opravit doporučení v Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="711-securely-manage-azure-secrets"></a>7,11: zabezpečená Správa tajných klíčů Azure

**Doprovodné** materiály: Azure Storage automaticky šifruje vaše data při jejich trvalém uložení do cloudu. Pro šifrování účtu úložiště můžete použít klíče spravované Microsoftem, nebo můžete spravovat šifrování s vlastními klíči. Pokud používáte klíče poskytované zákazníky, můžete využít Azure Key Vault k bezpečnému uložení klíčů. 

Kromě toho pravidelně otáčejte klíče účtu úložiště, abyste omezili dopad ztráty nebo zveřejnění klíčů účtu úložiště.

- [Šifrování služby Azure Storage pro neaktivní uložená data](./storage-service-encryption.md)

- [Správa přístupových klíčů účtu úložiště](./storage-account-keys-manage.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: bezpečné a automaticky spravované identity

**Pokyny**: pomocí Azure Active Directory a spravovaných identit autorizujte přístup k objektům blob a frontám v rámci účtů Azure Storage. Azure Blob a Queue Storage podporují ověřování Azure Active Directory (Azure AD) se spravovanými identitami pro prostředky Azure. Spravované identity pro prostředky Azure můžou autorizovat přístup k objektům blob a Queue pomocí přihlašovacích údajů Azure AD z aplikací běžících na virtuálních počítačích Azure, aplikací Functions, Virtual Machine Scale Sets a dalších služeb. Pomocí spravovaných identit pro prostředky Azure spolu s ověřováním Azure AD se můžete vyhnout ukládání přihlašovacích údajů k vašim aplikacím, které běží v cloudu. 

- [Jak udělit přístup k objektům blob Azure a zařadit data do fronty pomocí spravované identity](./storage-auth-aad-rbac-portal.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky. Microsoft zpracovává anti-malware pro základní platformu.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Doprovodné** materiály: použití ochrany před hrozbami pro Azure Storage k detekci nahrávání malwaru pro Azure Storage pomocí analýzy reputace hodnoty hash a podezřelého přístupu z aktivního uzlu pro ukončení provozu (proxy server anonymizace). 

Před odesláním do nevýpočetních prostředků Azure, jako jsou App Service, Data Lake Storage, Blob Storage atd. požadavky vaší organizace, můžete také předem vyhledat veškerý obsah pro malware.

- [Konfigurace rozšířené ochrany před internetovými útoky pro Azure Storage](./azure-defender-storage-configure.md?tabs=azure-portal)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky. Microsoft zpracovává anti-malware pro základní platformu.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: data v Microsoft azurem účtu úložiště se vždycky automaticky replikují, aby se zajistila odolnost a vysoká dostupnost. Azure Storage kopíruje vaše data, aby byla chráněná před plánovanými a neplánovanými událostmi, včetně přechodných selhání hardwaru, sítě nebo výpadků výpadků a obrovských přirozených havárií. Můžete se rozhodnout, že budete replikovat data ve stejném datovém centru, napříč datovými centry oblastí v rámci stejné oblasti nebo v geograficky oddělených oblastech. 

Můžete také povolit službě Azure Automation provádět běžné snímky objektů BLOB.

- [Porozumění Azure Storage redundanci a Service-Level smluv](./storage-redundancy.md)

- [Vytvoření snímku objektu BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Přehled Azure Automation](../../automation/automation-intro.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

**Doprovodné** materiály: aby bylo možné zálohovat data z podporovaných služeb účtu úložiště, je k dispozici více metod, včetně použití AzCopy nebo nástrojů třetích stran. Neměnné úložiště pro úložiště objektů BLOB v Azure umožňuje uživatelům ukládat datové objekty kritické pro podnikání do ČERVa (psát jednou, číst mnoho). Tento stav zpřístupňuje data, která nejsou Erasable a není upravitelná pro interval zadaný uživatelem.

- [Začínáme s nástrojem AzCopy](./storage-use-azcopy-v10.md)

- [Nastavení a Správa zásad neměnnosti pro úložiště objektů BLOB](../blobs/storage-blob-immutability-policies-manage.md?tabs=azure-portal)

Pomocí Azure CLI nebo PowerShellu se dají zálohovat klíče spravované zákazníkem a poskytnuté klíče v rámci služby Azure Key Vault. 

- [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

**Doprovodné** materiály: pravidelně provádějte obnovení dat Key Vault certifikátů, klíčů, spravovaných účtů úložiště a tajných kódů s následujícími příkazy PowerShellu: 

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret 

- [Postup obnovení certifikátů Key Vault](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

- [Postup obnovení klíčů Key Vault](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

- [Postup obnovení Key Vault spravovaných účtů úložiště](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Postup obnovení tajných kódů Key Vault](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret)

- [AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování objektů blob, souborů a tabulkových dat do nebo z účtu úložiště.](./storage-use-azcopy-v10.md)

Poznámka: Pokud chcete kopírovat data do služby Azure Table Storage a z ní, nainstalujte AzCopy verze 7,3.


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

**Pokyny**: Pokud chcete povolit klíče spravované zákazníkem v účtu úložiště, musíte použít Azure Key Vault k uložení klíčů. V trezoru klíčů musíte povolit jak obnovitelné odstranění, tak i Nemazat vlastnosti. Funkce obnovitelného odstranění Key Vault umožňuje obnovení odstraněných trezorů a objektů trezoru, jako jsou klíče, tajné klíče a certifikáty. Pokud dojde k zálohování dat účtu úložiště k Azure Storage objektů blob, povolte obnovitelné odstranění a uložte a obnovte data, když se odstraní objekty blob nebo snímky objektů BLOB. Zálohy byste měli považovat za citlivá data a v rámci tohoto směrného plánu použít příslušné ovládací prvky pro přístup a ochranu dat. Pro lepší ochranu můžete kromě toho uložit stav důležitých podnikových dat do ČERVa (jeden, co je třeba napsat jednou, číst mnoho).

- [Použití obnovitelného odstranění Azure Key Vault](../../key-vault/general/key-vault-recovery.md)

- [Obnovitelné odstranění objektů blob služby Azure Storage](../blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Ukládání důležitých podnikových dat objektů blob s využitím neměnného úložiště](../blobs/storage-blob-immutable-storage.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení.

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění. 

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka pomocí značek a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure, zejména těch, která zpracovávají citlivá data. Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](../../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: vyexportujte výstrahy a doporučení pro Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" pro výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../../security-center/workflow-automation.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11,1: Provádějte pravidelné testování průniku vašich prostředků Azure.

**Doprovodné** materiály: řiďte se pravidly společnosti Microsoft o zapojení, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft.

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../../security/benchmarks/overview.md)
- Další informace o [plánech zabezpečení Azure](../../security/benchmarks/security-baselines-overview.md)