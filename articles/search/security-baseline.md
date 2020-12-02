---
title: Základní hodnoty zabezpečení Azure pro Azure Kognitivní hledání
description: Základní hodnoty zabezpečení služby Azure Kognitivní hledání poskytují pokyny a prostředky pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 07ef3da9f67de92c3526b83222c1c8088660d7c7
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499862"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Základní hodnoty zabezpečení Azure pro Azure Kognitivní hledání

Tyto základní hodnoty zabezpečení se týkají pokynů z [Azure Security test 1,0](../security/benchmarks/overview.md) na Azure kognitivní hledání. Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah se seskupuje podle **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů vztahujících se na Azure kognitivní hledání. **Ovládací prvky** , které se nevztahují k Azure kognitivní hledání, nebo zákazníka jsou vyloučené.

Pokud chcete zjistit, jak se Azure Kognitivní hledání zcela mapuje na bezpečnostní srovnávací testy zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení azure kognitivní hledání](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: Ujistěte se, že všechna nasazení Microsoft Azure Virtual Network podsítí mají nastavenou skupinu zabezpečení sítě s pravidly pro implementaci schématu přístupu "s minimálním oprávněním". Povolí přístup pouze k důvěryhodným portům vaší aplikace a rozsahům IP adres. Pokud je to možné, nasaďte Azure Kognitivní hledání s privátním koncovým bodem Azure a umožněte tak privátní přístup k vašim službám z vaší virtuální sítě.

Kognitivní hledání podporuje taky další funkce zabezpečení sítě pro správu seznamů řízení přístupu k síti. Nakonfigurujte vyhledávací službu tak, aby povolovala komunikaci s důvěryhodnými zdroji tím, že omezuje přístup z konkrétních rozsahů veřejných IP adres pomocí své schopnosti brány firewall.

- [Postup konfigurace privátních koncových bodů pro Azure Kognitivní hledání](./service-create-private-endpoint.md)

- [Jak nakonfigurovat bránu firewall pro Azure Kognitivní hledání](./service-configure-firewall.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových karet

**Pokyny**: kognitivní hledání nelze nasadit přímo do virtuální sítě. Pokud je však vaše klientská aplikace nebo zdroje dat ve virtuální síti, můžete monitorovat a Protokolovat provoz pro tyto součásti v síti, včetně požadavků odesílaných službě vyhledávání v cloudu. Mezi standardní doporučení patří povolení protokolu toku skupin zabezpečení sítě a odesílání protokolů do Azure Storage nebo do pracovního prostoru Log Analytics. Volitelně můžete použít Analýza provozu pro přehledy o vzorech provozu.

- [Jak povolit protokoly toku skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: kognitivní hledání neposkytuje konkrétní funkci pro boj proti distribuovaným útokům DOS, ale můžete povolit DDoS Protection Standard ve virtuálních sítích přidružených ke službě kognitivní hledání pro obecnou ochranu.

- [Jak nakonfigurovat DDoS Protection](../ddos-protection/manage-ddos-protection.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Pokyny**: Povolte protokoly toku skupin zabezpečení sítě pro skupiny zabezpečení sítě, které chrání Azure Virtual Machines (VM), které se budou připojovat ke službě kognitivní hledání. Odešlete protokoly do účtu Azure Storage pro audit provozu. 

V případě potřeby povolte zachytávání paketů Network Watcher pro zkoumání aktivity neobvyklé.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení Network Watcher](../network-watcher/network-watcher-create.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: kognitivní hledání nepodporuje zjišťování neoprávněných vniknutí k síti, ale jako zmírnění vniknutí můžete nakonfigurovat pravidla brány firewall, která určují IP adresy, které přijímá služba kognitivní hledání. Nakonfigurujte privátní koncový bod tak, aby zůstal vyhledáný z veřejného Internetu.

- [Postup konfigurace klíčů spravovaných zákazníkem pro šifrování dat](./search-security-manage-encryption-keys.md)

- [Jak získat informace o klíčovém kódu spravovaném zákazníkem z indexů a mapování synonym](./search-security-get-encryption-keys.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: používejte značky služby, pokud používáte indexery a dovednosti v kognitivní hledání, které představují rozsah IP adres, které mají oprávnění k připojení k externím prostředkům. 

Povolte nebo zakažte provoz do prostředků zadáním názvu značky služby (například AzureCognitiveSearch) do příslušného zdrojového nebo cílového pole pravidla. 

- [Značky služby virtuální sítě](../virtual-network/service-tags-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: kognitivní hledání nemá nebo závisí na síťových prostředcích podle návrhu. Klientské aplikace a zdroje dat související s vaší aplikací pro vyhledávání můžou být ve virtuální síti, ale vyhledávací služba není nasazená v síti. 

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Pokyny**: kognitivní hledání můžete nakonfigurovat s privátním koncovým bodem Azure pro integraci vaší vyhledávací služby s virtuální sítí.  Použijte značky prostředků pro skupiny zabezpečení sítě a další prostředky, které se týkají zabezpečení sítě a toku provozu. Pro jednotlivá pravidla skupiny zabezpečení sítě použijte pole Popis k dokumentaci pravidel, která umožňují provoz do/ze sítě. 

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", abyste zajistili, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky. 

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledat nebo provádět akce s prostředky na základě jejich značek. 

- [Postup vytvoření privátního koncového bodu pro Kognitivní hledání](./service-create-private-endpoint.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Postup vytvoření Virtual Network Azure](../virtual-network/quick-create-portal.md)

- [Postup filtrování síťového provozu pomocí pravidel skupiny zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Doprovodné** materiály: kognitivní hledání nemá nebo závisí na žádné síťové součásti, takže konfigurace těchto prostředků se nedá monitorovat.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Doprovodné** materiály: kognitivní hledání nepodporuje konfiguraci vlastních zdrojů synchronizace času. Vyhledávací služba spoléhá na zdroje synchronizace času Microsoft a nezveřejňuje se pro zákazníky pro konfiguraci.

**Monitorování služby Azure Security Center:** Nelze použít

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: ingestování protokolů souvisejících s Kognitivní hledání prostřednictvím Azure monitor k agregaci dat zabezpečení generovaných zařízeními koncových bodů, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.
Případně můžete tato data povolit a zařadit do Azure Sentinel nebo SIEM třetí strany.

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: diagnostické a provozní protokoly poskytují přehled o podrobných operacích kognitivní hledání a jsou užitečné pro sledování služby a pro úlohy, které přistupují ke službě.  Chcete-li zachytit diagnostická data, povolte protokolování zadáním, kde jsou uloženy informace o protokolování.

- [Jak shromažďovat a analyzovat data protokolu pro Azure Kognitivní hledání](./search-monitor-logs.md)

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: historická data, která jsou informačními kanály do diagnostických metrik, se ve výchozím nastavení zachovají kognitivní hledání po dobu 30 dnů. V případě delšího uchování Nezapomeňte povolit nastavení, které určuje možnost úložiště pro uchování protokolovaných událostí a metrik.

V Azure Monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště. 

- [Změnit dobu uchovávání dat v Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů ze služby kognitivní hledání pro chování neobvyklé. Pomocí Log Analytics Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo jiného dodavatele SIEM.

- [Jak shromažďovat a analyzovat data protokolu pro Kognitivní hledání](./search-monitor-logs.md)

- [Jak vizualizovat data protokolu hledání v Power BI](./search-monitor-logs-powerbi.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Přečtěte si o službě Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md)

- [Jak provádět vlastní dotazy v Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: použití Security Center s Log Analytics pracovním prostorem pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech. Případně můžete povolit a začlenit data do Azure Sentinel.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Upozornění na data protokolu Log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: neplatí pro kognitivní hledání. Společnost Microsoft spravuje řešení ochrany proti malwaru pro základní platformu.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné** materiály: neplatí pro kognitivní hledání. Nevytváří ani nevyužívá protokoly DNS.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné** materiály: neplatí pro kognitivní hledání. Auditování příkazového řádku není pro Kognitivní hledání k dispozici.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Pokyny**: řízení přístupu na základě role v Azure (Azure RBAC) umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, skupinám instančních objektů a spravovaným identitám. Pro určité prostředky existují předdefinované role, které je možné inventarizovat nebo dotazovat pomocí nástrojů, jako jsou Azure CLI, Azure PowerShell nebo Azure Portal.

Role Kognitivní hledání jsou přidružené k oprávněním, která podporují úlohy správy na úrovni služby.  Tyto role neudělují přístup ke koncovému bodu služby. Přístup k operacím v rámci koncového bodu (například Správa indexů, naplnění indexu a dotazy na data hledání) použijte k ověření žádosti klíče rozhraní API.

- [Nastavení rolí pro přístup pro správu k Azure Kognitivní hledání](./search-security-rbac.md)

- [Vytváření a Správa klíčů rozhraní API pro službu Azure Kognitivní hledání](./search-security-api-keys.md)

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole)
- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné** materiály: neplatí pro kognitivní hledání. Nemá koncept výchozích hesel.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: kognitivní hledání nemá koncept účtů správců místní úrovně nebo Azure Active Directory (Azure AD), které lze použít ke správě indexů a operací. 

Použijte předdefinované role Azure AD, které se musí explicitně přiřadit k operacím správy. Vyvolejte modul Azure AD PowerShellu pro provádění dotazů ad-hoc pro zjišťování účtů, které jsou členy skupin pro správu.

- [Použití rolí pro přístup pro správu v Kognitivní hledání](./search-security-rbac.md)

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Pokyny**: použití ověřování pomocí jednotného přihlašování s Azure Active Directory (Azure AD) pro přístup k informacím služby Search pro operace správy podporované prostřednictvím Azure Resource Manager. 

Navažte proces, který omezí počet identit a přihlašovacích údajů, povolením jednotného přihlašování pro službu s již existujícími identitami vaší organizace.

- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: povolte funkci Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) a sledujte identitu Security Center a doporučení přístupu.

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md) 

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny**: použití pracovní stanice s privilegovaným přístupem (privilegovaným přístupem) s konfigurací Multi-Factor Authentication (MFA) nakonfigurovanou pro přihlášení a přístup k prostředkům Azure.

- [Pochopení zabezpečení pracovních stanic spravovaných Azure](../active-directory/devices/concept-azure-managed-workstation.md)
 

- [Jak povolit Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)
 

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použití sestav a monitorování zabezpečení služby Azure Active Directory (Azure AD) k detekci, kdy dojde k podezřelé nebo nebezpečné aktivitě v prostředí. Pomocí Security Center můžete monitorovat aktivitu identity a přístupu.

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](../active-directory/identity-protection/overview-identity-protection.md)

- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Doprovodné** materiály: neplatí pro kognitivní hledání. Pro přístup nepodporují použití schváleného umístění jako podmínky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: použití Azure Active Directory (Azure AD) jako centrálního ověřování a autorizačního systému pro úlohy správy na úrovni služby v Azure kognitivní hledání. Identity Azure AD neudělují přístup ke koncovému bodu vyhledávací služby.  Prostřednictvím klíčů rozhraní API jsou k dispozici přístup k operacím, jako je například Správa indexů, naplnění indexů a dotazy na data vyhledávání.

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Vytváření a Správa klíčů rozhraní API pro službu Azure Kognitivní hledání](./search-security-api-keys.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Pomocí kontrol identit a přístupů ke službě Azure AD můžete efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé. 

Zkontrolujte protokoly diagnostiky z Kognitivní hledání pro aktivitu v koncovém bodu vyhledávací služby, jako je například Správa indexů, naplnění indexu a dotazy.

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Monitorování operací a aktivit Azure Kognitivní hledání](./search-monitor-usage.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: přístup ke službě Azure Active Directory (Azure AD) přihlašovací aktivita, audit a zdroje protokolu událostí rizik, umožňují integraci s jakýmkoli nástrojem Siem nebo monitoring.

Zjednodušte tento proces vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Nakonfigurujte požadované výstrahy v rámci Log Analytics pracovního prostoru.

- [Jak integrovat protokoly aktivit Azure pomocí Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Doprovodné** materiály: použití funkcí ochrany identity Azure Active Directory (Azure AD) ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce související s identitami uživatelů. Ingestujte data do Azure Sentinel pro další šetření podle potřeby.

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md) 

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: neplatí pro kognitivní hledání. Customer Lockbox nepodporuje Kognitivní hledání.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Prostředky by měly být oddělené virtuální sítí nebo podsítí, vhodně označené a zabezpečené v rámci skupiny zabezpečení sítě nebo Azure Firewall. Prostředky, které ukládají nebo zpracovávají citlivá data, by měly být izolované. Pomocí privátního odkazu můžete nakonfigurovat privátní koncový bod, který se má Kognitivní hledání.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md) 

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Postup vytvoření privátního koncového bodu pro Kognitivní hledání](./service-create-private-endpoint.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Pokyny**: použití řešení třetí strany z Azure Marketplace v hraničních sítích pro monitorování neoprávněného přenosu citlivých informací a zablokování takových přenosů při upozornění na odborníky na zabezpečení informací.

Microsoft spravuje základní platformu a považuje veškerý obsah zákazníka za citlivý a chrání před ztrátou a expozicí zákaznických dat. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md) 

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: kognitivní hledání šifruje data při přenosu pomocí Transport Layer Security 1,2 a pro všechna připojení vynutila šifrování (SSL/TLS). Tím se zajistí, že všechna data budou v rámci přenosu mezi klientem a službou zašifrovaná.

- [Pochopení šifrování při přenosu pomocí Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

**Monitorování služby Azure Security Center:** Nelze použít

**Zodpovědnost**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro kognitivní hledání. Pokud je to nutné pro účely dodržování předpisů, implementujte řešení třetí strany. 

Microsoft spravuje základní platformu a považuje veškerý obsah zákazníka za citlivý a chrání před ztrátou a expozicí zákaznických dat. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: ke správě přístupu k prostředkům použijte službu Azure RBAC.

**Doprovodné** materiály: pro správu služeb použijte řízení přístupu na základě role Azure (Azure RBAC) ke správě přístupu ke klíčům a konfiguraci. Pro operace s obsahem, jako je indexování a dotazy, Kognitivní hledání používá místo modelu řízení přístupu založeného na identitě klíče. K řízení přístupu k klíčům použijte službu Azure RBAC.
- [Konfigurace řízení přístupu na základě role Azure](../role-based-access-control/role-assignments-portal.md) 

 
- [Použití rolí pro přístup pro správu k Kognitivní hledání](./search-security-rbac.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: neplatí pro kognitivní hledání. Tyto zásady jsou určené pro výpočetní prostředky. 

Společnost Microsoft spravuje základní infrastrukturu pro Kognitivní hledání a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Zodpovědnost**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: kognitivní hledání automaticky šifruje indexovaný obsah v klidovém tvaru pomocí klíčů spravovaných Microsoftem. Pokud potřebujete další ochranu, můžete výchozí šifrování doplnit druhou vrstvou šifrování pomocí klíčů, které vytvoříte a spravujete v Azure Key Vault.

- [Konfigurace klíčů spravovaných zákazníkem pro šifrování dat v Azure Kognitivní hledání](./search-security-manage-encryption-keys.md)

- [Principy šifrování neaktivních uložených dat v Azure](../security/fundamentals/encryption-atrest.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají v produkčních instancích kognitivní hledání a dalších důležitých nebo souvisejících prostředcích.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/platform/alerts-activity-log.md)

- [Postup vytvoření výstrah pro aktivity Kognitivní hledání](./search-monitor-logs.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: aktuálně není k dispozici pro kognitivní hledání.  Pro clustery, které ukládají obsah služby Search, zodpovídá společnost Microsoft za správu ohrožení zabezpečení těchto clusterů.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizované řešení pro správu oprav pro softwarové tituly třetích stran

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: neplatí pro kognitivní hledání. Microsoft provádí správu ohrožení zabezpečení v základních systémech, které podporují služby Kognitivní hledání Services.

**Monitorování služby Azure Security Center:** Nelze použít

**Zodpovědnost**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: neplatí pro kognitivní hledání. Není k dispozici žádné standardní systémy hodnocení rizik nebo bodování pro výsledky kontroly ohrožení zabezpečení.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se v předplatných dotázatte na všechny prostředky (například výpočetní prostředky, úložiště, síť, porty, protokoly atd.).  

Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v předplatných mohli vytvořit výčet všech předplatných Azure i prostředků.  

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md) 

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription) 

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure s metadaty k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry tam, kde je to vhodné, k uspořádání a sledování prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.
- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md) 

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md) 

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md) 

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: Definujte seznam schválených prostředků Azure souvisejících s indexováním a dovednosti zpracováním v kognitivní hledání.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: Doporučujeme, abyste definovali inventarizaci prostředků Azure, které byly schváleny k využití podle zásad vaší organizace a standardů, a potom monitorovat neschválené prostředky azure pomocí Azure Policy nebo grafu prostředků Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md) 

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné** materiály: neplatí pro kognitivní hledání. Tyto doprovodné materiály jsou určené pro výpočetní prostředky.

