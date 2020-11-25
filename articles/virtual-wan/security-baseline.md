---
title: Základní hodnoty zabezpečení Azure pro virtuální síť WAN
description: Základní základní údaje o zabezpečení sítě WAN poskytují pokyny a prostředky pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7f75f764f378118f9a34c1eee467e78ac279e19c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029043"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Základní hodnoty zabezpečení Azure pro virtuální síť WAN

Tato základní hodnota zabezpečení platí pro Microsoft Azure virtuální sítě WAN pokyny od [verze 2,0 Azure Security test](../security/benchmarks/overview.md) . Srovnávací test zabezpečení Azure poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Obsah je seskupen podle **ovládacích prvků zabezpečení** , které jsou definovány srovnávacím testem zabezpečení Azure, a souvisejícími pokyny platnými pro virtuální síť WAN. **Ovládací prvky** , které se nevztahují k virtuální síti WAN, se vyloučily.

Pokud chcete zjistit, jak se virtuální síť WAN kompletně mapuje na test zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení sítě WAN](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementace zabezpečení pro interní provoz

**Doprovodné** materiály: Microsoft Azure Virtual WAN poskytuje možnosti vlastního směrování a nabízí šifrování pro provoz ExpressRoute. Veškerou správu tras poskytuje směrovač virtuálního rozbočovače, který taky umožňuje přenosové připojení mezi virtuálními sítěmi. Šifrování provozu ExpressRoute pomocí virtuální sítě WAN zajišťuje zašifrovaný přenos mezi místními sítěmi a virtuálními sítěmi Azure přes ExpressRoute, aniž by bylo nutné přes veřejný Internet nebo používat veřejné IP adresy. 

- [Šifrování provozu ExpressRoute](virtual-wan-about.md#encryption)

- [Použití privátního odkazu ve virtuální síti WAN](howto-private-link.md)

- [Vlastní scénáře směrování](scenario-any-to-any.md)

- [Dokumentace k vlastní směrovací tabulce](how-to-virtual-hub-routing.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="ns-2-connect-private-networks-together"></a>NS-2: spojování privátních sítí 

**Doprovodné** materiály: Microsoft Azure ExpressRoute nabízí privátní připojení k Azure Virtual WAN. Vzhledem k tomu, že připojení ExpressRoute nepřecházejí přes veřejný Internet, ExpressRoute nabízí spolehlivější a rychlejší a nižší latenci než typická připojení k Internetu. Virtuální privátní síť můžete použít také k připojení k Azure prostřednictvím sítě VPN typu Site-to-Site (S2S) nebo VPN typu Point-to-Site (P2S).

- [ExpressRoute ve virtuální síti WAN](virtual-wan-expressroute-portal.md)

- [Přehled sítě VPN typu Site-to-site](virtual-wan-site-to-site-portal.md)

- [Přehled sítě VPN typu Point-to-site](virtual-wan-point-to-site-portal.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrana aplikací a služeb před útoky z externích sítí

**Pokyny**: virtuální síť WAN nevystavuje žádné koncové body externím sítím, které vyžadují, aby byly zabezpečeny pomocí konvenční ochrany sítě. K ochraně prostředků ve virtuálních sítích paprsků (libovolná virtuální síť připojená k virtuálnímu rozbočovači) pomocí služeb ochrany virtuální sítě můžete používat bezplatné služby. 

Pomocí Azure Firewall můžete chránit aplikace a služby před potenciálně škodlivým provozem z Internetu a dalších externích umístění. 

Pokud chcete svoje prostředky chránit před útoky na virtuální sítě Azure, vyberte DDoS Protection poskytované Azure. Použijte Azure Security Center ke zjištění rizikových konfigurací, které souvisejí s vašimi prostředky souvisejícími se sítí.

- [Dokumentace k Azure Firewall](/azure/firewall)

- [Správa Azure DDoS Protection Standard pomocí Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Doporučení pro Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: nasazení systémů pro zabránění vniknutí/prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: virtuální síť WAN je služba spravovaná Microsoftem. Nenabízí nativní detekci vniknutí ani možnosti prevence vniknutí. Služba Virtual Azure Firewall WAN ale nabízí funkce zabezpečení, které umožňují jednotný bod řízení zásad. Můžete vytvořit zásadu Azure Firewall a propojit zásadu s virtuálním rozbočovačem WAN, aby stávající virtuální síť WAN mohla fungovat jako zabezpečené virtuální rozbočovač, a to s požadovanými Azure Firewall nasazenými prostředky.

- [Konfigurace Azure Firewall ve virtuálním centru WAN](howto-firewall.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: zjednodušení pravidel zabezpečení sítě

**Pokyny**: Zjednodušte pravidla zabezpečení sítě pomocí Virtual Networkch značek služeb a definujte řízení přístupu k síti pro skupiny zabezpečení sítě nebo Azure firewall. Značky služeb lze použít místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby v poli zdroj nebo cíl pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Pochopení a použití značek služeb](../virtual-network/service-tags-overview.md)

- [Pochopení a použití skupin zabezpečení aplikací](/azure/virtual-network/security-overview#application-security-groups)

- [Dokumentace k Azure Firewall](/azure/firewall/)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Zabezpečená služba názvů domén (DNS)

**Doprovodné** materiály: zabezpečení funkcí DNS se poskytuje virtuální síti WAN s Azure firewall. Nakonfigurujte Azure Firewall fungovat jako proxy server DNS, který se stal zprostředkujícím pro žádosti DNS z klientských virtuálních počítačů na server DNS. Pro vlastní konfigurace serverů DNS povolte proxy serveru DNS, aby se zabránilo neshodě překladu názvů DNS, a v síťových pravidlech povolte filtrování plně kvalifikovaného názvu domény. 

- [Dokumentace k Azure Firewall](/azure/firewall/)

- [Azure Firewall nastavení DNS](/azure/firewall/dns-settings)

- [Použití Azure Firewall jako serveru DNS pro přeposílání s privátním odkazem](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [testování výkonnosti Azure Security: Správa identit](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardizace Azure Active Directory jako centrální systém pro ověřování a identifikaci

**Doprovodné** materiály: Azure Active Directory (Azure AD) je výchozí službou pro správu identit a přístupu pro služby Azure. včetně virtuální sítě WAN. Standardizace Azure AD pro řízení identity a správy přístupu vaší organizace v nástroji:

- Microsoft Cloud prostředky, jako jsou Azure Portal, Azure Storage, Virtual Machines Azure (Linux a Windows), Azure Key Vault, platforma jako služba (PaaS) a aplikace typu software jako služba (SaaS).
- Prostředky vaší organizace, jako jsou aplikace v Azure nebo podnikové síťové prostředky

Zabezpečte službu Azure AD jako vysoce prioritní v praxi cloudového zabezpečení vaší organizace. Vyhodnoťte svoji identitu a zabezpečení stav pomocí funkce skóre zabezpečení z Security Center k posouzení, jak úzce vaše konfigurace vyhovuje doporučením doporučeným postupem Microsoftu. Podle potřeby implementujte doporučení pro osvědčené postupy od Microsoftu pro vylepšení stav zabezpečení.

Azure AD podporuje také externí identity, které umožňují uživatelům bez účet Microsoft přihlašovat se k aplikacím a prostředkům s jejich externí identitou. 

Přečtěte si informace o používání Azure AD ve scénářích sítě VPN typu Point-to-site v odkazovaných odkazech.

- [Vytvoření tenanta Azure Active Directory (AD) pro připojení protokolu P2S OpenVPN](openvpn-azure-ad-tenant-multi-app.md)

- [Konfigurace ověřování Azure Active Directory pro uživatele VPN](virtual-wan-point-to-site-azure-ad.md)

- [Tenantů v Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: použití ovládacích prvků pro silné ověřování pro všechny přístupy založené na Azure Active Directory

**Doprovodné** materiály: v současné době se ověřování Azure Active Directory (Azure AD) poskytuje prostřednictvím integrace s virtuální sítí WAN typu Point-to-Site VPN.

Azure Active Directory (Azure AD) je výchozí službou pro správu identit a přístupu pro služby Azure. Azure AD podporuje ovládací prvky silného ověřování s využitím vícefaktorového ověřování a silných metod bez hesla.

Azure AD doporučuje pro ovládací prvky silného ověřování následující:

- Vícefaktorové ověřování – povolte vícefaktorové ověřování Azure AD a sledujte doporučení pro správu identit a přístupu v Azure Security Center, kde najdete doporučené postupy zabezpečení. Vynutili vícefaktorové ověřování u všech, vyberte možnost Uživatelé nebo na úrovni jednotlivých uživatelů na základě podmínek přihlášení a rizikových faktorů.

- Ověřování nepřipojeného hesla – k dispozici jsou tři možnosti ověřování s heslem. Mezi ně patří: Windows Hello pro firmy, Microsoft Authenticator aplikace a místní metody ověřování, jako jsou čipové karty.

Zajistěte, aby se nejvyšší úroveň metody silného ověřování používala pro správce a privilegované uživatele a aby mohli ostatní uživatelé zavést zásady silného ověřování.

- [Jak povolit vícefaktorové ověřování v P2S VPN pro virtuální síť WAN](openvpn-azure-ad-mfa.md)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM – 6: omezit přístup k prostředkům Azure na základě podmínek

**Pokyny**: povolení vícefaktorového ověřování Azure Active Directory (Azure AD) pro uživatele VPN (Point-to-site) s použitím ověřování Azure AD. Konfigurace vícefaktorového ověřování na základě jednotlivých uživatelů nebo využití vícefaktorového ověřování pomocí podmíněného přístupu Podmíněný přístup umožňuje přesnější kontrolu nad tím, jak by měl být povýšen druhý faktor. Umožňuje přiřazení vícefaktorového ověřování jenom k síti VPN a vyloučení dalších aplikací, které jsou vázané na tenanta Azure AD. 

Ověřování Azure AD je k dispozici pouze pro brány pomocí protokolu OpenVPN a klientů se systémem Windows.

- [Co je podmíněný přístup](../active-directory/conditional-access/overview.md)

- [Konfigurace ověřování Azure Active Directory pro uživatele VPN](virtual-wan-point-to-site-azure-ad.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Doprovodné** materiály: síť VPN typu Site-to-site ve virtuální síti WAN používá předsdílené klíče (PSK), které zákazník v jejich Azure Key Vault vytvořil a spravuje. Implementujte kontrolu přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

V případě GitHubu můžete k identifikaci přihlašovacích údajů nebo jiné formy tajných kódů v kódu použít funkci nativního prohledávání tajného klíče.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Kontrola tajného kódu GitHubu](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security benchmark: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: omezení přístupu pro správu na systémy kritické pro podnikání

**Pokyny**: Azure Virtual WAN používá řízení přístupu na základě role Azure (Azure RBAC) k izolaci přístupu k podnikovým systémům tím, že omezí přístup k předplatným a skupinám pro správu, ve kterých jsou udělen privilegovaný přístup.

Omezte také přístup k systémům pro správu, identitu a zabezpečení, které mají přístup pro správu k vašemu podnikovému přístupu, jako jsou Doména služby Active Directory řadiče, nástroje zabezpečení a nástroje pro správu systému s agenty nainstalovanými v podnikových důležitých systémech. Útočníci, kteří tyto systémy pro správu a zabezpečení ohrožují, si je můžou hned weaponize, aby vyzrazeni důležité obchodní prostředky.

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu.

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Přístup ke skupině pro správu](../governance/management-groups/overview.md#management-group-access) 

- [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: šifrování citlivých informací při přenosu

**Pokyny**: pro vaše požadavky na připojení použijte VPN typu Point-to-site, síť Site-to-Site VPN a šifrovanou Express Route s virtuální sítí WAN. Šifrování sítě VPN chrání data při přenosu z útoků typu "vzdálené správy" (například zachytávání přenosů), aby se zajistilo, že útočníci nemůžou data číst nebo upravovat. 

- [Point-to-site VPN](virtual-wan-point-to-site-portal.md)

- [Site-to-site VPN](virtual-wan-site-to-site-portal.md)

- [Šifrované ExpressRoute](vpn-over-expressroute.md)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

## <a name="asset-management"></a>Správa aktiv

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa prostředků](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>DOP. 1: Zajistěte, aby měl tým zabezpečení přehled o rizicích pro prostředky

**Doprovodné** materiály: Ujistěte se, že jsou bezpečnostním týmům udělená oprávnění čtenáře zabezpečení ve vašem tenantovi Azure a předplatných, aby mohli monitorovat bezpečnostní rizika pomocí Azure Security Center. 

V závislosti na tom, jak jsou strukturované zodpovědnosti týmu zabezpečení, může být monitorování rizik zabezpečení zodpovědností centrálního týmu zabezpečení nebo místního týmu. V takovém případě musí být přehledy a rizika zabezpečení vždy agregované centrálně v rámci organizace. 

Oprávnění čtenář zabezpečení je možné využít široce pro celého tenanta (kořenovou skupinu pro správu) nebo vymezenou pro skupiny pro správu nebo konkrétní předplatná. 

Poznámka: k získání přehledu o úlohách a službách může být potřeba další oprávnění. 

- [Přehled role čtecího modulu zabezpečení](../role-based-access-control/built-in-roles.md#security-reader)

- [Přehled služby Azure Skupiny pro správu](../governance/management-groups/overview.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>DOP. 2: Ujistěte se, že má tým zabezpečení přístup k inventáři prostředků a metadatům.

**Doprovodné** materiály: použití značek pro prostředky Azure, skupiny prostředků a předplatná k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí. Azure Virtual WAN taky podporuje nasazení prostředků na základě Azure Resource Manager, se kterými můžete exportovat šablony assetů. 

- [Průvodce rozhodováním ohledně pojmenování a označování prostředků](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Správa inventáře Azure Security Center prostředků](../security-center/asset-inventory.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: pomocí Azure monitor můžete vytvořit pravidla pro aktivaci výstrah při zjištění neschválené služby. Služba Virtual WAN přináší dohromady mnoho funkcí sítě, zabezpečení a směrování, aby poskytovala jediné provozní rozhraní. Brány VPN pro virtuální sítě WAN, brány ExpressRoute a Azure Firewall mají k dispozici protokolování a metriky prostřednictvím Azure Monitor. 
 

- [Protokoly a metriky virtuálních sítí WAN](logs-metrics.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: použití podmíněného přístupu Azure k omezení schopnosti uživatelů pracovat se správcem prostředků Azure pomocí konfigurace "blokování přístupu" pro aplikaci "Microsoft Azure Management".

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: povolení detekce hrozeb pro prostředky Azure

**Pokyny**: síť VPN typu Point-to-site s virtuální sítí WAN je integrovaná s Azure Active Directory (Azure AD). Azure AD poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD, které jsou integrované s Azure Monitor, Azure Sentinel, SIEM nebo monitorovací nástroje pro složitější účely monitorování a analýzy hrozeb. Jsou to:

- Přihlásit se – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny provedené u všech prostředků v rámci služby Azure AD, například přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.
- Rizikové přihlášení – rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Pomocí Azure Security Center můžete vytvářet výstrahy pro určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření, včetně zastaralých účtů v rámci předplatného. Kromě základního monitorování hygieny zabezpečení použijte modul ochrany před internetovými útoky z Security Center k shromažďování podrobnějších výstrah zabezpečení z jednotlivých výpočetních prostředků Azure (virtuálních počítačů, kontejnerů, aplikační služby), datových prostředků (SQL DB a úložiště) a vrstev služeb Azure. Díky této funkci můžete mít přehled o anomáliích v účtech v jednotlivých prostředcích.

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Povolit Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurace Azure Firewall ve virtuálním centru WAN](howto-firewall.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: povolení detekce hrozeb pro správu identit a přístupu v Azure

**Pokyny**: síť VPN typu Point-to-site s virtuální sítí WAN je integrovaná s Azure Active Directory (Azure AD). Azure AD poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD, které jsou integrované s Azure Monitor, Azure Sentinel, SIEM nebo monitorovací nástroje pro složitější účely monitorování a analýzy hrozeb. Jsou to:

- Přihlásit se – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny provedené u všech prostředků v rámci služby Azure AD, například přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.
- Rizikové přihlášení – rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Pomocí Azure Security Center můžete vytvářet výstrahy pro určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření, včetně zastaralých účtů v rámci předplatného. Kromě základního monitorování hygieny zabezpečení použijte modul ochrany před internetovými útoky z Security Center k shromažďování podrobnějších výstrah zabezpečení z jednotlivých výpočetních prostředků Azure (virtuálních počítačů, kontejnerů, aplikační služby), datových prostředků (SQL DB a úložiště) a vrstev služeb Azure. Díky této funkci můžete mít přehled o anomáliích v účtech v jednotlivých prostředcích.

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Povolit Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurace Azure Firewall ve virtuálním centru WAN](howto-firewall.md)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: povolení protokolování pro síťové aktivity Azure

**Pokyny**: monitorování Azure Virtual WAN pomocí Azure monitor. Služba Virtual WAN přináší dohromady mnoho funkcí sítě, zabezpečení a směrování, aby poskytovala jediné provozní rozhraní. Brány VPN pro virtuální sítě WAN, brány ExpressRoute a Azure Firewall mají k dispozici protokolování a metriky prostřednictvím Azure Monitor. Položky protokolu aktivit jsou shromažďovány ve výchozím nastavení a lze je zobrazit v Azure Portal. K zobrazení všech operací odeslaných do vašeho předplatného Azure můžete použít protokoly aktivit Azure (dřív označované jako operační protokoly a protokoly auditu).

Pro virtuální síť WAN jsou k dispozici také různé diagnostické protokoly a lze je nakonfigurovat pro prostředek virtuální sítě WAN pomocí Azure Portal.  Můžete se rozhodnout, že se má Log Analytics, streamovat do centra událostí nebo jednoduše archivovat do účtu úložiště. 
 

- [Protokoly a metriky virtuálních sítí WAN](logs-metrics.md)

- [Metriky a protokoly Azure Firewallu](/azure/firewall/logs-and-metrics)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: povolení protokolování pro prostředky Azure

**Pokyny**: protokoly aktivit Azure, povolené automatické, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky Azure Virtual WAN s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

Povolte protokoly prostředků Azure pro virtuální síť WAN. Pomocí Azure Security Center a Azure Policy můžete povolit protokoly prostředků a shromažďovat data protokolů. Tyto protokoly můžou být klíčové pro pozdější zkoumání incidentů zabezpečení a provádění forenzních cvičení.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Pochopení Azure Security Center shromažďování dat](../security-center/security-center-enable-data-collection.md)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizace správy protokolu zabezpečení a analýzy

**Pokyny**: povolení protokolování zabezpečení pro virtuální síť WAN s Azure monitor. Služba Virtual WAN přináší dohromady mnoho funkcí sítě, zabezpečení a směrování, aby poskytovala jediné provozní rozhraní. Brány VPN pro virtuální sítě WAN, brány ExpressRoute a Azure Firewall mají k dispozici protokolování a metriky prostřednictvím Azure Monitor. Položky protokolu aktivit jsou shromažďovány ve výchozím nastavení a lze je zobrazit v Azure Portal. K zobrazení všech operací odeslaných do vašeho předplatného Azure můžete použít protokoly aktivit Azure (dřív označované jako operační protokoly a protokoly auditu). 

Pro virtuální síť WAN jsou k dispozici také různé diagnostické protokoly a lze je nakonfigurovat pro prostředek virtuální sítě WAN pomocí Azure Portal. Odeslání do Log Analytics, streamování do centra událostí nebo pouhá archivace na účet úložiště. Kromě toho povolte a zaveďte data do Azure Sentinel nebo řešení pro správu událostí a zabezpečení třetí strany. 
 

- [Protokoly a metriky virtuálních sítí WAN](logs-metrics.md)

- [Metriky a protokoly Azure Firewallu](/azure/firewall/logs-and-metrics)

Zabezpečení Azure Virtual WAN se poskytuje prostřednictvím Azure Firewall. 

- [Dokumentace k Azure Firewall](/azure/firewall/overview)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurace uchovávání úložiště protokolu

**Doprovodné** materiály: Konfigurace uchovávání protokolů podle požadavků na dodržování předpisů, nařízení a podnikových požadavků. V Azure Monitor můžete nastavit dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte Azure Storage, Data Lake nebo Log Analytics účty pracovního prostoru pro dlouhodobé a archivační úložiště.

- [Změnit dobu uchovávání dat v Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center upozornění a export doporučení](../security-center/continuous-export.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – aktualizace procesu reakce na incidenty pro Azure

**Doprovodné** materiály: Ujistěte se, že vaše organizace má procesy, které reagují na incidenty zabezpečení, aktualizovala tyto procesy pro Azure a pravidelně je vykonává za účelem zajištění připravenosti.

- [Implementace zabezpečení v podnikovém prostředí](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referenční příručka k reakci na incidenty](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Příprava – oznámení o nastavení incidentu

**Doprovodné** materiály: nastavte kontaktní informace incidentu zabezpečení v Azure Security Center. Tyto kontaktní informace používá společnost Microsoft k tomu, aby vás kontaktovala v případě, že služba MSRC (Microsoft Security Response Center) zjistí, že vaše data jsou přístupná ze strany, která není protiprávní nebo oprávněná. Máte také možnost přizpůsobit upozornění na incidenty a oznámení v různých službách Azure v závislosti na potřebách reakce na incidenty. 

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detekce a analýza – vytvoření incidentů na základě výstrah s vysokou kvalitou

**Doprovodné** materiály: Ujistěte se, že máte proces pro vytváření vysoce kvalitních výstrah a měření kvality výstrah. To vám umožní naučit se lekce z minulých incidentů a upřednostnit výstrahy pro analytiky, takže nemusejí ztrácet čas u falešně pozitivních výsledků. 

Výstrahy s vysokou kvalitou se dají vytvářet na základě zkušeností z minulých incidentů, ověřených zdrojů komunity a nástrojů určených k vygenerování a vyčištění výstrah odmítání a korelace různých zdrojů signálu. 

Azure Security Center poskytuje výstrahy vysoké kvality napříč mnoha prostředky Azure. K streamování výstrah do Azure Sentinel můžete použít datový konektor ASC. Azure Sentinel umožňuje vytvářet Pokročilá pravidla výstrah pro automatické generování incidentů pro účely šetření. 

Vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce exportu, která vám usnadní identifikaci rizik pro prostředky Azure. Vyexportujte výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem.

- [Postup konfigurace exportu](../security-center/continuous-export.md)

- [Jak streamovat výstrahy do Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: detekce a analýza – prozkoumání incidentu

**Doprovodné** materiály: Zajistěte, aby Analytiki mohli zadávat dotazy a používat různé zdroje dat při zkoumání potenciálních incidentů, abyste mohli vytvořit úplný přehled o tom, co se stalo. K tomu, aby bylo možné sledovat aktivity potenciálního útočníka v rámci dezaktivačního řetězu, by měly být shromažďovány různé protokoly.  Měli byste taky zajistit, aby byly informace o studiu a učení pro jiné analytiky a budoucí historické odkazy.  

Zdroje dat pro účely šetření zahrnují centralizované zdroje protokolování, které jsou již shromažďovány ze služeb v oboru a spuštěných systémů, ale mohou také zahrnovat:

- Síťová data – pomocí protokolů toků služby skupiny zabezpečení sítě, Azure Network Watcher a Azure Monitor zaznamenává protokoly toku sítě a další informace o analýze. 

- Snímky spuštěných systémů: 

    - Použijte schopnost snímku virtuálního počítače Azure a vytvořte snímek disku běžícího systému. 

    - Pomocí funkce nativního výpisu paměti operačního systému vytvořte snímek paměti běžícího systému.

    - Pomocí funkce snímků služeb Azure nebo vlastní schopnosti svého softwaru můžete vytvářet snímky spuštěných systémů.

Azure Sentinel poskytuje rozsáhlou analýzu dat napříč všemi zdroji protokolů a portálem pro správu případů, který umožňuje spravovat úplný životní cyklus incidentů. Informace o dataintelligence během šetření můžou být přidruženy k incidentu pro účely sledování a vytváření sestav. 

- [Snímek disku s počítačem s Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Vytvoření snímku disku počítače se systémem Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure podporu pro diagnostické informace a shromažďování výpisu paměti](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Prozkoumat incidenty pomocí služby Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detekce a analýza – určení priorit incidentů

**Doprovodné** materiály: Poskytněte kontext analytikům, na kterých incidenty zaměřte na první, na základě závažnosti výstrahy a citlivosti prostředků. 

Azure Security Center přiřadí každému upozornění závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Navíc můžete označovat prostředky pomocí značek a vytvořit systém pojmenování k identifikaci a kategorizaci prostředků Azure, zejména těch, které zpracovávají citlivá data.  Máte zodpovědnost za to, že je možné určit prioritu nápravy výstrah na základě závažnosti prostředků a prostředí Azure, ve kterých došlo k incidentu.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: zahrnutí, vyhubení a obnovení – automatizace zpracování incidentů

**Doprovodné** materiály: automatizace ručních opakujících se úloh, které urychlují dobu odezvy a snižují zátěž analytiků. Ruční provádění úloh trvá déle, což zpomaluje jednotlivé incidenty a snižuje počet incidentů, které může analytik zvládnout. Ruční úlohy také zvyšují únavu analytiků, což zvyšuje riziko lidské chyby, která způsobuje zpoždění, a snižuje schopnost analytiků efektivně se zaměřit na složité úlohy. Pomocí funkcí automatizace pracovních postupů v Azure Security Center a Azure Sentinel můžete automaticky aktivovat akce nebo spustit PlayBook a reagovat na příchozí výstrahy zabezpečení. PlayBook provádí akce, jako je odesílání oznámení, zakázání účtů a izolace problematických sítí. 

- [Konfigurace automatizace pracovního postupu v Security Center](../security-center/workflow-automation.md)

- [Nastavení automatických odpovědí na hrozby v Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Nastavení automatických odpovědí na hrozby v Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="posture-and-vulnerability-management"></a>Stav a Správa ohrožení zabezpečení

*Další informace najdete v článku o [stav a správě ohrožení zabezpečení Azure Security test](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: provedení pravidelné simulace útoků

**Doprovodné** materiály: jak je potřeba, proveďte testování průniku nebo červené týmové aktivity na svých prostředcích Azure a zajistěte nápravu všech kritických zjištění zabezpečení.
Postupujte podle pravidel pro testování průniku Microsoft Cloud a zajistěte, aby testy průniku nenarušily zásady Microsoftu. Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft.

- [Testování průniku v Azure](../security/fundamentals/pen-testing.md)

- [Pravidla testování průniku pro zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="endpoint-security"></a>Zabezpečení koncového bodu

*Další informace najdete v tématu [srovnávací zabezpečení Azure Security: Endpoint Security](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: použití detekce a odpovědi koncového bodu (EDR)

**Doprovodné** materiály: zákazníkům není explicitně povoleno konfigurovat zjišťování koncových bodů a nastavení odpovědí. Virtual Machines v nabídce Azure Virtual WAN ale tyto možnosti používají. Přečtěte si další informace o těchto obecných funkcích na odkazovaných odkazech. 

- [Přehled rozšířené ochrany před internetovými útoky v programu Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Služba ochrany ATP v programu Microsoft Defender pro servery Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Služba ochrany ATP v programu Microsoft Defender pro servery jiné než Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

## <a name="governance-and-strategy"></a>Zásady správného řízení a strategie

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zásady správného řízení a strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definování správy prostředků a strategie ochrany dat 

**Doprovodné** materiály: Ujistěte se, že máte dokument a sdělíte jasné strategii pro nepřetržité monitorování a ochranu systémů a dat. Určení priorit zjišťování, hodnocení, ochrany a monitorování důležitých podnikových dat a systémů. 

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

-   Standardní klasifikace dat v souladu s obchodními riziky

-   Organizace zabezpečení – přehled o rizicích a inventáři prostředků 

-   Schválení organizace služby Azure pomocí organizace zabezpečení 

-   Zabezpečení prostředků prostřednictvím jejich životního cyklu

-   Požadovaná strategie řízení přístupu v souladu s klasifikací dat organizace

-   Používání funkcí Azure Native a ochrany dat třetích stran

-   Požadavky na šifrování dat pro případy použití v přenosech a v REST

-   Příslušné kryptografické standardy

Další informace najdete v následujících odkazech:
- [Doporučení architektury zabezpečení Azure – úložiště, data a šifrování](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Základy zabezpečení Azure – zabezpečení, šifrování a úložiště dat Azure](../security/fundamentals/encryption-overview.md)

- [Rozhraní pro přijetí do cloudu – osvědčené postupy zabezpečení a šifrování dat Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Test zabezpečení Azure – Správa prostředků](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security test – ochrana dat](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definování strategie podnikové segmentace 

**Doprovodné** materiály: zřízení strategie pro celé podniky za účelem segmentace přístupu k assetům pomocí kombinace identity, sítě, aplikace, předplatného, skupiny pro správu a dalších ovládacích prvků.

Pečlivě vyvážit nutnost oddělení zabezpečení s nutností povolit každodenní provoz systémů, které musí vzájemně komunikovat, a přistupovat k datům.

Zajistěte, aby se strategie segmentace implementovala konzistentně napříč typy ovládacích prvků včetně zabezpečení sítě, modelů identit a přístupu a oprávnění aplikace/modelů přístupu a ovládacích prvků pro lidské procesy.

- [Doprovodné materiály k strategii segmentace v Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Doprovodné materiály k strategii segmentace v Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Sjednotit segmentaci sítě pomocí strategie segmentace v podniku](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definování strategie správy stav zabezpečení

**Doprovodné** materiály: nepřetržitě měřit a zmírnit rizika pro jednotlivé prostředky a prostředí, ve kterém jsou hostovány. Určete prioritu prostředků s vysokou hodnotou a vysoce vydaných ploch útoků, jako jsou publikované aplikace, příchozí a výstupní body sítě, koncové body uživatele a správce atd.

- [Azure Security test – stav a Správa ohrožení zabezpečení](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: zarovnání rolí organizace, odpovědností a accountabilities

**Doprovodné** materiály: Ujistěte se, že máte dokument a komunikujete s jasnými strategiemi pro role a zodpovědnost ve vaší organizaci zabezpečení. Stanovte prioritu poskytování jasných zodpovědností při rozhodování o zabezpečení, informování všech uživatelů na sdílené zodpovědnosti a vzdělávání technických týmů na technologii pro zabezpečení cloudu.

- [Osvědčené postupy zabezpečení Azure 1 – lidé: vzdělávání týmů na cestě cloudového zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Osvědčené postupy zabezpečení Azure 2 – lidé: vzdělávání týmů na technologii Cloud Security](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Osvědčené postupy zabezpečení Azure 3 – proces: přiřazení zodpovědnosti pro rozhodování o zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definování strategie zabezpečení sítě

**Doprovodné** materiály: navažte přístup k zabezpečení sítě Azure v rámci celkové strategie řízení přístupu zabezpečení vaší organizace.  

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

-   Centralizovaná správa sítě a zodpovědnost zabezpečení

-   Model segmentace virtuální sítě zarovnané na strategii podnikového segmentace

-   Strategie oprav v různých scénářích hrozeb a útoků

-   Strategie pro Internet Edge a příchozí a odchozí přenosy

-   Hybridní cloudová a místní vzájemné propojení strategie

-   Aktuální artefakty zabezpečení sítě (například síťové diagramy, referenční Síťová architektura)

Další informace najdete v následujících odkazech:
- [Osvědčené postupy zabezpečení Azure 11 – architektura. Jednotná strategie zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Testování zabezpečení Azure – zabezpečení sítě](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Přehled zabezpečení sítě Azure](../security/fundamentals/network-overview.md)

- [Strategie architektury podnikové sítě](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definování strategie identity a privilegovaného přístupu

**Doprovodné** materiály: navažte přístup k identitě Azure a privilegovanému přístupu v rámci celkové strategie řízení přístupu zabezpečení vaší organizace.  

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

-   Centralizovaný systém identit a ověřování a jeho vzájemné propojení s dalšími interními a externími systémy identit

-   Metody silného ověřování v různých případech a podmínkách použití

-   Ochrana vysoce privilegovaných uživatelů

-   Anomálie monitorování a zpracování aktivit uživatelů  

-   Uživatelská identita a kontrola přístupu a proces odsouhlasení

Další informace najdete v následujících odkazech:

- [Azure Security benchmark – Správa identit](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security test – privilegovaný přístup](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Osvědčené postupy zabezpečení Azure 11 – architektura. Jednotná strategie zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Přehled zabezpečení služby Azure Identity Management](../security/fundamentals/identity-management-overview.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definování strategie protokolování a reakce na hrozby

**Doprovodné** materiály: vytvoření strategie protokolování a reakce na hrozby pro rychlé detekci a nápravu hrozeb při splnění požadavků na dodržování předpisů. Určete prioritu poskytování analytiků s vysoce kvalitními výstrahami a bezproblémové prostředí, aby se mohly soustředit na hrozby, nikoli na integraci a ruční kroky. 

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

-   Role a odpovědnosti organizace v oblasti operací zabezpečení (SecOps) 

-   Dobře definovaný proces reakce na incidenty, který je v souladu s NIST nebo jiným oborovým rozhraním 

-   Shromažďování a uchovávání protokolů pro podporu detekce hrozeb, reakce na incidenty a požadavků na dodržování předpisů

-   Centralizované zobrazení a korelační informace o hrozbách, používání SIEM, nativních možností Azure a dalších zdrojů 

-   Komunikace a plán oznámení vašim zákazníkům, dodavatelům a veřejným stranám zájmu

-   Použití nativních platforem Azure a platforem třetích stran pro zpracování incidentů, jako je například protokolování a detekce hrozeb, forenzní a náprava či eradikace útoků

-   Procesy pro zpracování incidentů a aktivit po incidentech, jako jsou zjištěné lekce a uchovávání důkazů

Další informace najdete v následujících odkazech:

- [Azure Security test benchmark – protokolování a detekce hrozeb](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Srovnávací test zabezpečení Azure – odpověď na incident](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Doporučený postup zabezpečení Azure 4 – proces. Aktualizace procesů reakce na incidenty pro Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Průvodce rozhodnutím o přijetí, protokolování a vytváření sestav pro Azure](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure Enterprise Scale, Management a monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu [Přehled Azure Security benchmark v2](/azure/security/benchmarks/overview) .
- Další informace o [plánech zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
