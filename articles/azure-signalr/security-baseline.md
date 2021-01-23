---
title: Směrný plán zabezpečení Azure pro službu Azure Signal
description: Základní údaje o zásadách zabezpečení služby Azure Signaler poskytují pokyny a zdroje pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b750d4cc911dc72b60974171dd884b373921fad5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734592"
---
# <a name="azure-security-baseline-for-azure-signalr-service"></a>Směrný plán zabezpečení Azure pro službu Azure Signal

Tato základní hodnota zabezpečení se týká pokynů z [Azure Security test 2,0](../security/benchmarks/overview.md) na službu Azure Signal. Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah se seskupuje podle **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů vztahujících se na službu Azure Signal. **Ovládací prvky** , které se nevztahují na službu Azure Signal, se vyloučily.

 
Pokud chcete zjistit, jak se v nástroji Azure Signal úplně mapuje srovnávací test zabezpečení Azure, přečtěte si [úplný soubor mapování standardních hodnot zabezpečení služby Azure Signal](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementace zabezpečení pro interní provoz

**Pokyny**: služba signalizace Microsoft Azure nepodporuje nasazení přímo do virtuální sítě, protože z tohoto důvodu nemůžete využít určité síťové funkce s prostředky nabídky, jako jsou skupiny zabezpečení sítě, směrovací tabulky nebo jiná zařízení závislá na síti, jako je například Azure firewall. 

Služba signalizace Azure vám ale umožní vytvořit soukromé koncové body pro zabezpečení provozu mezi prostředky ve vaší virtuální síti a službou Azure Signal.

K omezení příchozího a odchozího provozu do služby Azure Signal Service můžete také použít značky služeb a nakonfigurovat pravidla skupiny zabezpečení sítě.

- [Použití privátních koncových bodů pro službu Azure Signal Service](howto-private-endpoints.md)

- [Konfigurace řízení přístupu k síti](howto-network-access-control.md)

- [Použití značek služeb pro službu Azure Signal Service](howto-service-tags.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="ns-2---connect-private-networks-together"></a>NS-2: – propojení privátních sítí  

**Doprovodné** materiály: pomocí privátních koncových bodů Zabezpečte provoz mezi vaší virtuální sítí a službou Azure Signal. Pokud chcete vytvořit privátní připojení mezi datacentry Azure a místní infrastrukturou v prostředí s okolním umístěním, vyberte Azure ExpressRoute nebo virtuální privátní síť (VPN) Azure. 

Připojení ExpressRoute nevyužívají veřejný Internet a nabízejí spolehlivější, rychlejší a nižší latenci než typická připojení k Internetu. Pro sítě VPN typu Point-to-site a VPN typu Site-to-site můžete připojit místní zařízení nebo sítě k virtuální síti pomocí libovolné kombinace těchto možností sítě VPN a Azure ExpressRoute.

Pokud chcete propojit dvě nebo víc virtuálních sítí v Azure společně, použijte partnerský vztah virtuálních sítí. Síťový provoz mezi partnerskými virtuálními sítěmi je privátní a udržuje se v páteřní síti Azure.

- [Použití privátních koncových bodů pro službu Azure Signal Service](howto-private-endpoints.md)

- [Jaké jsou modely připojení ExpressRoute](../expressroute/expressroute-connectivity-models.md)

- [Azure VPN – přehled](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Zřízení přístupu privátní sítě ke službám Azure

**Pokyny**: použití privátního odkazu Azure k povolení privátního přístupu ke službě Azure Signal Service z vašich virtuálních sítí bez přechodu na Internet.

Soukromý přístup je kromě ověřování a zabezpečení provozu nabízených službami Azure další mírou důkladné ochrany.

- [Principy privátního odkazu Azure](../private-link/private-link-overview.md)

- [Použití privátních koncových bodů pro službu Azure Signal Service](howto-private-endpoints.md)

- [Konfigurace řízení přístupu k síti](howto-network-access-control.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: zjednodušení pravidel zabezpečení sítě

**Pokyny**: pomocí značek služeb Azure Virtual Network můžete definovat řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall nakonfigurovaných pro prostředky služby pro signály Azure. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (například: AzureSignalR) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Pochopení a používání značek služeb](../virtual-network/service-tags-overview.md)

- [Použití značek služeb pro službu Azure Signal Service](howto-service-tags.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [Azure Security Benchmark: správa identit](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizace Azure Active Directory jako centrálního systému pro identifikaci a ověřování

**Pokyny**: služba signalizace Azure používá jako výchozí službu pro správu identit a přístupu službu Azure Active Directory (Azure AD). Službu Azure AD byste měli standardizovat, abyste mohli řídit identitu a správu přístupu vaší organizace v nástroji:

- Microsoft Cloud prostředky, jako jsou Azure Portal, Azure Storage, virtuální počítače Azure (Linux a Windows), Azure Key Vault, PaaS a SaaS aplikace.
- prostředky vaší organizace, jako jsou aplikace v Azure nebo prostředky vaší podnikové sítě

Služba signalizace Azure podporuje jenom ověřování Azure AD pro rovinu správy, ale ne pro rovinu dat. Tady je seznam předdefinovaných rolí ve službě Azure Signal Service:

- Přispěvatel signálu
- Čtečka AccessKey signálu

Zabezpečení služby Azure AD by mělo mít vysokou prioritu v praxi cloudového zabezpečení vaší organizace. Azure AD poskytuje bezpečnostní skóre identity, které vám pomůže vyhodnotit stav zabezpečení identity vzhledem k doporučeným osvědčeným postupům od Microsoftu. Pomocí skóre můžete vyhodnotit, jak přesně vaše konfigurace vyhovuje doporučeným osvědčeným postupům, a provádět v zabezpečení vylepšení.

Azure AD podporuje externí identity, které umožňují uživatelům bez účet Microsoft přihlašovat se k aplikacím a prostředkům s jejich externí identitou.

- [Architektura tenantů v Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Použití externích zprostředkovatelů identity pro aplikaci](../active-directory/external-identities/identity-providers.md)

- [Co je skóre zabezpečení identity v Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Zabezpečená a automatická správa identit aplikací

**Pokyny**: služba signalizace Azure používá spravované identity Azure pro nelidské účty, jako je například jednosměrné volání ve scénáři bez serveru. Pro přístup k dalším prostředkům Azure se doporučuje použít funkci spravované identity Azure. Služba Azure Signaler se může nativně ověřit pro služby Azure nebo prostředky, které podporují ověřování Azure Active Directory (Azure AD) prostřednictvím předdefinovaného pravidla udělení přístupu bez použití pevně zakódovaného pověření ve zdrojovém kódu nebo konfiguračních souborech.

- [Spravované identity Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Služby, které podporují spravované identity prostředků Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) 

- [Spravované identity pro službu Azure Signal Service](howto-use-managed-identity.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Použití jednotného přihlašování (SSO) Azure AD pro přístup k aplikacím

**Pokyny**: služba signalizace azure používá Azure Active Directory (Azure AD) k poskytování správy identit a přístupu k prostředkům signalizace. Patří sem podnikové identity, jako jsou zaměstnanci, i externí identity, jako jsou partneři a dodavatelé. Umožňuje jednotné přihlašování pro správu a zabezpečení přístupu k datům a prostředkům vaší organizace místně a v cloudu. Připojte všechny své uživatele, aplikace a zařízení k Azure AD, ať máte bezproblémový, zabezpečený přístup a lepší viditelnost a kontrolu.

- [Principy jednotného přihlašování k aplikacím pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Použití řídicích prvků silného ověřování pro veškerý přístup založený na Azure Active Directory

**Pokyny**: služba signalizace azure používá Azure Active Directory (Azure AD), která podporuje ovládací prvky silného ověřování prostřednictvím služby Multi-Factor Authentication a metody silného hesla.

- Vícefaktorové ověřování – povolte službu Multi-Factor Authentication služby Azure AD a sledujte Azure Security Center doporučení pro správu identit a přístupu pro příslušné osvědčené postupy v nastavení služby Multi-Factor Authentication. Vícefaktorové ověřování se dá vyhovět všem vybraným uživatelům nebo na úrovni jednotlivých uživatelů na základě podmínek přihlášení a rizikových faktorů.

- Ověřování bez hesla – K dispozici jsou tři možnosti ověřování bez hesla: Windows Hello pro firmy, aplikace Microsoft Authenticator a místní metody ověřování, jako jsou čipové karty.

Zajistěte, aby se pro správce a privilegované uživatele používala nejvyšší úroveň konkrétní metody silného ověřování, a pro ostatní uživatele zaveďte odpovídající zásady silného ověřování.

- [Povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Úvod do možností ověřování bez hesla pro Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Výchozí zásady hesel Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminujte chybná hesla pomocí Azure Active Directory ochrany heslem.](../active-directory/authentication/concept-password-ban-bad.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorování a upozornění na anomálie účtů

**Pokyny**: služba signalizace Azure je integrovaná s Azure Active Directory v nástroji, která poskytuje následující zdroje dat:

- Přihlášení – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.

- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.

- Rizikové přihlášení – rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Tyto zdroje dat je možné integrovat s Azure Monitor, v systému Azure Sentinel nebo v systémech správy událostí a SIEM (Event Management) třetích stran.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako jsou nadměrný počet neúspěšných pokusů o ověření nebo zastaralé účty v předplatném.

Rozšířená ochrana před internetovými útoky (ATP) v Azure je řešení zabezpečení, které dokáže na základě signálů Active Directory identifikovat, detekovat a vyšetřovat pokročilé hrozby, ohrožené identity a škodlivé činnosti vnitřních účastníků.

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../security-center/security-center-identity-access.md)

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Omezení přístupu k prostředkům Azure na základě podmínek

**Pokyny**: služba signalizace azure podporuje Azure Active Directory (Azure AD) podmíněný přístup pro přesnější řízení přístupu na základě uživatelem definovaných podmínek, jako je třeba přihlášení uživatelů z určitých ROZSAHŮ IP adres, aby se přihlášení používalo. Podrobné zásady správy relace ověřování se také dají použít pro různé případy použití.

- [Přehled podmíněného přístupu Azure](../active-directory/conditional-access/overview.md)

- [Obecné zásady podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurace správy relací ověřování pomocí podmíněného přístupu](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security Benchmark: privilegovaný přístup](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Ochrana a omezení vysoce privilegovaných uživatelů

**Doprovodné** materiály: nejdůležitější předdefinované role jsou Azure Active Directory (Azure AD) jsou globální správce a správce privilegovaných rolí, protože uživatelé přiřazení k těmto dvěma rolím můžou delegovat role správce:

- Globální správce/správce společnosti: uživatelé s touto rolí mají přístup ke všem funkcím pro správu ve službě Azure AD a službám, které používají identity Azure AD.

- Správce privilegovaných rolí: uživatelé s touto rolí můžou spravovat přiřazení rolí v Azure Active Directory (Azure AD) i v Azure AD Privileged Identity Management (PIM). Tato role navíc umožňuje správu všech aspektů PIM a administrativních jednotek.

Služba signalizace Azure má integrované vysoce privilegované role. Omezte počet vysoce privilegovaných účtů nebo rolí a chraňte Azure AD tyto účty na vyšší úrovni, protože uživatelé s tímto oprávněním můžou přímo nebo nepřímo číst a upravovat všechny prostředky v prostředí Azure.

S využitím služby Azure AD Privileged Identity Management (PIM) můžete povolit privilegovaný přístup podle potřeby (JIT) k prostředkům Azure a Azure AD. JIT uděluje dočasná oprávnění k provádění privilegovaných úloh, pouze když je uživatelé potřebují. PIM může také generovat upozornění zabezpečení v případě podezřelých nebo nebezpečných aktivit ve vaší organizaci Azure AD.

- [Přispěvatel signálu](../role-based-access-control/built-in-roles.md#signalr-contributor)

- [Oprávnění role správce v Azure AD](../active-directory/roles/permissions-reference.md)

- [Používání upozornění zabezpečení služby Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](../active-directory/roles/security-planning.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Omezení přístupu pro správu k nejdůležitějším podnikovým systémům

**Pokyny**: služba signalizace Azure používá řízení přístupu na základě role Azure (Azure RBAC) k izolaci přístupu k důležitým podnikovým systémům tím, že omezí přístup k předplatným a skupinám pro správu, ve kterých jsou udělen privilegovaný přístup.

Ujistěte se, že jste také omezili přístup k systémům pro správu, identitu a zabezpečení, které mají přístup pro správu k vašemu podnikovému přístupu, jako jsou například Doména služby Active Directory Controllers, nástroje zabezpečení a nástroje pro správu systému s agenty nainstalovanými na podnikových důležitých systémech. Útočníci, kteří tyto systémy pro správu a zabezpečení ohrožují, si je můžou hned weaponize, aby vyzrazeni důležité obchodní prostředky.

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu.

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Přístup ke skupině pro správu](../governance/management-groups/overview.md#management-group-access)

- [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Pravidelná kontrola a sjednocování přístupu uživatelů

**Pokyny**: pravidelně kontrolujte uživatelské účty a přiřazení přístupu, abyste měli jistotu, že účty a jejich úroveň přístupu jsou platné.

Služba signalizace Azure používá ke správě svých prostředků účty Azure Active Directory (Azure AD), pravidelně kontrolují uživatelské účty a přiřazení přístupu, aby bylo zajištěno, že jsou účty a jejich přístup platné. Kontroly přístupu Azure AD můžete použít ke kontrole členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí. Vytváření sestav Azure AD může poskytovat protokoly, které vám pomůžou zjistit zastaralé účty. K usnadnění procesu kontroly můžete použít také Azure AD Privileged Identity Management k vytvoření pracovního postupu sestavy kontroly přístupu.

Mezi předdefinované role ve službě Azure Signal Service patří:

- Přispěvatel signálu
- Čtečka AccessKey signálu

Kromě toho je možné Azure Privileged Identity Management nakonfigurovat tak, aby výstrahu při vytváření nadměrného počtu účtů správců a identifikaci účtů správce, které jsou zastaralé nebo nesprávně nakonfigurované.

- [Vytvoření kontroly přístupu pro role prostředků Azure v Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Přispěvatel signálu](../role-based-access-control/built-in-roles.md#signalr-contributor)

- [Čtečka AccessKey signálu](../role-based-access-control/built-in-roles.md#signalr-accesskey-reader)

-

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Nastavení nouzového přístupu v Azure AD

**Pokyny**: služba signalizace Azure používá ke správě svých prostředků službu Azure Active Directory (Azure AD). Pokud se chcete vyhnout náhodnému uzamčení vaší organizace Azure AD, nastavte účet pro nouzový přístup pro přístup, když nebudete moct použít normální účty pro správu. Účty pro nouzový přístup jsou obvykle vysoce privilegované a neměly by se přiřazovat konkrétním jednotlivcům. Jejich použití je omezené na nouzové nebo kritické situace, kdy se nedají použít běžné účty pro správu.

Měli byste zajistit, aby přihlašovací údaje (například heslo, certifikát nebo čipová karta) pro účty pro nouzový přístup byly zabezpečené a aby o nich věděli jenom ti, kteří jsou oprávněni je použít jenom v případě nouze.

- [Správa účtů pro nouzový přístup v Azure AD](../active-directory/roles/security-emergency-access.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizace správy nároků 

**Pokyny**: služba signalizace Azure je integrovaná s Azure Active Directory (Azure AD) pro správu prostředků. Pomocí funkcí správy opravňujících ke službě Azure AD můžete automatizovat pracovní postupy pro žádosti o přístup, včetně přiřazení přístupu, revizí a vypršení platnosti. Podporuje se také duální nebo vícenásobné schválení.

- [Co jsou kontroly přístupu Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Co je Správa nároků Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Použití pracovních stanic s privilegovaným přístupem

**Pokyny:** Zabezpečené, izolované pracovní stanice jsou nesmírně důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a obsluha nejdůležitějších služeb. Pro úlohy správy Používejte vysoce zabezpečené uživatelské pracovní stanice nebo Azure bastionu. K nasazení zabezpečené a spravované uživatelské pracovní stanice pro úlohy správy použijte Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) nebo Microsoft Intune. Zabezpečené pracovní stanice se dají centrálně spravovat, aby byla vynucena zabezpečená konfigurace, včetně silného ověřování, základních úrovní softwaru a hardwaru a omezeného logického a síťového přístupu.

- [Pochopení pracovních stanic s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Nasazení pracovní stanice s privilegovaným přístupem](/security/compass/privileged-access-deployment)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Využívání správy jen v potřebném rozsahu (princip nejnižší úrovně nutných oprávnění) 

**Pokyny**: služba signalizace je integrovaná s řízením přístupu na základě role Azure (Azure RBAC) za účelem správy prostředků. Azure RBAC umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Přiřaďte tyto role uživatelům, skupinám instančních objektů a spravovaným identitám. Předdefinované předdefinované role existují pro určité prostředky a tyto role mohou být v inventáři nebo prostřednictvím nástrojů, jako je Azure CLI, Azure PowerShell nebo Azure Portal. 

Oprávnění k prostředkům přiřazená prostřednictvím Azure RBAC by vždy měla být omezená pouze na to, co konkrétní role vyžadují. Tento přístup doplňuje přístup založený na přístupu podle potřeby (JIT) ve službě Azure AD Privileged Identity Management (PIM) a měl by se pravidelně kontrolovat.

Předdefinované role ve službě Signaler:

- Přispěvatel signálu
- Čtečka AccessKey signálu

Pomocí integrovaných rolí můžete přidělit oprávnění a v případě potřeby vytvářet pouze vlastní role.

- [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md)

- [Konfigurace řízení přístupu na základě role Azure](../role-based-access-control/role-assignments-portal.md)

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrana citlivých dat

**Doprovodné** materiály: Chraňte citlivá data omezením přístupu pomocí Access Control založeného na rolích Azure (Azure RBAC), řízení přístupu na základě sítě a konkrétních ovládacích prvků ve službách Azure (například šifrování v SQL a dalších databázích).

Aby se zajistilo konzistentní řízení přístupu, všechny typy řízení přístupu by měly být v souladu s vaší podnikovou strategií segmentace. Podniková strategie segmentace by měla vycházet také z umístění citlivých nebo důležitých obchodních dat a systémů.

Pro základní platformu spravovanou Microsoftem platí, že Microsoft považuje veškerý obsah zákazníků za citlivý a zajišťuje ochranu před ztrátou a vystavením zákaznických dat. V zájmu zajištění zabezpečení zákaznických dat v rámci Azure Microsoft implementovat určité výchozí kontrolní mechanismy a funkce ochrany dat.

- [Řízení přístupu na základě role (RBAC) Azure](../role-based-access-control/overview.md)

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Neuvedeno

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

**Doprovodné** materiály: použití značek pro prostředky Azure, skupiny prostředků a předplatná k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

- [Správa inventáře Azure Security Center prostředků](../security-center/asset-inventory.md)

- [Další informace o označování prostředků najdete v průvodci pro pojmenování a označení prostředku.](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používání jenom schválených služeb Azure

**Doprovodné** materiály: pomocí Azure Policy můžete auditovat a omezovat, které služby můžou uživatelé zřídit ve vašem prostředí. Pomocí Azure Resource Graphu se můžete dotazovat na prostředky v rámci jejich předplatných a zjišťovat je. Pomocí Azure Monitoru můžete také vytvořit pravidla pro aktivaci upozornění při zjištění neschválené služby.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Zajištění zabezpečení správy životního cyklu prostředků

**Pokyny:** Vytvořte nebo aktualizujte zásady zabezpečení, které v rámci procesů správy životního cyklu prostředků řeší úpravy s potenciálně velkým dopadem. Tyto úpravy obsahují změny, ale nejsou omezeny na: zprostředkovatelé identity a přístup, citlivost dat, konfigurace sítě a přiřazení oprávnění správce. Ve službě Azure Signal Service tyto změny zahrnují: znovu vygenerovat přístupový klíč, vytvořit nebo aktualizovat privátní koncový bod, spravovat řízení přístupu k síti.

Pokud už je nepotřebujete, odeberte prostředky Azure.

- [Odstranění prostředku a skupiny prostředků Azure](../azure-resource-manager/management/delete-resource-group.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat se správcem prostředků Azure tak, že nakonfigurujete "blokování přístupu" pro aplikaci "Microsoft Azure Management".

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v tématu [Azure Security Benchmark: protokolování a detekce hrozeb](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Povolení detekce hrozeb pro správu identit a přístupu v Azure

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD, které jsou integrované s Azure monitor, Azure Sentinel nebo jiné nástroje Siem/Monitoring pro výkonnější monitorování a analytické účely:

- Přihlásit se – sestava Sign-n poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.

- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.

- Riziková přihlášení – rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako jsou nadměrný počet neúspěšných pokusů o ověření nebo zastaralé účty v předplatném. Kromě základního monitorování hygieny zabezpečení může modul ochrany před hrozbami služby Azure Security Center také shromažďovat podrobnější upozornění zabezpečení z jednotlivých výpočetních prostředků Azure (virtuální počítače, kontejnery, App Service), datových prostředků (databáze SQL a úložiště) a vrstev služeb Azure. Tato funkce umožňuje získat přehled o anomáliích u účtů v rámci jednotlivých prostředků.

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Povolení ochrany identit Azure](../active-directory/identity-protection/overview-identity-protection.md)

- [Ochrana před hrozbami v Azure Security Center](../security-center/azure-defender.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Povolení protokolování pro síťové aktivity Azure

**Pokyny**: služba signalizace Azure není určena k nasazení do virtuálních sítí, protože z tohoto důvodu nemůžete povolit protokolování toku skupin zabezpečení sítě, směrovat provoz přes bránu firewall nebo provést zachytávání paketů.

Služba Azure Signaler ale protokoluje síťový provoz, který IT zpracovává pro přístup zákazníků. V rámci nasazených prostředků nabídky povolte protokoly prostředků a nakonfigurujte tyto protokoly, které se budou odesílat do účtu úložiště pro dlouhodobé uchovávání a auditování.

- [Protokoly prostředků pro službu Azure Signal Service](signalr-howto-diagnostic-logs.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Povolení protokolování pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit, které jsou automaticky k dispozici, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky služby signalizace Azure s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

Povolte protokoly prostředků Azure pro službu Azure Signal. Pomocí Azure Security Center a Azure Policy můžete povolit protokoly prostředků a shromažďovat data protokolů. Tyto protokoly můžou být klíčové pro pozdější zkoumání incidentů zabezpečení a provádění forenzních cvičení.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Protokoly prostředků pro službu Azure Signal Service](signalr-howto-diagnostic-logs.md)

- [Pochopení Azure Security Center shromažďování dat](../security-center/security-center-enable-data-collection.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizace správy a analýz protokolu zabezpečení

**Doprovodné** materiály: centralizace úložiště protokolování a analýzy pro povolení korelace. U každého zdroje protokolu se ujistěte, že jste přiřadili vlastníka dat, pokyny pro přístup, umístění úložiště, jaké nástroje se používají ke zpracování a přístupu k datům a k požadavkům na uchovávání dat.

Ujistěte se, že Integrujte protokoly aktivit Azure do svého centrálního protokolování. Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Kromě toho povolte a zaveďte data do Azure Sentinel nebo do systému správy událostí a SIEM (Security Information and Event Management) třetích stran.

Řada organizací zvolí použití Azure Sentinel pro "horká" data, která se používají často a Azure Storage pro "studená" data, která se používají méně často.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

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

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Zřízení zabezpečených konfigurací pro služby Azure 

**Pokyny**: služba signalizace Azure podporuje níže uvedené zásady pro konkrétní služby, které jsou k dispozici v Azure Security Center k auditování a prosazování konfigurací vašich prostředků Azure. Dá se nakonfigurovat v Azure Security Center nebo s iniciativou Azure Policy.

Pomocí Azure modrotisky můžete automatizovat nasazení a konfiguraci služeb a prostředí aplikací, včetně šablon Azure Resource Manageru, řízení přístupu na základě rolí (Azure RBAC) a zásad v jediné definici podrobného plánu.

- [Práce se zásadami zabezpečení v Azure Security Center](../security-center/tutorial-security-policy.md)

- [Audit dodržování předpisů u prostředků služby signalizace Azure pomocí Azure Policy](signalr-howto-azure-policy.md)

- [Kurz – vytváření a Správa zásad pro prosazování dodržování předpisů](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprint](../governance/blueprints/overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Udržování zabezpečených konfigurací pro služby Azure

**Doprovodné** materiály: pomocí Azure Security Center můžete monitorovat standardní hodnoty konfigurace a vymáhat je pomocí Azure Policy [odepřít] a [nasadit, pokud neexistuje], abyste vynutili zabezpečenou konfiguraci pro službu Azure Signal Service. Zásady služby Azure Signal Service zahrnují:

Další informace jsou k dispozici na odkazovaných odkazech.

- [Audit dodržování předpisů u prostředků služby signalizace Azure pomocí Azure Policy](signalr-howto-azure-policy.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>Souč_hod-3: Vytvoření zabezpečených konfigurací pro výpočetní prostředky

**Pokyny**: k vytvoření zabezpečených konfigurací ve službě Azure Signal Service použijte Azure Security Center a Azure Policy.

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

-   Požadovaná strategie řízení přístupu podle klasifikace dat organizace

-   Využívání funkcí ochrany dat nativních pro Azure a funkcí ochrany dat třetích stran

-   Požadavky na šifrování přenášených a neaktivních uložených dat

-   Vhodné kryptografické standardy

Další informace jsou k dispozici na odkazovaných odkazech.

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