Doporučuje se, abyste měli inventarizaci softwarových aplikací, které byly schváleny podle zásad vaší organizace a standardů zabezpečení, a monitorovat všechny neschválené softwarové tituly nainstalované ve výpočetních prostředcích Azure.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: neplatí pro kognitivní hledání. Nezveřejňuje žádné výpočetní prostředky ani neumožňuje instalaci softwarových aplikací na žádný z jejích prostředků.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Pokyny**: pomocí Azure Policy můžete umístit omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, a to pomocí těchto integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat nebo zjišťovat prostředky v rámci předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/index.md) 

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro aplikace běžící na výpočetních prostředcích.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Doprovodné** materiály: pro správu služeb můžete pomocí podmíněného přístupu Azure omezit schopnost uživatelů komunikovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management. 

Řízení přístupu ke klíčům, které se používají k ověřování požadavků pro všechny ostatní operace, zejména o těch, které se týkají obsahu s Kognitivní hledání.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. Search můžete vytvořit vlastní zásady pro auditování nebo prosazování konfigurace prostředků Azure kognitivní hledání. Můžete také použít předdefinované definice Azure Policy pro Kognitivní hledání služby, jako například:

- Povolení protokolování auditu pro prostředky Azure

Azure Resource Manager má možnost Exportovat šablonu do JavaScript Object Notation (JSON), která by měla být přezkoumána, aby konfigurace splňovala požadavky na zabezpečení vaší organizace. 

