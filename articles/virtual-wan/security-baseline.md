---
title: Základní hodnoty zabezpečení Azure pro virtuální síť WAN
description: Základní základní údaje o zabezpečení sítě WAN poskytují pokyny a prostředky pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 409c8d163b52e56a1eef68a23e4db79956a68722
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328677"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Základní hodnoty zabezpečení Azure pro virtuální síť WAN

Tato základní hodnota zabezpečení platí pro Microsoft Azure virtuální sítě WAN pokyny od [verze 2,0 Azure Security test](../security/benchmarks/overview.md) . Srovnávací test zabezpečení Azure poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Obsah je seskupen podle **ovládacích prvků zabezpečení** , které jsou definovány srovnávacím testem zabezpečení Azure, a souvisejícími pokyny platnými pro virtuální síť WAN. **Ovládací prvky** , které se nevztahují k virtuální síti WAN, se vyloučily.

Pokud chcete zjistit, jak se virtuální síť WAN kompletně mapuje na test zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení sítě WAN](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementace zabezpečení pro interní provoz

**Doprovodné** materiály: Azure Virtual WAN nepodporuje nasazování přímo do virtuální sítě. Můžete ale přesto použít pravidla skupiny zabezpečení sítě u prostředků paprsků, použít ochrany Azure Firewall nebo vytvořit vlastní směrovací tabulky, abyste zabránili nebo povolili privátní provoz.

- [Vlastní scénáře směrování](scenario-any-to-any.md)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="ns-2-connect-private-networks-together"></a>NS-2: spojování privátních sítí 

**Pokyny** : pomocí Azure ExpressRoute nebo virtuální privátní sítě (VPN) Azure můžete vytvořit privátní připojení mezi datacentry Azure a místní infrastrukturou v prostředí kolokace. Připojení ExpressRoute nevyužívají veřejný Internet, nabízejí spolehlivější, rychlejší a nižší latenci než typická připojení k Internetu. 

Pro VPN typu Point-to-site a VPN typu Site-to-site připojte místní zařízení nebo sítě k virtuální síti pomocí libovolné kombinace těchto možností sítě VPN a ExpressRoute. K propojení dvou nebo více virtuálních sítí v Azure použijte Virtual Network partnerský vztah. Síťový provoz mezi partnerskými virtuálními sítěmi je privátní a udržuje se v páteřní síti Azure.

- [ExpressRoute ve virtuální síti WAN](virtual-wan-expressroute-portal.md)

- [Přehled sítě VPN typu Site-to-site](virtual-wan-site-to-site-portal.md)

- [Přehled sítě VPN typu Point-to-site](virtual-wan-point-to-site-portal.md)

- [Privátní propojení](howto-private-link.md)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Odpovědnost** : sdílená

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: vytvoření přístupu privátní sítě ke službám Azure

**Pokyny** : použití privátního odkazu Azure k povolení privátního přístupu k Azure Virtual WAN z vašich virtuálních sítí bez přechodu na Internet. Privátní přístup je kromě ověřování a zabezpečení provozu, které nabízí služby Azure, další opatření pro důkladnou hloubku.

- [Principy privátního odkazu Azure](../private-link/private-link-overview.md)

- [Privátní propojení virtuálních sítí WAN](howto-private-link.md)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrana aplikací a služeb před útoky z externích sítí

**Doprovodné** materiály: Chraňte svoje prostředky Azure Virtual WAN proti útokům z externích sítí, včetně útoků DDoS (Distributed Denial of Service), útoků specifických pro aplikace a nevyžádaných a potenciálně škodlivých internetových přenosů. 

Pomocí Azure Firewall můžete chránit aplikace a služby před potenciálně škodlivým provozem z Internetu a dalších externích umístění. Vyberte Azure DDoS Protection pro vaše prostředky před útoky ve virtuálních sítích Azure. Použijte Azure Security Center k detekci rizik s konfigurací, které souvisí s vašimi prostředky souvisejícími se sítí.

- [Dokumentace k Azure Firewall](/azure/firewall)

- [Správa Azure DDoS Protection Standard pomocí Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Doporučení pro Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Monitorování Azure Security Center** : Ano

**Odpovědnost** : sdílená

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: nasazení systémů pro zabránění vniknutí/prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: nejlepší způsob, jak nasadit systémy zjišťování nebo prevence vniknutí do Azure Virtual WAN, je použití síťového virtuálního zařízení v sítích paprsků připojených k virtuálnímu rozbočovači.  Další informace jsou k dispozici ve scénářích směrování na odkazovaných odkazech. 

- [Scénář: Směrování provozu přes síťové virtuální zařízení](scenario-route-through-nva.md)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: zjednodušení pravidel zabezpečení sítě

**Doprovodné** materiály: pomocí značek služeb Azure Virtual Network můžete definovat řízení přístupu k síti ve skupinách zabezpečení sítě Azure nebo Azure firewall nakonfigurovaných pro virtuální sítě WAN. 

Při vytváření pravidel zabezpečení používejte značky služby místo konkrétních IP adres. Zadáním názvu značky služby (například {VirtualWAN: Virtual Network}) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Zabezpečená služba názvů domén (DNS)

**Doprovodné** materiály: Azure Virtual WAN nabízí vlastní servery DNS pro brány VPN typu Point-to-site. 

Dodržujte osvědčené postupy pro zabezpečení DNS, které vám umožní zmírnit časté útoky, jako je dangling DNS, útoky na zesílení DNS, poškození DNS a falšování identity atd.

Pokud se jako autoritativní služba DNS používá Azure DNS, zajistěte, aby byly zóny a záznamy DNS chráněné před náhodnými nebo škodlivými úpravami pomocí řízení přístupu založeného na rolích Azure (Azure RBAC) a zámků prostředků.

- [Přehled Azure DNS](../dns/dns-overview.md) 

- [Příručka pro nasazení zabezpečení DNS (Domain Name System)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Zabránit položkám DNS v dangling a vyhnout se převzetí subdomény](../security/fundamentals/subdomain-takeover.md)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [testování výkonnosti Azure Security: Správa identit](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardizace Azure Active Directory jako centrální systém pro ověřování a identifikaci

**Pokyny** : síť VPN typu Point-to-site pro Azure Virtual WAN je integrovaná s Azure Active Directory (Azure AD), což je výchozí služba pro správu identit a přístupu v Azure. Službu Azure AD byste měli standardizovat, abyste mohli řídit identitu a správu přístupu vaší organizace v nástroji:

- Microsoft Cloud prostředky, jako jsou Azure Portal, Azure Storage, aplikace Azure Virtual Machines (Linux a Windows), Azure Key Vault, PaaS a SaaS.
- Prostředky vaší organizace, jako jsou aplikace v Azure nebo podnikové síťové prostředky.

Zabezpečte službu Azure AD s vysokou prioritou v praxi cloudového zabezpečení vaší organizace. Posuzujete svou identitu a zabezpečení stav pomocí funkce skóre zabezpečení Azure Security Center k posouzení, jak úzce vyhovuje vaše konfigurace doporučení osvědčených postupů. Implementujte doporučení pro osvědčené postupy od Microsoftu pro vylepšení stav zabezpečení.

Azure AD podporuje také externí identity, které umožňují uživatelům bez účet Microsoft přihlašovat se k aplikacím a prostředkům s jejich externí identitou. Přečtěte si informace o používání Azure AD ve scénářích sítě VPN typu Point-to-site v odkazovaných odkazech.

- [Vytvoření tenanta Azure Active Directory (AD) pro připojení protokolu P2S OpenVPN](openvpn-azure-ad-tenant-multi-app.md)

- [Konfigurace ověřování Azure Active Directory pro uživatele VPN](virtual-wan-point-to-site-azure-ad.md)

- [Tenantů v Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Použít externí zprostředkovatele identity pro aplikaci](/azure/active-directory/b2b/identity-providers)

**Monitorování Azure Security Center** : Ano

**Odpovědnost** : sdílená

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: použití ovládacích prvků pro silné ověřování pro všechny přístupy založené na Azure Active Directory

**Pokyny** : síť VPN typu Point-to-site pro Azure Virtual WAN je integrovaná s Azure Active Directory (Azure AD), která podporuje ovládací prvky silného ověřování s vícefaktorového ověřováním a s metodami silného hesla.

- Vícefaktorové ověřování – povolte vícefaktorové ověřování Azure AD a sledujte doporučení pro správu identit a přístupu Azure Security Center, které se týkají doporučených postupů pro nastavení vícefaktorového ověřování. Vícefaktorové ověřování se dá vyhovět všem, vybírat uživatele nebo na úrovni jednotlivých uživatelů na základě podmínek přihlášení a rizikových faktorů.

- Ověřování nepřipojeného hesla – k dispozici jsou tři možnosti ověřování s heslem. Patří mezi ně metody Windows Hello pro firmy, Microsoft Authenticator aplikace a místní ověřování, jako jsou čipové karty.

Pro správce a privilegované uživatele zajistěte, aby se používala nejvyšší úroveň metody silného ověřování, následovaná zavedením příslušných zásad silného ověřování jiným uživatelům.

- [Jak povolit vícefaktorové ověřování v P2S VPN pro virtuální síť WAN](openvpn-azure-ad-mfa.md)

**Monitorování Azure Security Center** : Ano

**Odpovědnost** : sdílená

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM – 6: omezit přístup k prostředkům Azure na základě podmínek

**Pokyny** : síť VPN typu Point-to-site pro Azure Virtual WAN podporuje podmíněný přístup Azure AD pro přesnější řízení přístupu na základě uživatelem definovaných podmínek. Například přihlášení uživatelů z určitých rozsahů IP adres musí používat vícefaktorové ověřování pro přihlášení. Podrobné zásady správy relace ověřování se dají použít i pro různé případy použití.

- [Společné zásady podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurace správy relace ověřování pomocí podmíněného přístupu](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

- [Podmíněný přístup k síti VPN Virtual WAN P2S](openvpn-azure-ad-mfa.md#conditional)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny** : implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

V případě GitHubu můžete k identifikaci přihlašovacích údajů nebo jiné formy tajných kódů v kódu použít funkci nativního prohledávání tajného klíče.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Kontrola tajného kódu GitHubu](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security benchmark: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: omezení přístupu pro správu na systémy kritické pro podnikání

**Pokyny** : Azure Virtual WAN používá řízení přístupu na základě role Azure (Azure RBAC) k izolaci přístupu k podnikovým systémům tím, že omezí přístup k předplatným a skupinám pro správu, ve kterých jsou udělen privilegovaný přístup.

Omezte také přístup k systémům pro správu, identitu a zabezpečení, které mají přístup pro správu k vašemu podnikovému přístupu, jako jsou Doména služby Active Directory řadiče, nástroje zabezpečení a nástroje pro správu systému s agenty nainstalovanými v podnikových důležitých systémech. Útočníci, kteří tyto systémy pro správu a zabezpečení ohrožují, si je můžou hned weaponize, aby vyzrazeni důležité obchodní prostředky.

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu.

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Přístup ke skupině pro správu](../governance/management-groups/overview.md#management-group-access) 

- [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: použití pracovních stanic s privilegovaným přístupem

**Doprovodné** materiály: zabezpečené, izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a kritické operátory služby. Pro úlohy správy použijte vysoce zabezpečenou pracovní stanici uživatelů nebo Azure bastionu. Pomocí Azure Active Directory (Azure AD), programu Microsoft Defender Advanced Threat Protection (ATP) nebo Microsoft Intune nasaďte zabezpečenou a spravovanou pracovní stanici uživatelů pro úlohy správy. Zabezpečená pracovní stanice se dají centrálně spravovat tak, aby vynutila zabezpečenou konfiguraci, včetně silného ověřování, standardních hodnot softwaru a hardwaru, omezeného logického a síťového přístupu.

- [Pochopení pracovních stanic s privilegovaným přístupem](../active-directory/devices/concept-azure-managed-workstation.md)

- [Nasazení privilegované pracovní stanice přístupu](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: šifrování citlivých informací při přenosu

**Doprovodné** materiály: šifrování je klíčové pro provoz na externích a veřejných sítích.

- Použijte řízení přístupu a přenos dat by se měl chránit před útoky typu "vzdálené správy" (například zachytávání provozu) pomocí šifrování, aby mohli útočníci snadno číst nebo upravovat data.

- Zajistěte přenos přes protokol HTTP, aby všichni klienti připojující se k prostředkům Azure mohli vyjednávat TLS v 1.2 nebo vyšší.-

- Pro vzdálenou správu použijte místo nešifrovaného protokolu SSH (pro Linux) nebo RDP/TLS (pro Windows). Zastaralé verze protokolu SSL/TLS/SSH, protokoly a slabé šifry by měly být zakázány.-

- V základní infrastruktuře poskytuje Azure data při přenosu dat mezi datacentry Azure ve výchozím nastavení.

Obecně poskytujeme šifrování na zabezpečené páteřní síti Microsoftu a příležitostech k šifrování provozu ve vaší síti VPN typu Site-to-Site VPN přes Azure ExpressRoute a v síti VPN typu Point-to-site.

- [Pochopení šifrování při přenosu pomocí Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informace o zabezpečení TLS](/security/engineering/solving-tls1-problem)

- [Dvojité šifrování pro Azure data při přenosu](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitorování Azure Security Center** : Ano

**Odpovědnost** : sdílená

## <a name="asset-management"></a>Správa aktiv

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa prostředků](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>DOP. 2: Ujistěte se, že má tým zabezpečení přístup k inventáři prostředků a metadatům.

**Doprovodné** materiály: použití značek pro prostředky Azure, skupiny prostředků a předplatná k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

Azure Virtual WAN taky podporuje nasazení prostředků založené na Azure Resource Manager a dotazy na Azure Resource Graph. 

- [Další informace o označování prostředků najdete v průvodci pro pojmenování a označení prostředku.](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md) 

- [Správa inventáře Azure Security Center prostředků](../security-center/asset-inventory.md)

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: pomocí Azure Policy můžete omezit služby, které lze ve vašem prostředí zřídit. Dotazování a zjišťování prostředků pomocí Azure Resource graphu v rámci předplatných a použití Azure Monitor k vytvoření pravidel pro aktivaci výstrah při zjištění neschválené služby.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny** : použití podmíněného přístupu Azure k omezení schopnosti uživatelů pracovat se správcem prostředků Azure pomocí konfigurace "blokování přístupu" pro aplikaci "Microsoft Azure Management".

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: povolení detekce hrozeb pro správu identit a přístupu v Azure

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD, které jsou integrované s Azure monitor, Azure Sentinel, Siem nebo monitorovací nástroje pro výkonnější monitorování a analytické případy použití. Jsou to:

- Přihlásit se – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny provedené u všech prostředků v rámci služby Azure AD, například přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.
- Rizikové přihlášení – rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Pomocí Azure Security Center můžete vytvářet výstrahy pro určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření, včetně zastaralých účtů v rámci předplatného. Kromě základního monitorování hygieny zabezpečení použijte modul ochrany před internetovými útoky Security Center ke shromažďování podrobnějších výstrah zabezpečení z jednotlivých výpočetních prostředků Azure (virtuálních počítačů, kontejnerů, aplikační služby), datových prostředků (SQL DB a úložiště) a vrstev služeb Azure. Díky této funkci můžete mít přehled o anomáliích v účtech v jednotlivých prostředcích.

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Povolit Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorování Azure Security Center** : Ano

**Odpovědnost** : sdílená

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: povolení protokolování pro síťové aktivity Azure

**Pokyny** : pomocí nástrojů pro zachytávání paketů sítě VPN můžete zaznamenávat síťové pakety na cestách mezi prostředky Azure Virtual WAN. To může pomoct s procesem ladění souvisejícím s vaší hybridní sítí. I když ve virtuální síti WAN nemůžete nasadit skupinu zabezpečení sítě, můžete ji nasadit do prostředků virtuální sítě s paprsky.

Povolte pro auditování provozu protokoly toku skupin zabezpečení sítě ve skupinách zabezpečení sítě.

Povolte funkci Azure Analýza provozu pro zpracování protokolů toku, které jsou uchovávány v účtu úložiště, a poté je odešlete do pracovního prostoru Log Analytics. Analýza provozu poskytuje další přehledy o toku provozu pro sítě Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Jak povolit protokoly toku skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md) 

Virtuální síť WAN nevytváří ani nezpracovává protokoly dotazů DNS, které by bylo nutné povolit.

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: povolení protokolování pro prostředky Azure

**Pokyny** : protokoly aktivit Azure, povolené automatické, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky Azure Virtual WAN s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek. Virtual WAN ale nevytváří protokoly prostředků Azure.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 
- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitorování Azure Security Center** : Ano

**Odpovědnost** : sdílená

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizace správy protokolu zabezpečení a analýzy

**Doprovodné** materiály: centralizace úložiště protokolování a analýzy pro povolení korelace. U každého zdroje protokolu se ujistěte, že jste přiřadili vlastníka dat, pokyny pro přístup, umístění úložiště, jaké nástroje se používají ke zpracování a přístupu k datům a k požadavkům na uchovávání dat. Ujistěte se, že Integrujte protokoly aktivit Azure do centrálních systémů protokolování. 

Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty úložiště Azure pro dlouhodobé a archivační úložiště. Kromě toho povolte a zaveďte data do Azure Sentinel nebo SIEM třetí strany.

Řada organizací zvolí použití Azure Sentinel pro "horká" data, která se používají často a Azure Storage pro "studená" data, která se používají méně často.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Odpovědnost** : sdílená

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – aktualizace procesu reakce na incidenty pro Azure

**Doprovodné** materiály: Ujistěte se, že vaše organizace má procesy, které reagují na incidenty zabezpečení, aktualizovala tyto procesy pro Azure a pravidelně je vykonává za účelem zajištění připravenosti. Ujistěte se, že je nabídka služby zahrnutá v procesu reakce na incidenty, pokud je to možné.

- [Implementace zabezpečení v podnikovém prostředí](https://aka.ms/AzSec4) 
- [Referenční příručka k reakci na incidenty](https://aka.ms/IRRG)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Příprava – oznámení o nastavení incidentu

**Doprovodné** materiály: nastavte kontaktní informace incidentu zabezpečení v Azure Security Center. Tyto kontaktní informace používá společnost Microsoft k tomu, aby vás kontaktovala v případě, že služba MSRC (Microsoft Security Response Center) zjistí, že vaše data jsou přístupná ze strany, která není protiprávní nebo oprávněná. Máte také možnost přizpůsobit upozornění na incidenty a oznámení v různých službách Azure v závislosti na potřebách reakce na incidenty.

Kontaktní údaje incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba MSRC (Microsoft Security Response Center) zjistí, že vaše data jsou přístupná ze strany, která není protiprávní nebo oprávněná. Projděte si incidenty, abyste měli jistotu, že jsou problémy zcela vyřešené.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md) 

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detekce a analýza – vytvoření incidentů na základě výstrah s vysokou kvalitou

**Doprovodné** materiály: Azure Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Navíc můžete jasně označit předplatná (například produkci, neprodukční) a vytvořit názvový systém, který bude jasně identifikovat a kategorizovat prostředky Azure.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: detekce a analýza – prozkoumání incidentu

**Pokyny** : Vytvoření Průvodce odpověďmi na incidenty pro vaši organizaci. Provádějte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů z detekce až po přezkoumání po jednotlivých událostech.

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detekce a analýza – určení priorit incidentů

**Doprovodné** materiály: Azure Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Navíc můžete jasně označit předplatné (například produkci, neprodukční prostředí) a vytvořit názvový systém, který bude jasně identifikovat a kategorizovat prostředky Azure.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center** : Ano

**Odpovědnost** : sdílená

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: zahrnutí, vyhubení a obnovení – automatizace zpracování incidentů

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Azure Logic Apps" na výstrahy a doporučení zabezpečení.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

## <a name="posture-and-vulnerability-management"></a>Stav a Správa ohrožení zabezpečení

*Další informace najdete v článku o [stav a správě ohrožení zabezpečení Azure Security test](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: provedení pravidelné simulace útoků

**Doprovodné** materiály: podle vašich požadavků, proveďte testování průniku nebo červené týmové aktivity na svých prostředcích Azure a zajistěte nápravu všech důležitých zjištění zabezpečení.

Postupujte podle pravidel pro testování průniku Microsoft Cloud a zajistěte, aby testy průniku nenarušily zásady Microsoftu. Využijte strategii Microsoftu, vykonávání červeného seskupování a testování průniku živých webů proti cloudové infrastruktuře, službám a aplikacím spravovaným Microsoftem.

- [Testování průniku v Azure](../security/fundamentals/pen-testing.md)

- [Pravidla testování průniku pro zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

## <a name="endpoint-security"></a>Zabezpečení koncového bodu

*Další informace najdete v tématu [srovnávací zabezpečení Azure Security: Endpoint Security](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: použití detekce a odpovědi koncového bodu (EDR)

**Doprovodné** materiály: zákazníkům není explicitně povoleno konfigurovat zjišťování koncových bodů a nastavení odpovědí. Virtual Machines v nabídce Azure Virtual WAN ale tyto možnosti používají. Přečtěte si další informace o těchto obecných funkcích na odkazovaných odkazech. 

- [Přehled rozšířené ochrany před internetovými útoky v programu Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Služba ochrany ATP v programu Microsoft Defender pro servery Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Služba ochrany ATP v programu Microsoft Defender pro servery jiné než Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Monitorování Azure Security Center** : Ano

**Odpovědnost** : sdílená

## <a name="governance-and-strategy"></a>Zásady správného řízení a strategie

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zásady správného řízení a strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definování správy prostředků a strategie ochrany dat 

**Doprovodné** materiály: Ujistěte se, že máte dokument a sdělíte jasné strategii pro nepřetržité monitorování a ochranu systémů a dat. Určení priorit zjišťování, hodnocení, ochrany a monitorování důležitých podnikových dat a systémů. 

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

- Standardní klasifikace dat v souladu s obchodními riziky
- Organizace zabezpečení – přehled o rizicích a inventáři prostředků 
- Schválení organizace služby Azure pomocí organizace zabezpečení 
- Zabezpečení prostředků prostřednictvím jejich životního cyklu
- Požadovaná strategie řízení přístupu v souladu s klasifikací dat organizace
- Používání funkcí Azure Native a ochrany dat třetích stran
- Požadavky na šifrování dat pro případy použití v přenosech a v REST
- Příslušné kryptografické standardy

Zkontrolujte další informace, které jsou k dispozici na odkazovaných odkazech.

- [Doporučení architektury zabezpečení Azure – úložiště, data a šifrování](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Základy zabezpečení Azure – zabezpečení, šifrování a úložiště dat Azure](../security/fundamentals/encryption-overview.md)

- [Rozhraní pro přijetí do cloudu – osvědčené postupy zabezpečení a šifrování dat Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definování strategie podnikové segmentace 

**Doprovodné** materiály: zřízení strategie pro celé podniky za účelem segmentace přístupu k assetům pomocí kombinace identity, sítě, aplikace, předplatného, skupiny pro správu a dalších ovládacích prvků. Pečlivě vyvážit nutnost oddělení zabezpečení s nutností povolit každodenní provoz systémů, které musí vzájemně komunikovat, a přistupovat k datům.

Zajistěte, aby se strategie segmentace implementovala konzistentně napříč typy ovládacích prvků včetně zabezpečení sítě, modelů identit a přístupu a oprávnění aplikace nebo modelů přístupu a ovládacích prvků pro lidské procesy.

- [Doprovodné materiály k strategii segmentace v Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Doprovodné materiály k strategii segmentace v Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Sjednotit segmentaci sítě pomocí strategie segmentace v podniku](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definování strategie správy stav zabezpečení

**Doprovodné** materiály: nepřetržitě měřit a zmírnit rizika pro jednotlivé prostředky a prostředí, kde jsou hostované. Určete prioritu prostředků s vysokou hodnotou a vysoce vydaných ploch útoků, jako jsou publikované aplikace, příchozí a výstupní body sítě, koncových bodů uživatelů a správců atd.

- [Azure Security test – stav a Správa ohrožení zabezpečení](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: zarovnání rolí organizace, odpovědností a accountabilities

**Doprovodné** materiály: Ujistěte se, že máte dokument a komunikujete s jasnými strategiemi pro role a zodpovědnost ve vaší organizaci zabezpečení. Nasměrujte své úsilí na základě jasných zodpovědností při rozhodování o zabezpečení, porozumět všem na sdíleném modelu zodpovědnosti a pedagogujte technické týmy technologie pro zabezpečení cloudu.

- [Osvědčené postupy zabezpečení Azure 1 – lidé: vzdělávání týmů na cestě cloudového zabezpečení](https://aka.ms/AzSec1)

- [Osvědčené postupy zabezpečení Azure 2 – lidé: vzdělávání týmů na technologii Cloud Security](https://aka.ms/AzSec2)

- [Osvědčené postupy zabezpečení Azure 3 – proces: přiřazení zodpovědnosti pro rozhodování o zabezpečení cloudu](https://aka.ms/AzSec3)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definování strategie zabezpečení sítě

**Doprovodné** materiály: navažte přístup k zabezpečení sítě Azure v rámci celkové strategie řízení přístupu zabezpečení vaší organizace. Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

- Centralizovaná správa sítě a zodpovědnost zabezpečení
- Model segmentace virtuální sítě zarovnané na strategii podnikového segmentace
- Strategie oprav v různých scénářích hrozeb a útoků
- Strategie pro Internet Edge a příchozí a odchozí přenosy
- Hybridní cloudová a místní vzájemné propojení strategie
- Aktuální artefakty zabezpečení sítě (například síťové diagramy, referenční Síťová architektura)

Zkontrolujte další informace, které jsou k dispozici na odkazovaných odkazech.

- [Osvědčené postupy zabezpečení Azure 11 – architektura. Jednotná strategie zabezpečení](https://aka.ms/AzSec11)

- [Testování zabezpečení Azure – zabezpečení sítě](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Přehled zabezpečení sítě Azure](../security/fundamentals/network-overview.md)

- [Strategie architektury podnikové sítě](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definování strategie identity a privilegovaného přístupu

**Doprovodné** materiály: navažte přístup k identitě Azure a privilegovanému přístupu v rámci celkové strategie řízení přístupu zabezpečení vaší organizace. Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

- Centralizovaný systém identit a ověřování a jeho vzájemné propojení s dalšími interními a externími systémy identit
- Metody silného ověřování v různých případech a podmínkách použití
- Ochrana vysoce privilegovaných uživatelů
- Anomálie monitorování a zpracování aktivit uživatelů  
- Uživatelská identita a kontrola přístupu a proces odsouhlasení

Zkontrolujte další informace, které jsou k dispozici na odkazovaných odkazech.

- [Azure Security benchmark – Správa identit](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security test – privilegovaný přístup](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Osvědčené postupy zabezpečení Azure 11 – architektura. Jednotná strategie zabezpečení](https://aka.ms/AzSec11)

- [Přehled zabezpečení služby Azure Identity Management](../security/fundamentals/identity-management-overview.md)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definování strategie protokolování a reakce na hrozby

**Doprovodné** materiály: vytvoření strategie protokolování a reakce na hrozby pro rychlé detekci a nápravu hrozeb při splnění požadavků na dodržování předpisů. Určete prioritu poskytování analytiků s vysoce kvalitními výstrahami a bezproblémové prostředí, aby se mohly soustředit na hrozby, nikoli na integraci a ruční kroky. 

Tato strategie by měla zahrnovat zdokumentované doprovodné materiály, zásady a standardy pro následující prvky: 

- Role a odpovědnosti organizace v oblasti operací zabezpečení (SecOps)
- Dobře definovaný proces reakce na incidenty, který je v souladu s NIST nebo jiným oborovým rozhraním
- Shromažďování a uchovávání protokolů pro podporu detekce hrozeb, reakce na incidenty a požadavků na dodržování předpisů
- Centralizované zobrazení a korelační informace o hrozbách, použití SIEM, nativní možnosti Azure a další zdroje – komunikace a plán oznámení se zákazníky, dodavateli a veřejnými stranami zájmu
- Použití nativních platforem Azure a platforem třetích stran pro zpracování incidentů, jako je například protokolování a detekce hrozeb, forenzní a náprava či eradikace útoků
- Procesy pro zpracování incidentů a aktivit po incidentech, jako jsou zjištěné lekce a uchovávání důkazů

Zkontrolujte další informace, které jsou k dispozici na odkazovaných odkazech.
- [Azure Security test benchmark – protokolování a detekce hrozeb](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Srovnávací test zabezpečení Azure – odpověď na incident](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Doporučený postup zabezpečení Azure 4 – proces. Aktualizace procesů reakce na incidenty pro Cloud](https://aka.ms/AzSec4)

- [Průvodce rozhodnutím o přijetí, protokolování a vytváření sestav pro Azure](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu [Přehled Azure Security benchmark v2](/azure/security/benchmarks/overview) .
- Další informace o [plánech zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
