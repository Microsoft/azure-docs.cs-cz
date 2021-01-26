---
title: Základní hodnoty zabezpečení Azure pro virtuální počítače s Windows
description: Základní hodnoty zabezpečení virtuálních počítačů s Windows poskytují pokyny a zdroje pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e987f96402f4c922bdaca8ecf32348bc99c34199
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798395"
---
# <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Základní hodnoty zabezpečení Azure pro virtuální počítače s Windows

Tato základní hodnota zabezpečení se týká pokynů z [Azure Security test 2,0](../security/benchmarks/overview.md) na virtuální plochu Windows. Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah se seskupuje podle **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů vztahujících se na virtuální plochu Windows. Neexistují **ovládací prvky** , které se vztahují k virtuálním plochám Windows.

Služba Virtual Desktop systému Windows zahrnuje samotnou službu, Windows 10 Enterprise pro virtuální SKU s více relacemi a také FSLogix. Doporučení zabezpečení související s FSLogix najdete v tématu [základní informace o zabezpečení úložiště](../storage/common/security-baseline.md). Služba má agenta běžícího na virtuálních počítačích, ale vzhledem k tomu, že virtuální počítače mají pod kontrolou plnou kontrolu nad zákazníkem, dodržujte [doporučení zabezpečení pro výpočetní](../virtual-machines/windows/security-baseline.md) prostředky.

