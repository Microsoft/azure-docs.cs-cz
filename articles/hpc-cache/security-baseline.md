---
title: Základní hodnota zabezpečení Azure pro mezipaměť HPC Azure
description: Základní hodnoty zabezpečení mezipaměti HPC Azure obsahují doprovodné materiály a zdroje pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 850171a4f1f303ef344fa5007325093c6363615b
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789073"
---
# <a name="azure-security-baseline-for-azure-hpc-cache"></a>Základní hodnota zabezpečení Azure pro mezipaměť HPC Azure

Tato základní hodnota zabezpečení platí pro Microsoft Azure mezipaměti HPC pokyny od [verze Azure Security test 2,0](../security/benchmarks/overview.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah je seskupen podle **ovládacích prvků zabezpečení** , které jsou definovány srovnávacím testem zabezpečení Azure, a souvisejícími pokyny platnými pro mezipaměť prostředí Azure HPC. Neexistují **ovládací prvky** , které se vztahují na mezipaměť prostředí Azure HPC.

Pokud chcete zjistit, jak Azure HPC cache kompletně mapuje do srovnávacího testu zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení mezipaměti HPC Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementace zabezpečení pro interní provoz

**Pokyny**: při nasazení prostředků mezipaměti prostředí Azure HPC je nutné vytvořit nebo použít existující virtuální síť. 

Zajistěte, aby všechny virtuální sítě Azure dodržovaly princip segmentace podniku, který se rovná obchodním rizikům. Každý systém, který by mohl organizaci zvýšit riziko, by se měl izolovat v rámci vlastní virtuální sítě a dostatečně zabezpečený pomocí skupiny zabezpečení sítě (NSG) a/nebo Azure Firewall.

Předtím, než budete moci použít mezipaměť, by měly být nastavené dvě požadavky týkající se sítě: 

- Vyhrazená podsíť pro instanci mezipaměti Azure HPC

- Podpora DNS, aby mohla mezipaměť získat přístup k úložišti a dalším prostředkům

Mezipaměť prostředí Azure HPC potřebuje vyhrazenou podsíť s těmito kvalitou: 

- Podsíť musí mít k dispozici minimálně 64 IP adres.

- Podsíť nemůže hostovat žádné jiné virtuální počítače, a to ani pro související služby, jako jsou klientské počítače.

- Pokud používáte více instancí Azure HPC cache, každá z nich potřebuje vlastní podsíť.

Osvědčeným postupem je vytvořit novou podsíť pro každou mezipaměť. V rámci vytváření mezipaměti můžete vytvořit novou virtuální síť a podsíť.

Pokud chcete použít mezipaměť HPC s místním úložištěm NAS, musíte zajistit, aby některé porty v místní síti povolovaly neomezený provoz z podsítě mezipaměti HPC Azure. 

- [Postup konfigurace portů pro přístup k úložišti NFS](hpc-cache-prerequisites.md#nfs-storage-requirements)

- [Jak vytvořit skupinu zabezpečení sítě s pravidly zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Jak nasadit a nakonfigurovat Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="ns-2-connect-private-networks-together"></a>NS-2: spojování privátních sítí

**Pokyny**: pomocí Azure ExpressRoute nebo virtuální privátní sítě (VPN) Azure můžete vytvořit privátní připojení mezi datacentry Azure a místní infrastrukturou v prostředí kolokace. Připojení ExpressRoute nevyužívají veřejný Internet a nabízejí spolehlivější, rychlejší a nižší latenci než typická připojení k Internetu. Pro sítě VPN typu Point-to-site a VPN typu Site-to-site můžete připojit místní zařízení nebo sítě k virtuální síti pomocí libovolné kombinace těchto možností sítě VPN a Azure ExpressRoute. 

Pokud chcete propojit dvě nebo víc virtuálních sítí v Azure společně, použijte partnerský vztah virtuálních sítí. Síťový provoz mezi partnerskými virtuálními sítěmi je privátní a udržuje se v páteřní síti Azure.

- [Jaké jsou modely připojení ExpressRoute](../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN – přehled](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Zřízení přístupu privátní sítě ke službám Azure

**Pokyny**: pomocí koncových bodů služby Azure Virtual Network můžete poskytnout zabezpečený přístup k mezipaměti HPC. Koncové body služby jsou optimalizovanou trasou přes páteřní síť Azure bez přechodu na Internet. 

Mezipaměť HPC nepodporuje použití privátního propojení Azure k zabezpečení koncových bodů správy na privátní síť. 

Privátní přístup je dodatečnou mírou důkladné ochrany, kromě ověřování a zabezpečení provozu nabízených službami Azure.

- [Vysvětlení koncových bodů služby Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) 

- [Informace o tom, jak připojit mezipaměť prostředí Azure HPC](hpc-cache-mount.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrana aplikací a služeb před útoky z externích sítí

**Doprovodné** materiály: Chraňte své prostředky mezipaměti HPC proti útokům z externích sítí, včetně útoků DDoS (Distributed Denial of Service), útoků specifických pro aplikace a nevyžádaných a potenciálně škodlivých internetových přenosů. 

Azure zahrnuje nativní možnosti pro tuto ochranu: 

- Pomocí Azure Firewall můžete chránit aplikace a služby před potenciálně škodlivým provozem z Internetu a dalších externích umístění. 
- Chraňte své prostředky proti útokům DDoS tím, že ve svých virtuálních sítích Azure povolíte standardní ochranu DDoS. 
- Použijte Azure Security Center k detekci rizik s konfigurací, které souvisí se síťovými prostředky.

Mezipaměť prostředí Azure HPC není určena ke spouštění webových aplikací a nevyžaduje konfiguraci jakýchkoli dalších nastavení ani nasazení jakýchkoli dalších síťových služeb pro ochranu před útoky z externích sítí, které cílí na webové aplikace.

- [Dokumentace k Azure Firewall](../firewall/index.yml) 

- [Správa Azure DDoS Protection Standard pomocí Azure Portal](../ddos-protection/manage-ddos-protection.md) 

- [Doporučení pro Azure Security Center](../security-center/recommendations-reference.md#recs-networking)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: nasazení systémů pro zabránění vniknutí/prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: použití Azure firewall s filtrováním založeném na hrozbách k upozorňování a zablokování provozu do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu. 

Pokud potřebujete kontrolu datové části, můžete nasadit řešení pro zjištění neoprávněného vniknutí třetí strany a zabránit neoprávněnému přístupu k systému (ID/IP) z Azure Marketplace s možnostmi kontroly zatížení. Alternativně můžete použít ID hostitele/IP adresy nebo řešení pro detekci a odpověď na základě hostitele (EDR) ve spojení s nebo místo síťových ID/IP adres.

Poznámka: Pokud máte regulativní nebo jiný požadavek na použití IDENTIFIKÁTORů/IP adres, ujistěte se, že je vždycky vyladěný tak, aby poskytoval vysoce kvalitní výstrahy pro řešení SIEM.

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace možností ID třetích stran](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [EDR schopnost služby Microsoft Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: zjednodušení pravidel zabezpečení sítě

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro nabídky, které se dají nasadit do virtuálních sítí Azure, nebo mají možnost definovat seskupení povolených rozsahů IP adres pro efektivní správu. Mezipaměť HPC v současné době nepodporuje značky služeb. 

Osvědčeným postupem je vytvořit novou podsíť pro každou mezipaměť. V rámci vytváření mezipaměti můžete vytvořit novou virtuální síť a podsíť.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Zabezpečená služba názvů domén (DNS)

**Doprovodné** materiály: Dodržujte osvědčené postupy pro zabezpečení DNS, které vám umožní zmírnit běžné útoky jako dangling DNS, útoky na servery DNS, poškození DNS a falšování identity a další.

Mezipaměť prostředí Azure HPC potřebuje DNS pro přístup k prostředkům mimo privátní virtuální síť mezipaměti. Pokud váš pracovní postup zahrnuje prostředky mimo Azure, musíte kromě použití Azure DNS nastavit a zabezpečit vlastní server DNS.

- Pokud chcete získat přístup k koncovým bodům služby Azure Blob Storage, klientským počítačům na bázi Azure nebo jiným prostředkům Azure, použijte Azure DNS.
- Pokud chcete získat přístup k místnímu úložišti nebo se připojit k mezipaměti z klientů mimo Azure, musíte vytvořit vlastní server DNS, který dokáže tyto názvy hostitelů přeložit.
- Pokud váš pracovní postup obsahuje interní i externí prostředky, nastavte si vlastní server DNS tak, aby přenesl požadavky na rozlišení specifické pro Azure na server Azure DNS. 

Když se jako autoritativní služba DNS používá Azure DNS, zajistěte, aby byly zóny a záznamy DNS chráněné před náhodnými nebo škodlivými úpravami pomocí Azure RBAC a zámků prostředků.

Pokud konfigurujete vlastní server DNS, ujistěte se, že se dodržují tyto pokyny zabezpečení:

- [Příručka pro nasazení zabezpečení DNS (Domain Name System)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Zabránit položkám DNS v dangling a vyhnout se převzetí subdomény](../security/fundamentals/subdomain-takeover.md) 

- [Přečtěte si další informace o požadavcích na přístup DNS pro mezipaměť HPC.](hpc-cache-prerequisites.md#dns-access)

- [Přehled Azure DNS](../dns/dns-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [Azure Security Benchmark: správa identit](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizace Azure Active Directory jako centrálního systému pro identifikaci a ověřování

**Doprovodné** materiály: mezipaměť prostředí Azure HPC není integrovaná s Azure Active Directory pro interní operace. Služba Azure AD se ale dá použít k ověřování uživatelů v Azure Portal nebo rozhraní příkazového řádku, aby bylo možné vytvářet, zobrazovat a spravovat nasazení mezipaměti HPC a související součásti.

Služba Azure Active Directory (Azure AD) je výchozí službou pro správu identit a přístupu v Azure. Službu Azure AD byste měli standardizovat, abyste mohli řídit identitu a správu přístupu vaší organizace v nástroji:

- Microsoft Cloud prostředky, jako jsou Azure Portal, Azure Storage, virtuální počítače Azure (Linux a Windows), Azure Key Vault, PaaS a SaaS aplikace.

- prostředky vaší organizace, jako jsou aplikace v Azure nebo prostředky vaší podnikové sítě

Zabezpečení služby Azure AD by mělo mít vysokou prioritu v praxi cloudového zabezpečení vaší organizace. Azure AD poskytuje bezpečnostní skóre identity, které vám pomůže vyhodnotit stav zabezpečení identity vzhledem k doporučeným osvědčeným postupům od Microsoftu. Pomocí skóre můžete vyhodnotit, jak přesně vaše konfigurace vyhovuje doporučeným osvědčeným postupům, a provádět v zabezpečení vylepšení.

Poznámka: Azure AD podporuje externí identitu, která umožňuje uživatelům bez účet Microsoft přihlašovat se k aplikacím a prostředkům s jejich externí identitou.

- [Architektura tenantů v Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Používání externích zprostředkovatelů identit pro aplikaci](../active-directory/external-identities/identity-providers.md) 

- [Co je skóre zabezpečení identity v Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Zabezpečená a automatická správa identit aplikací

**Doprovodné** materiály: mezipaměť HPC používá pro jiné než lidské účty identity spravované službou Azure, jako jsou služby nebo automatizace. Místo vytvoření výkonnějšího lidského účtu pro přístup k prostředkům nebo jejich provádění doporučujeme použít funkci spravované identity Azure. 

Mezipaměť HPC se dá nativně ověřit pro služby nebo prostředky Azure, které podporují ověřování Azure AD prostřednictvím předdefinovaných pravidel udělení přístupu. Předejdete tak nutnosti používat pevně zakódované přihlašovací údaje ve zdrojovém kódu nebo konfiguračních souborech.

- [Identity spravované Azure](../active-directory/managed-identities-azure-resources/overview.md) 

- [Služby, které podporují spravované identity prostředků Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Použití jednotného přihlašování (SSO) Azure AD pro přístup k aplikacím

**Pokyny**: mezipaměť prostředí Azure HPC není integrovaná se službou Azure AD pro interní operace. Služba Azure AD se ale dá použít k ověřování uživatelů v Azure Portal nebo rozhraní příkazového řádku, aby bylo možné vytvářet, zobrazovat a spravovat nasazení mezipaměti HPC a související součásti.

Azure Active Directory poskytuje správu identit a přístupu pro prostředky Azure, cloudové aplikace a místní aplikace. Patří sem podnikové identity, jako jsou zaměstnanci, i externí identity, jako jsou partneři a dodavatelé. Díky tomu je možné použít jednotné přihlašování (SSO) ke správě a zabezpečení přístupu k datům a prostředkům vaší organizace v místním prostředí a v cloudu. Připojte všechny své uživatele, aplikace a zařízení k Azure AD, ať máte bezproblémový, zabezpečený přístup a lepší viditelnost a kontrolu.

- [Principy jednotného přihlašování k aplikacím pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Použití řídicích prvků silného ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: i když Azure HPC cache Neintegruje se službou Azure AD pro interní operace, dá se Azure AD použít k ověřování uživatelů v Azure Portal nebo rozhraní PŘÍKAZového řádku, aby bylo možné vytvářet, zobrazovat a spravovat nasazení mezipaměti HPC a související součásti.  

Azure AD podporuje ovládací prvky silného ověřování prostřednictvím služby Multi-Factor Authentication (MFA) a metod se silnými hesly.

- Multi-Factor Authentication: Povolte Azure AD MFA a sledujte Azure Security Center doporučení pro správu identit a přístupu pro některé osvědčené postupy při instalaci MFA. Vícefaktorové ověřování se dá vyhovět všem uživatelům, vybrat uživatele nebo na úrovni jednotlivých uživatelů na základě podmínek přihlášení a rizikových faktorů.
- Ověřování bez hesla – k dispozici jsou tři možnosti ověřování bez hesla: Windows Hello pro firmy, aplikace Microsoft Authenticator a místní metody ověřování, jako jsou čipové karty.

Pro správce a privilegované uživatele se ujistěte, že používáte nejvyšší úroveň metody silného ověřování. Zaveďte příslušné zásady silného ověřování pro ostatní uživatele.

Mezipaměť HPC Azure také podporuje starší ověřování pomocí hesla pro klientské systémy, které se připojují k mezipaměti. Mezi tyto typy připojení patří pouze cloudové účty (uživatelské účty vytvořené přímo v Azure), které mají základní zásady pro hesla nebo hybridní účty (uživatelské účty, které pocházejí z místní služby Active Directory), které budou následovat po místních zásadách hesel. 

Při použití ověřování založeného na hesle poskytuje Azure AD funkci ochrany heslem, která uživatelům brání v nastavení hesla, která se dají snadno uhodnout. Společnost Microsoft poskytuje globální seznam zakázaných hesel, která jsou aktualizována na základě telemetrie, a zákazníci mohou seznam rozšířit podle svých potřeb (například pomocí brandingu, kulturních odkazů atd.). Tato ochrana heslem se dá použít jenom pro cloudové a hybridní účty.

Poznámka: ověřování založené jenom na přihlašovacích údajích hesla je náchylné k oblíbeným metodám útoku. Pro zajištění vyššího zabezpečení používejte silné ověřování, jako je MFA a zásady silného hesla. Pokud používáte aplikace třetích stran a služby tržiště, které mají výchozí hesla, při prvním nastavení služby nastavte nové zabezpečené heslo. 

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Úvod do možností ověřování bez hesla pro Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Výchozí zásady hesel Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminace špatných hesel s využitím ochrany hesel Azure AD](../active-directory/authentication/concept-password-ban-bad.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorování a upozornění na anomálie účtů

**Doprovodné** materiály: mezipaměť prostředí Azure HPC může použít Azure Active Directory pro správu uživatelů z Azure Portal.  Azure Active Directory poskytuje následující zdroje dat:

- Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů.

- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.

- Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Tyto zdroje dat je možné integrovat s Azure Monitor, s Sentinelou v Azure a systémy SIEM třetích stran.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření a u zastaralých účtů v rámci předplatného.

Rozšířená ochrana před internetovými útoky (ATP) v Azure je řešení zabezpečení, které dokáže na základě signálů Active Directory identifikovat, detekovat a vyšetřovat pokročilé hrozby, ohrožené identity a škodlivé činnosti vnitřních účastníků.

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md) 

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](../active-directory/identity-protection/overview-identity-protection.md) 

- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../security-center/security-center-identity-access.md)

 

- [Upozornění v modulu ochrany na základě analýzy hrozeb služby Azure Security Center](../security-center/alerts-reference.md) 

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminace nezamýšleného prozrazení přihlašovacích údajů

**Doprovodné** materiály: nepoužitelné; Mezipaměť HPC neumožňuje zákazníkům nasadit do běžícího prostředí žádná trvalá data.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security Benchmark: privilegovaný přístup](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Omezení přístupu pro správu k nejdůležitějším podnikovým systémům

**Doprovodné** materiály: mezipaměť HPC používá Azure RBAC k izolaci přístupu k podnikovým systémům tím, že omezuje přístup k důležitým systémům pomocí omezení přístupu k předplatným a skupinám pro správu, v nichž jsou udělen privilegovaný přístup.

Vytvořte standardní operační postupy kolem použití vyhrazených účtů pro správu. Mezipaměť HPC vyžaduje, aby uživatelé měli v předplatném dostatečná oprávnění pro vytváření síťových adaptérů. Pokud používáte službu BLOB Storage, je pro přístup k úložišti pro mezipaměť HPC nutné Přispěvatel účtu úložiště rolí Azure a přispěvatel dat úložiště. 

Ujistěte se, že také omezíte přístup k systémům pro správu, identitu a zabezpečení, které mají přístup pro správu k důležitým podnikovým prostředkům, jako jsou Doména služby Active Directory Controllers (DCs), nástroje zabezpečení a nástroje pro správu systému s agenty nainstalovanými v důležitých podnikových systémech. Útočníci, kteří tyto systémy pro správu a zabezpečení ohrožují, si je můžou hned weaponize, aby naohrozili důležité obchodní prostředky.

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu.

- [Oprávnění RBAC mezipaměti HPC Azure](hpc-cache-prerequisites.md#permissions)

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Přístup ke skupině pro správu](../governance/management-groups/overview.md#management-group-access)

- [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Pravidelná kontrola a sjednocování přístupu uživatelů

**Pokyny**: pravidelně kontrolujte uživatelské účty a přiřazení přístupu, abyste měli jistotu, že účty a jejich úrovně přístupu jsou platné. 

Mezipaměť HPC Azure může používat účty Azure Active Directory (Azure AD) ke správě přístupu uživatelů prostřednictvím Azure Portal a souvisejících rozhraní. Azure AD nabízí kontroly přístupu, které vám pomůžou kontrolovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Vytváření sestav Azure AD může poskytovat protokoly, které vám pomůžou zjistit zastaralé účty. K usnadnění procesu kontroly můžete použít také Azure AD Privileged Identity Management k vytvoření pracovního postupu sestavy kontroly přístupu.

Kromě toho je možné Azure Privileged Identity Management nakonfigurovat tak, aby výstrahu při vytváření nadměrného počtu účtů správců a identifikaci účtů správce, které jsou zastaralé nebo nesprávně nakonfigurované.

Poznámka: některé služby Azure podporují místní uživatele a role, které nejsou spravované prostřednictvím služby Azure AD. Tyto uživatele budete muset spravovat samostatně.

Když použijete cíle úložiště NFS, budete muset spolupracovat s správci sítě a správci brány firewall, aby ověřili nastavení přístupu a zajistili, že mezipaměť prostředí Azure HPC bude moct komunikovat se systémy úložiště NFS.

- [Seznam oprávnění mezipaměti HPC si můžete přečíst v tématu požadavky na mezipaměť prostředí Azure HPC.](hpc-cache-prerequisites.md) 

- [Vytvoření kontroly přístupu pro role prostředků Azure v Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Nastavení nouzového přístupu v Azure AD

**Doprovodné** materiály: mezipaměť HPC může použít Azure Active Directory ke správě přístupu k prostředkům prostřednictvím Azure Portal. Pokud se chcete vyhnout náhodnému uzamčení vaší organizace Azure AD, nastavte účet pro nouzový přístup pro přístup, když nebudete moct použít normální účty pro správu. Účty pro nouzový přístup jsou obvykle vysoce privilegované a neměly by se přiřazovat konkrétním jednotlivcům. Jejich použití je omezené na nouzové nebo kritické situace, kdy se nedají použít běžné účty pro správu.

Měli byste zajistit, aby přihlašovací údaje (například heslo, certifikát nebo čipová karta) pro účty pro nouzový přístup byly zabezpečené a aby o nich věděli jenom ti, kteří jsou oprávněni je použít jenom v případě nouze.

- [Správa účtů pro nouzový přístup v Azure AD](../active-directory/roles/security-emergency-access.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizace správy nároků 

**Doprovodné** materiály: mezipaměť HPC může použít Azure Active Directory ke správě přístupu k prostředkům mezipaměti prostřednictvím Azure Portal. 

Pomocí funkcí správy opravňujících ke službě Azure AD můžete automatizovat pracovní postupy pro žádosti o přístup, včetně přiřazení přístupu, revizí a vypršení platnosti. Podporuje se také duální nebo vícenásobné schválení. 

- [Co jsou kontroly přístupu Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Co je Správa nároků Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Využívání správy jen v potřebném rozsahu (princip nejnižší úrovně nutných oprávnění) 

**Doprovodné** materiály: mezipaměť HPC je integrovaná s řízením přístupu na základě role (RBAC) Azure pro správu prostředků. Azure RBAC umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, skupinám instančních objektů a spravovaným identitám. Pro určité prostředky existují předdefinované role, které je možné inventarizovat nebo dotazovat pomocí nástrojů, jako jsou Azure CLI, Azure PowerShell nebo Azure Portal. 

Oprávnění, která přiřadíte k prostředkům prostřednictvím Azure RBAC, by měla být vždy omezená na to, co role vyžadují. Tím se doplňují přístup JIT (just-in-time) Azure AD Privileged Identity Management (PIM) a je třeba je pravidelně kontrolovat.

Využijte k přidělování oprávnění předdefinované role a vlastní role vytvářejte pouze v případě potřeby.

- [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Konfigurace řízení přístupu na základě role Azure](../role-based-access-control/role-assignments-portal.md) 

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: zjištění, klasifikace a označení citlivých dat 

**Doprovodné** materiály: mezipaměť HPC spravuje citlivá data, ale nemá schopnost vyhledávat, klasifikovat a označovat citlivá data.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrana citlivých dat

**Doprovodné** materiály: Chraňte citlivá data omezením přístupu pomocí Access Control založeného na rolích Azure (Azure RBAC), řízení přístupu na základě sítě a konkrétních ovládacích prvků ve službách Azure (například šifrování v SQL a dalších databázích).

Aby se zajistilo konzistentní řízení přístupu, všechny typy řízení přístupu by měly být v souladu s vaší podnikovou strategií segmentace. Podniková strategie segmentace by měla vycházet také z umístění citlivých nebo důležitých obchodních dat a systémů.

Pro základní platformu spravovanou Microsoftem platí, že Microsoft považuje veškerý obsah zákazníků za citlivý a zajišťuje ochranu před ztrátou a vystavením zákaznických dat. V zájmu zajištění zabezpečení zákaznických dat v rámci Azure Microsoft implementovat určité výchozí kontrolní mechanismy a funkce ochrany dat.

- [Řízení přístupu na základě role (RBAC) Azure](../role-based-access-control/overview.md) 

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Sdílené

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Monitorování neoprávněného přenosu citlivých dat

**Doprovodné** materiály: mezipaměť HPC přenáší citlivá data, ale nepodporuje monitorování neoprávněného přenosu citlivých dat.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Šifrování citlivých informací při přenosu

**Doprovodné** materiály: mezipaměť HPC podporuje šifrování dat při přenosu s protokolem TLS verze 1.2 nebo vyšším.

I když je to pro přenosy v privátních sítích volitelné, je to pro provoz v externích i veřejných sítích velmi důležité. U přenosů HTTP zajistěte, aby všichni klienti připojující se k prostředkům Azure mohli vyjednávat TLS v 1.2 nebo vyšší verzi. Pro vzdálenou správu použijte místo nešifrovaného protokolu SSH (pro Linux) nebo RDP/TLS (pro Windows). Zastaralé verze a protokoly SSL, TLS a SSH a slabé šifry by měly být zakázané.

Ve výchozím nastavení poskytuje Azure šifrování dat při přenosu mezi datovými centry Azure.

- [Pochopení šifrování při přenosu pomocí Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informace o zabezpečení TLS](/security/engineering/solving-tls1-problem) 

- [Dvojité šifrování pro Azure data při přenosu](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Šifrování citlivých neaktivních uložených dat

**Doprovodné** materiály: pro doplnění řízení přístupu by měla být neaktivní data chráněná před útoky typu "vzdálené správy" (například přístup k podkladovým úložištím) pomocí šifrování. To pomáhá zajistit, že útočníci nemohou snadno číst nebo upravovat data.

Azure ve výchozím nastavení poskytuje šifrování dat v klidovém stavu. U vysoce citlivých dat máte možnost implementovat další šifrování v klidovém umístění všech prostředků Azure, kde jsou k dispozici. Azure spravuje vaše šifrovací klíče ve výchozím nastavení, ale Azure poskytuje možnosti pro správu vlastních klíčů (klíčů spravovaných zákazníkem) pro určité služby Azure.

Všechna data uložená v Azure, včetně na discích mezipaměti, jsou v klidovém stavu zašifrovaná pomocí klíčů spravovaných Microsoftem. Pokud chcete spravovat klíče používané k šifrování vašich dat, je třeba přizpůsobit nastavení mezipaměti prostředí Azure HPC.

Pokud je to nutné pro dodržování předpisů u výpočetních prostředků, implementujte nástroj třetí strany, například řešení ochrany před únikem informací na základě automatizovaného hostitele, abyste vynutili řízení přístupu k datům i v případě, že se data zkopírují mimo systém.

- [Používání šifrovacích klíčů spravovaných zákazníkem s využitím mezipaměti HPC Azure](./hpc-cache-create.md?tabs=azure-portal#enable-azure-key-vault-encryption-optional)

- [Principy šifrování neaktivních uložených dat v Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Postup konfigurace šifrovacích klíčů spravovaných zákazníkem](../storage/common/customer-managed-keys-configure-key-vault.md) 

- [Model šifrování a tabulka správy klíčů](../security/fundamentals/encryption-atrest.md)

 

- [Data s dvojitým šifrováním v Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="asset-management"></a>Správa aktiv

*Další informace najdete v tématu [Azure Security Benchmark: správa prostředků](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Zajištění přehledu bezpečnostního týmu o rizicích pro prostředky

**Pokyny:** Zajistěte udělení oprávnění Čtenář zabezpečení ve vašem tenantovi Azure a vašich předplatných bezpečnostním týmům, aby mohly monitorovat bezpečnostní rizika s využitím služby Azure Security Center. 

V závislosti na nastavení struktury odpovědnosti bezpečnostních týmů může být monitorování bezpečnostních rizik odpovědností centrálního nebo místního bezpečnostního týmu. Přehledy zabezpečení a bezpečnostní rizika se však v rámci organizace musí vždy agregovat centrálně. 

Oprávnění Čtenář zabezpečení je možné přidělit pro celého tenanta (kořenová skupina pro správu) nebo je vymezit na konkrétní skupiny pro správu nebo konkrétní předplatná. 

Poznámka: K získání přehledu o úlohách a službách se můžou vyžadovat další oprávnění. 

- [Přehled role Čtenář zabezpečení](../role-based-access-control/built-in-roles.md#security-reader)

- [Přehled skupin pro správu Azure](../governance/management-groups/overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Zajištění přístupu k inventáři a metadatům prostředků pro bezpečnostní tým

**Doprovodné** materiály: mezipaměť prostředí Azure HPC podporuje použití značek. Použijte značky pro prostředky Azure, skupiny prostředků a předplatná k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. 

Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí. Značky lze přidat při vytváření mezipaměti a také po nasazení mezipaměti. 

Pomocí inventáře virtuálních počítačů Azure můžete automatizovat shromažďování informací o softwaru na Virtual Machines. Z Azure Portal je k dispozici název softwaru, verze, Vydavatel a čas aktualizace. Chcete-li získat přístup k datu instalace a dalším informacím, povolte diagnostiku na úrovni hosta a přiveďte protokoly událostí systému Windows do Log Analyticsho pracovního prostoru.
Mezipaměť HPC neumožňuje spuštění aplikace nebo instalace softwaru na svých prostředcích. 

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md) 

- [Správa inventáře Azure Security Center prostředků](../security-center/asset-inventory.md) 

- [Průvodce rozhodováním ohledně pojmenování a označování prostředků](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json) 

- [Jak povolit inventář virtuálních počítačů Azure](../automation/automation-tutorial-installed-software.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používání jenom schválených služeb Azure

**Doprovodné** materiály: mezipaměť HPC podporuje nasazení Azure Resource Manager. Pomocí Azure Policy můžete auditovat a omezovat služby, které můžou uživatelé zřídit ve vašem prostředí. Pomocí Azure Resource Graphu se můžete dotazovat na prostředky v rámci jejich předplatných a zjišťovat je. Pomocí Azure Monitoru můžete také vytvořit pravidla pro aktivaci upozornění při zjištění neschválené služby.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/index.md) 

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Zajištění zabezpečení správy životního cyklu prostředků

**Doprovodné** materiály: nelze použít. Mezipaměť prostředí Azure HPC se nedá použít k zajištění zabezpečení prostředků v procesu správy životního cyklu. Je zodpovědností zákazníka udržovat atributy a síťové konfigurace prostředků, které jsou považovány za vysoký dopad. 

Doporučujeme, aby zákazník vytvořil proces pro zachycení atributů a změny konfigurace sítě, měření dopadu na změnu a vytvoření úloh nápravy podle potřeby.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v tématu [Azure Security Benchmark: protokolování a detekce hrozeb](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: povolení detekce hrozeb pro prostředky Azure

**Pokyny**: Využijte možnost Azure Security Center integrovanou funkci detekce hrozeb a povolte Azure Defender (ve formě rozšířené ochrany před internetovými útoky Azure) pro vaše prostředky mezipaměti HPC. Azure Defender for HPC cache poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k prostředkům mezipaměti nebo jejich zneužití.

Předejte všechny protokoly z mezipaměti HPC do svého SIEMu, který se dá použít k nastavení rozpoznávání vlastních hrozeb. Ujistěte se, že sledujete různé typy prostředků Azure pro potenciální hrozby a anomálie. Zaměřte se na získávání vysoce kvalitních výstrah, které omezí falešně pozitivní výsledky pro analytiky k řazení. Výstrahy se dají nacházet z dat protokolů, agentů nebo jiných dat.

- [Ochrana před hrozbami v Azure Security Center](../security-center/azure-defender.md) 

- [Referenční příručka pro Azure Security Center výstrahy zabezpečení](../security-center/alerts-reference.md) 

- [Vytváření vlastních pravidel analýzy pro detekci hrozeb](../sentinel/tutorial-detect-threats-custom.md) 

- [Funkce Azure Sentinel pro internetovou analýzu hrozeb](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Povolení detekce hrozeb pro správu identit a přístupu v Azure

**Doprovodné** materiály: Azure AD poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD, které jsou integrované s Azure monitor, Azure Sentinel nebo jiné nástroje Siem/Monitoring pro výkonnější monitorování a analytické účely:
- Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů.

- Protokoly auditu – poskytují sledovatelnost prostřednictvím protokolů pro všechny změny prováděné různými funkcemi v rámci služby Azure AD. Mezi příklady protokolů auditu patří změny provedené u všech prostředků v rámci služby Azure AD, jako je přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.

- Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření nebo zastaralé účty v rámci předplatného. Kromě základního monitorování hygieny zabezpečení může modul ochrany před hrozbami služby Azure Security Center také shromažďovat podrobnější upozornění zabezpečení z jednotlivých výpočetních prostředků Azure (virtuální počítače, kontejnery, App Service), datových prostředků (databáze SQL a úložiště) a vrstev služeb Azure. Díky této funkci můžete mít přehled o anomáliích v účtech v jednotlivých prostředcích.

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Povolení ochrany identit Azure](../active-directory/identity-protection/overview-identity-protection.md) 

- [Ochrana před hrozbami v Azure Security Center](../security-center/azure-defender.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Povolení protokolování pro síťové aktivity Azure

**Pokyny**: Kromě běžně dostupných nástrojů pro zachytávání paketů můžete používat brány VPN a jejich funkci zachycení paketů k zaznamenávání síťových paketů na cestách mezi vašimi virtuálními sítěmi.

Nasaďte skupinu zabezpečení sítě v síti, ve které jsou nasazené prostředky mezipaměti prostředí Azure HPC. Povolte pro auditování provozu protokoly toku skupin zabezpečení sítě ve skupinách zabezpečení sítě.

Protokoly toku se uchovávají v účtu úložiště. Umožněte Analýza provozu řešení pro zpracování a odesílání protokolů toku do pracovního prostoru Log Analytics. Analýza provozu poskytuje další přehledy o toku provozu pro sítě Azure. Analýza provozu vám může pomáhat vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

Mezipaměť potřebuje DNS pro přístup k prostředkům mimo svou virtuální síť. V závislosti na tom, jaké prostředky používáte, možná budete muset nastavit vlastní server DNS a nakonfigurovat přesměrování mezi tímto serverem a Azure DNS servery. 

Implementujte řešení od jiného výrobce z Azure Marketplace pro řešení protokolování DNS podle potřeby vaší organizace.

- [Konfigurace zachycení paketů pro brány VPN](../vpn-gateway/packet-capture.md) 

- [Jak povolit protokoly toku skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md) 

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md) 

- [Další informace o požadavcích DNS](hpc-cache-prerequisites.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Povolení protokolování pro prostředky Azure

**Doprovodné** materiály: prostředky mezipaměti HPC Azure automaticky vytváří protokoly aktivit. Tyto protokoly obsahují všechny operace zápisu (PUT, POST, DELETE), ale nezahrnují operace čtení (GET). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo ke sledování, jak uživatel ve vaší organizaci změnil prostředek.

Pomocí Azure Security Center a Azure Policy můžete také povolit protokoly prostředků Azure pro mezipaměť HPC a shromažďování dat a jejich protokolování. Tyto protokoly můžou být klíčové pro pozdější zkoumání incidentů zabezpečení a provádění forenzních cvičení.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Pochopení Azure Security Center shromažďování dat](../security-center/security-center-enable-data-collection.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizace správy a analýz protokolu zabezpečení

**Doprovodné** materiály: centralizace úložiště protokolování a analýzy pro povolení korelace. U každého zdroje protokolu zajistěte, aby byl přiřazen vlastník dat, pokyny pro přístup, umístění úložiště, jaké nástroje jsou používány pro zpracování a přístup k datům a požadavky na uchovávání dat.

Ujistěte se, že Integrujte protokoly aktivit Azure do svého centrálního protokolování. Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Kromě toho povolte a zaveďte data do Azure Sentinel nebo SIEM třetí strany.

Řada organizací zvolí použití Azure Sentinel pro "horká" data, která se používají často a Azure Storage pro "studená" data, která se používají méně často.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – aktualizace procesu reakce na incidenty pro Azure

**Pokyny:** Zajistěte, aby vaše organizace měla procesy, jak reagovat na incidenty zabezpečení, aby tyto procesy aktualizovala pro Azure a pravidelně je testovala za účelem zajištění připravenosti.

- [Implementace zabezpečení v podnikovém prostředí](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referenční příručka k reakcím na incidenty](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="ir-2-preparation--set-up-incident-notification"></a>IR-2: Příprava – nastavení oznámení o incidentu 

**Pokyny:** Nastavte kontaktní informace pro incidenty zabezpečení ve službě Azure Security Center. Tyto kontaktní informace používá společnost Microsoft k tomu, aby vás kontaktovala v případě, že služba MSRC (Microsoft Security Response Center) zjistí, že s vašimi daty někdo nezákonně nebo neoprávněně pracoval. Máte také možnost přizpůsobit upozornění na incidenty a oznámení o incidentech v různých službách Azure v závislosti na tom, jak potřebujete na incidenty reagovat. 

- [Nastavení kontaktu ve službě Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detekce a analýza – vytvoření incidentů na základě upozornění s vysokou kvalitou

**Pokyny:** Zajistěte, abyste měli proces pro vytváření vysoce kvalitních upozornění a měření jejich kvality. To vám umožní poučit se z minulých incidentů a stanovit priority upozornění pro analytiky, aby nemuseli ztrácet čas s falešně pozitivními výsledky. 

Vysoce kvalitní upozornění se dají vytvářet na základě zkušeností z minulých incidentů, ověřených zdrojů komunity a nástrojů určených k vygenerování a vyčištění upozornění spojením a sladěním různých zdrojů signálu. 

Azure Security Center poskytuje vysoce kvalitní výstrahy napříč mnoha prostředky Azure. Pomocí datového konektoru ASC můžete upozornění streamovat do služby Azure Sentinel. Azure Sentinel umožňuje vytvářet rozšířená pravidla upozornění, která automaticky generují incidenty pro účely šetření. 

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

Azure Security Center jednotlivým upozorněním přiřazuje závažnost, která vám pomůže určit, jaká upozornění by se měla vyšetřit jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

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

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>Souč_hod-3: Vytvoření zabezpečených konfigurací pro výpočetní prostředky

**Pokyny**: pomocí Azure Security Center a Azure Policy navažte zabezpečené konfigurace na všech výpočetních prostředcích, včetně virtuálních počítačů, kontejnerů a dalších.

- [Jak monitorovat Azure Security Center doporučení](../security-center/security-center-recommendations.md) 

- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Provádění pravidelné simulace útoku

**Pokyny:** Podle potřeby provádějte testování průniku nebo aktivity červeného týmu na svých prostředcích Azure a zajistěte nápravu všech kritických zjištění v oblasti zabezpečení.
Postupujte podle pravidel zapojení testování průniku cloudových služeb Microsoftu (Microsoft Cloud Penetration Testing), abyste měli jistotu, že testy průniku neporušují zásady Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem.

- [Testování průniku v Azure](../security/fundamentals/pen-testing.md)

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="backup-and-recovery"></a>Zálohování a obnovy

*Další informace najdete v tématu [Azure Security Benchmark: zálohování a obnovení](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: zajištění pravidelného automatizovaného zálohování

**Doprovodné** materiály: vzhledem k tomu, že mezipaměť prostředí Azure HPC je řešení pro ukládání do mezipaměti, nikoli úložným systémem, zaměřte se na to, že jsou data v jejich cílových úložištích pravidelně zálohována. Řiďte se standardními postupy pro kontejnery objektů BLOB v Azure a zálohujte všechny místní cíle úložiště. 

Chcete-li minimalizovat přerušení v případě regionálního výpadku, můžete podniknout kroky k zajištění přístupu k datům mezi oblastmi.  

Každá instance mezipaměti HPC Azure se spouští v rámci konkrétního předplatného a v jedné oblasti. To znamená, že váš pracovní postup mezipaměti může být přerušený, pokud má oblast plný výpadek. Z důvodu minimalizace tohoto narušení by organizace měla používat back-end úložiště, které je přístupné z více oblastí. Toto úložiště může být místní systém NAS s příslušnou podporou DNS nebo úložištěm objektů BLOB v Azure, které se nachází v jiné oblasti než mezipaměť.

V případě, že váš pracovní postup pokračuje v primární oblasti, data se ukládají v dlouhodobém úložišti mimo oblast. Pokud oblast mezipaměti nebude k dispozici, můžete vytvořit duplicitní instanci Azure HPC cache v sekundární oblasti, připojit se ke stejnému úložišti a pokračovat v práci z nové mezipaměti.

- [Další informace o regionálním převzetí služeb při selhání](hpc-region-recovery.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="br-2-encrypt-backup-data"></a>BR-2: šifrování zálohovaných dat

**Doprovodné** materiály: Zajistěte, aby byly zálohy chráněny proti útokům. To by mělo zahrnovat šifrování záloh v zájmu ochrany před ztrátou důvěrných informací.

Pro místní zálohování pomocí Azure Backup k dispozici šifrování v klidovém formátu pomocí hesla, které zadáte. V případě pravidelného zálohování služeb Azure se zálohovaná data automaticky šifrují pomocí klíčů spravovaných platformou Azure. Můžete se rozhodnout Šifrovat zálohu pomocí klíčů spravovaných zákazníkem. V takovém případě zajistěte, aby byl tento klíč spravovaný zákazníkem v trezoru klíčů také v oboru zálohování.

Mezipaměť prostředí Azure HPC je taky chráněná šifrováním hostitele virtuálních počítačů na spravovaných discích, které uchovávají data uložená v mezipaměti, a to i v případě, že pro disky mezipaměti přidáte klíč zákazníka. Přidáním klíče spravovaného zákazníkem pro dvojité šifrování získáte další úroveň zabezpečení pro zákazníky s vysokými nároky na zabezpečení. Podrobnosti najdete v tématu šifrování služby Azure Disk Storage na straně serveru.

Použijte řízení přístupu na základě role v Azure Backup, Azure Key Vault nebo jiné prostředky k ochraně záloh a klíčů spravovaných zákazníkem. Kromě toho můžete povolit pokročilé funkce zabezpečení, které budou vyžadovat vícefaktorové ověřování, aby bylo možné změnit nebo odstranit zálohy.

- [Šifrování hostitele virtuálních počítačů](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

- [Šifrování na straně serveru služby Azure Disk Storage](../virtual-machines/disk-encryption.md)

- [Přehled funkcí zabezpečení v Azure Backup](../backup/security-overview.md) 

- [Šifrování zálohovaných dat pomocí klíčů spravovaných zákazníkem](../backup/encryption-at-rest-with-cmk.md)  

- [Postup zálohování klíčů Key Vault v Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-5.1.0)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: pravidelně zajistěte, aby bylo možné obnovit zálohované klíče spravované zákazníkem.

- [Postup obnovení klíčů Key Vault v Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-5.1.0)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Zmírnění rizika ztracených klíčů

**Doprovodné** materiály: Ujistěte se, že máte zavedené míry, abyste zabránili ztrátě klíčů a obnovili je. Povolte v Azure Key Vaultu obnovitelné odstranění a ochranu před vymazáním, aby byly klíče chráněné proti náhodnému nebo zlomyslnému odstranění.

- [Jak povolit obnovitelné odstranění a ochranu před vymazáním v Key Vaultu](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="governance-and-strategy"></a>Zásady správného řízení a strategie

*Další informace najdete v tématu [Azure Security Benchmark: zásady správného řízení a strategie](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definování strategie správy prostředků a ochrany dat 

**Pokyny:** Zadokumentujte si a dále sdělujte jasnou strategii pro nepřetržité monitorování a ochranu systémů a dat. Stanovte priority zjišťování, hodnocení, ochrany a monitorování nejdůležitějších podnikových dat a systémů. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující prvky: 

-   Standard klasifikace dat v souladu s obchodními riziky

-   Přehled organizace zabezpečení o rizicích a inventáři prostředků 

-   Schválení služeb Azure k používání provedené organizací zabezpečení 

-   Zabezpečení prostředků po dobu jejich životního cyklu

-   Požadovaná strategie řízení přístupu v souladu s klasifikací dat organizace

-   Použití funkcí pro ochranu dat nativních pro Azure i třetích stran

-   Požadavky na šifrování dat pro případy použití v přenosech a u neaktivních uložených dat

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

- [Azure Security Benchmark – privilegovaný přístup](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Osvědčený postup zabezpečení Azure 11 – Architektura: jednotná strategie zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Přehled zabezpečení správy identit v Azure](../security/fundamentals/identity-management-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definování strategie protokolování a reakcí na hrozby

**Pokyny:** Zřiďte strategii protokolování a reakcí na hrozby, která umožní rychle zjistit a zneškodnit hrozby při současném splnění požadavků na dodržování předpisů. Stanovte prioritu poskytování vysoce kvalitních upozornění a bezproblémového prostředí analytikům, aby se mohli soustředit na hrozby, nikoli na integraci a ruční kroky. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující prvky: 

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