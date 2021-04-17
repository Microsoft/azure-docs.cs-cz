---
title: Základní hodnoty zabezpečení Azure pro Azure Storage
description: Základní Azure Storage zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 28d5bdc788e3b292545fd4c016fae36149f3a600
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589444"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Základní hodnoty zabezpečení Azure pro Azure Storage

Tato základní hodnota zabezpečení platí pro Azure Storage pokynů od [zabezpečení Azure Security test 1,0](../../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Azure Storage. **Ovládací prvky** , které se nevztahují k Azure Storage byly vyloučeny.

 
Pokud chcete zjistit, jak Azure Storage kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Azure Storage Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: Nakonfigurujte bránu firewall účtu úložiště tak, že omezíte přístup na klienty z konkrétních rozsahů veřejných IP adres, vyberete virtuální sítě nebo konkrétní prostředky Azure.  Můžete také nakonfigurovat privátní koncové body, aby provoz do služby úložiště z vašeho podniku prochází výhradně v privátních sítích.

Poznámka: klasické účty úložiště nepodporují brány firewall a virtuální sítě.

- [Jak nakonfigurovat bránu Azure Storage firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Postup konfigurace privátních koncových bodů pro Azure Storage](storage-private-endpoints.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné** materiály: Azure Storage poskytuje vrstvený model zabezpečení. Přístup k účtu úložiště můžete omezit na požadavky pocházející ze zadaných IP adres, rozsahů IP adres nebo ze seznamu podsítí v Azure Virtual Network. K zabezpečení síťových prostředků v Azure můžete použít Azure Security Center a postupovat podle doporučení ochrany sítě. Také Povolte protokol toků skupin zabezpečení sítě pro virtuální sítě nebo podsíť nakonfigurovanou pro účty úložiště přes bránu firewall účtu úložiště a odešlete protokoly do účtu úložiště pro audit provozu. 

 
Všimněte si, že pokud máte k účtu úložiště připojené privátní koncové body, nemůžete pro podsítě konfigurovat pravidla skupiny zabezpečení sítě. 
 

 
- [Konfigurace bran firewall Azure Storage a virtuálních sítí](storage-network-security.md) 
 

 
- [Jak povolit protokoly toku NSG](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 
 

 
- [Principy zabezpečení sítě, které poskytuje Azure Security Center](../../security-center/security-center-network-recommendations.md) 
 

 
- [Principy privátních koncových bodů pro Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: nepoužitelné; doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: povolení služby Azure Defender pro úložiště pro váš účet Azure Storage. Azure Defender for Storage nabízí další vrstvu informací o zabezpečení, která zjišťuje neobvyklé a případně i škodlivé pokusy o přístup nebo využití účtů úložiště.  Azure Security Center integrované výstrahy jsou založené na aktivitách, pro které byla síťová komunikace přidružena k IP adrese, která byla úspěšně vyřešena, bez ohledu na to, zda je IP adresa známou nebo zda se jedná o známou IP adresu (například známý cryptominer) nebo IP adresu, která nebyla dříve rozpoznána jako riziková. Výstrahy zabezpečení se spouštějí při výskytu anomálií v aktivitě. 

- [Konfigurace Azure Defenderu pro úložiště](azure-defender-storage-configure.md) 

- [Pochopení Azure Security Center integrované analýzy hrozeb](../../security-center/azure-defender.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Pokyny**: Network Watcher Capture paketů umožňuje vytvořit relace zachycení pro sledování provozu mezi účtem úložiště a virtuálním počítačem. K dispozici jsou filtry pro relaci zachycení, aby bylo možné zachytit pouze požadovaný provoz. Zachytávání paketů pomáhá diagnostikovat anomálie sítě, jak proaktivní, tak i proaktivní. Mezi další použití patří shromažďování statistik sítě, získávání informací o neoprávněných vniknutích k síti, ladění komunikace mezi klientem a serverem a mnoho dalšího. Schopnost vzdáleně aktivovat zachycení paketů, usnadňuje zatížení ručního zachycení paketů na požadovaném virtuálním počítači, který šetří cenné časy. 

- [ Správa zachytávání paketů pomocí Azure Network Watcher pomocí portálu](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny**: Azure Defender pro úložiště poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům úložiště nebo jejich zneužití. Výstrahy zabezpečení se spouštějí při výskytu anomálií v aktivitě. Tyto výstrahy zabezpečení jsou integrovány do centra Azure Security Center a jsou také odesílány e-mailem správcům předplatného s podrobnostmi o podezřelé aktivitě a doporučeními, jak hrozby prozkoumat a opravit. 

- [Konfigurace Azure Defenderu pro úložiště](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: nepoužitelné; doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: u prostředků ve virtuálních sítích, které potřebují přístup k vašemu účtu úložiště, použijte značky Virtual Network služby pro nakonfigurovanou Virtual Network k definování řízení přístupu k síti u skupin zabezpečení sítě nebo na Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (například úložiště) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo zamítnout přenos pro odpovídající službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny. 

Pokud je třeba přístup k síti určit na konkrétní účty úložiště, použijte Virtual Network zásady koncového bodu služby.

- [Další informace o použití značek služeb](../../virtual-network/service-tags-overview.md)

- [Další informace o zásadách koncového bodu služby virtuální sítě pro Azure Storage](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky přidružené k vašemu Azure Storage účtu pomocí Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. Storage a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě vašich prostředků účtu úložiště. 

Můžete také využít integrované definice zásad související s účtem úložiště, například: účty úložiště by měly používat koncový bod služby virtuální sítě. 

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md) 

- [Ukázky Azure Policy pro úložiště](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage) 

- [Ukázky Azure Policy pro síť](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network) 

- [Vytvoření Azure Blueprint](../../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro skupiny zabezpečení sítě a další prostředky, které se týkají zabezpečení sítě a toku provozu. Označování vám umožní přidružit předdefinované a vlastní páry název-hodnota k danému síťovému prostředku, což vám pomůže organizovat síťové prostředky a propojit prostředky Azure zpátky s návrhem sítě.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky. 

Skupiny zabezpečení sítě podporují značky, ale jednotlivá pravidla zabezpečení ne. Pravidla zabezpečení mají pole **popisu** , které můžete použít k uložení některých informací, které byste obvykle umístili do tagu.

- [Vytvoření a použití značek prostředků](../../azure-resource-manager/management/tag-resources.md)

- [Postup filtrování síťového provozu pomocí skupiny zabezpečení sítě](../../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: k protokolování změn konfigurace síťových prostředků použijte Azure Policy.  Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.  

 
- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)
 
 
- [Vytváření výstrah v Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: ingestování protokolů prostřednictvím Azure monitor k agregaci dat zabezpečení generovaných zařízeními koncových bodů, síťovými prostředky a dalšími systémy zabezpečení. V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště, a to v případě funkcí zabezpečení, jako je například neměnné úložiště a zajištěné uchování.

 
- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: analýza úložiště Azure poskytuje protokoly objektů blob, front a tabulek. Pomocí Azure Portal můžete nakonfigurovat, které protokoly se budou zaznamenávat pro váš účet.   

 
- [Jak nakonfigurovat monitorování pro účet Azure Storage](manage-storage-analytics-logs.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: při ukládání protokolů událostí zabezpečení do účtu Azure Storage nebo v pracovním prostoru Log Analytics můžete zásady uchovávání informací nastavit podle požadavků vaší organizace.  

 
- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

 
- [Změnit dobu uchovávání dat v Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: Chcete-li zkontrolovat protokoly Azure Storage, jsou k dispozici běžné možnosti, jako jsou například dotazy prostřednictvím nabídky Log Analytics a také jedinečná možnost zobrazení souborů protokolu přímo. V Azure Storage se protokoly ukládají v objektech blob, ke kterým se musí `http://accountname.blob.core.windows.net/$logs` přihlašovat přímo v (ve výchozím nastavení je složka protokolování skrytá, takže budete muset přejít přímo. V příkazech seznamu se nezobrazí. 

 
Také pro svůj účet úložiště povolte Azure Defender pro úložiště. Azure Defender for Storage nabízí další vrstvu informací o zabezpečení, která zjišťuje neobvyklé a případně i škodlivé pokusy o přístup nebo využití účtů úložiště. Výstrahy zabezpečení se spouštějí při výskytu anomálií v aktivitě. Tyto výstrahy zabezpečení jsou integrovány do centra Azure Security Center a jsou také odesílány e-mailem správcům předplatného s podrobnostmi o podezřelé aktivitě a doporučeními, jak hrozby prozkoumat a opravit. 
 

 
- [Protokolovat a kontrolovat data](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored) 
 

 
- [Konfigurace Azure Defenderu pro úložiště](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: v Azure Security Center povolte účet úložiště v Azure Defenderu. Povolte nastavení diagnostiky pro účet úložiště a odešlete protokoly do pracovního prostoru Log Analytics. Připojte pracovní prostor Log Analytics do Azure Sentinel, protože poskytuje řešení pro automatizované odpovědi na orchestraci zabezpečení (společnosti). To umožňuje vytvořit playbooky (automatizovaná řešení) a použít je k nápravě problémů zabezpečení.  

 
- [Jak připojit Azure Sentinel](../../sentinel/quickstart-onboard.md)  

 
- [Správa výstrah v Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)  

 
- [Upozornění na data protokolu Log Analytics](../../azure-monitor/alerts/tutorial-response.md)  

 
- [Protokolování analýz služby Azure Storage](storage-analytics-logging.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Pokyny**: použijte Azure Security Center a umožněte službě Azure Defender pro úložiště zjišťovat nahrávání malwaru, aby se Azure Storage pomocí analýzy reputace hodnot hash a podezřelého přístupu z aktivního uzlu pro ukončení práce (proxy server anonymizace). 

- [Konfigurace Azure Defenderu pro úložiště](azure-defender-storage-configure.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné** materiály: řešení Azure DNS Analytics (Preview) v Azure monitor shromažďuje přehledy o infrastruktuře DNS v oblasti zabezpečení, výkonu a provozu.  V současné době nepodporuje Azure Storage účty, ale můžete použít řešení protokolování DNS třetích stran.  

 
- [Získejte přehled o vaší infrastruktuře DNS pomocí řešení DNS Analytics Preview.](../../azure-monitor/insights/dns-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Azure Active Directory (Azure AD) mají předdefinované role, které se musí explicitně přiřadit a které jsou Queryable. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné** materiály: účty Azure Storage ani Azure Active Directory (Azure AD) mají koncept výchozích nebo prázdných hesel. Azure Storage implementuje model řízení přístupu, který podporuje řízení přístupu na základě role Azure (Azure RBAC), stejně jako sdílené klíče a signatury sdíleného přístupu (SAS). Charakteristika sdíleného klíče a ověřování SAS je, že k volajícímu není přidružena žádná identita, a proto nelze provést autorizaci na základě oprávnění zabezpečení.

- [Autorizace přístupu k datům v Azure Storage](storage-auth.md)

- [Porozumění objektům zabezpečení a řízení přístupu pro účet Azure Storage](storage-introduction.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu, které mají přístup k vašemu účtu úložiště. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Pomocí Azure Active Directory (Azure AD) Privileged Identity Management privilegovaných rolí pro služby Microsoftu a Azure Resource Manager můžete taky povolit přístup za běhu nebo jen tolik.

- [Pochopení Azure Security Center identity a přístupu](../../security-center/security-center-identity-access.md)

- [Přehled Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné** materiály: kdykoli je to možné, použijte službu Azure Active Directory (Azure AD) SSO místo konfigurace jednotlivých samostatných přihlašovacích údajů pro každou službu. Použijte Azure Security Center doporučení pro správu identit a přístupu.
 

 
- [Princip jednotného přihlašování s Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)
 

 
- [Autorizace přístupu k datům v Azure Storage](storage-auth.md)
 

 
- [Autorizace přístupu k objektům blob a frontám pomocí Azure AD](storage-auth-aad.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a postupujte podle Azure Security Center doporučení pro správu identit a přístupu, která vám pomůžou ochránit prostředky účtu úložiště.

- [Postup povolení vícefaktorového ověřování v Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Doprovodné** materiály: Používejte privilegovaným přístupem (Privileged Access Workstations) s nakonfigurovaným vícefaktorového ověřováním, které je nakonfigurované pro přihlášení a konfiguraci prostředků účtu úložiště.  

 
- [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

 
- [Postup povolení vícefaktorového ověřování v Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: odeslání výstrah detekce rizik Azure Security Center do Azure monitor a konfigurace vlastního upozorňování/oznámení pomocí skupin akcí. Povolte Azure Defender pro účet úložiště a vygenerujte výstrahy pro podezřelou aktivitu. Kromě toho můžete pomocí Azure Active Directory (Azure AD) zjišťovat rizika a zobrazovat výstrahy a sestavy týkající se rizikového chování uživatelů.

- [Konfigurace Azure Defenderu pro úložiště](azure-defender-storage-configure.md)
 

- [Vysvětlení zjišťování rizik Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)
 

- [Jak nakonfigurovat skupiny akcí pro vlastní výstrahy a oznámení](../../azure-monitor/alerts/action-groups.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí. 

- [Postup konfigurace pojmenovaných umístění v Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure poskytuje řízení přístupu na základě role Azure (Azure RBAC) pro jemně odstupňovanou kontrolu nad přístupem klienta k prostředkům v účtu úložiště.   Použijte přihlašovací údaje Azure AD, pokud je to možné, místo použití klíče účtu, který může být snáze ohrožen. Když návrh aplikace vyžaduje pro přístup k úložišti objektů BLOB sdílené přístupové podpisy, pomocí přihlašovacích údajů Azure AD vytvořte delegovaný přístup uživatelů (SAS), pokud je to možné, pro zajištění vysokého zabezpečení.

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Použití poskytovatele prostředků Azure Storage k přístupu k prostředkům správy](authorization-resource-provider.md)

- [Jak nakonfigurovat přístup k objektům blob Azure a zařadit data do fronty pomocí Azure RBAC v Azure Portal](storage-auth-aad-rbac-portal.md)

- [Autorizace přístupu k datům v Azure Storage](storage-auth.md)

- [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Přečtěte si protokoly Azure Active Directory (Azure AD), které vám pomůžou zjistit zastaralé účty, které můžou obsahovat role pro správu účtu úložiště. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím, které se dají použít pro přístup k prostředkům účtu úložiště a přiřazování rolí. Přístup uživatelů by měl být pravidelně přezkoumán, aby se zajistilo, že pouze přípravní uživatelé budou mít přístup i nadále. 
 

 
Pomocí sdíleného přístupového podpisu (SAS) taky můžete zajistit zabezpečený delegovaný přístup k prostředkům ve vašem účtu úložiště, aniž byste tak ohrozili zabezpečení vašich dat. Můžete řídit, k jakým prostředkům má klient přístup, jaká oprávnění jsou na těchto prostředcích a jak dlouho je SAS platné, mezi další parametry.
 

 
Přečtěte si také anonymní přístup pro čtení kontejnerů a objektů BLOB. Ve výchozím nastavení může ke kontejneru a ke všem v něm obsaženým objektům blob získat přístup jen uživatel, kterému byla udělena příslušná oprávnění. Azure Monitor můžete použít k upozornění na anonymní přístup k účtům úložiště pomocí podmínky anonymního ověřování.
 

 
Jedním z efektivních způsobů, jak snížit riziko přístupu k nepodezřelému uživatelskému účtu, je omezit dobu trvání přístupu, který uživatelům udělíte. Časově omezené identifikátory URI SAS představují jeden efektivní způsob, jak automaticky vypršet přístup uživatelů k účtu úložiště. Časté střídání klíčů účtu úložiště je navíc způsob, jak zajistit, že neočekávaný přístup prostřednictvím klíčů účtu úložiště je omezené trvání.
 

 
- [Pochopení sestav Azure AD](/azure/active-directory/reports-monitoring/) 
 

 
- [Postup zobrazení a změny přístupu na úrovni účtu Azure Storage](storage-auth-aad-rbac-portal.md)
 

 
- [Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md)
 

 
- [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../blobs/anonymous-read-access-configure.md)
 

 
- [Monitorování účtu úložiště na webu Azure Portal](manage-storage-analytics-logs.md)
 

 
- [Správa přístupových klíčů účtu úložiště](storage-account-keys-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Pokyny**: pomocí analýza úložiště Zaprotokolujte podrobné informace o úspěšných a neúspěšných požadavcích na službu úložiště. Všechny protokoly se ukládají v objektech blob bloku v kontejneru s názvem $logs, které se automaticky vytvoří, když je Analýza úložiště povolený pro účet úložiště.
 

Vytvořte nastavení diagnostiky pro uživatelské účty Azure Active Directory (Azure AD), odešlete protokoly auditu a protokoly přihlášení do pracovního prostoru Log Analytics. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics.

Pokud chcete monitorovat selhání ověřování u účtů Azure Storage, můžete vytvořit upozornění, která vás upozorní, když se dosáhne určitých prahových hodnot pro metriky prostředků úložiště. Kromě toho použijte Azure Monitor k upozornění na anonymní přístup k účtům úložiště pomocí podmínky anonymního ověřování.

- [Protokolování analýz služby Azure Storage](storage-analytics-logging.md)
 

- [Integrace protokolů aktivit Azure do služby Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)
 

- [Jak nakonfigurovat výstrahy metrik pro účty Azure Storage](manage-storage-analytics-logs.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Doprovodné** materiály: použití funkcí pro rizika a ochranu Identity služby Azure Active Directory (Azure AD) ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce související s prostředky vašeho účtu úložiště. Pomocí služby Azure Sentinel byste měli povolit automatizované odezvy pro implementaci reakcí zabezpečení vaší organizace.

- [Zobrazení rizikových přihlášení Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: ve scénářích podpory, kde Microsoft potřebuje získat přístup k zákaznickým datům, Customer Lockbox (Preview pro účet úložiště) poskytuje rozhraní pro zákazníky, kteří budou kontrolovat a schvalovat nebo odmítat žádosti o přístup k datům zákazníků. Microsoft nepožaduje ani nepožaduje přístup k tajným klíčům vaší organizace uloženým v účtu úložiště.

- [Pochopení Customer Lockbox](../../security/fundamentals/customer-lockbox-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které pomáhají při sledování prostředků účtu úložiště, které ukládají nebo zpracovávají citlivé informace. 

- [Vytváření a používání značek](../../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace izolace pomocí samostatného předplatného, skupin pro správu a účtů úložiště pro jednotlivé domény zabezpečení, jako je prostředí a citlivost na data.  Můžete omezit účet úložiště pro řízení úrovně přístupu k účtům úložiště, které vaše aplikace a podniková prostředí vyžadují, na základě typu a podmnožiny používaných sítí. Při konfiguraci síťových pravidel mají přístup k účtu úložiště jenom aplikace požadující data přes zadanou sadu sítí. Přístup k Azure Storage můžete řídit přes Azure RBAC (Azure RBAC).

Můžete také nakonfigurovat privátní koncové body, aby se zlepšily zabezpečení jako provoz mezi vaší virtuální sítí a službou průchod přes páteřní síť Microsoftu, což eliminuje expozici veřejného Internetu.

- [Vytvoření dalších předplatných Azure](../../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../../azure-resource-manager/management/tag-resources.md)

- [Konfigurace bran firewall Azure Storage a virtuálních sítí](storage-network-security.md)

- [Koncové body služby pro virtuální síť](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Pokyny**: u prostředků účtu úložiště, které ukládají nebo zpracovávají citlivé informace, označte prostředky jako citlivé pomocí značek. Chcete-li snížit riziko ztráty dat prostřednictvím exfiltrace, omezte odchozí síťový provoz pro účty Azure Storage pomocí Azure Firewall.  

Kromě toho můžete pomocí zásad koncového bodu služby virtuální sítě vyfiltrovat odchozí přenosy virtuální sítě na účty Azure Storage prostřednictvím koncového bodu služby a exfiltraceit data pouze na konkrétní Azure Storage účty.

- [Konfigurace bran firewall Azure Storage a virtuálních sítí](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Zásady koncového bodu služby pro virtuální síť pro službu Azure Storage](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Principy ochrany zákaznických dat v Azure](../../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: můžete vynutili použití protokolu HTTPS povolením zabezpečeného přenosu vyžadovaného pro účet úložiště. Po povolení této možnosti budou připojení pomocí protokolu HTTP zamítnuta.  Navíc můžete pomocí Azure Security Center a Azure Policy vynutili zabezpečený přenos pro váš účet úložiště.

- [Postup při vyžadování zabezpečeného přenosu v Azure Storage](storage-require-secure-transfer.md)

- [Zásady zabezpečení Azure monitorované pomocí Security Center](../../security-center/policy-reference.md)

**Odpovědnost:** Sdílené

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 4.4](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: funkce pro identifikaci dat ještě nejsou k dispozici pro Azure Storage účet a související prostředky. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů. 

- [Principy ochrany zákaznických dat v Azure](../../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte řízení přístupu na základě role

**Pokyny**: Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím řízení přístupu na základě role Azure (Azure RBAC). Azure Storage definuje sadu integrovaných rolí RBAC v Azure, které zahrnují společné sady oprávnění používané pro přístup k datům BLOB nebo Queue.

- [Přiřazení rolí Azure RBAC pro účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [Použití poskytovatele prostředků Azure Storage k přístupu k prostředkům správy](authorization-resource-provider.md)

- [Jak nakonfigurovat přístup k objektům blob Azure a zařadit data do fronty pomocí Azure RBAC v Azure Portal](storage-auth-aad-rbac-portal.md)

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autorizace přístupu k datům v Azure Storage](storage-auth.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: Azure Storage šifrování je povolené pro všechny účty úložiště a nedá se zakázat. Azure Storage automaticky šifruje vaše data, když se trvale uloží do cloudu. Při čtení dat ze služby Azure Storage jsou data před vrácením dešifrována službou Azure Storage. Azure Storage šifrování vám umožní zabezpečit data v klidovém prostředí, aniž byste museli upravovat kód nebo přidávat kód do žádné aplikace. 

- [Principy šifrování Azure Storage v klidovém umístění](storage-service-encryption.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro změny prostředků účtů úložiště, ke kterým dochází.  Můžete také povolit protokolování Azure Storage, abyste mohli sledovat, jak byl každý požadavek vytvořený proti Azure Storage autorizovaný. Protokoly označují, zda byl požadavek anonymně vytvořen pomocí tokenu OAuth 2,0, pomocí sdíleného klíče nebo pomocí sdíleného přístupového podpisu (SAS).  Kromě toho použijte Azure Monitor k upozornění na anonymní přístup k účtům úložiště pomocí podmínky anonymního ověřování. 

 
- [Vytvoření upozornění pro události protokolu aktivit Azure](../../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Protokolování analýz služby Azure Storage](storage-analytics-logging.md) 

 
- [Jak nakonfigurovat výstrahy metrik pro účty Azure Storage](manage-storage-analytics-logs.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: dodržujte doporučení Azure Security Center a průběžně auditujte a sledujte konfiguraci účtů úložiště.  

- [Doporučení k zabezpečení – Referenční příručka](../../security-center/recommendations-reference.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: nepoužitelné; Microsoft provádí správu ohrožení zabezpečení pro základní systémy, které podporují účty úložiště.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: použijte výchozí hodnocení rizik (zabezpečené skóre) poskytované Azure Security Center. 

- [Pochopení Azure Security Center zabezpečeného skóre](/azure/security-center/security-center-secure-score)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny prostředky (včetně účtů úložiště) v rámci vašich předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

- [Jak vytvářet dotazy pomocí Azure graphu](../../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription)

- [Pochopení Azure RBAC](../../role-based-access-control/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek k prostředkům účtu úložiště, které dávají metadata k logickému uspořádání do taxonomie. 

- [Vytváření a používání značek](../../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování účtů úložiště a souvisejících prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného. 

K detekci neautorizovaných prostředků Azure taky použijte Azure Defender pro úložiště. 

- [Vytvoření dalších předplatných Azure](../../cost-management-billing/manage/create-subscription.md) 

- [Postup vytvoření Skupiny pro správu](../../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../../azure-resource-manager/management/tag-resources.md)

- [Konfigurace Azure Defenderu pro úložiště](azure-defender-storage-configure.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Pokyny**: budete muset vytvořit inventarizaci schválených prostředků Azure podle potřeb vaší organizace.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad: 

 
 - Žádné povolené typy prostředků 
 
 - Povolené typy prostředků 
 

 
Kromě toho se pomocí grafu prostředků Azure Dotazujte na prostředky v rámci předplatných a vyhledejte je. To může pomáhat v prostředích s vysokým zabezpečením, jako jsou ty s účty úložiště. 
 

 
- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md) 
 

 
- [Jak vytvářet dotazy pomocí Azure graphu](../../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Pokyny**: zákazník může zabránit vytváření nebo používání prostředků pomocí Azure Policy podle požadavků zásad společnosti zákazníka. 

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

Další informace jsou k dispozici na odkazovaných odkazech.

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. ClassicStorage**:

[!INCLUDE [Resource Policy for Microsoft.ClassicStorage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.classicstorage-6-9.md)]

**Azure Policy předdefinované definice – Microsoft. Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-6-9.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management. To může zabránit vytváření a změnám prostředků v prostředí s vysokým zabezpečením, jako jsou ty, které mají účty úložiště. 

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: použijte aliasy Azure Policy v oboru názvů **Microsoft. Storage** k vytvoření vlastních zásad pro auditování nebo prosazování konfigurace instancí účtu úložiště. Můžete také použít předdefinované definice Azure Policy pro Azure Storage účet, například:

- Auditování neomezeného síťového přístupu k účtům úložiště
- Nasazení Azure Defenderu pro úložiště
- Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.
- Měl by se povolit zabezpečený přenos do účtů úložiště

Použijte doporučení z Azure Security Center jako standardní hodnoty konfigurace pro vaše účty úložiště.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] k vymáhání zabezpečených nastavení napříč prostředky účtu úložiště. 

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md) 

- [Porozumění efektům Azure Policy](../../governance/policy/concepts/effects.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Používejte Azure Repos pro bezpečné ukládání a správu kódu, jako jsou vlastní zásady Azure, šablony Azure Resource Manager, požadované konfigurační skripty stavu atd. Pokud chcete získat přístup k prostředkům, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps nebo Azure AD, pokud jsou integrované s TFS.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: využijte Azure Policy k oznamování, auditu a prosazování systémových konfigurací pro účet úložiště. Dále můžete vyvinout proces a kanál pro správu výjimek zásad. 

- [ Konfigurace a Správa Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: využijte Azure Security Center k provádění kontrol standardních hodnot vašich účtů Azure Storage. 

- [Jak opravit doporučení v Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: Azure Storage automaticky šifruje vaše data při jejich trvalém uložení do cloudu. Pro šifrování účtu úložiště můžete použít klíče spravované Microsoftem, nebo můžete spravovat šifrování s vlastními klíči. Pokud používáte klíče poskytované zákazníky, můžete využít Azure Key Vault k bezpečnému uložení klíčů. 

Kromě toho pravidelně otáčejte klíče účtu úložiště, abyste omezili dopad ztráty nebo zveřejnění klíčů účtu úložiště.

- [Šifrování služby Azure Storage pro neaktivní uložená data](storage-service-encryption.md)

- [Správa přístupových klíčů účtu úložiště](storage-account-keys-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Pokyny**: autorizovat přístup k objektům blob a frontám v rámci účtů Azure Storage pomocí Azure Active Directory (Azure AD) a spravovaných identit. Azure Blob a Queue Storage podporují ověřování Azure AD se spravovanými identitami pro prostředky Azure. 

Spravované identity pro prostředky Azure můžou autorizovat přístup k datům objektů BLOB a Queue pomocí přihlašovacích údajů Azure AD z aplikací běžících ve službě Azure Virtual Machines r, aplikace Functions, sady škálování virtuálních počítačů a dalších služeb. Pomocí spravovaných identit pro prostředky Azure spolu s ověřováním Azure AD se můžete vyhnout ukládání přihlašovacích údajů k vašim aplikacím, které běží v cloudu.

- [Jak udělit přístup k objektům blob Azure a zařadit data do fronty pomocí spravované identity](storage-auth-aad-rbac-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

- [ Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Doprovodné** materiály: použijte Azure Defender pro úložiště k detekci nahrávání malwaru, aby se Azure Storage pomocí analýzy reputace hodnoty hash a podezřelého přístupu z aktivního uzlu pro ukončení provozu (proxy server anonymizace). 
 

 
Před odesláním do nevýpočetních prostředků Azure, jako jsou App Service, Data Lake Storage, Blob Storage a další pro splnění požadavků vaší organizace, můžete také předem vyhledat obsah pro malware.
 

 
- [Konfigurace Azure Defenderu pro úložiště](azure-defender-storage-configure.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: data v Microsoft azurem účtu úložiště se vždycky automaticky replikují, aby se zajistila odolnost a vysoká dostupnost. Azure Storage kopíruje vaše data, aby byla chráněná před plánovanými a neplánovanými událostmi, včetně přechodných selhání hardwaru, sítě nebo výpadků výpadků a obrovských přirozených havárií. Můžete se rozhodnout, že budete replikovat data ve stejném datovém centru, napříč datovými centry oblastí v rámci stejné oblasti nebo v geograficky oddělených oblastech. 

Můžete také povolit službě Azure Automation provádět běžné snímky objektů BLOB.

- [Porozumění Azure Storage redundanci a Service-Level smluv](storage-redundancy.md)

- [Vytvoření snímku objektu BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Přehled Azure Automation](../../automation/automation-intro.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné** materiály: aby bylo možné zálohovat data z podporovaných služeb účtu úložiště, je k dispozici více metod, včetně použití AzCopy nebo nástrojů třetích stran. Neměnné úložiště pro úložiště objektů BLOB v Azure umožňuje uživatelům ukládat datové objekty kritické pro podnikání do ČERVa (psát jednou, číst mnoho). Tento stav zpřístupňuje data, která nejsou Erasable a není upravitelná pro interval zadaný uživatelem.
 

Klíče spravované zákazníkem a poskytnuté klíče je možné zálohovat v rámci služby Azure Key Vault pomocí rozhraní příkazového řádku Azure CLI nebo PowerShellu. 

 
- [Začínáme s nástrojem AzCopy](storage-use-azcopy-v10.md)  

- [Nastavení a Správa zásad neměnnosti pro úložiště objektů BLOB](../blobs/storage-blob-immutability-policies-manage.md)
 

 
- [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: pravidelně provádějte obnovení dat Key Vault certifikátů, klíčů, spravovaných účtů úložiště a tajných kódů s následujícími příkazy PowerShellu:

Restore-AzKeyVaultCertificate

Restore-AzKeyVaultKey

Restore-AzKeyVaultManagedStorageAccount

Restore-AzKeyVaultSecret

- [Postup obnovení certifikátů Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Postup obnovení klíčů Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Postup obnovení Key Vault spravovaných účtů úložiště](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Postup obnovení tajných kódů Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování objektů blob, souborů a tabulkových dat do nebo z účtu úložiště.](storage-use-azcopy-v10.md)

Poznámka: Pokud chcete kopírovat data do služby Azure Table Storage a z ní, nainstalujte AzCopy verze 7,3.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: Pokud chcete povolit klíče spravované zákazníkem v účtu úložiště, musíte použít Azure Key Vault k uložení klíčů. V trezoru klíčů musíte povolit jak obnovitelné odstranění, tak i Nemazat vlastnosti.  Funkce obnovitelného odstranění Key Vault umožňuje obnovení odstraněných trezorů a objektů trezoru, jako jsou klíče, tajné klíče a certifikáty.  Pokud dojde k zálohování dat účtu úložiště k Azure Storage objektů blob, povolte obnovitelné odstranění a uložte a obnovte data, když se odstraní objekty blob nebo snímky objektů BLOB.  Zálohy byste měli považovat za citlivá data a v rámci tohoto směrného plánu použít příslušné ovládací prvky pro přístup a ochranu dat.  Pro lepší ochranu můžete kromě toho uložit stav důležitých podnikových dat do ČERVa (jeden, co je třeba napsat jednou, číst mnoho).

- [Použití obnovitelného odstranění Azure Key Vault](../../key-vault/general/key-vault-recovery.md)

- [Obnovitelné odstranění objektů blob služby Azure Storage](../blobs/soft-delete-blob-overview.md)

- [Ukládání důležitých podnikových dat objektů blob s využitím neměnného úložiště](../blobs/storage-blob-immutable-storage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení.

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Průvodce zpracováním incidentů zabezpečení počítače s NIST](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění. 

 
Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka pomocí značek a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure, zejména těch, která zpracovávají citlivá data.  Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.
 

 
- [Výstrahy zabezpečení ve službě Azure Security Center](../../security-center/security-center-alerts-overview.md)
 

 
- [Používání značek k uspořádání prostředků Azure](../../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: vyexportujte výstrahy a doporučení pro Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" pro výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure.
    

- [ Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../../security-center/workflow-automation.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: řiďte se pravidly společnosti Microsoft o zapojení, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft.

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