Můžete také použít doporučení z Azure Security Center jako standardní hodnoty konfigurace pro prostředky Azure. 

- [Azure Policy kontroly dodržování předpisů pro Azure Kognitivní hledání](./security-controls-policy.md)

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [nasazení, pokud neexistují] efekty, abyste vynutili zabezpečená nastavení napříč prostředky služby kognitivní hledání. 

Šablony Azure Resource Manager lze použít k údržbě konfigurace zabezpečení vašich prostředků Azure, které vaše organizace vyžaduje. 

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

- [Azure Policy kontroly dodržování předpisů pro Azure Kognitivní hledání](./security-controls-policy.md)

- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Přehled šablon Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Pokud používáte vlastní definice Azure Policy, použijte k bezpečnému ukládání a správě kódu službu Azure DevOps nebo Azure Repos.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

- [Dokumentace k Azure Repos](/azure/devops/repos/index)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro prostředky služby Kognitivní hledání pomocí Azure Policy. 

Pomocí aliasů můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě. Můžete také využít integrované definice zásad, které souvisejí s vašimi konkrétními prostředky. 

Kromě toho můžete pomocí Azure Automation nasadit změny konfigurace a spravovat výjimky zásad. 

- [Azure Policy kontroly dodržování předpisů pro Azure Kognitivní hledání](./security-controls-policy.md)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: použijte Security Center k provádění kontrol směrného plánu vašich prostředků služby kognitivní hledání.  Kromě toho použijte Azure Policy k upozornění a auditování konfigurace prostředků. 

