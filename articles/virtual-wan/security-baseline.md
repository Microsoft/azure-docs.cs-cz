---
title: Základní hodnoty zabezpečení Azure pro virtuální síť WAN
description: Základní základní údaje o zabezpečení sítě WAN poskytují pokyny a prostředky pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6f487467b08332eea4ee19a7fb8836d843bd254f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582657"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Základní hodnoty zabezpečení Azure pro virtuální síť WAN

Tato základní hodnota zabezpečení platí pro Microsoft Azure virtuální sítě WAN pokyny od [verze 2,0 Azure Security test](../security/benchmarks/overview.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah je seskupen podle **ovládacích prvků zabezpečení** , které jsou definovány srovnávacím testem zabezpečení Azure, a souvisejícími pokyny platnými pro virtuální síť WAN. **Ovládací prvky** , které se nevztahují k virtuální síti WAN, se vyloučily.

Pokud chcete zjistit, jak se virtuální síť WAN kompletně mapuje na test zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení sítě WAN](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementace zabezpečení pro interní provoz

**Doprovodné** materiály: Microsoft Azure Virtual WAN poskytuje možnosti vlastního směrování a nabízí šifrování pro provoz ExpressRoute. Veškerou správu tras poskytuje směrovač virtuálního rozbočovače, který taky umožňuje přenosové připojení mezi virtuálními sítěmi. Šifrování provozu ExpressRoute pomocí virtuální sítě WAN zajišťuje zašifrovaný přenos mezi místními sítěmi a virtuálními sítěmi Azure přes ExpressRoute, aniž by bylo nutné přes veřejný Internet nebo používat veřejné IP adresy. 

- [Šifrování provozu ExpressRoute](virtual-wan-about.md#encryption)

- [Použití privátního odkazu ve virtuální síti WAN](howto-private-link.md)

- [Vlastní scénáře směrování](scenario-any-to-any.md)

- [Dokumentace k vlastní směrovací tabulce](how-to-virtual-hub-routing.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ns-2-connect-private-networks-together"></a>NS-2: spojování privátních sítí 

**Doprovodné** materiály: Microsoft Azure ExpressRoute nabízí privátní připojení k Azure Virtual WAN. Vzhledem k tomu, že připojení ExpressRoute nepřecházejí přes veřejný Internet, ExpressRoute nabízí spolehlivější a rychlejší a nižší latenci než typická připojení k Internetu. Virtuální privátní síť můžete použít také k připojení k Azure prostřednictvím sítě VPN typu Site-to-Site (S2S) nebo VPN typu Point-to-Site (P2S).

- [ExpressRoute ve virtuální síti WAN](virtual-wan-expressroute-portal.md)

- [Přehled sítě VPN typu Site-to-site](virtual-wan-site-to-site-portal.md)

- [Přehled sítě VPN typu Point-to-site](virtual-wan-point-to-site-portal.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrana aplikací a služeb před útoky z externích sítí

**Pokyny**: virtuální síť WAN nevystavuje žádné koncové body externím sítím, které vyžadují, aby byly zabezpečeny pomocí konvenční ochrany sítě. K ochraně prostředků ve virtuálních sítích paprsků (libovolná virtuální síť připojená k virtuálnímu rozbočovači) pomocí služeb ochrany virtuální sítě můžete používat bezplatné služby. 

Pomocí Azure Firewall můžete chránit aplikace a služby před potenciálně škodlivým provozem z Internetu a dalších externích umístění. 

Pokud chcete svoje prostředky chránit před útoky na virtuální sítě Azure, vyberte DDoS Protection poskytované Azure. Použijte Azure Security Center ke zjištění rizikových konfigurací, které souvisejí s vašimi prostředky souvisejícími se sítí.

- [Dokumentace k Azure Firewall](../firewall/index.yml)

- [Správa Azure DDoS Protection Standard pomocí Azure Portal](../ddos-protection/manage-ddos-protection.md) 

- [Doporučení pro Azure Security Center](../security-center/recommendations-reference.md#recs-networking)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: nasazení systémů pro zabránění vniknutí/prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: virtuální síť WAN je služba spravovaná Microsoftem. Nenabízí nativní detekci vniknutí ani možnosti prevence vniknutí. Služba Virtual Azure Firewall WAN ale nabízí funkce zabezpečení, které umožňují jednotný bod řízení zásad. Můžete vytvořit zásadu Azure Firewall a propojit zásadu s virtuálním rozbočovačem WAN, aby stávající virtuální síť WAN mohla fungovat jako zabezpečené virtuální rozbočovač, a to s požadovanými Azure Firewall nasazenými prostředky.

- [Konfigurace Azure Firewall ve virtuálním centru WAN](howto-firewall.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: zjednodušení pravidel zabezpečení sítě

**Pokyny**: Zjednodušte pravidla zabezpečení sítě pomocí Virtual Networkch značek služeb a definujte řízení přístupu k síti pro skupiny zabezpečení sítě nebo Azure firewall. Značky služeb lze použít místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby v poli zdroj nebo cíl pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Pochopení a použití značek služeb](../virtual-network/service-tags-overview.md)

- [Pochopení a použití skupin zabezpečení aplikací](../virtual-network/network-security-groups-overview.md#application-security-groups)

- [Dokumentace k Azure Firewall](../firewall/index.yml)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Zabezpečená služba názvů domén (DNS)

**Doprovodné** materiály: zabezpečení funkcí DNS se poskytuje virtuální síti WAN s Azure firewall. Nakonfigurujte Azure Firewall fungovat jako proxy server DNS, který se stal zprostředkujícím pro žádosti DNS z klientských virtuálních počítačů na server DNS. Pro vlastní konfigurace serverů DNS povolte proxy serveru DNS, aby se zabránilo neshodě překladu názvů DNS, a v síťových pravidlech povolte filtrování plně kvalifikovaného názvu domény. 

- [Dokumentace k Azure Firewall](../firewall/index.yml)

- [Azure Firewall nastavení DNS](../firewall/dns-settings.md)

- [Použití Azure Firewall jako serveru DNS pro přeposílání s privátním odkazem](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [Azure Security Benchmark: správa identit](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizace Azure Active Directory jako centrálního systému pro identifikaci a ověřování

**Doprovodné** materiály: Azure Active Directory (Azure AD) je výchozí službou pro správu identit a přístupu pro služby Azure. včetně virtuální sítě WAN. Standardizace Azure AD pro řízení identity a správy přístupu vaší organizace v nástroji:

- Microsoft Cloud prostředky, jako jsou Azure Portal, Azure Storage, Virtual Machines Azure (Linux a Windows), Azure Key Vault, platforma jako služba (PaaS) a aplikace typu software jako služba (SaaS).
- Prostředky vaší organizace, jako jsou aplikace v Azure nebo podnikové síťové prostředky

Zabezpečte službu Azure AD jako vysoce prioritní v praxi cloudového zabezpečení vaší organizace. Vyhodnoťte svoji identitu a zabezpečení stav pomocí funkce skóre zabezpečení z Security Center k posouzení, jak úzce vaše konfigurace vyhovuje doporučením doporučeným postupem Microsoftu. Podle potřeby implementujte doporučení pro osvědčené postupy od Microsoftu pro vylepšení stav zabezpečení.

Azure AD podporuje také externí identity, které umožňují uživatelům bez účet Microsoft přihlašovat se k aplikacím a prostředkům s jejich externí identitou. 

Přečtěte si informace o používání Azure AD ve scénářích sítě VPN typu Point-to-site v odkazovaných odkazech.

- [Vytvoření tenanta Azure Active Directory (AD) pro připojení protokolu P2S OpenVPN](openvpn-azure-ad-tenant-multi-app.md)

- [Konfigurace ověřování Azure Active Directory pro uživatele VPN](virtual-wan-point-to-site-azure-ad.md)

- [Architektura tenantů v Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Použití řídicích prvků silného ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: v současné době se ověřování Azure Active Directory (Azure AD) poskytuje prostřednictvím integrace s virtuální sítí WAN typu Point-to-Site VPN.

Azure Active Directory (Azure AD) je výchozí službou pro správu identit a přístupu pro služby Azure. Azure AD podporuje ovládací prvky silného ověřování s využitím vícefaktorového ověřování a silných metod bez hesla.

Azure AD doporučuje pro ovládací prvky silného ověřování následující:

- Vícefaktorové ověřování – povolte vícefaktorové ověřování Azure AD a sledujte doporučení pro správu identit a přístupu v Azure Security Center, kde najdete doporučené postupy zabezpečení. Vynutili vícefaktorové ověřování u všech, vyberte možnost Uživatelé nebo na úrovni jednotlivých uživatelů na základě podmínek přihlášení a rizikových faktorů.

- Ověřování nepřipojeného hesla – k dispozici jsou tři možnosti ověřování s heslem. Mezi ně patří: Windows Hello pro firmy, Microsoft Authenticator aplikace a místní metody ověřování, jako jsou čipové karty.

Zajistěte, aby se nejvyšší úroveň metody silného ověřování používala pro správce a privilegované uživatele a aby mohli ostatní uživatelé zavést zásady silného ověřování.

- [Jak povolit vícefaktorové ověřování v P2S VPN pro virtuální síť WAN](openvpn-azure-ad-mfa.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Omezení přístupu k prostředkům Azure na základě podmínek

**Pokyny**: povolení vícefaktorového ověřování Azure Active Directory (Azure AD) pro uživatele VPN (Point-to-site) s použitím ověřování Azure AD. Konfigurace vícefaktorového ověřování na základě jednotlivých uživatelů nebo využití vícefaktorového ověřování pomocí podmíněného přístupu Podmíněný přístup umožňuje přesnější kontrolu nad tím, jak by měl být povýšen druhý faktor. Umožňuje přiřazení vícefaktorového ověřování jenom k síti VPN a vyloučení dalších aplikací, které jsou vázané na tenanta Azure AD. 

Ověřování Azure AD je k dispozici pouze pro brány pomocí protokolu OpenVPN a klientů se systémem Windows.

- [Co je podmíněný přístup](../active-directory/conditional-access/overview.md)

- [Konfigurace ověřování Azure Active Directory pro uživatele VPN](virtual-wan-point-to-site-azure-ad.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminace nezamýšleného prozrazení přihlašovacích údajů

**Doprovodné** materiály: síť VPN typu Site-to-site ve virtuální síti WAN používá předsdílené klíče (PSK), které zákazník v jejich Azure Key Vault vytvořil a spravuje. Implementujte kontrolu přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

V případě GitHubu můžete k identifikaci přihlašovacích údajů nebo jiné formy tajných kódů v kódu použít funkci nativního skenování tajných kódů.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Skenování tajných kódů GitHubu](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security Benchmark: privilegovaný přístup](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Omezení přístupu pro správu k nejdůležitějším podnikovým systémům

**Pokyny**: Azure Virtual WAN používá řízení přístupu na základě role Azure (Azure RBAC) k izolaci přístupu k podnikovým systémům tím, že omezí přístup k předplatným a skupinám pro správu, ve kterých jsou udělen privilegovaný přístup.

Omezte také přístup k systémům pro správu, identitu a zabezpečení, které mají přístup pro správu k vašemu podnikovému přístupu, jako jsou Doména služby Active Directory řadiče, nástroje zabezpečení a nástroje pro správu systému s agenty nainstalovanými v podnikových důležitých systémech. Útočníci, kteří tyto systémy pro správu a zabezpečení ohrožují, si je můžou hned weaponize, aby vyzrazeni důležité obchodní prostředky.

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu.

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Přístup ke skupině pro správu](../governance/management-groups/overview.md#management-group-access) 

- [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Šifrování citlivých informací při přenosu

**Pokyny**: pro vaše požadavky na připojení použijte VPN typu Point-to-site, síť Site-to-Site VPN a šifrovanou Express Route s virtuální sítí WAN. Šifrování sítě VPN chrání data při přenosu z útoků typu "vzdálené správy" (například zachytávání přenosů), aby se zajistilo, že útočníci nemůžou data číst nebo upravovat. 

- [Point-to-site VPN](virtual-wan-point-to-site-portal.md)

- [Site-to-site VPN](virtual-wan-site-to-site-portal.md)

- [Šifrované ExpressRoute](vpn-over-expressroute.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

## <a name="asset-management"></a>Správa aktiv

*Další informace najdete v tématu [Azure Security Benchmark: správa prostředků](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Zajištění přehledu bezpečnostního týmu o rizicích pro prostředky

**Pokyny:** Zajistěte udělení oprávnění Čtenář zabezpečení ve vašem tenantovi Azure a vašich předplatných bezpečnostním týmům, aby mohly monitorovat bezpečnostní rizika s využitím služby Azure Security Center. 

V závislosti na nastavení struktury odpovědnosti bezpečnostních týmů může být monitorování bezpečnostních rizik odpovědností centrálního nebo místního bezpečnostního týmu. V takovém případě musí být přehledy a rizika zabezpečení vždy agregované centrálně v rámci organizace. 

Oprávnění čtenář zabezpečení je možné využít široce pro celého tenanta (kořenovou skupinu pro správu) nebo vymezenou pro skupiny pro správu nebo konkrétní předplatná. 

Poznámka: K získání přehledu o úlohách a službách se můžou vyžadovat další oprávnění. 

- [Přehled role Čtenář zabezpečení](../role-based-access-control/built-in-roles.md#security-reader)

- [Přehled skupin pro správu Azure](../governance/management-groups/overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Zajištění přístupu k inventáři a metadatům prostředků pro bezpečnostní tým

**Doprovodné** materiály: použití značek pro prostředky Azure, skupiny prostředků a předplatná k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí. Azure Virtual WAN taky podporuje nasazení prostředků na základě Azure Resource Manager, se kterými můžete exportovat šablony assetů. 

- [Průvodce rozhodováním ohledně pojmenování a označování prostředků](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Správa inventáře Azure Security Center prostředků](../security-center/asset-inventory.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používání jenom schválených služeb Azure

**Doprovodné** materiály: pomocí Azure monitor můžete vytvořit pravidla pro aktivaci výstrah při zjištění neschválené služby. Služba Virtual WAN přináší dohromady mnoho funkcí sítě, zabezpečení a směrování, aby poskytovala jediné provozní rozhraní. Brány VPN pro virtuální sítě WAN, brány ExpressRoute a Azure Firewall mají k dispozici protokolování a metriky prostřednictvím Azure Monitor. 
 

- [Protokoly a metriky virtuálních sítí WAN](logs-metrics.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: použití podmíněného přístupu Azure k omezení schopnosti uživatelů pracovat se správcem prostředků Azure pomocí konfigurace "blokování přístupu" pro aplikaci "Microsoft Azure Management".

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v tématu [Azure Security Benchmark: protokolování a detekce hrozeb](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: povolení detekce hrozeb pro prostředky Azure

**Pokyny**: síť VPN typu Point-to-site s virtuální sítí WAN je integrovaná s Azure Active Directory (Azure AD). Azure AD poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD, které jsou integrované s Azure Monitor, Azure Sentinel, SIEM nebo monitorovací nástroje pro složitější účely monitorování a analýzy hrozeb. Jsou to:

- Přihlásit se – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny provedené u všech prostředků v rámci služby Azure AD, například přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.
- Rizikové přihlášení – rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Pomocí Azure Security Center můžete vytvářet výstrahy pro určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření, včetně zastaralých účtů v rámci předplatného. Kromě základního monitorování hygieny zabezpečení použijte modul ochrany před internetovými útoky z Security Center k shromažďování podrobnějších výstrah zabezpečení z jednotlivých výpočetních prostředků Azure (virtuálních počítačů, kontejnerů, aplikační služby), datových prostředků (SQL DB a úložiště) a vrstev služeb Azure. Tato funkce umožňuje získat přehled o anomáliích u účtů v rámci jednotlivých prostředků.

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Povolení ochrany identit Azure](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurace Azure Firewall ve virtuálním centru WAN](howto-firewall.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Povolení detekce hrozeb pro správu identit a přístupu v Azure

**Pokyny**: síť VPN typu Point-to-site s virtuální sítí WAN je integrovaná s Azure Active Directory (Azure AD). Azure AD poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD, které jsou integrované s Azure Monitor, Azure Sentinel, SIEM nebo monitorovací nástroje pro složitější účely monitorování a analýzy hrozeb. Jsou to:

- Přihlásit se – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny provedené u všech prostředků v rámci služby Azure AD, například přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.
- Rizikové přihlášení – rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Pomocí Azure Security Center můžete vytvářet výstrahy pro určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření, včetně zastaralých účtů v rámci předplatného. Kromě základního monitorování hygieny zabezpečení použijte modul ochrany před internetovými útoky z Security Center k shromažďování podrobnějších výstrah zabezpečení z jednotlivých výpočetních prostředků Azure (virtuálních počítačů, kontejnerů, aplikační služby), datových prostředků (SQL DB a úložiště) a vrstev služeb Azure. Tato funkce umožňuje získat přehled o anomáliích u účtů v rámci jednotlivých prostředků.

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Povolení ochrany identit Azure](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurace Azure Firewall ve virtuálním centru WAN](howto-firewall.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Povolení protokolování pro síťové aktivity Azure

**Pokyny**: monitorování Azure Virtual WAN pomocí Azure monitor. Služba Virtual WAN přináší dohromady mnoho funkcí sítě, zabezpečení a směrování, aby poskytovala jediné provozní rozhraní. Brány VPN pro virtuální sítě WAN, brány ExpressRoute a Azure Firewall mají k dispozici protokolování a metriky prostřednictvím Azure Monitor. Položky protokolu aktivit jsou shromažďovány ve výchozím nastavení a lze je zobrazit v Azure Portal. K zobrazení všech operací odeslaných do vašeho předplatného Azure můžete použít protokoly aktivit Azure (dřív označované jako operační protokoly a protokoly auditu).

Pro virtuální síť WAN jsou k dispozici také různé diagnostické protokoly a lze je nakonfigurovat pro prostředek virtuální sítě WAN pomocí Azure Portal.  Můžete se rozhodnout, že se má Log Analytics, streamovat do centra událostí nebo jednoduše archivovat do účtu úložiště. 
 

- [Protokoly a metriky virtuálních sítí WAN](logs-metrics.md)

- [Metriky a protokoly Azure Firewallu](../firewall/logs-and-metrics.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Povolení protokolování pro prostředky Azure

**Pokyny**: protokoly aktivit Azure, povolené automatické, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky Azure Virtual WAN s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

Povolte protokoly prostředků Azure pro virtuální síť WAN. Pomocí Azure Security Center a Azure Policy můžete povolit protokoly prostředků a shromažďovat data protokolů. Tyto protokoly můžou být klíčové pro pozdější zkoumání incidentů zabezpečení a provádění forenzních cvičení.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/essentials/platform-logs-overview.md) 

- [Pochopení Azure Security Center shromažďování dat](../security-center/security-center-enable-data-collection.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizace správy a analýz protokolu zabezpečení

**Pokyny**: povolení protokolování zabezpečení pro virtuální síť WAN s Azure monitor. Služba Virtual WAN přináší dohromady mnoho funkcí sítě, zabezpečení a směrování, aby poskytovala jediné provozní rozhraní. Brány VPN pro virtuální sítě WAN, brány ExpressRoute a Azure Firewall mají k dispozici protokolování a metriky prostřednictvím Azure Monitor. Položky protokolu aktivit jsou shromažďovány ve výchozím nastavení a lze je zobrazit v Azure Portal. K zobrazení všech operací odeslaných do vašeho předplatného Azure můžete použít protokoly aktivit Azure (dřív označované jako operační protokoly a protokoly auditu). 

Pro virtuální síť WAN jsou k dispozici také různé diagnostické protokoly a lze je nakonfigurovat pro prostředek virtuální sítě WAN pomocí Azure Portal. Odeslání do Log Analytics, streamování do centra událostí nebo pouhá archivace na účet úložiště. Kromě toho povolte a zaveďte data do Azure Sentinel nebo řešení pro správu událostí a zabezpečení třetí strany. 
 

- [Protokoly a metriky virtuálních sítí WAN](logs-metrics.md)

- [Metriky a protokoly Azure Firewallu](../firewall/logs-and-metrics.md)

Zabezpečení Azure Virtual WAN se poskytuje prostřednictvím Azure Firewall. 

- [Dokumentace k Azure Firewall](../firewall/overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurace uchovávání úložiště protokolů

**Doprovodné** materiály: Konfigurace uchovávání protokolů podle požadavků na dodržování předpisů, nařízení a podnikových požadavků. V Azure Monitor můžete nastavit dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte Azure Storage, Data Lake nebo Log Analytics účty pracovního prostoru pro dlouhodobé a archivační úložiště.

- [Změnit dobu uchovávání dat v Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center upozornění a export doporučení](../security-center/continuous-export.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – aktualizace procesu reakce na incidenty pro Azure

**Pokyny:** Zajistěte, aby vaše organizace měla procesy, jak reagovat na incidenty zabezpečení, aby tyto procesy aktualizovala pro Azure a pravidelně je testovala za účelem zajištění připravenosti.

- [Implementace zabezpečení v podnikovém prostředí](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referenční příručka k reakcím na incidenty](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Příprava – nastavení oznámení o incidentu

**Pokyny:** Nastavte kontaktní informace pro incidenty zabezpečení ve službě Azure Security Center. Tyto kontaktní informace používá společnost Microsoft k tomu, aby vás kontaktovala v případě, že služba MSRC (Microsoft Security Response Center) zjistí, že s vašimi daty někdo nezákonně nebo neoprávněně pracoval. Máte také možnost přizpůsobit upozornění na incidenty a oznámení o incidentech v různých službách Azure v závislosti na tom, jak potřebujete na incidenty reagovat. 

- [Nastavení kontaktu ve službě Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detekce a analýza – Vytváření incidentů na základě vysoce kvalitních upozornění

**Pokyny:** Ujistěte se, že máte nastavené procesy vytváření vysoce kvalitních upozornění a měření kvality upozornění. To vám umožní učit se z minulých incidentů a určovat prioritu upozornění pro analytiky, aby neztráceli čas falešně pozitivními výsledky. 

Vysoce kvalitní upozornění je možné vytvářet na základě zkušeností z minulých incidentů, ověřených komunitních zdrojů a nástrojů, které spojují a korelují signály z různorodých zdrojů a generují a mažou upozornění. 

Azure Security Center nabízí vysoce kvalitní upozornění pro celou řadu prostředků Azure. Pomocí datového konektoru ASC můžete upozornění streamovat do služby Azure Sentinel. Azure Sentinel umožňuje vytvářet rozšířená pravidla upozornění, která automaticky generují incidenty pro účely šetření. 

Pomocí funkce exportu můžete upozornění a doporučení služby Azure Security Center exportovat, abyste mohli lépe identifikovat rizika pro prostředky Azure. Upozornění a doporučení můžete exportovat ručně nebo průběžně a nepřetržitě.

- [Konfigurace exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detekce a analýza – Vyšetřování incidentu

**Pokyny:** Zajistěte, aby analytici mohli při vyšetřování potenciálních incidentů dotazovat a používat různorodé zdroje dat a mohli tak získat ucelenou představu o tom, co se stalo. Aby se zabránilo slepým místům, měly by se shromažďovat různorodé protokoly, které umožní sledovat aktivity potenciálního útočníka v celém průběhu útoku.  Měli byste také zajistit zaznamenávání přehledů a poznatků pro další analytiky a budoucí použití jako historické referenční informace.  

Mezi zdroje dat pro vyšetřování patří centrální zdroje protokolování, ve kterých se již shromažďují protokoly ze služeb v příslušném oboru a spuštěných systémů, ale můžou mezi ně patřit také:

- Síťová data – S využitím protokolů toků skupin zabezpečení sítě a služeb Azure Network Watcher a Azure Monitor můžete zachytávat protokoly toků sítě a další analytické informace. 

- Snímky spuštěných systémů: 

    - S využitím funkce vytváření snímků virtuálního počítače Azure můžete vytvořit snímek disku spuštěného systému. 

    - S využitím nativní funkce výpisu paměti operačního systému můžete vytvořit snímek paměti spuštěného systému.

    - S využitím funkce vytváření snímků služeb Azure nebo vlastních funkcí vašeho softwaru můžete vytvářet snímky spuštěných systémů.

Azure Sentinel nabízí rozsáhlé analýzy dat z prakticky jakéhokoli zdroje protokolů a portál pro správu případů, na kterém můžete spravovat celý životní cyklus incidentů. Analytické informace zjištěné během vyšetřování je možné přidružovat k incidentům pro účely sledování a generování sestav. 

- [Snímek disku počítače s Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snímek disku počítače s Linuxem](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Shromažďování výpisu paměti a diagnostických informací ze strany podpory Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Vyšetřování incidentů s využitím služby Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detekce a analýza – stanovení priorit incidentů

**Pokyny:** Poskytněte analytikům kontext, aby věděli, na jaké incidenty se zaměřit jako první v závislosti na závažnosti upozornění a citlivosti prostředků. 

Azure Security Center jednotlivým upozorněním přiřazuje závažnost, která vám pomůže určit, jaká upozornění by se měla vyšetřit jako první. Závažnost vychází z míry důvěry služby Security Center v závěr nebo analýzu, na základě kterých se upozornění vygenerovalo, a také z úrovně spolehlivosti, že za aktivitou, která vedla k upozornění, byl škodlivý záměr.

Kromě toho můžete prostředky označit pomocí značek a vytvořit systém vytváření názvů, který vám pomůže identifikovat a kategorizovat prostředky Azure, zejména prostředky zpracovávající citlivá data.  Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Zadržení, zničení a obnovení – automatizace zpracování incidentů

**Pokyny:** Automatizujte ruční opakující se úlohy, ay se zrychlila doba reakce a snížilo se zatížení analytiků. Ruční provádění úloh trvá déle, což zpomaluje jednotlivé incidenty a snižuje počet incidentů, které může analytik zvládnout. Ruční úlohy také analytiky více unavují, což zvyšuje riziko zpoždění v důsledku lidských chyb a snižuje schopnost analytiků efektivně se soustředit na složité úlohy. S využitím funkcí automatizace pracovních postupů ve službách Azure Security Center a Azure Sentinel můžete v reakci na příchozí upozornění zabezpečení automaticky aktivovat akce nebo spouštět playbooky. Playbook provádí akce, jako jsou odesílání oznámení, zakazování účtů nebo izolace problematických sítí. 

- [Konfigurace automatizace pracovních postupů ve službě Security Center](../security-center/workflow-automation.md)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="posture-and-vulnerability-management"></a>Stav a správa ohrožení zabezpečení

*Další informace najdete v tématu [Azure Security Benchmark: stav a správa ohrožení zabezpečení](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Provádění pravidelné simulace útoku

**Pokyny:** Podle potřeby provádějte testování průniku nebo aktivity červeného týmu na svých prostředcích Azure a zajistěte nápravu všech kritických zjištění v oblasti zabezpečení.
Postupujte podle pravidel zapojení testování průniku cloudových služeb Microsoftu (Microsoft Cloud Penetration Testing), abyste měli jistotu, že testy průniku neporušují zásady Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem.

- [Testování průniku v Azure](../security/fundamentals/pen-testing.md)

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="endpoint-security"></a>Zabezpečení koncového bodu

*Další informace najdete v tématu [srovnávací zabezpečení Azure Security: Endpoint Security](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: použití detekce a odpovědi koncového bodu (EDR)

**Doprovodné** materiály: zákazníkům není explicitně povoleno konfigurovat zjišťování koncových bodů a nastavení odpovědí. Virtual Machines v nabídce Azure Virtual WAN ale tyto možnosti používají. Přečtěte si další informace o těchto obecných funkcích na odkazovaných odkazech. 

- [Přehled rozšířené ochrany před internetovými útoky v programu Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Služba ochrany ATP v programu Microsoft Defender pro servery Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Služba ochrany ATP v programu Microsoft Defender pro servery jiné než Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílená

## <a name="governance-and-strategy"></a>Zásady správného řízení a strategie

*Další informace najdete v tématu [Azure Security Benchmark: zásady správného řízení a strategie](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definování strategie správy prostředků a ochrany dat 

**Pokyny:** Zadokumentujte si a dále sdělujte jasnou strategii pro nepřetržité monitorování a ochranu systémů a dat. Stanovte priority zjišťování, hodnocení, ochrany a monitorování nejdůležitějších podnikových dat a systémů. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující prvky: 

-   Standard klasifikace dat v souladu s obchodními riziky

-   Přehled organizace zabezpečení o rizicích a inventáři prostředků 

-   Schválení služeb Azure k používání provedené organizací zabezpečení 

-   Zabezpečení prostředků po dobu jejich životního cyklu

-   Požadovaná strategie řízení přístupu podle klasifikace dat organizace

-   Využívání funkcí ochrany dat nativních pro Azure a funkcí ochrany dat třetích stran

-   Požadavky na šifrování přenášených a neaktivních uložených dat

-   Vhodné kryptografické standardy

Další informace najdete v následujících referenčních materiálech:
- [Doporučení architektury zabezpečení Azure – úložiště, data a šifrování](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Základy zabezpečení Azure – zabezpečení, šifrování a úložiště dat Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – osvědčené postupy zabezpečení a šifrování dat Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – správa prostředků](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark – ochrana dat](../security/benchmarks/security-controls-v2-data-protection.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definování strategie segmentace podniku 

**Pokyny:** Zřiďte celopodnikovou strategii za účelem segmentace přístupu k prostředkům pomocí kombinace identity, sítě, aplikace, předplatného, skupiny pro správu a dalších řídicích prvků.

Pečlivě vyvažte nutnost oddělení zabezpečení s nutností povolit každodenní provoz systémů, které musí vzájemně komunikovat a pracovat s daty.

Zajistěte, aby se strategie segmentace implementovala konzistentně ve všech typech řídicích prvků včetně zabezpečení sítě, modelů identit a přístupu a modelů oprávnění/přístupu aplikací a řídicích prvků pro lidské procesy.

- [Pokyny ke strategii segmentace v Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Pokyny ke strategii segmentace v Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Soulad segmentace sítě a strategie segmentace podniku](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definování strategie správy stavu zabezpečení

**Pokyny:** Nepřetržitě měřte a zmírňujte rizika pro jednotlivé prostředky a prostředí, ve kterém jsou hostovány. Stanovte prioritu prostředků s vysokou hodnotou a míst nejvíce vystavených útokům, jako jsou publikované aplikace, body sítě pro příchozí a odchozí přenosy dat, koncové body uživatelů a správců atd.

- [Azure Security Benchmark – správa stavu a ohrožení zabezpečení](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Soulad rolí a odpovědností organizace

**Pokyny:** Je nutné zadokumentovat a dále sdělovat jasnou strategii pro role a odpovědnosti ve vaší organizaci zabezpečení. Stanovte prioritu poskytování jasné odpovědnosti při rozhodování o zabezpečení, zároveň vyškolte všechny uživatele na model sdílené odpovědnosti a vyškolte technické týmy na technologii pro zabezpečení cloudu.

- [Osvědčený postup zabezpečení Azure 1 – Lidé: školení týmů v oblasti zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Osvědčený postup zabezpečení Azure 2 – Lidé: školení týmů na technologii zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Osvědčený postup zabezpečení Azure 3 – Proces: přiřazení odpovědnosti za rozhodování o zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definování strategie zabezpečení sítě

**Pokyny:** Zřiďte postup pro zabezpečení sítě Azure jako součást celkové strategie řízení přístupu zabezpečení vaší organizace.  

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující prvky: 

-   Centralizovaná správa sítě a odpovědnost za zabezpečení

-   Model segmentace virtuální sítě v souladu se strategií segmentace podniku

-   Strategie náprav v různých situacích ohrožení a útoků

-   Strategie pro přechodový bod na internet a příchozí a odchozí přenosy

-   Strategie vzájemného propojení hybridního cloudu a místního připojení

-   Aktuální artefakty zabezpečení sítě (například síťové diagramy, referenční síťová architektura)

Další informace najdete v následujících referenčních materiálech:
- [Osvědčený postup zabezpečení Azure 11 – Architektura: jednotná strategie zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – zabezpečení sítě](../security/benchmarks/security-controls-v2-network-security.md)

- [Přehled zabezpečení sítě v Azure](../security/fundamentals/network-overview.md)

- [Strategie architektury podnikové sítě](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definování strategie identity a privilegovaného přístupu

**Pokyny:** Zřiďte postupy pro identitu a privilegovaný přístup Azure jako součást celkové strategie řízení přístupu zabezpečení vaší organizace.  

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující prvky: 

-   Centralizovaný systém identit a ověřování a jeho vzájemné propojení s dalšími interními a externími systémy identit

-   Metody silného ověřování v různých případech a podmínkách použití

-   Ochrana vysoce privilegovaných uživatelů

-   Monitorování a zpracování anomálních aktivit uživatelů  

-   Uživatelská identita a kontrola přístupu a proces odsouhlasení

Další informace najdete v následujících referenčních materiálech:

- [Azure Security Benchmark – správa identit](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark – privilegovaný přístup](../security/benchmarks//security-controls-v2-privileged-access.md)

- [Osvědčený postup zabezpečení Azure 11 – Architektura: jednotná strategie zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Přehled zabezpečení správy identit v Azure](../security/fundamentals/identity-management-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definování strategie protokolování a reakcí na hrozby

**Pokyny:** Nastavte strategii protokolování a reakce na hrozby, abyste mohli rychle detekovat a zmírňovat hrozby při současném splnění požadavků na dodržování předpisů. Jako prioritu si stanovte poskytování vysoce kvalitních upozornění a bezproblémových prostředí analytikům, aby se mohli soustředit na hrozby, a ne na integraci a ruční kroky. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující aspekty: 

-   Role a odpovědnosti organizace v oblasti operací zabezpečení (SecOps) 

-   Dobře definovaný proces reakce na incidenty, který je v souladu s NIST nebo jinou oborovou architekturou 

-   Shromažďování a uchovávání protokolů pro podporu detekce hrozeb, reakce na incidenty a požadavků na dodržování předpisů

-   Centralizované zobrazení a korelační informace o hrozbách, používání systému SIEM (správa akcí a informací o zabezpečení), nativních možností Azure a dalších zdrojů 

-   Plán komunikace a oznámení pro vaše zákazníky, dodavatele a veřejné partnery

-   Použití nativních platforem Azure a platforem třetích stran pro zpracování incidentů, jako je například protokolování a detekce hrozeb, forenzní zkoumání a náprava po útocích či jejich zneškodnění

-   Procesy pro zpracování incidentů a aktivity po incidentech, jako je poučení a uchovávání důkazů

Další informace najdete v následujících referenčních materiálech:

- [Azure Security Benchmark – protokolování a detekce hrozeb](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – reakce na incidenty](../security/benchmarks/security-controls-v2-incident-response.md)

- [Osvědčený postup zabezpečení Azure 4 – Proces: aktualizace procesů reakcí na incidenty pro cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Architektura přechodu na Azure a průvodce rozhodováním o protokolování a vytváření sestav](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Škálování, správa a monitorování Azure na podnikové úrovni](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](../security/benchmarks/overview.md).
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)