Pokud chcete zjistit, jak se virtuální plocha Windows kompletně mapuje na srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot zabezpečení virtuálního počítače se systémem Windows](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementace zabezpečení pro interní provoz

**Pokyny**: při nasazení virtuálních počítačů, které se mají zaregistrovat na virtuální plochu Windows, musíte vytvořit nebo použít existující virtuální síť. Zajistěte, aby všechny virtuální sítě Azure dodržovaly princip segmentace podniku, který se rovná obchodním rizikům. Všechny systémy, které by mohly způsobit vyšší riziko pro organizaci, by měly být izolované v rámci své vlastní virtuální sítě a dostatečně zabezpečené pomocí skupiny zabezpečení sítě nebo Azure Firewall.

Pomocí adaptivních funkcí posílení zabezpečení sítě v Azure Security Center můžete doporučit konfigurace skupin zabezpečení sítě, které omezují porty a zdrojové IP adresy s odkazem na pravidla externích přenosů sítě.

Na základě vašich aplikací a strategie segmentace segmentace můžete omezit nebo povolit provoz mezi interními prostředky na základě pravidel skupiny zabezpečení sítě. Pro konkrétní dobře definované aplikace (jako je například aplikace se třemi vrstvami) může to být vysoce zabezpečený odepřít ve výchozím nastavení, povolit výjimku "přístup". To se nemusí dobře škálovat, pokud máte mnoho aplikací a koncových bodů vzájemně pracujících. Můžete také použít Azure Firewall v případech, kdy je centrální Správa nutná přes velký počet podnikových segmentů nebo paprsků (v topologii hub/paprsků).

Pro skupiny zabezpečení sítě přidružené k virtuálnímu počítači (které jsou součástí virtuálních počítačů Windows) je potřeba, abyste povolili odchozí přenosy do konkrétních koncových bodů. 

- [Zjistěte, jaké adresy URL je potřeba mít povolený přístup k virtuálnímu počítači s Windows.](safe-url-list.md)

- [Adaptivní posílení zabezpečení sítě v Azure Security Center](../security-center/security-center-adaptive-network-hardening.md) 

- [Azure Firewall pro virtuální počítač s Windows ](../firewall/protect-windows-virtual-desktop.md)

- [Jak vytvořit skupinu zabezpečení sítě s pravidly zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Jak nasadit a nakonfigurovat Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ns-2-connect-private-networks-together"></a>NS-2: spojování privátních sítí

**Pokyny**: pomocí Azure ExpressRoute nebo virtuální privátní sítě Azure můžete vytvořit privátní připojení mezi datacentry Azure a místní infrastrukturou v prostředí kolokace. Připojení ExpressRoute nevyužívají veřejný Internet, nabízejí spolehlivější a rychlejší a nižší latenci než typická připojení přes Internet. 

Pro virtuální privátní sítě typu Point-to-site a Site-to-site můžete připojit místní zařízení nebo sítě k virtuální síti pomocí libovolné kombinace možností virtuální privátní sítě a Azure ExpressRoute.

Pomocí partnerského vztahu virtuálních sítí můžete propojit dvě nebo víc virtuálních sítí společně v Azure. Síťový provoz mezi partnerskými virtuálními sítěmi je privátní a zůstává v páteřní síti Azure.

- [Jaké jsou modely připojení ExpressRoute](../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN – přehled](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Partnerský vztah virtuální sítě](/azure/virtual-network/virtual-network-peering-overvie)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Ochrana aplikací a služeb před útoky z externích sítí

**Doprovodné** materiály: použijte Azure firewall k ochraně aplikací a služeb před potenciálně škodlivým provozem z Internetu a dalších externích umístění. Chraňte své prostředky virtuálních klientů s Windows proti útokům z externích sítí, včetně distribuovaných útoků na útoky, specifických pro aplikace, nevyžádaných a potenciálně škodlivých internetových přenosů. Zabezpečte své prostředky proti distribuovaným útokům na útoky tím, že ve virtuálních sítích Azure povolíte standardní ochranu DDoS. Pomocí Azure Security Center můžete detekovat nepřesná rizika konfigurace související s prostředky souvisejícími se sítí.

Virtuální počítač s Windows není určený ke spouštění webových aplikací a nevyžaduje konfiguraci jakýchkoli dalších nastavení ani nasazení žádné další síťové služby, která je chrání před útoky z externích sítí, které cílí na webové aplikace.

- [Dokumentace k Azure Firewall](/azure/firewall)

- [Správa Azure DDoS Protection Standard pomocí Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Doporučení pro Azure Security Center](../security-center/recommendations-reference.md#networking-recommendations)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: nasazení systémů pro zabránění vniknutí/prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: použití Azure firewall s filtrováním na základě hrozeb pro výstrahy a volitelně zablokování provozu do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu. Když se vyžaduje Kontrola datové části, můžete z Azure Marketplace nasadit řešení pro zjišťování nebo prevenci neoprávněných vniknutí třetí strany. 

Pokud máte regulativní nebo jiný požadavek na zjišťování nebo ochranu před neoprávněnými vniknutími, zajistěte, aby byl vždy vyladěný tak, aby poskytoval vysoce kvalitní výstrahy do řešení SIEM (Security Information and Event Management).

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace zahrnuje možnosti ID třetích stran.](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [EDR schopnost služby Microsoft Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/overviewendpoint-detection-response)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: zjednodušení pravidel zabezpečení sítě

**Pokyny**: pomocí značek služeb Azure Virtual Network můžete definovat řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall nakonfigurovaných pro prostředky virtuálních počítačů s Windows. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (například: WindowsVirtualDesktop) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Pochopení a používání značek služeb](../virtual-network/service-tags-overview.md)

- [Další informace o tom, co se týká značky služby virtuálních počítačů s Windows, najdete tady. ](safe-url-list.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [Azure Security Benchmark: správa identit](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizace Azure Active Directory jako centrálního systému pro identifikaci a ověřování

**Pokyny**: virtuální plocha Windows používá jako výchozí službu pro správu identit a přístupu Azure Active Directory (Azure AD). Službu Azure AD byste měli standardizovat, abyste mohli řídit identitu a správu přístupu vaší organizace v nástroji:

- Microsoft Cloud prostředky, jako jsou Azure Portal, Azure Storage, virtuální počítače Azure (Linux a Windows), Azure Key Vault, PaaS a SaaS aplikace.

- prostředky vaší organizace, jako jsou aplikace v Azure nebo prostředky vaší podnikové sítě

Zabezpečení služby Azure AD by mělo mít vysokou prioritu v praxi cloudového zabezpečení vaší organizace. Azure AD poskytuje bezpečnostní skóre identity, které vám pomůže vyhodnotit stav zabezpečení identity vzhledem k doporučeným osvědčeným postupům od Microsoftu. Pomocí skóre můžete vyhodnotit, jak přesně vaše konfigurace vyhovuje doporučeným osvědčeným postupům, a provádět v zabezpečení vylepšení.

Azure AD podporuje externí identity, které umožňují uživatelům bez účet Microsoft přihlašovat se k aplikacím a prostředkům s jejich externí identitou.

- [Architektura tenantů v Azure AD](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Použití externích zprostředkovatelů identity pro aplikaci](/azure/active-directory/b2b/identity-providers)

- [Co je skóre zabezpečení identit v Azure AD?](../active-directory/fundamentals/identity-secure-score.md)

- [Konkrétní role, které potřebujete k provozu s virtuálním počítačem s Windows ](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Zabezpečená a automatická správa identit aplikací

**Pokyny**: virtuální plocha Windows podporuje spravované identity Azure pro jiné než lidské účty, jako jsou služby nebo automatizace. Místo vytvoření výkonnějšího lidského účtu pro přístup k prostředkům nebo jejich provádění doporučujeme použít funkci Azure Managed identity. 

Windows Virtual Desktop doporučuje použít Azure Active Directory (Azure AD) k vytvoření instančního objektu s omezenými oprávněními na úrovni prostředků ke konfiguraci instančních objektů s přihlašovacími údaji certifikátu a vrácení zpět klientským tajným klíčům. V obou případech se Azure Key Vault dá použít ve spojení se spravovanými identitami Azure, takže běhové prostředí (například funkce Azure) může přihlašovací údaje získat z trezoru klíčů.

- [Služby, které podporují spravované identity prostředků Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Instanční objekt Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Vytvoření instančního objektu s certifikáty](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Použití Azure Key Vault k registraci objektu zabezpečení](../key-vault/general/authentication.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Použití jednotného přihlašování (SSO) Azure AD pro přístup k aplikacím

**Pokyny**: virtuální plocha Windows používá Azure Active Directory (Azure AD) k zajištění správy identit a přístupu k prostředkům Azure, cloudovým aplikacím a místním aplikacím. Patří sem podnikové identity, jako jsou zaměstnanci, i externí identity, jako jsou partneři a dodavatelé. Díky tomu je možné použít jednotné přihlašování (SSO) ke správě a zabezpečení přístupu k datům a prostředkům vaší organizace v místním prostředí a v cloudu. Připojte všechny své uživatele, aplikace a zařízení k Azure AD, abyste mohli zajistit bezproblémové zabezpečení přístupu s větší viditelností a řízením.

- [Principy jednotného přihlašování k aplikacím pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Použití řídicích prvků silného ověřování pro veškerý přístup založený na Azure Active Directory 

**Pokyny**: virtuální plocha Windows používá Azure Active Directory (Azure AD), která podporuje ovládací prvky silného ověřování, a to prostřednictvím vícefaktorového ověřování a silných metod bez hesla.

- Vícefaktorové ověřování – povolte vícefaktorové ověřování Azure AD a sledujte doporučení pro správu identit a přístupu z Azure Security Center pro některé osvědčené postupy v nastavení vícefaktorového ověřování. Vícefaktorové ověřování lze vyhovět všem uživatelům nebo na úrovni jednotlivých uživatelů, a to na základě podmínek přihlášení a rizikových faktorů.

- Ověřování bez hesla – k dispozici jsou tři možnosti ověřování bez hesla: Windows Hello pro firmy, aplikace Microsoft Authenticator a místní metody ověřování, jako jsou čipové karty.

Virtuální počítač s Windows podporuje starší ověřování založené na heslech, jako jsou jenom cloudové účty (uživatelské účty vytvořené přímo v Azure), které mají základní zásady pro hesla nebo hybridní účty (uživatelské účty z místní služby Azure AD, které následují po místních zásadách hesel). Při použití ověřování založeného na hesle poskytuje Azure AD funkci ochrany heslem, která uživatelům brání v nastavení hesla, která se snadno vyhodnotí. Společnost Microsoft poskytuje globální seznam zakázaných hesel, která jsou aktualizována na základě telemetrie, a zákazníci mohou seznam rozšířit podle svých potřeb (například branding, kulturní odkazy atd.). Tato ochrana heslem se dá použít jenom pro cloudové a hybridní účty.

Ověřování založené jenom na přihlašovacích údajích hesla je náchylné k oblíbeným metodám útoku. Pro zajištění vyššího zabezpečení používejte silné ověřování, jako je vícefaktorové ověřování a zásady silného hesla. Pro aplikace třetích stran a služby Marketplace, které mohou mít výchozí hesla, je třeba je změnit při počátečním nastavení služby.

Pro správce a privilegované uživatele zkontrolujte, že se používají nejvyšší úroveň metod silného ověřování, a pak uveďte příslušné zásady silného ověřování pro ostatní uživatele.

- [Úvod do možností ověřování bez hesla pro Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Výchozí zásady hesel Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Eliminujte chybná hesla pomocí Azure Active Directory ochrany heslem.](../active-directory/authentication/concept-password-ban-bad.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorování a upozornění na anomálie účtů

**Pokyny**: virtuální klient Windows je integrovaný do služby Azure Active Directory (Azure AD), která poskytuje následující zdroje dat:

- Přihlášení – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.

- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.

- Rizikové přihlášení – rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Tyto zdroje dat je možné integrovat s Azure Monitor, Azure Sentinelem nebo systémy správy událostí a SIEM (Security Information) třetích stran. Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako jsou nadměrný počet neúspěšných pokusů o ověření nebo zastaralé účty v předplatném. Rozšířená ochrana před internetovými útoky (ATP) v Azure je řešení zabezpečení, které dokáže na základě signálů Active Directory identifikovat, detekovat a vyšetřovat pokročilé hrozby, ohrožené identity a škodlivé činnosti vnitřních účastníků.

- [Sestavy aktivit auditu v Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Zobrazení rizikových přihlášení Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Upozornění v modulu ochrany na základě analýzy hrozeb služby Azure Security Center](../security-center/alerts-reference.md)

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Omezení přístupu k prostředkům Azure na základě podmínek

**Pokyny**: virtuální plocha Windows podporuje podmíněný přístup pomocí služby Azure Active Directory (Azure AD) pro podrobný přístup k řízení přístupu na základě uživatelem definovaných podmínek. Například přihlášení uživatelů z určitých rozsahů IP adres může být vyžadováno pro použití vícefaktorového ověřování pro přístup. 

V různých případech použití je taky možné použít podrobné zásady správy relace ověřování.

- [Přehled podmíněného přístupu Azure](../active-directory/conditional-access/overview.md) 

- [Obecné zásady podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurace správy relací ověřování pomocí podmíněného přístupu](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [Informace o nastavení podmíněného přístupu specifického pro virtuální počítače s Windows najdete tady.](set-up-mfa.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security Benchmark: privilegovaný přístup](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Omezení přístupu pro správu k nejdůležitějším podnikovým systémům

**Pokyny**: virtuální plocha Windows používá řízení přístupu na základě role Azure (Azure RBAC) k izolaci přístupu k důležitým podnikovým systémům. Ujistěte se, že jste také omezili přístup k systémům pro správu, identitu a zabezpečení, které mají přístup pro správu k vašemu podnikovému přístupu, jako jsou Doména služby Active Directory Controllers, nástroje zabezpečení a nástroje pro správu systému s agenty nainstalovanými na důležitých podnikových systémech. Útočníci, kteří ohrožují tyto systémy správy a zabezpečení, můžou potenciálně hned weaponize k ohrožení důležitých podnikových prostředků.

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu.

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Přístup ke skupině pro správu](../governance/management-groups/overview.md#management-group-access) 

- [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Minimální oprávnění správce potřebná ke správě virtuálních klientů Windows](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Pravidelná kontrola a sjednocování přístupu uživatelů

**Pokyny**: virtuální počítač s Windows používá ke správě svých prostředků účty Azure Active Directory (Azure AD), pravidelně kontrolují uživatelské účty a přiřazení přístupu, aby se zajistilo, že účty a jejich přístup budou platné.

Pomocí kontrol přístupu ke službě Azure AD zkontrolujte členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Vytváření sestav Azure AD může poskytovat protokoly, které vám pomůžou zjistit zastaralé účty.

Kromě toho je možné Azure Privileged Identity Management nakonfigurovat tak, aby výstrahu při vytváření nadměrného počtu účtů správců a identifikaci účtů správce, které jsou zastaralé nebo nesprávně nakonfigurované.

Některé služby Azure podporují místní uživatele a role, které se nespravují prostřednictvím služby Azure AD. Tyto uživatele budete muset spravovat samostatně.

- [Předdefinované role pro virtuální počítače s Windows](rbac.md)

- [Vytvoření kontroly přístupu pro role prostředků Azure v Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Nastavení nouzového přístupu v Azure AD

**Pokyny**: virtuální plocha Windows používá ke správě prostředků službu Azure Active Directory (Azure AD). Pokud se chcete vyhnout náhodnému uzamčení vaší organizace Azure AD, nastavte účet pro nouzový přístup pro přístup, když nebudete moct použít normální účty pro správu. Účty pro nouzový přístup jsou obvykle vysoce privilegované a neměly by se přiřazovat konkrétním jednotlivcům. Jejich použití je omezené na nouzové nebo kritické situace, kdy se nedají použít běžné účty pro správu.

Měli byste zajistit, aby přihlašovací údaje (například heslo, certifikát nebo čipová karta) pro účty pro nouzový přístup byly zabezpečené a aby o nich věděli jenom ti, kteří jsou oprávněni je použít jenom v případě nouze.

- [Správa účtů pro nouzový přístup v Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizace správy nároků 

**Pokyny**: virtuální plocha Windows je integrovaná s Azure Active Directory (Azure AD) pro správu prostředků. Využijte funkce správy opravňujících k Azure AD k automatizaci pracovních postupů žádostí o přístup, včetně přiřazení přístupu, revizí a vypršení platnosti. V dalších duálních případech se podporuje i schválení dvou nebo více fází.

- [Co jsou kontroly přístupu Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Co je Správa nároků Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Použití pracovních stanic s privilegovaným přístupem

**Doprovodné** materiály: zabezpečené a izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých rolí, například správců, vývojářů a důležitých operátorů služeb. Pro úlohy správy Používejte vysoce zabezpečené uživatelské pracovní stanice nebo Azure bastionu. 

Pomocí Azure Active Directory (Azure AD), programu Microsoft Defender Advanced Threat Protection (ATP) nebo Microsoft Intune nasaďte zabezpečenou a spravovanou pracovní stanici uživatelů pro úlohy správy. Zabezpečenou pracovní stanici je možné centrálně spravovat tak, aby vynutila zabezpečenou konfiguraci, včetně silného ověřování, standardních hodnot softwaru a hardwaru, omezeného logického a síťového přístupu.

- [Pochopení pracovních stanic s privilegovaným přístupem](/azure/active-directory/devices/concept-azure-managed-workstation) 

- [Nasazení pracovní stanice s privilegovaným přístupem](/azure/active-directory/devices/howto-azure-managed-workstation)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Využívání správy jen v potřebném rozsahu (princip nejnižší úrovně nutných oprávnění) 

**Pokyny**: virtuální klient Windows je integrovaný s řízením přístupu na základě role Azure (Azure RBAC) za účelem správy prostředků. Azure RBAC umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, skupinám instančních objektů a spravovaným identitám. Pro určité prostředky existují předdefinované role, které je možné inventarizovat nebo dotazovat pomocí nástrojů, jako jsou Azure CLI, Azure PowerShell nebo Azure Portal. 

Oprávnění, která přiřadíte k prostředkům pomocí Azure RBAC, by měla být vždy omezená na ta, která jsou vyžadována rolemi. Tím se doplňuje přístup k JIT (just in time) Privileged Identity Management (PIM) s Azure Active Directory (Azure AD) a měl by se pravidelně kontrolovat.

Kromě toho používejte předdefinované role k přidělení oprávnění a k vytváření vlastních rolí v případě potřeby.

- [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Konfigurace řízení přístupu na základě role Azure](../role-based-access-control/role-assignments-portal.md) 

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Předdefinované role pro virtuální počítače s Windows](rbac.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: výběr procesu schválení pro podporu Microsoftu  

**Doprovodné** materiály: ve scénářích podpory, kde Microsoft potřebuje získat přístup k zákaznickým datům, podporuje Windows Virtual Desktop Customer Lockbox k tomu, aby poskytovala rozhraní pro kontrolu a schválení nebo odmítnutí žádostí o přístup k datům zákazníků.

- [Pochopení Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Zjišťování, klasifikace a označení citlivých dat

**Doprovodné** materiály: zjišťování, klasifikace a označování citlivých dat, aby bylo možné navrhnout příslušné ovládací prvky. K tomu je potřeba zajistit, aby byly citlivé informace uloženy, zpracovávány a bezpečně přenášeny technologickými systémy organizace.

Použijte Azure Information Protection (a příslušný nástroj pro kontrolu) pro citlivé informace v dokumentech Office v Azure, v místním prostředí, na Office 365 a na dalších místech.

Služba Azure SQL Information Protection vám může pomoct s klasifikací a označováním informací uložených v databázích Azure SQL.

- [Označování citlivých informací s využitím služby Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Implementace zjišťování dat Azure SQL](/azure/sql-database/sql-database-data-discovery-and-classification)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrana citlivých dat

**Doprovodné** materiály: Chraňte citlivá data omezením přístupu pomocí Access Control založeného na rolích Azure (Azure RBAC), řízení přístupu na základě sítě a konkrétních ovládacích prvků ve službách Azure (například šifrování v SQL a dalších databázích).

Aby se zajistilo konzistentní řízení přístupu, všechny typy řízení přístupu by měly být v souladu s vaší podnikovou strategií segmentace. Podniková strategie segmentace by měla vycházet také z umístění citlivých nebo důležitých obchodních dat a systémů.

Microsoft považuje veškerý obsah zákazníka za citlivý a chrání před ztrátou a expozicí zákaznických dat. V zájmu zajištění zabezpečení zákaznických dat v rámci Azure Microsoft implementovat určité výchozí kontrolní mechanismy a funkce ochrany dat.

- [Řízení přístupu na základě role (RBAC) Azure](../role-based-access-control/overview.md) 

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Monitorování neautorizovaného přenosu citlivých dat

**Pokyny:** Monitorujte neautorizovaný přenos dat do umístění mimo dohled a kontrolu podniku. To obvykle zahrnuje monitorování neobvyklých aktivit (velké nebo neobvyklé přenosy), které můžou značit neautorizovanou exfiltraci dat.

Funkce rozšířené ochrany před internetovými útoky (ATP) pomocí Azure Storage a služby Azure SQL ATP mohou upozorňovat na neobvyklé přenos informací, což znamená, co by mohlo být neoprávněné přenosy citlivých informací.

Azure Information Protection (AIP) nabízí funkce monitorování důvěrných a označených informací.

Využijte řešení pro ochranu před únikem informací, jako jsou například hostitelé, k prosazování vyšetřovací nástroj a preventivních kontrol, které zabraňují exfiltrace dat.

- [Povolení ochrany ATP pro Azure SQL](../azure-sql/database/threat-detection-overview.md) 

- [Povolení ochrany ATP pro službu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="asset-management"></a>Správa aktiv

*Další informace najdete v tématu [Azure Security Benchmark: správa prostředků](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Zajištění přehledu bezpečnostního týmu o rizicích pro prostředky

**Pokyny:** Zajistěte udělení oprávnění Čtenář zabezpečení ve vašem tenantovi Azure a vašich předplatných bezpečnostním týmům, aby mohly monitorovat bezpečnostní rizika s využitím služby Azure Security Center. 

V závislosti na tom, jak jsou strukturované zodpovědnosti týmu zabezpečení, může být monitorování rizik zabezpečení zodpovědností centrálního týmu zabezpečení nebo místního týmu. Přehledy zabezpečení a bezpečnostní rizika se však v rámci organizace musí vždy agregovat centrálně. 

Oprávnění Čtenář zabezpečení je možné přidělit pro celého tenanta (kořenová skupina pro správu) nebo je vymezit na konkrétní skupiny pro správu nebo konkrétní předplatná. 

K viditelnosti úloh a služeb se můžou vyžadovat další oprávnění. 

- [Přehled role Čtenář zabezpečení](../role-based-access-control/built-in-roles.md#security-reader)

- [Přehled skupin pro správu Azure](../governance/management-groups/overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Zajištění přístupu k inventáři a metadatům prostředků pro bezpečnostní tým

**Doprovodné** materiály: použití značek pro prostředky Azure, skupiny prostředků a předplatná k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

Pomocí inventáře virtuálních počítačů Azure můžete automatizovat shromažďování informací o softwaru na Virtual Machines. Z Azure Portal je k dispozici název softwaru, verze, Vydavatel a čas aktualizace. Chcete-li získat přístup k datu instalace a dalším informacím, povolte diagnostiku na úrovni hosta a přiveďte protokoly událostí systému Windows do Log Analyticsho pracovního prostoru.

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md) 

- [Správa inventáře Azure Security Center prostředků](../security-center/asset-inventory.md) 

- [Průvodce rozhodováním ohledně pojmenování a označování prostředků](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Jak povolit inventář virtuálních počítačů Azure](../automation/automation-tutorial-installed-software.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používání jenom schválených služeb Azure

**Doprovodné** materiály: pomocí Azure Policy můžete auditovat a omezovat, které služby můžou uživatelé zřídit ve vašem prostředí. Pomocí Azure Resource Graphu se můžete dotazovat na prostředky v rámci jejich předplatných a zjišťovat je. Pomocí Azure Monitoru můžete také vytvořit pravidla pro aktivaci upozornění při zjištění neschválené služby.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Zajištění zabezpečení správy životního cyklu prostředků

**Doprovodné** materiály: nelze použít. Virtuální počítač s Windows se nedá použít k zajištění zabezpečení prostředků v procesu správy životního cyklu. Je zodpovědností zákazníka udržovat atributy a síťové konfigurace prostředků, které jsou považovány za vysoce ovlivněné. 

Doporučuje se, aby zákazník vytvořil proces pro zachycení atributů a změny konfigurace sítě, jak je to možné, a to měření vlivu na změny a vytváření úloh.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat se správcem prostředků Azure tak, že nakonfigurujete "blokování přístupu" pro aplikaci "Microsoft Azure Management".

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>DOP. 6: používejte ve výpočetních prostředcích jenom schválené aplikace.

**Pokyny**: pomocí inventáře virtuálních počítačů Azure můžete automatizovat shromažďování informací o veškerém softwaru na virtuálních počítačích. Z Azure Portal je k dispozici název softwaru, verze, Vydavatel a čas aktualizace. Chcete-li získat přístup k datu instalace a dalším informacím, povolte diagnostiku na úrovni hosta a přiveďte protokoly událostí systému Windows do Log Analyticsho pracovního prostoru.

- [Jak povolit inventář virtuálních počítačů Azure](../automation/automation-tutorial-installed-software.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v tématu [Azure Security Benchmark: protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: povolení detekce hrozeb pro prostředky Azure

**Pokyny**: Využijte možnost Azure Security Center integrovanou funkci detekce hrozeb a povolte Azure Defender (ve formě rozšířené ochrany před internetovými útoky Azure) pro prostředky virtuálních počítačů s Windows. Azure Defender pro Windows Virtual Desktop nabízí další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k prostředkům virtuálního počítače s Windows nebo jejich zneužití.

Předejte všechny protokoly z virtuálního klienta Windows do řešení pro správu událostí zabezpečení (SIEM), které se dá použít k nastavení rozpoznávání vlastních hrozeb. Ujistěte se, že sledujete různé typy prostředků Azure pro potenciální hrozby a anomálie. Zaměřte se na získávání vysoce kvalitních výstrah, které omezí falešně pozitivní výsledky pro analytiky k řazení. Výstrahy se dají nacházet z dat protokolů, agentů nebo jiných dat.

- [Ochrana před hrozbami v Azure Security Center](/azure/security-center/threat-protection) 

- [Referenční příručka pro Azure Security Center výstrahy zabezpečení](../security-center/alerts-reference.md)

- [Vytváření vlastních pravidel analýzy pro detekci hrozeb](../sentinel/tutorial-detect-threats-custom.md) 

- [Funkce Azure Sentinel pro internetovou analýzu hrozeb](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Povolení detekce hrozeb pro správu identit a přístupu v Azure

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD, které jsou integrované s Azure monitor, Azure Sentinel nebo jiné Siem (Security Information and Event Management) nebo nástroje pro monitorování pro další sofistikované funkce monitorování a analýzy:

- Přihlášení – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.

- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.

- Rizikové přihlášení – rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření a zastaralých účtů v rámci předplatného. Kromě základního monitorování hygieny zabezpečení může modul ochrany před internetovými útoky ve službě Azure Security Center taky shromažďovat podrobnější výstrahy zabezpečení od jednotlivých výpočetních prostředků Azure (virtuální počítače, kontejnery, App Service), datové prostředky (SQL DB a Storage) a vrstvy služeb Azure. Díky této funkci můžete mít přehled o anomáliích v účtech v jednotlivých prostředcích.

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Povolení ochrany identit Azure](../active-directory/identity-protection/overview-identity-protection.md) 

- [Ochrana před hrozbami v Azure Security Center](/azure/security-center/threat-protection)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Povolení protokolování pro síťové aktivity Azure

**Pokyny**: virtuální počítač s Windows nevytváří ani nezpracovává protokoly dotazů služby DNS (Domain Name Service). Prostředky, které jsou zaregistrované ve službě, ale můžou vydávat protokoly toku.

Povolte a Shromážděte protokoly prostředků a toků skupin zabezpečení sítě, protokoly Azure Firewall a brány firewall webových aplikací (WAF) pro analýzu zabezpečení pro podporu vyšetřování incidentů, loveckí hrozeb a generování výstrah zabezpečení. Protokoly toku můžete odeslat do pracovního prostoru Azure Monitor Log Analytics a pak pomocí Analýza provozu získat přehledy.

- [Jak povolit protokoly toku skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Metriky a protokoly Azure Firewallu](/azure/firewall/logs-and-metrics) 

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md) 

- [Řešení monitorování sítě Azure v Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Povolení protokolování pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit, které jsou automaticky povoleny, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky virtuálních počítačů s Windows s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizace správy a analýz protokolu zabezpečení

**Doprovodné** materiály: centralizace úložiště protokolování a analýzy pro povolení korelace. U každého zdroje protokolu se ujistěte, že jste přiřadili vlastníka dat, pokyny pro přístup, umístění úložiště, nástroje používané pro zpracování a přístup k datům a požadavky na uchovávání dat.

Ujistěte se, že Integrujte protokoly aktivit Azure do svého centrálního protokolování. Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Kromě toho povolte a zaveďte data do Azure Sentinel nebo správy událostí zabezpečení jiných výrobců (SIEM). Řada organizací zvolí použití Azure Sentinel pro "horká" data, která se používají často a Azure Storage pro "studená" data, která se používají méně často.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](/azure/security/benchmarks/security-controls-v2-incident-response).*

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

Azure Security Center jednotlivým upozorněním přiřazuje závažnost, která vám pomůže určit, jaká upozornění by se měla vyšetřit jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho můžete prostředky označit pomocí značek a vytvořit systém vytváření názvů, který vám pomůže identifikovat a kategorizovat prostředky Azure, zejména prostředky zpracovávající citlivá data. Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

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

*Další informace najdete v tématu [Azure Security Benchmark: stav a správa ohrožení zabezpečení](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>Souč_hod-3: Vytvoření zabezpečených konfigurací pro výpočetní prostředky

**Pokyny**: pomocí Azure Security Center a Azure Policy navažte zabezpečené konfigurace na všech výpočetních prostředcích, včetně virtuálních počítačů, kontejnerů a dalších.

K vytvoření konfigurace zabezpečení operačního systému vyžadovaného vaší organizací můžete použít vlastní image operačního systému nebo konfiguraci stavu Azure Automation.

- [Jak monitorovat Azure Security Center doporučení](../security-center/security-center-recommendations.md) 

- [Přehled konfigurace stavu Azure Automation](../automation/automation-dsc-overview.md) 

- [Prostředí Windows Virtual Desktop](environment-setup.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: udržování zabezpečených konfigurací pro výpočetní prostředky

**Pokyny**: pomocí Azure Security Center a Azure Policy pravidelně vyhodnoťte a opravte rizika konfigurace na výpočetních prostředcích Azure, včetně virtuálních počítačů, kontejnerů a dalších. Kromě toho můžete použít šablony Azure Resource Manager, vlastní image operačního systému nebo konfiguraci stavu Azure Automation k údržbě konfigurace zabezpečení operačního systému vyžadovaného vaší organizací. Šablony virtuálních počítačů Microsoft v kombinaci s konfigurací stavu Azure Automation můžou pomoct při plnění a údržbě požadavků na zabezpečení.

Azure Marketplace image virtuálních počítačů publikované Microsoftem jsou spravované a udržované společností Microsoft.

Azure Security Center může také kontrolovat ohrožení zabezpečení v imagi kontejneru a provádí nepřetržité monitorování konfigurace Docker v kontejnerech proti srovnávacímu testu Docker Internet Security. Pomocí stránky doporučení Azure Security Center můžete zobrazit doporučení a opravit problémy.

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Jak vytvořit virtuální počítač Azure ze šablony ARM](../virtual-machines/windows/ps-template.md) 

- [Přehled konfigurace stavu Azure Automation](../automation/automation-dsc-overview.md) 

- [Zabezpečení kontejnerů ve službě Security Center](../security-center/container-security.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: bezpečné uložení vlastních imagí operačního systému a kontejneru

**Pokyny**: Windows Virtual Desktop umožňuje zákazníkům spravovat image operačního systému. Pomocí řízení přístupu na základě role Azure (Azure RBAC) zajistěte, aby k vašim vlastním imagím měli přístup jenom autorizovaní uživatelé. Pomocí Galerie sdílených imagí Azure můžete své image sdílet s různými uživateli, instančními objekty nebo skupinami služby Active Directory v rámci vaší organizace. Uložte image kontejneru do Azure Container Registry a pomocí RBAC zajistěte, aby měli přístup jenom autorizovaní uživatelé.

- [Pochopení Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [Přehled Galerie sdílených imagí](/azure/virtual-machines/windows/shared-image-galleries)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6: provádění posouzení ohrožení zabezpečení softwaru

**Pokyny**: virtuální počítač s Windows umožňuje nasadit vlastní virtuální počítače a zaregistrovat je do služby a také používat SQL Database běžící v prostředí.

Virtuální plocha Windows může pro posouzení ohrožení zabezpečení síťových zařízení a webových aplikací využít řešení třetí strany. Při provádění vzdálených kontrol nepoužívejte jediný, trvalá a administrativní účet. Zvažte implementaci metodologie zřizování JIT pro účet skenování. Přihlašovací údaje pro účet pro kontrolu by měly být chráněné, monitorované a používané jenom pro kontrolu ohrožení zabezpečení.

V případě potřeby můžete exportovat výsledky kontroly v konzistentních intervalech a porovnat výsledky s předchozími kontrolami a ověřit tak, že chyby zabezpečení byly opraveny.

Pro posouzení ohrožení zabezpečení na virtuálních počítačích Azure (a serverech SQL) použijte doporučení od Azure Security Center. Azure Security Center má integrovaný skener ohrožení zabezpečení pro virtuální počítače, image kontejnerů a SQL Database.

Podle potřeby exportujte výsledky kontroly v konzistentních intervalech a porovnejte výsledky s předchozími kontrolami, abyste ověřili, že chyby zabezpečení byly opraveny. Pokud používáte doporučení ke správě ohrožení zabezpečení, kterou navrhla Azure Security Center, můžete se na portál vybraného řešení překlopit a zobrazit historická data kontroly.

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Integrovaná bezpečnostní skenery pro virtuální počítače](/azure/security-center/built-in-vulnerability-assessment) 
- [Posouzení ohrožení zabezpečení SQL](../azure-sql/database/sql-vulnerability-assessment.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Rychlá a automatická náprava ohrožení zabezpečení softwaru

**Pokyny**: virtuální počítač s Windows nepoužívá nebo nevyžaduje žádný software třetí strany. Virtuální počítač s Windows ale umožňuje nasadit vlastní virtuální počítače a zaregistrovat je ve službě.

Pomocí Azure Automation Update Management nebo řešení třetí strany zajistěte, aby byly na virtuálních počítačích s Windows serverem nainstalované nejnovější aktualizace zabezpečení. U virtuálních počítačů s Windows ověřte, že je povolená možnost web Windows Update a že se nastaví automatické aktualizace.

Použijte řešení pro správu oprav třetí strany pro software třetích stran nebo System Center Updates Publisher pro Configuration Manager.

- [Jak nakonfigurovat Update Management pro virtuální počítače v Azure](/azure/automation/automation-update-management) 

- [Správa aktualizací a oprav pro virtuální počítače Azure](/azure/automation/automation-tutorial-update-management)

- [Konfigurace služby Microsoft Endpoint Configuration Manager pro virtuální počítače s Windows](configure-automatic-updates.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Provádění pravidelné simulace útoku

**Pokyny**: virtuální počítač s Windows neumožňuje zákazníkům provádět vlastní testování průniku na svých prostředcích virtuálních počítačů s Windows.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="endpoint-security"></a>Zabezpečení koncového bodu

*Další informace najdete v tématu [srovnávací zabezpečení Azure Security: Endpoint Security](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: použití detekce a odpovědi koncového bodu (EDR)

**Pokyny**: virtuální počítač s Windows neposkytuje žádné konkrétní možnosti pro procesy zjišťování koncových bodů a odpověď (EDR). Prostředky zaregistrované do služby ale můžou využívat možnosti zjišťování koncových bodů a odpovědi. 

Povolte zjišťování koncových bodů a možnosti odezvy pro servery a klienty a integrujte je s řešeními SIEM (Security Information and Event Management) a procesy zabezpečení.

Rozšířená ochrana před internetovými útoky z Microsoft Defenderu poskytuje funkce zjišťování koncových bodů a odezvy jako součást platformy zabezpečení podnikového koncového bodu, aby se zabránilo, detekci, prozkoumání a reakci na pokročilé hrozby.

- [Přehled rozšířené ochrany před internetovými útoky v programu Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [Služba ochrany ATP v programu Microsoft Defender pro servery Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [Služba ochrany ATP v programu Microsoft Defender pro servery jiné než Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [ATP v programu Microsoft Defender pro netrvalou infrastrukturu virtuálních klientských počítačů](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: použití centrálně spravovaného malwarového softwaru pro moderní správu

**Doprovodné** materiály: Chraňte své prostředky virtuálních klientů s Windows pomocí centralizovaného spravovaného a moderního antimalwarového řešení, které je možné v reálném čase a pravidelné kontrole.

Azure Security Center může automaticky identifikovat použití řady oblíbených antimalwarových řešení pro vaše virtuální počítače a ohlásit stav spouštění služby Endpoint Protection a dávat doporučení.

Microsoft Antimalware pro Azure Cloud Services je výchozí Antimalwarový program pro virtuální počítače s Windows (VM). K detekci malwaru nahraného do Azure Storage účtů taky můžete použít detekci hrozeb s Azure Security Center pro datové služby.

- [Jak nakonfigurovat Microsoft Antimalware pro Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md) 

- [Podporovaná řešení ochrany koncových bodů](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: kontrola, jestli je antimalwarový software a signatury aktualizované

**Pokyny**: Ujistěte se, že signatury antimalwaru se aktualizují rychle a konzistentně.

Použijte doporučení v Azure Security Center: "COMPUTE &amp; Apps", abyste zajistili aktuálnost všech virtuálních počítačů a kontejnerů pomocí nejnovějších signatur.

Microsoft Antimalware bude ve výchozím nastavení automaticky instalovat nejnovější podpisy a aktualizace stroje.

- [Jak nasadit Microsoft Antimalware pro Azure Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md) 

- [Posouzení a doporučení služby Endpoint Protection v Azure Security Center](../security-center/security-center-endpoint-protection.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="backup-and-recovery"></a>Zálohování a obnovy

*Další informace najdete v tématu [Azure Security Benchmark: zálohování a obnovení](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: zajištění pravidelného automatizovaného zálohování

**Doprovodné** materiály: Ujistěte se, že zálohujete systémy a data a udržujete provozní kontinuitu po neočekávané události. To by mělo být pokyny pro všechny cíle pro cíl bodu obnovení (RPO) a čas obnovení (RTO).

Povolte Azure Backup a nakonfigurujte zdroj zálohy (například virtuální počítače Azure, SQL Server, databáze HANA nebo sdílené složky) a také požadovanou frekvenci a dobu uchování.

V případě vyšší úrovně redundance můžete povolit možnost geograficky redundantního úložiště pro replikaci zálohovaných dat do sekundární oblasti a obnovení pomocí obnovení mezi oblastmi.

- [Provozní kontinuita a zotavení po havárii na podnikové úrovni](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [Postup povolení Azure Backup](/azure/backup/) 

- [Povolení obnovení mezi oblastmi](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) 

- [Jak nastavit provozní kontinuitu a plán zotavení po havárii na virtuálním počítači s Windows](disaster-recovery.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="br-2-encrypt-backup-data"></a>BR-2: šifrování zálohovaných dat

**Doprovodné** materiály: Ujistěte se, že jsou zálohy chráněny proti útokům. To by mělo zahrnovat šifrování záloh v zájmu ochrany před ztrátou důvěrných informací.

V případě pravidelného zálohování služeb Azure se zálohovaná data automaticky šifrují pomocí klíčů spravovaných platformou Azure. Můžete se rozhodnout Šifrovat zálohu pomocí klíče spravovaného zákazníkem. V takovém případě zajistěte, aby byl tento klíč spravovaný zákazníkem v trezoru klíčů také v oboru zálohování.

Použijte řízení přístupu na základě role v Azure Backup, Azure Key Vault nebo jiné prostředky k ochraně záloh a klíčů spravovaných zákazníkem. Kromě toho můžete povolit pokročilé funkce zabezpečení, které budou vyžadovat vícefaktorové ověřování, aby bylo možné změnit nebo odstranit zálohy.

Přehled funkcí zabezpečení v Azure Backup/Azure/Backup/Security-Overview 

- [Šifrování zálohovaných dat pomocí klíčů spravovaných zákazníkem](/azure/backup/encryption-at-rest-with-cmk) 

- [Postup zálohování klíčů Key Vault v Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0&amp;preserve-view=true)

- [Funkce zabezpečení, které vám pomůžou chránit hybridní zálohy před útoky](/azure/backup/backup-azure-security-feature#prevent-attacks)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: doporučuje se pravidelně ověřovat integritu dat pro záložní média pomocí procesu obnovení dat, abyste zajistili, že zálohování bude správně fungovat.

- [Postup obnovení souborů ze zálohy virtuálního počítače Azure](/azure/backup/backup-azure-restore-files-from-vm)

- [Implementace zabezpečení](/azure/backup/backup-azure-restore-files-from-vm#security-implementations)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="governance-and-strategy"></a>Zásady správného řízení a strategie

*Další informace najdete v tématu [Azure Security Benchmark: zásady správného řízení a strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

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
- [Doporučení architektury zabezpečení Azure – úložiště, data a šifrování](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Základy zabezpečení Azure – zabezpečení, šifrování a úložiště dat Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – osvědčené postupy zabezpečení a šifrování dat Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark – správa prostředků](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security Benchmark – ochrana dat](/azure/security/benchmarks/security-benchmark-v2-data-protection)

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

- [Azure Security Benchmark – správa stavu a ohrožení zabezpečení](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

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

- [Azure Security Benchmark – zabezpečení sítě](/azure/security/benchmarks/security-benchmark-v2-network-security)

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

- [Azure Security Benchmark – správa identit](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security Benchmark – privilegovaný přístup](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

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

- [Azure Security Benchmark – protokolování a detekce hrozeb](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark – reakce na incidenty](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Osvědčený postup zabezpečení Azure 4 – Proces: aktualizace procesů reakcí na incidenty pro cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Architektura přechodu na Azure a průvodce rozhodováním o protokolování a vytváření sestav](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Škálování, správa a monitorování Azure na podnikové úrovni](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