- [Jak opravit doporučení v Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Azure Policy kontroly dodržování předpisů pro Azure Kognitivní hledání](./security-controls-policy.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: Používejte spravované identity Azure ve spojení s Azure Key Vault k zjednodušení správy tajných kódů pro vaše cloudové aplikace.
- [Jak používat spravované identity pro prostředky Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 
- [Vytvoření Key Vault](../key-vault/general/quick-create-portal.md) 

- [Jak zajistit Key Vault ověřování pomocí spravované identity](../key-vault/general/assign-access-policy-portal.md) 

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Pokyny**: pomocí spravované identity azure udělte kognitivní hledání přístup k jiným službám Azure, jako jsou Key Vault a zdroje dat indexeru, pomocí automaticky spravované Identity v Azure Active Directory (Azure AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Azure Key Vault bez jakýchkoli přihlašovacích údajů v kódu. 

- [Nastavení připojení indexeru ke zdroji dat pomocí spravované identity](./search-howto-managed-identities-data-sources.md)

- [Konfigurace klíčů spravovaných zákazníkem pro šifrování dat pomocí spravované identity](./search-security-manage-encryption-keys.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Doprovodné** materiály: neplatí pro kognitivní hledání. Nehostuje kód a nemá žádná pověření k identifikaci.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Používejte centrálně spravovaný antimalwarový software

**Doprovodné** materiály: neplatí pro kognitivní hledání. Toto doporučení je určené pro výpočetní prostředky.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Kognitivní hledání), ale neběží na zákaznickém obsahu.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: předběžná kontrola veškerého obsahu, který se nahrává do nevýpočetních prostředků Azure, jako jsou Kognitivní hledání, Blob Storage, Azure SQL Database a tak dále. 

Je vaše zodpovědnost za to, že se veškerý obsah nahrává do prostředků Azure, které nejsou ve výpočetním prostředí, předem. Microsoft nemůže získat přístup k zákaznickým datům, a proto nemůže za vás provádět kontrolu proti malwarovým kontrolám zákaznického obsahu.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované

**Doprovodné** materiály: neplatí pro kognitivní hledání. Neumožňuje nainstalovat antimalwarová řešení do prostředků IT. Pro základní platformu Microsoft zpracovává aktualizace jakéhokoli antimalwarového softwaru a podpisů. 

Pro všechny výpočetní prostředky, které vlastní vaše organizace a které se používají ve vašem řešení hledání, použijte doporučení v Security Centerch výpočetních &amp; aplikacích, abyste zajistili aktuálnost všech koncových bodů s nejnovějšími podpisy. Pro Linux použijte řešení ochrany proti malwaru třetí strany.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: obsah uložený ve vyhledávací službě nejde zálohovat prostřednictvím Azure Backup nebo jakýchkoli jiných vestavěných mechanismů, ale můžete znovu sestavit index ze zdrojového kódu aplikace a primárních zdrojů dat nebo vytvořit vlastní nástroj pro načtení a uložení indexovaného obsahu.

- [Index GitHubu – ukázka zálohování – obnovení](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné** materiály: kognitivní hledání v současné době nepodporuje automatizované zálohování pro data ve vyhledávací službě a je třeba je zálohovat pomocí ručního procesu.  Klíče spravované zákazníkem můžete zálohovat také v Azure Key Vault. 

- [Zálohování a obnovení indexu služby Azure Kognitivní hledání](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Postup zálohování klíčů Key Vault v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: kognitivní hledání aktuálně nepodporuje automatizované zálohování pro data ve vyhledávací službě a je třeba je zálohovat a obnovovat prostřednictvím ručního procesu.  Pravidelně provádějte obnovování obsahu ručně zálohovaných dat, abyste zajistili ucelenou integritu procesu zálohování.

- [Zálohování a obnovení indexu služby Azure Kognitivní hledání](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Postup obnovení klíčů Key Vault v Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: kognitivní hledání v současné době nepodporuje automatizované zálohování pro data ve vyhledávací službě a je třeba je zálohovat pomocí ručního procesu.  Klíče spravované zákazníkem můžete zálohovat také v Azure Key Vault. 

Povolí v Key Vault ochranu před náhodným odstraněním a vyprázdněním, aby se chránily klíče proti náhodnému nebo škodlivému odstranění. Pokud se Azure Storage používá k ukládání ručních záloh, povolte obnovitelné odstranění a uložte a obnovte data, když se odstraní objekty blob nebo snímky objektů BLOB. 

- [Zálohování a obnovení indexu služby Azure Kognitivní hledání](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Jak povolit obnovitelné odstranění a ochranu před vymazáním v Key Vaultu](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Obnovitelné odstranění pro Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: vývoj Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování incidentů a správu, které se mají zjišťovat, na kontrolu po jednotlivých událostech.

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Navíc můžete označit odběry pomocí značek a vytvořit systém pojmenování k identifikaci a kategorizaci prostředků Azure, zejména těch, které zpracovávají citlivá data. Je vaše zodpovědnost za to, že je možné určit prioritu nápravy výstrah na základě závažnosti prostředků a prostředí Azure, ve kterých došlo k incidentu.

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Podívejte se na publikaci NIST, "Průvodce testováním, školením a cvičením programů pro IT plány a možnosti"](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo průběžně. Pomocí konektoru Security Center Data můžete streamovat výstrahy do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: postupujte podle pravidel pro testování průniku Microsoft Cloud, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem.
- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)