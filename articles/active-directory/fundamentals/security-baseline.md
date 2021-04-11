---
title: Základní hodnoty zabezpečení Azure pro Azure Active Directory
description: Základní Azure Active Directory zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: active-directory
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8d4203b7b5d7742bf198778864fa4f42b7423596
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107286013"
---
# <a name="azure-security-baseline-for-azure-active-directory"></a>Základní hodnoty zabezpečení Azure pro Azure Active Directory

Tato základní hodnota zabezpečení platí pro Azure Active Directory pokynů od [zabezpečení Azure Security test 2,0](../../security/benchmarks/overview.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Azure Active Directory. 

> [!NOTE]
> **Ovládací prvky** , které se nevztahují na Azure Active Directory nebo pro které je odpovědnost od Microsoftu, jsou z tohoto směrného plánu vyloučené. Pokud chcete zjistit, jak Azure Active Directory kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Azure Active Directory Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: zjednodušení pravidel zabezpečení sítě

**Pokyny**: pomocí značek služeb Azure Virtual Network můžete definovat řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall nakonfigurovaných pro vaše Azure Active Directory prostředky. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby, jako je například "Azureactivedirectory selhala" v příslušném zdrojovém nebo cílovém poli pravidla, můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny. 

- [Pochopení a používání značek služeb](../../virtual-network/service-tags-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [Azure Security Benchmark: správa identit](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizace Azure Active Directory jako centrálního systému pro identifikaci a ověřování

**Pokyny**: jako výchozí službu pro správu identit a přístupu použijte Azure Active Directory (Azure AD). Službu Azure AD byste měli standardizovat, abyste mohli řídit identitu a správu přístupu vaší organizace v nástroji: 
- Microsoft Cloud prostředky, jako jsou Azure Portal, Azure Storage, virtuální počítače Azure (Linux a Windows), Azure Key Vault, PaaS a SaaS aplikace. 
- prostředky vaší organizace, jako jsou aplikace v Azure nebo prostředky vaší podnikové sítě 
 

Zabezpečení služby Azure AD by mělo mít vysokou prioritu v praxi cloudového zabezpečení vaší organizace. Azure AD poskytuje bezpečnostní skóre identity, které vám pomůže vyhodnotit stav zabezpečení identity vzhledem k doporučeným osvědčeným postupům od Microsoftu. Pomocí skóre můžete vyhodnotit, jak přesně vaše konfigurace vyhovuje doporučeným osvědčeným postupům, a provádět v zabezpečení vylepšení. 

Azure AD podporuje externí identitu, která umožňuje uživatelům bez účet Microsoft přihlašovat se k aplikacím a prostředkům s jejich externí identitou. 

- [Architektura tenantů v Azure Active Directory](../develop/single-and-multi-tenant-apps.md)

- [Jak vytvořit a nakonfigurovat instanci Azure AD](active-directory-access-create-new-tenant.md)

- [Použití externích zprostředkovatelů identity pro aplikaci](/azure/active-directory/b2b/identity-providers)

- [Co je skóre zabezpečení identity v Azure Active Directory](identity-secure-score.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Zabezpečená a automatická správa identit aplikací

**Doprovodné** materiály: použití spravované identity pro prostředky Azure pro jiné než lidské účty, jako jsou služby nebo automatizace, se místo vytvoření výkonnějšího lidského účtu pro přístup k prostředkům a jejich spuštění doporučuje použít funkci identity spravované službou Azure. Můžete nativně ověřit služby nebo prostředky Azure, které podporují ověřování Azure Active Directory (Azure AD) prostřednictvím předdefinovaného pravidla udělení přístupu, aniž byste museli používat pevně zakódované přihlašovací údaje ve zdrojovém kódu nebo konfiguračních souborech. Nemůžete přiřadit spravované identity Azure k prostředkům Azure AD, ale Azure AD je mechanismus ověřování se spravovanými identitami přiřazenými k prostředkům jiných služeb.

- [Spravovaná identita pro prostředky Azure](../managed-identities-azure-resources/overview.md)

- [Služby, které podporují spravovanou identitu pro prostředky Azure](../managed-identities-azure-resources/services-support-managed-identities.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Použití jednotného přihlašování (SSO) Azure AD pro přístup k aplikacím

**Doprovodné** materiály: použijte Azure Active Directory k zajištění správy identit a přístupu do prostředků Azure, cloudových aplikací a místních aplikací. Patří sem podnikové identity, jako jsou zaměstnanci, i externí identity, jako jsou partneři a dodavatelé. Díky tomu je možné použít jednotné přihlašování (SSO) ke správě a zabezpečení přístupu k datům a prostředkům vaší organizace v místním prostředí a v cloudu. Připojte všechny své uživatele, aplikace a zařízení k Azure AD, ať máte bezproblémový, zabezpečený přístup a lepší viditelnost a kontrolu.

 
- [Principy jednotného přihlašování k aplikacím pomocí Azure AD](../manage-apps/what-is-single-sign-on.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Použití řídicích prvků silného ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: použijte Azure Active Directory k podpoře silného ověřování ovládacích prvků prostřednictvím vícefaktorového ověřování (MFA) a metod se silnými hesly.
- Vícefaktorové ověřování – povolte vícefaktorové ověřování Azure AD a projděte si doporučení pro správu identit a přístupu služby Azure Security Center, odkud při instalaci MFA využijte některé osvědčené postupy. Vícefaktorové ověřování se dá vynutit u všech uživatelů, u vybraných uživatelů nebo u jednotlivých uživatelů na základě podmínek přihlašování a rizikových faktorů.
- Ověřování bez hesla – k dispozici jsou tři možnosti ověřování bez hesla: Windows Hello pro firmy, Microsoft Authenticator aplikace a místní metody ověřování, jako jsou čipové karty.

Pro správce a privilegované uživatele zkontrolujte, že se používá nejvyšší úroveň metody silného ověřování, a pak uveďte příslušné zásady silného ověřování pro ostatní uživatele.

Azure AD podporuje starší ověřování založené na heslech, jako jsou jenom cloudové účty (uživatelské účty vytvořené přímo ve službě Azure AD), které mají základní zásady pro hesla nebo hybridní účty (uživatelské účty, které pocházejí z místní služby Active Directory), které budou následovat po místních zásadách hesel. Při použití ověřování založeného na hesle poskytuje Azure AD funkci ochrany heslem, která uživatelům brání v nastavení hesla, která se dají snadno uhodnout. Společnost Microsoft poskytuje globální seznam zakázaných hesel, která jsou aktualizována na základě telemetrie, a zákazníci mohou seznam rozšířit podle svých potřeb (například branding, kulturní odkazy atd.). Tato ochrana heslem se dá použít jenom pro cloudové a hybridní účty.

Ověřování založené jenom na přihlašovacích údajích hesla je náchylné k oblíbeným metodám útoku. Pro zajištění vyššího zabezpečení používejte silné ověřování, jako je MFA a zásady silného hesla. Pro aplikace třetích stran a služby tržiště, které mohou mít výchozí hesla, je byste je měli změnit při počátečním nastavení služby.

 
- [Jak nasadit Azure AD MFA](../authentication/howto-mfa-getstarted.md) 

 

 
- [Úvod do možností ověřování bez hesla pro Azure Active Directory](../authentication/concept-authentication-passwordless.md) 

 

 
- [Výchozí zásady hesel Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminace špatných hesel s využitím ochrany hesel Azure AD](../authentication/concept-password-ban-bad.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorování a upozornění na anomálie účtů

**Doprovodné** materiály: Azure Active Directory poskytuje následující zdroje dat:

 
- Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů.

 
- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.

 
- Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

 
- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

 

Tyto zdroje dat je možné integrovat s Azure Monitor, s Sentinelou v Azure a systémy SIEM třetích stran.

 

 
Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako jsou nadměrný počet neúspěšných pokusů o ověření nebo zastaralé účty v předplatném.

 

 
Rozšířená ochrana před internetovými útoky (ATP) v Azure je řešení zabezpečení, které dokáže na základě signálů Active Directory identifikovat, detekovat a vyšetřovat pokročilé hrozby, ohrožené identity a škodlivé činnosti vnitřních účastníků.

 

 
- [Sestavy aktivit auditu v Azure Active Directory](../reports-monitoring/concept-audit-logs.md) 

 

 
- [Zobrazení rizikových přihlášení Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

 

 
- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

 

 
- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../../security-center/security-center-identity-access.md) 

 

 
- [Upozornění v modulu ochrany na základě analýzy hrozeb služby Azure Security Center](../../security-center/alerts-reference.md) 

 

 
- [Integrace protokolů aktivit Azure do služby Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Omezení přístupu k prostředkům Azure na základě podmínek

**Doprovodné** materiály: použití podmíněného přístupu Azure Active Directory (Azure AD) pro přesnější řízení přístupu na základě uživatelem definovaných podmínek, jako je třeba přihlášení uživatelů z určitých ROZSAHŮ IP adres, bude muset používat MFA pro přihlášení. Podrobné zásady správy relace ověřování se také dají použít pro různé případy použití.

 
- [Přehled podmíněného přístupu Azure AD](../conditional-access/overview.md) 

 

 
- [Společné zásady podmíněného přístupu](../conditional-access/concept-conditional-access-policy-common.md) 

 

 
- [Konfigurace správy relací ověřování pomocí podmíněného přístupu](../conditional-access/howto-conditional-access-session-lifetime.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: zabezpečený přístup uživatelů k starším aplikacím

**Doprovodné** materiály: Ujistěte se, že máte k dispozici moderní řízení přístupu a monitorování relací pro starší verze aplikací a data, která ukládají a zpracovávají. I když se sítě VPN běžně používají pro přístup k starším aplikacím, často mají jenom základní řízení přístupu a omezené monitorování relací.

 
Azure Proxy aplikací služby AD umožňuje publikovat starší verze místních aplikací vzdáleným uživatelům pomocí jednotného přihlašování a při explicitním ověřování důvěryhodnosti vzdálených uživatelů a zařízení pomocí podmíněného přístupu Azure AD.

 

 
Alternativně Microsoft Cloud App Security je služba CASB (Cloud Access Security Broker), která poskytuje ovládací prvky pro sledování relací aplikací uživatele a blokování akcí (pro starší místní aplikace a aplikace cloudového softwaru jako služby (SaaS)).

 

 
- [Proxy aplikací služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#what-is-application-proxy) 

 

 
- [Microsoft Cloud App Security osvědčené postupy](/cloud-app-security/best-practices)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security Benchmark: privilegovaný přístup](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Ochrana a omezení vysoce privilegovaných uživatelů

**Doprovodné** materiály: nejdůležitější předdefinované role jsou globální správce a správce privilegovaných rolí, protože uživatelé přiřazení k těmto dvěma rolím můžou delegovat role správce:

- Globální správce: uživatelé s touto rolí mají přístup ke všem funkcím pro správu v Azure AD a službám, které používají identity Azure AD.

- Správce privilegovaných rolí: uživatelé s touto rolí můžou spravovat přiřazení rolí v Azure AD i v Azure AD Privileged Identity Management (PIM). Tato role navíc umožňuje správu všech aspektů PIM a administrativních jednotek.

Pokud používáte vlastní role s přiřazenými konkrétními privilegovanými oprávněními, můžete mít jiné kritické role, které je potřeba řídit. A můžete také chtít použít podobné ovládací prvky pro účet správce důležitých obchodních prostředků.

Azure AD má vysoce privilegované účty: uživatele a instanční objekty, které jsou přímo nebo nepřímo přiřazené nebo mají nárok na role správce globálního správce nebo privilegované role a další vysoce privilegované role v Azure AD a Azure.

Omezte počet vysoce privilegovaných účtů a chraňte tyto účty na vyšší úrovni, protože uživatelé s tímto oprávněním můžou přímo nebo nepřímo číst a upravovat všechny prostředky v prostředí Azure.

S využitím služby Azure AD Privileged Identity Management (PIM) můžete povolit privilegovaný přístup podle potřeby (JIT) k prostředkům Azure a Azure AD. JIT uděluje dočasná oprávnění k provádění privilegovaných úloh, pouze když je uživatelé potřebují. PIM může také generovat upozornění zabezpečení v případě podezřelých nebo nebezpečných aktivit ve vaší organizaci Azure AD.

- [Oprávnění role správce v Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [Používání upozornění zabezpečení služby Azure Privileged Identity Management](../privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Omezení přístupu pro správu k nejdůležitějším podnikovým systémům

**Doprovodné** materiály: pomocí Azure Active Directory Privileged Identity Management a vícefaktorového ověřování můžete omezit přístup pro správu k důležitým podnikovým systémům.

- [Privileged Identity Management schválení žádostí o aktivaci rolí](../privileged-identity-management/azure-ad-pim-approval-workflow.md)

- [Multi-Factor Authentication a podmíněný přístup](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Pravidelná kontrola a sjednocování přístupu uživatelů

**Pokyny**: pravidelně kontrolujte přiřazení přístupu uživatelských účtů, abyste zajistili, že jsou účty a jejich přístup platné, zejména zaměřené na role s vysokou úrovní oprávnění, včetně Správce globálního správce a privilegovaných rolí. Kontroly přístupu Azure Active Directory (Azure AD) můžete použít ke kontrole členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí, jak pro role Azure AD, tak pro role Azure. Vytváření sestav Azure AD může poskytovat protokoly, které vám pomůžou zjistit zastaralé účty. K usnadnění procesu kontroly můžete použít také Azure AD Privileged Identity Management k vytvoření pracovního postupu sestavy kontroly přístupu.

Kromě toho je možné Azure Privileged Identity Management nakonfigurovat tak, aby výstrahu při vytváření nadměrného počtu účtů správců a identifikaci účtů správce, které jsou zastaralé nebo nesprávně nakonfigurované.

- [Vytvoření kontroly přístupu rolí Azure AD v Privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-start-security-review.md)

- [Vytvoření kontroly přístupu pro role prostředků Azure v Privileged Identity Management (PIM)](../privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Používání kontrol přístupu a identit Azure AD](../governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Nastavení nouzového přístupu v Azure AD

**Doprovodné** materiály: Chcete-li zabránit náhodnému uzamčení vaší organizace Azure AD, nastavte účet pro nouzový přístup pro přístup, pokud nelze použít normální účty pro správu. Účty pro nouzový přístup jsou obvykle vysoce privilegované a neměly by se přiřazovat konkrétním jednotlivcům.
Jejich použití je omezené na nouzové nebo kritické situace, kdy se nedají použít běžné účty pro správu.

Měli byste zajistit, aby přihlašovací údaje (například heslo, certifikát nebo čipová karta) pro účty pro nouzový přístup byly zabezpečené a aby o nich věděli jenom ti, kteří jsou oprávněni je použít jenom v případě nouze.

- [Správa účtů pro nouzový přístup v Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizace správy nároků 

**Doprovodné** materiály: použití funkcí správy opravňujících k Azure AD k automatizaci pracovních postupů žádostí o přístup, včetně přiřazení přístupu, revizí a vypršení platnosti. Podporuje se také duální nebo vícenásobné schválení.

- [Co je Správa nároků Azure AD](../governance/entitlement-management-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Použití pracovních stanic s privilegovaným přístupem

**Pokyny:** Zabezpečené, izolované pracovní stanice jsou nesmírně důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a obsluha nejdůležitějších služeb. Pro úlohy správy Používejte vysoce zabezpečené uživatelské pracovní stanice nebo Azure bastionu. K nasazení zabezpečené a spravované uživatelské pracovní stanice pro úlohy správy použijte Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) nebo Microsoft Intune. Zabezpečené pracovní stanice se dají centrálně spravovat, aby byla vynucena zabezpečená konfigurace, včetně silného ověřování, základních úrovní softwaru a hardwaru a omezeného logického a síťového přístupu.

- [Zabezpečení zařízení jako součást privilegovaného přístupu](/security/compass/privileged-access-devices)

- [Implementace privilegovaného přístupu](/security/compass/privileged-access-deployment)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Využívání správy jen v potřebném rozsahu (princip nejnižší úrovně nutných oprávnění) 

**Doprovodné** materiály: zákazníci můžou nakonfigurovat nasazení Azure Active Directory (Azure AD) pro nejnižší oprávnění tím, že uživatelům přiřadí role s minimálními oprávněními, které uživatelé potřebují k dokončení svých úloh správy.

- [Oprávnění role správce v Azure AD](../roles/permissions-reference.md)

- [Přiřazení rolí pro správu v Azure AD](../roles/manage-roles-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: výběr procesu schválení pro podporu Microsoftu  

**Doprovodné** materiály: Azure Active Directory nepodporuje bezpečnostní modul zákazníka. Společnost Microsoft může spolupracovat se zákazníky přes jiné než bezpečnostní metody ke schválení pro přístup k zákaznickým datům.

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrana citlivých dat

**Doprovodné** materiály: Vezměte v úvahu následující pokyny pro implementaci ochrany citlivých dat:

- **Izolace:** Adresář je hranice dat, kterou služba Azure Active Directory (Azure AD) ukládá a zpracovává data pro zákazníka. Zákazníci by měli určit, kde chtějí většinu svých zákaznických dat Azure AD umístit, nastavením vlastnosti Country (země) ve svém adresáři.

- **Segmentace:** Role globálního správce má úplnou kontrolu nad všemi daty adresáře a pravidla, která řídí přístup a pokyny pro zpracování. Adresář může být rozdělen do jednotek pro správu a bude zřízen s uživateli a skupinami, které mají být spravovány správci těchto jednotek, globální Správci mohou pověřit zodpovědnost na jiné zásady ve své organizaci jejich přiřazením k předem definovaným rolím nebo vlastním rolím, které mohou vytvořit.

 
- **Přístup:** Oprávnění lze použít pro uživatele, skupinu, roli, aplikaci nebo zařízení. Přiřazení může být pro konfiguraci Privileged Identity Management trvalé nebo dočasné. 
  
- **Šifrování:** Azure AD šifruje veškerá neaktivní nebo přenosová data. Nabídka neumožňuje zákazníkům šifrovat data adresáře pomocí vlastního šifrovacího klíče. 

Pokud chcete zjistit, jak se vybraná země mapuje na fyzické umístění svého adresáře, přečtěte si článek kde se nachází vaše data uložená v článku.

- [Kde jsou vaše data uložená v článku](https://www.microsoft.com/trust-center/privacy/data-location)

Když zákazník používá různé nástroje, funkce a aplikace služby Azure AD, které komunikují s adresářem, použijte článek Azure Active Directory – kde jsou vaše data uložená?

- [Kde jsou vaše data umístěná na řídicím panelu](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)

- [Dokumentace k rolím Azure AD](/azure/active-directory/roles/)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Šifrování citlivých informací při přenosu

**Doprovodné** materiály: pro doplnění řízení přístupu by se data v přenosu měla chránit před útoky typu "vzdálené správy" (např. zachycení provozu) pomocí šifrování, aby útočníci mohli snadno číst nebo upravovat data.

Azure AD podporuje šifrování dat při přenosu s TLS v 1.2 nebo vyšším.

I když je to pro přenosy v privátních sítích volitelné, je to pro provoz v externích i veřejných sítích velmi důležité. U přenosů HTTP zajistěte, aby všichni klienti připojující se k prostředkům Azure mohli vyjednávat TLS v 1.2 nebo vyšší verzi. Pro vzdálenou správu použijte místo nešifrovaného protokolu SSH (pro Linux) nebo RDP/TLS (pro Windows). Zastaralé verze a protokoly SSL, TLS a SSH a slabé šifry by měly být zakázané.

Ve výchozím nastavení poskytuje Azure šifrování dat při přenosu mezi datovými centry Azure.

- [Pochopení šifrování při přenosu pomocí Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit) 

- [Informace o zabezpečení TLS](/security/engineering/solving-tls1-problem) 

- [Dvojité šifrování pro Azure data při přenosu](https://docs.microsoft.com/azure/security/fundamentals/double-encryption#data-in-transit)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="asset-management"></a>Správa aktiv

*Další informace najdete v tématu [Azure Security Benchmark: správa prostředků](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Zajištění přehledu bezpečnostního týmu o rizicích pro prostředky

**Pokyny:** Zajistěte udělení oprávnění Čtenář zabezpečení ve vašem tenantovi Azure a vašich předplatných bezpečnostním týmům, aby mohly monitorovat bezpečnostní rizika s využitím služby Azure Security Center. 

V závislosti na nastavení struktury odpovědnosti bezpečnostních týmů může být monitorování bezpečnostních rizik odpovědností centrálního nebo místního bezpečnostního týmu. Přehledy zabezpečení a bezpečnostní rizika se však v rámci organizace musí vždy agregovat centrálně. 

Oprávnění Čtenář zabezpečení je možné přidělit pro celého tenanta (kořenová skupina pro správu) nebo je vymezit na konkrétní skupiny pro správu nebo konkrétní předplatná. 

Aby bylo možné získat přehled o úlohách a službách, může být potřeba další oprávnění. 

- [Přehled role Čtenář zabezpečení](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Přehled skupin pro správu Azure](../../governance/management-groups/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Doprovodné** materiály: použití podmíněného přístupu Azure Active Directory (Azure AD) k omezení schopnosti uživatelů pracovat se službou Azure AD prostřednictvím Azure Portal konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v tématu [Azure Security Benchmark: protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: povolení detekce hrozeb pro prostředky Azure

**Doprovodné** materiály: použijte funkci Azure Active Directory (Azure AD) identity pro detekci hrozeb integrovanou ve službě Azure AD. 
 
 
 

 
Azure AD vytváří protokoly aktivit, které se často používají k detekci hrozeb a k lovu hrozeb. Protokoly přihlášení k Azure AD poskytují záznam o aktivitě ověřování a autorizace pro uživatele, služby a aplikace. Protokoly auditu Azure AD sledují změny provedené u tenanta Azure AD, včetně změn, které zlepšují nebo zmenšují stav zabezpečení.

- [Co je Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)

- [Připojení dat Azure AD Identity Protection k Azure Sentinel](../../sentinel/connect-azure-ad-identity-protection.md)

- [Připojení dat Azure Active Directory k Azure Sentinel](../../sentinel/connect-azure-active-directory.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Povolení detekce hrozeb pro správu identit a přístupu v Azure

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD, které jsou integrované s Azure monitor, Azure Sentinel nebo jiné nástroje Siem/Monitoring pro výkonnější monitorování a analytické účely: 
- Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů. 
- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad. 
- Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 
- Rizikové uživatele – rizikové uživatel je indikátorem uživatelského účtu, který mohl být napadený. 

Detekce rizik ochrany identity je ve výchozím nastavení povolená a nevyžaduje žádný proces připojování. Členitost nebo riziková data se určují podle SKU licence. 

- [Sestavy aktivit auditu v Azure Active Directory](../reports-monitoring/concept-audit-logs.md)  

- [Povolení ochrany identit Azure](../identity-protection/overview-identity-protection.md)  

- [Ochrana před hrozbami v Azure Security Center](/azure/security-center/threat-protection)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Povolení protokolování pro prostředky Azure

**Doprovodné** materiály: Azure Active Directory (Azure AD) vytváří protokoly aktivit. Na rozdíl od některých služeb Azure služba Azure AD nerozlišuje mezi protokoly aktivit a prostředků. Protokoly aktivit jsou automaticky dostupné v části Azure AD v Azure Portal a je možné je exportovat do Azure Monitor, Azure Event Hubs, Azure Storage, systémů Siem a dalších umístění.
 
- Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů.  
 
- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.  
 
 

Služba Azure AD taky poskytuje protokoly související se zabezpečením, které se dají zobrazit v sestavách Azure AD nebo v nich integrovat s Azure Monitor, Azure Sentinel nebo jinými nástroji SIEM/Monitoring pro výkonnější monitorování a analytické účely: 
- Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
- Rizikové uživatele – rizikové uživatel je indikátorem uživatelského účtu, který mohl být napadený. 

Detekce rizik ochrany identity je ve výchozím nastavení povolená a nevyžaduje žádný proces připojování. Členitost nebo riziková data se určují podle SKU licence. 

 
- [Sestavy aktivit a zabezpečení v Azure Active Directory](../reports-monitoring/overview-reports.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizace správy a analýz protokolu zabezpečení

**Doprovodné** materiály: Doporučujeme, abyste při zákaznících, kteří chtějí centralizovat své protokoly zabezpečení pro snazší lov hrozeb a analýzu stav zabezpečení, použili následující pravidla:
 
- Centralizace úložiště protokolování a analýzy pro povolení korelace. Pro každý zdroj protokolu v rámci služby Azure AD se ujistěte, že jste přiřadili vlastníka dat, pokyny pro přístup, umístění úložiště, jaké nástroje se používají ke zpracování a přístupu k datům a k požadavkům na uchovávání dat.  
 
- Ujistěte se, že Integrujte protokoly aktivit Azure do svého centrálního protokolování. Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.  
 
- Kromě toho povolte a zaveďte data do Azure Sentinel nebo SIEM třetí strany.  
 

Řada organizací zvolí použití Azure Sentinel pro "horká" data, která se používají často a Azure Storage pro "studená" data, která se používají méně často.  
 

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)   
 

- [Jak připojit Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurace uchovávání úložiště protokolů

**Doprovodné** materiály: Ujistěte se, že všechny účty úložiště nebo pracovní prostory Log Analytics používané pro ukládání protokolů Azure Active Directory přihlašování, protokolů auditu a protokolů rizikových dat mají dobu uchování protokolu nastavenou v souladu s předpisy pro dodržování předpisů vaší organizace.

V Azure Monitor můžete nastavit dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte Azure Storage, Data Lake nebo Log Analytics účty pracovního prostoru pro dlouhodobé a archivační úložiště.

- [Jak nakonfigurovat dobu uchování Log Analytics pracovního prostoru](/azure/azure-monitor/platform/manage-cost-storage)  

- [Ukládání protokolů prostředků v Azure Storagem účtu](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – aktualizace procesu reakce na incidenty pro Azure

**Pokyny:** Zajistěte, aby vaše organizace měla procesy, jak reagovat na incidenty zabezpečení, aby tyto procesy aktualizovala pro Azure a pravidelně je testovala za účelem zajištění připravenosti.

- [Implementace zabezpečení v podnikovém prostředí](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referenční příručka k reakcím na incidenty](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Příprava – nastavení oznámení o incidentu

**Pokyny:** Nastavte kontaktní informace pro incidenty zabezpečení ve službě Azure Security Center. Tyto kontaktní informace používá společnost Microsoft k tomu, aby vás kontaktovala v případě, že služba MSRC (Microsoft Security Response Center) zjistí, že s vašimi daty někdo nezákonně nebo neoprávněně pracoval. Máte také možnost přizpůsobit upozornění na incidenty a oznámení o incidentech v různých službách Azure v závislosti na tom, jak potřebujete na incidenty reagovat. 

- [Jak nastavit kontakt zabezpečení pro Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detekce a analýza – vytvoření incidentů na základě vysoce kvalitních výstrah 

**Pokyny:** Zajistěte, abyste měli proces pro vytváření vysoce kvalitních upozornění a měření jejich kvality. To vám umožní poučit se z minulých incidentů a stanovit priority upozornění pro analytiky, aby nemuseli ztrácet čas s falešně pozitivními výsledky. 

Vysoce kvalitní upozornění se dají vytvářet na základě zkušeností z minulých incidentů, ověřených zdrojů komunity a nástrojů určených k vygenerování a vyčištění upozornění spojením a sladěním různých zdrojů signálu. 

Azure Security Center poskytuje vysoce kvalitní výstrahy napříč mnoha prostředky Azure. Pomocí datového konektoru ASC můžete upozornění streamovat do služby Azure Sentinel. Azure Sentinel umožňuje vytvářet rozšířená pravidla upozornění, která automaticky generují incidenty pro účely šetření. 

Pomocí funkce exportu můžete upozornění a doporučení služby Azure Security Center exportovat, abyste mohli lépe identifikovat rizika pro prostředky Azure. Upozornění a doporučení můžete exportovat ručně nebo průběžně a nepřetržitě.

- [Konfigurace exportu](../../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detekce a analýza – Vyšetřování incidentu

**Pokyny:** Zajistěte, aby analytici mohli při vyšetřování potenciálních incidentů dotazovat a používat různorodé zdroje dat a mohli tak získat ucelenou představu o tom, co se stalo. Aby se zabránilo slepým místům, měly by se shromažďovat různorodé protokoly, které umožní sledovat aktivity potenciálního útočníka v celém průběhu útoku.  Měli byste také zajistit zaznamenávání přehledů a poznatků pro další analytiky a budoucí použití jako historické referenční informace.  

Mezi zdroje dat pro vyšetřování patří centrální zdroje protokolování, ve kterých se již shromažďují protokoly ze služeb v příslušném oboru a spuštěných systémů, ale můžou mezi ně patřit také:

- Síťová data – S využitím protokolů toků skupin zabezpečení sítě a služeb Azure Network Watcher a Azure Monitor můžete zachytávat protokoly toků sítě a další analytické informace. 

- Snímky spuštěných systémů: 

    - S využitím funkce vytváření snímků virtuálního počítače Azure můžete vytvořit snímek disku spuštěného systému. 

    - S využitím nativní funkce výpisu paměti operačního systému můžete vytvořit snímek paměti spuštěného systému.

    - S využitím funkce vytváření snímků služeb Azure nebo vlastních funkcí vašeho softwaru můžete vytvářet snímky spuštěných systémů.

Azure Sentinel nabízí rozsáhlé analýzy dat z prakticky jakéhokoli zdroje protokolů a portál pro správu případů, na kterém můžete spravovat celý životní cyklus incidentů. Analytické informace zjištěné během vyšetřování je možné přidružovat k incidentům pro účely sledování a generování sestav. 

- [Snímek disku počítače s Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snímek disku počítače s Linuxem](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Shromažďování výpisu paměti a diagnostických informací ze strany podpory Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Vyšetřování incidentů s využitím služby Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detekce a analýza – stanovení priorit incidentů

**Pokyny:** Poskytněte analytikům kontext, aby věděli, na jaké incidenty se zaměřit jako první v závislosti na závažnosti upozornění a citlivosti prostředků. 

Azure Security Center jednotlivým upozorněním přiřazuje závažnost, která vám pomůže určit, jaká upozornění by se měla vyšetřit jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho můžete prostředky označit pomocí značek a vytvořit systém vytváření názvů, který vám pomůže identifikovat a kategorizovat prostředky Azure, zejména prostředky zpracovávající citlivá data.  Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Zadržení, zničení a obnovení – automatizace zpracování incidentů

**Pokyny:** Automatizujte ruční opakující se úlohy, ay se zrychlila doba reakce a snížilo se zatížení analytiků. Ruční provádění úloh trvá déle, což zpomaluje jednotlivé incidenty a snižuje počet incidentů, které může analytik zvládnout. Ruční úlohy také analytiky více unavují, což zvyšuje riziko zpoždění v důsledku lidských chyb a snižuje schopnost analytiků efektivně se soustředit na složité úlohy. S využitím funkcí automatizace pracovních postupů ve službách Azure Security Center a Azure Sentinel můžete v reakci na příchozí upozornění zabezpečení automaticky aktivovat akce nebo spouštět playbooky. Playbook provádí akce, jako jsou odesílání oznámení, zakazování účtů nebo izolace problematických sítí. 

- [Konfigurace automatizace pracovních postupů ve službě Security Center](../../security-center/workflow-automation.md)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="posture-and-vulnerability-management"></a>Stav a správa ohrožení zabezpečení

*Další informace najdete v tématu [Azure Security Benchmark: stav a správa ohrožení zabezpečení](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Zřízení zabezpečených konfigurací pro služby Azure 

**Pokyny**: řešení pro správu identit a přístupu od Microsoftu umožňují chránit přístup k aplikacím a prostředkům v místním prostředí i v cloudu. Je důležité, aby organizace dodržovaly osvědčené postupy zabezpečení, aby bylo zajištěno, že je jejich implementace správy identit a přístupu zabezpečená a odolná vůči útokům. 

Na základě strategie implementace správy identit a přístupu by vaše organizace měla postupovat podle pokynů k osvědčeným postupům Microsoftu k zabezpečení vaší infrastruktury identity. 

Organizace, které spolupracují s externími partnery, by taky měly posoudit a implementovat příslušné konfigurace zásad správného řízení, zabezpečení a dodržování předpisů, aby se snížila rizika zabezpečení a chránily citlivé prostředky.

- [Osvědčené postupy správy identit a zabezpečení řízení přístupu v Azure](../../security/fundamentals/identity-management-best-practices.md)

- [Pět kroků pro zabezpečení infrastruktury identity](../../security/fundamentals/steps-secure-identity.md)

- [Zabezpečení externí spolupráce v Azure Active Directory a Microsoft 365](secure-external-access-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Udržování zabezpečených konfigurací pro služby Azure

**Pokyny**: Microsoft Secure skore poskytuje organizacím měření stav zabezpečení a doporučení, která mohou pomoci chránit organizace před hrozbami. Doporučuje se organizacím pravidelně kontrolovat jejich bezpečné skóre pro navrhované akce zlepšování, aby se zlepšila jejich stav zabezpečení identity. 

- [Jaké je bezpečné skóre identity v Azure Active Directory?](identity-secure-score.md)

- [Bezpečnostní skóre Microsoftu](/microsoft-365/security/mtp/microsoft-secure-score)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Provádění pravidelné simulace útoku

**Pokyny:** Podle potřeby provádějte testování průniku nebo aktivity červeného týmu na svých prostředcích Azure a zajistěte nápravu všech kritických zjištění v oblasti zabezpečení.
Postupujte podle pravidel zapojení testování průniku cloudových služeb Microsoftu (Microsoft Cloud Penetration Testing), abyste měli jistotu, že testy průniku neporušují zásady Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem.

- [Testování průniku v Azure](../../security/fundamentals/pen-testing.md)

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="governance-and-strategy"></a>Zásady správného řízení a strategie

*Další informace najdete v tématu [Azure Security Benchmark: zásady správného řízení a strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

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
- [Doporučení architektury zabezpečení Azure – úložiště, data a šifrování](/azure/architecture/framework/security/storage-data-encryption)
 

 
- [Základy zabezpečení Azure – zabezpečení, šifrování a úložiště dat Azure](../../security/fundamentals/encryption-overview.md) 

 
- [Cloud Adoption Framework – osvědčené postupy zabezpečení a šifrování dat Azure](../../security/fundamentals/data-encryption-best-practices.md) 

 
- [Azure Security Benchmark – správa prostředků](/azure/security/benchmarks/security-benchmark-v2-asset-management) 

 
- [Azure Security Benchmark – ochrana dat](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definování strategie segmentace podniku 

**Pokyny:** Zřiďte celopodnikovou strategii za účelem segmentace přístupu k prostředkům pomocí kombinace identity, sítě, aplikace, předplatného, skupiny pro správu a dalších řídicích prvků.

Pečlivě vyvažte nutnost oddělení zabezpečení s nutností povolit každodenní provoz systémů, které musí vzájemně komunikovat a pracovat s daty.

Zajistěte, aby se strategie segmentace implementovala konzistentně ve všech typech řídicích prvků včetně zabezpečení sítě, modelů identit a přístupu a modelů oprávnění/přístupu aplikací a řídicích prvků pro lidské procesy.

- [Pokyny ke strategii segmentace v Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Pokyny ke strategii segmentace v Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Soulad segmentace sítě a strategie segmentace podniku](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definování strategie správy stavu zabezpečení

**Pokyny:** Nepřetržitě měřte a zmírňujte rizika pro jednotlivé prostředky a prostředí, ve kterém jsou hostovány. Stanovte prioritu prostředků s vysokou hodnotou a míst nejvíce vystavených útokům, jako jsou publikované aplikace, body sítě pro příchozí a odchozí přenosy dat, koncové body uživatelů a správců atd.

- [Azure Security Benchmark – správa stavu a ohrožení zabezpečení](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Soulad rolí a odpovědností organizace

**Pokyny:** Je nutné zadokumentovat a dále sdělovat jasnou strategii pro role a odpovědnosti ve vaší organizaci zabezpečení. Stanovte prioritu poskytování jasné odpovědnosti při rozhodování o zabezpečení, zároveň vyškolte všechny uživatele na model sdílené odpovědnosti a vyškolte technické týmy na technologii pro zabezpečení cloudu.

- [Osvědčený postup zabezpečení Azure 1 – Lidé: školení týmů v oblasti zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Osvědčený postup zabezpečení Azure 2 – Lidé: školení týmů na technologii zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Osvědčený postup zabezpečení Azure 3 – Proces: přiřazení odpovědnosti za rozhodování o zabezpečení cloudu](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

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

- [Přehled zabezpečení sítě v Azure](../../security/fundamentals/network-overview.md)

- [Strategie architektury podnikové sítě](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

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

- [Přehled zabezpečení správy identit v Azure](../../security/fundamentals/identity-management-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

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

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
