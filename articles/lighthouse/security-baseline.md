---
title: Základní hodnoty zabezpečení Azure pro Azure Lighthouse
description: Základní plán zabezpečení Azure Lighthouse poskytuje pokyny a materiály k postupům pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 51c786d12c372276d1cb007cc5a929ab6a6302f7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94974861"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>Základní hodnoty zabezpečení Azure pro Azure Lighthouse

Tato základní hodnota zabezpečení se vztahuje na pokyny od [verze Azure Security test 2,0](../security/benchmarks/overview.md) pro Azure Lighthouse. Srovnávací test zabezpečení Azure poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Obsah se seskupuje podle **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů vztahujících se na Azure Lighthouse. **Ovládací prvky** , které se nevztahují na Azure Lighthouse, se vyloučily.

Pokud chcete zjistit, jak Azure Lighthouse kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení Azure Lighthouse](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [testování výkonnosti Azure Security: Správa identit](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardizace Azure Active Directory jako centrální systém pro ověřování a identifikaci

**Pokyny**: Azure Lighthouse používá jako výchozí službu pro správu identit a přístupu Azure Active Directory (Azure AD). Standardizace Azure AD pro řízení identity a správy přístupu vaší organizace v nástroji:
- Microsoft Cloud prostředky, jako jsou Azure Portal, Azure Storage, virtuální počítače Azure (Linux a Windows), Azure Key Vault, PaaS a SaaS aplikace.
- Prostředky vaší organizace, jako jsou aplikace v Azure nebo podnikové síťové prostředky.

V případě Azure Lighthouse mají určení uživatelé ve spravovaném tenantovi integrovanou roli Azure, která jim umožňuje přístup k delegovaným předplatným nebo skupinám prostředků v tenantovi zákazníka. Všechny předdefinované role jsou v současné době podporovány s výjimkou vlastník nebo jakýchkoli předdefinovaných rolí s oprávněním dataactions. Role správce přístupu uživatelů je podporovaná jenom pro omezené použití při přiřazování rolí ke spravovaným identitám. Vlastní role a role správců pro klasický odběr nejsou podporovány.

- [Tenantů v Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Použít externí zprostředkovatele identity pro aplikaci](/azure/active-directory/b2b/identity-providers) 

- [Jaké je bezpečné skóre identity v Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: zabezpečená a Automatická správa identit aplikací

**Pokyny**: spravované identity Azure se můžou ověřit u služeb a prostředků Azure, které podporují ověřování Azure AD. Ověřování je povoleno prostřednictvím předdefinovaných pravidel udělení přístupu, což vyloučí pevně zakódované přihlašovací údaje ve zdrojovém kódu nebo konfiguračních souborech. S Azure Lighthouse můžou uživatelé s rolí správce přístupu uživatele v předplatném zákazníka vytvořit spravovanou identitu v tenantovi daného zákazníka. I když tato role není pro Azure Lighthouse všeobecně podporovaná, dá se použít v tomto konkrétním scénáři, takže uživatelé s tímto oprávněním můžou k spravovaným identitám přiřadit jednu nebo více specifických rolí.

Pro služby, které nepodporují spravované identity, použijte službu Azure AD k vytvoření instančního objektu s omezenými oprávněními na úrovni prostředků. Azure Lighthouse umožňuje instančním objektům přístup k zákaznickým prostředkům v závislosti na rolích, které jsou během procesu připojování uděleny. Doporučuje se nakonfigurovat instanční objekty s přihlašovacími údaji certifikátu a vrátit se k klientským tajným klíčům. V obou případech se Azure Key Vault dá použít společně se spravovanými identitami Azure, takže běhové prostředí (například funkce Azure) může přihlašovací údaje načíst z trezoru klíčů.

- [Spravované identity Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Instanční objekt Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Použití Azure Key Vault k registraci objektu zabezpečení](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [Vytvoření uživatele, který může přiřadit role ke spravované identitě v tenantovi zákazníka](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: použití ovládacích prvků pro silné ověřování pro všechny přístupy založené na Azure Active Directory

**Pokyny**: vyžadovat Multi-Factor Authentication (MFA) pro všechny uživatele ve vašem spravovaném tenantovi, včetně uživatelů, kteří budou mít přístup k delegovaným zákaznickým prostředkům. Doporučujeme, abyste zákazníkům požádali o implementaci vícefaktorového ověřování ve svých klientech.

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: monitorování a upozornění na anomálie účtů

**Doprovodné** materiály: Azure AD poskytuje následující zdroje dat: 

-   Přihlášení – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.

-   Protokoly auditu – poskytuje sledovatelnost prostřednictvím protokolů pro všechny změny provedené prostřednictvím různých funkcí služby Azure AD. Příklady protokolů auditu v protokolovaných změnách zahrnují přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.

-   Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

-   Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Tyto zdroje dat je možné integrovat s Azure Monitor, s Sentinelou v Azure a systémy SIEM třetích stran.

Poskytovatelé služeb pomocí Azure Lighthouse můžou přesměrovat protokoly Azure AD do Azure Sentinel a zobrazovat nebo nastavovat výstrahy napříč klienty, aby mohli sledovat anomálie účtů a upozorňovat na ně.

- [Sestavy aktivit auditu v Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Jak zobrazit rizikové přihlašování Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Správa pracovních prostorů Sentinel Azure ve velkém měřítku](how-to/manage-sentinel-workspaces.md)

- [Připojení dat z Azure AD k Azure Sentinel](../sentinel/connect-azure-active-directory.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM – 6: omezit přístup k prostředkům Azure na základě podmínek

**Pokyny**: Azure Lighthouse nepodporuje funkci podmíněného přístupu pro delegované prostředky zákazníků. V části Správa tenanta použijte podmíněný přístup Azure AD pro přesnější řízení přístupu na základě uživatelem definovaných podmínek, jako je třeba vyžadování přihlášení uživatelů z určitých rozsahů IP adres pro použití Multi-Factor Authentication (MFA). Pomocí zásad podmíněného přístupu Azure AD pro různé případy použití lze také použít podrobnou správu relace ověřování. 

Vícefaktorové ověřování byste měli vyžadovat pro všechny uživatele ve vašem spravovaném tenantovi, včetně uživatelů, kteří budou mít přístup k delegovaným zákaznickým prostředkům. Doporučujeme, abyste zákazníkům požádali o implementaci vícefaktorového ověřování ve svých klientech.

- [Přehled podmíněného přístupu Azure](../active-directory/conditional-access/overview.md)

- [Běžné zásady podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurace správy relací ověřování pomocí podmíněného přístupu](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security benchmark: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: ochrana a omezení vysoce privilegovaných uživatelů

**Doprovodné** materiály: Omezte počet vysoce privilegovaných uživatelských účtů a chraňte tyto účty na vyšší úrovni. Pro povolení a používání Azure Lighthouse se nevyžaduje účet globálního správce.

Aby bylo možné získat přístup k datům protokolu aktivit na úrovni tenanta, musí být k účtu přiřazená integrovaná role Azure Monitoring Reader v kořenovém oboru (/). Vzhledem k tomu, že role čtenář monitorování v kořenovém oboru je širokou úrovní přístupu, doporučujeme přiřadit tuto roli k hlavnímu účtu služby a nikoli jednotlivým uživatelům nebo skupině. Toto přiřazení musí provést uživatel, který má roli globálního správce s dalšími oprávněními vyšší úrovně přístupu. Tento zvýšený přístup by se měl přidat hned před provedením přiřazení role a pak se po dokončení přiřazení odebere.

- [Oprávnění role správce v Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Přiřazení přístupu k monitorování dat protokolu aktivit na úrovni tenanta](how-to/monitor-delegation-changes.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: omezení přístupu pro správu na systémy kritické pro podnikání

**Pokyny**: Azure Lighthouse používá řízení přístupu na základě role Azure (Azure RBAC) k izolaci přístupu k podnikovým systémům tím, že omezuje přístup k důležitým systémům tím, že omezí účty, ve kterých jsou udělen privilegovaný přístup k předplatným a skupinám pro správu.

Nezapomeňte postupovat podle principu minimálního oprávnění, aby uživatelé měli jenom oprávnění potřebná k provádění jejich konkrétních úloh.

Ujistěte se, že také omezíte přístup k systémům pro správu, identitu a zabezpečení, které mají přístup pro správu k vašemu podnikovému přístupu, jako jsou Doména služby Active Directory Controllers (DCs), nástroje zabezpečení a nástroje pro správu systému s agenty nainstalovanými v důležitých podnikových systémech. Útočníci, kteří tyto systémy pro správu a zabezpečení ohrožují, si je můžou hned weaponize, aby vyzrazeni důležité obchodní prostředky.

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu.

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Přístup ke skupině pro správu](../governance/management-groups/overview.md#management-group-access)

- [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Osvědčené postupy pro definování uživatelů a rolí pro Azure Lighthouse](concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: pravidelně kontrolovat a sjednotit přístup uživatelů

**Pokyny**: Azure Lighthouse používá ke správě svých prostředků účty služby Azure Active Directory (Azure AD), pravidelně kontrolují uživatelské účty a přiřazení přístupu, aby bylo zajištěno, že účty a jejich přístup jsou platné. Kontroly přístupu Azure AD můžete použít ke kontrole členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí. Vytváření sestav Azure AD může poskytovat protokoly, které vám pomůžou zjistit zastaralé účty. K usnadnění procesu kontroly můžete použít také Azure AD Privileged Identity Management k vytvoření pracovního postupu sestavy kontroly přístupu.

Zákazníci si můžou na Azure Portal zkontrolovat úroveň přístupu udělených uživatelům v rámci správy tenanta prostřednictvím Azure Lighthouse. Tito uživatelé můžou tento přístup kdykoli odebrat.

Kromě toho je možné Azure Privileged Identity Management nakonfigurovat tak, aby výstrahu při vytváření nadměrného počtu účtů správců a identifikaci účtů správce, které jsou zastaralé nebo nesprávně nakonfigurované.

Poznámka: některé služby Azure podporují místní uživatele a role, které se nespravují prostřednictvím služby Azure AD. Tyto uživatele budete muset spravovat samostatně.

- [Vytvoření kontroly přístupu pro role prostředků Azure v Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Odebrání přístupu k delegování](how-to/remove-delegation.md)

- [Jak používat kontroly identity a přístupu v Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Zobrazení stránky poskytovatelé služeb v Azure Portal](how-to/view-manage-service-providers.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: nastavení nouzového přístupu v Azure AD

**Doprovodné** materiály: Azure Lighthouse je integrovaný s Azure Active Directory pro správu prostředků. Pokud se chcete vyhnout náhodnému uzamčení vaší organizace Azure AD, nastavte účet pro nouzový přístup pro přístup, když nebudete moct použít normální účty pro správu. Účty pro nouzový přístup jsou obvykle vysoce privilegované a neměly by být přiřazeny konkrétním jednotlivcům. Účty pro nouzový přístup jsou omezené na nouzové nebo "oddělovací sklo", kde se nedají použít běžné účty pro správu.

Měli byste zajistit, aby pověření (například heslo, certifikát nebo čipová karta) pro účty pro nouzový přístup byla zabezpečená a známá jenom jednotlivcům, kteří jsou oprávněni je používat jenom v nouzi.

- [Správa účtů pro nouzový přístup v Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizace správy nároků 

**Pokyny**: Služba Azure Lighthouse je integrovaná s Azure Active Directory (Azure AD) pro správu prostředků. Pomocí funkcí správy opravňujících ke službě Azure AD můžete automatizovat pracovní postupy pro žádosti o přístup, včetně přiřazení přístupu, revizí a vypršení platnosti. Podporuje se také duální nebo vícenásobné schválení.

- [Co jsou kontroly přístupu Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Co je Správa nároků Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: použití pracovních stanic s privilegovaným přístupem

**Doprovodné** materiály: zabezpečené, izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a kritické operátory služby. V závislosti na vašich požadavcích můžete pomocí vysoce zabezpečených uživatelských pracovních stanic nebo Azure bastionu provádět úlohy správy s Azure Lighthouse v produkčních prostředích. Pomocí Azure Active Directory, programu Microsoft Defender Advanced Threat Protection (ATP) a/nebo Microsoft Intune nasaďte zabezpečenou a spravovanou pracovní stanici uživatelů pro úlohy správy. Zabezpečená pracovní stanice se dají centrálně spravovat tak, aby vynutila zabezpečenou konfiguraci, včetně silného ověřování, standardních hodnot softwaru a hardwaru a omezeného logického a síťového přístupu. 

- [Pochopení pracovních stanic s privilegovaným přístupem](../active-directory/devices/concept-azure-managed-workstation.md)

- [Nasazení privilegované pracovní stanice přístupu](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: sledování pouze dostatečné správy (princip nejnižší úrovně oprávnění) 

**Pokyny**: Azure Lighthouse je integrovaný s řízením přístupu na základě role (RBAC) Azure pro správu prostředků. Azure RBAC umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto předdefinované role můžete přiřadit uživatelům, skupinám, objektům služby a spravovaným identitám. Pro určité prostředky jsou předem definované předdefinované role a tyto role můžou být v inventáři nebo prostřednictvím nástrojů, jako je Azure CLI, Azure PowerShell nebo Azure Portal. Oprávnění, která přiřadíte k prostředkům prostřednictvím Azure RBAC, by měla být vždy omezená na to, co role vyžadují. Tím se doplňují přístup JIT (just in time) Azure AD Privileged Identity Management (PIM) a měl by se pravidelně kontrolovat. Pomocí integrovaných rolí můžete přidělit oprávnění a v případě potřeby vytvářet pouze vlastní role.

Azure Lighthouse umožňuje přístup k delegovaným zákaznickým prostředkům pomocí integrovaných rolí Azure. Ve většině případů budete chtít přiřadit tyto role k objektu skupiny nebo instančnímu objektu, a ne k mnoha jednotlivým uživatelským účtům. To vám umožní přidat nebo odebrat přístup pro jednotlivé uživatele bez nutnosti aktualizace a opětovného publikování plánu, když se změní vaše požadavky na přístup.

Pro delegování zákaznických prostředků do spravovaného tenanta musí být nasazení provedeno účtem bez hosta v tenantovi zákazníka, který má předdefinovanou roli předplatného pro odběr (nebo který obsahuje skupiny prostředků, které jsou připojené).

- [Pochopení tenantů, uživatelů a rolí v Azure Lighthouse](concepts/tenants-users-roles.md)

- [Jak použít princip nejnižších oprávnění pro Azure Lighthouse](concepts/recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)

- [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Jak používat kontroly identity a přístupu v Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

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

**Pokyny**: týmy zabezpečení pro zákazníky mohou kontrolovat protokoly aktivit a zobrazit tak aktivity prováděné poskytovateli služeb, kteří používají Azure Lighthouse. 

Pokud chce poskytovatel pro zabezpečení zkontrolovat prostředky delegovaného zákazníka, musí mít autorizaci bezpečnostního týmu zabudovanou roli čtenáře.

- [Jak zákazník může zkontrolovat aktivitu poskytovatele služeb](how-to/view-service-provider-activity.md)

- [Určení rolí při připojování zákazníka k Azure Lighthouse](how-to/onboard-customer.md#define-roles-and-permissions)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: pomocí Azure Policy můžete auditovat a omezovat, které služby můžou uživatelé zřídit ve vašem prostředí. Pomocí Azure Resource graphu se můžete dotazovat a zjišťovat prostředky v rámci svých předplatných. Pomocí Azure Monitor můžete také vytvořit pravidla pro aktivaci výstrah při zjištění neschválené služby.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>DOP. 4: zajištění zabezpečení správy životního cyklu prostředků

**Pokyny**: Odeberte přístup k prostředkům, které byly delegovány prostřednictvím Azure Lighthouse, jakmile už nejsou potřeba, aby poskytovatelé služeb už nemuseli mít přístup. Přístup může odebrat buď zákazník, nebo poskytovatel služeb. 

- [Odebrání přístupu k delegování](how-to/remove-delegation.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Doprovodné** materiály: Služba Azure Lighthouse je integrovaná s Azure Active Directory (Azure AD) pro identitu a ověřování. Pomocí podmíněného přístupu Azure můžete omezit schopnost uživatelů pracovat se správcem prostředků Azure tak, že nakonfigurujete "blokování přístupu" pro aplikaci Microsoft Azure Management.

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: povolení detekce hrozeb pro prostředky Azure

**Doprovodné** materiály: prostřednictvím Azure Lighthouse můžete monitorovat prostředky Azure vašich zákazníků na potenciální hrozby a anomálie. Zaměřte se na získávání vysoce kvalitních výstrah, které omezí falešně pozitivní výsledky pro analytiky k řazení. Výstrahy se dají nacházet z dat protokolů, agentů nebo jiných dat.

Využijte Azure Security Center integrovanou funkci detekce hrozeb, která vychází z monitorování telemetrie služeb Azure a analýzy protokolů služby. Data se shromažďují pomocí Log Analytics agenta, který čte různé konfigurace a protokoly událostí související se zabezpečením ze systému a kopíruje data do vašeho pracovního prostoru pro účely analýzy. 

Kromě toho použijte Azure Sentinel k sestavování analytických pravidel, která navzájemují hrozby, které odpovídají konkrétním kritériím v prostředí zákazníka. Pravidla generují incidenty, když jsou kritéria shodná, takže můžete prozkoumat jednotlivé incidenty. Funkce Sentinel Azure může taky importovat analýzy hrozeb jiných výrobců, aby se zlepšila její schopnost detekce hrozeb. 

- [Ochrana před hrozbami v Azure Security Center](/azure/security-center/threat-protection)

- [Referenční příručka pro Azure Security Center výstrahy zabezpečení](../security-center/alerts-reference.md)

- [Vytváření vlastních pravidel analýzy pro detekci hrozeb](../sentinel/tutorial-detect-threats-custom.md)

- [Správa pracovních prostorů Sentinel Azure ve velkém měřítku](how-to/manage-sentinel-workspaces.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: povolení detekce hrozeb pro správu identit a přístupu v Azure

**Doprovodné** materiály: prostřednictvím Azure Lighthouse můžete pomocí Azure Security Center upozorňovat na určité podezřelé aktivity v klientech zákazníků, které spravujete, jako je například nadměrný počet neúspěšných pokusů o ověření a zastaralé účty v rámci předplatného.

Azure Active Directory (Azure AD) poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD nebo v nich integrovat s využitím Azure Monitor, Azure Sentinel nebo jiné nástroje SIEM/Monitoring pro výkonnější monitorování a analytické účely:
- Přihlášení – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny provedené u všech prostředků v rámci Azure AD, jako je přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.
- Rizikové přihlášení – rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako je například nadměrný počet neúspěšných pokusů o ověření, zastaralých účtů v předplatném. Kromě základního monitorování hygieny zabezpečení může modul ochrany před internetovými útoky Azure Security Center taky shromažďovat podrobnější výstrahy zabezpečení z jednotlivých výpočetních prostředků Azure (virtuální počítače, kontejnery, App Service), datové prostředky (SQL DB a Storage) a vrstvy služeb Azure. Tato funkce poskytuje přehled o anomáliích v účtech v jednotlivých prostředcích. 

- [Vylepšené služby a scénáře s využitím Azure Lighthouse](concepts/cross-tenant-management-experience.md#enhanced-services-and-scenarios)

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Povolit Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Ochrana před hrozbami v Azure Security Center](/azure/security-center/threat-protection)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: povolení protokolování pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit, které jsou automaticky k dispozici, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky Azure Lighthouse s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

S Azure Lighthouse můžete protokolovat pomocí Azure Monitor škálovatelným způsobem napříč klienty zákazníka, které spravujete. Vytvářejte Log Analytics pracovní prostory přímo v klientech zákazníka, aby zákaznická data zůstala ve svých klientech a nemuseli je exportovat. Díky tomu je možné centralizované monitorování všech prostředků nebo služeb, které Log Analytics podporuje, a poskytuje větší flexibilitu v tom, jaké typy dat sledujete.

Zákazníci, kteří mají delegované předplatné pro Azure Lighthouse, mohou zobrazit data protokolu aktivit Azure, aby viděli všechny akce, které provedli. To zákazníkům poskytuje přehled o operacích, které poskytovatelé služeb provádějí, spolu s operacemi prováděnými uživateli v rámci vlastního klienta Azure Active Directory (Azure AD) zákazníka.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Monitorování delegovaných prostředků ve velkém měřítku](how-to/monitor-at-scale.md)

- [Zobrazení aktivity poskytovatele služeb](how-to/view-service-provider-activity.md)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizace správy protokolu zabezpečení a analýzy

**Doprovodné** materiály: centralizace úložiště protokolování a analýzy pro povolení korelace. U každého zdroje protokolu se ujistěte, že jste přiřadili vlastníka dat, pokyny pro přístup, umístění úložiště, jaké nástroje se používají ke zpracování a přístupu k datům a k požadavkům na uchovávání dat.

Ujistěte se, že Integrujte protokoly aktivit Azure do svého centrálního protokolování. Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Kromě toho povolte a zaveďte data do Azure Sentinel nebo SIEM třetí strany.

S Azure Lighthouse můžete protokolovat pomocí Azure Monitor škálovatelným způsobem napříč klienty zákazníka, které spravujete. Vytvářejte Log Analytics pracovní prostory přímo v klientech zákazníka, aby zákaznická data zůstala ve svých klientech a nemuseli je exportovat. Díky tomu je možné centralizované monitorování všech prostředků nebo služeb, které Log Analytics podporuje, a poskytuje větší flexibilitu v tom, jaké typy dat sledujete.

Zákazníci, kteří mají delegované předplatné pro Azure Lighthouse, mohou zobrazit data protokolu aktivit Azure, aby viděli všechny akce, které provedli. To zákazníkům poskytuje přehled o operacích, které poskytovatelé služeb provádějí, spolu s operacemi prováděnými uživateli v rámci vlastního klienta Azure Active Directory (Azure AD) zákazníka.

Řada organizací zvolí použití Azure Sentinel pro "horká" data, která se používají často a Azure Storage pro "studená" data, která se používají méně často.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Monitorování delegovaných prostředků ve velkém měřítku](how-to/monitor-at-scale.md)

- [Jak můžou zákazníci zobrazit aktivitu poskytovatele služeb](how-to/view-service-provider-activity.md)

- [Správa pracovních prostorů Sentinel Azure ve velkém měřítku](how-to/manage-sentinel-workspaces.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurace uchovávání úložiště protokolu

**Doprovodné** materiály: Azure Lighthouse aktuálně nevytváří žádné protokoly související se zabezpečením. Zákazníci, kteří chtějí zobrazit aktivitu poskytovatele služeb, mohou konfigurovat uchovávání protokolů podle dodržování předpisů, nařízení a obchodních požadavků. 

V Azure Monitor můžete nastavit dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte Azure Storage, Data Lake nebo Log Analytics účty pracovního prostoru pro dlouhodobé a archivační úložiště.

- [Změnit dobu uchovávání dat v Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center výstrahy a doporučení exportovat](../security-center/continuous-export.md) konfiguraci a zákazník nemůže nastavit žádné uchovávání protokolu.

- [Jak zákazník může zkontrolovat data protokolu aktivit pro poskytovatele služeb](how-to/view-service-provider-activity.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

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

Vysoce kvalitní výstrahy se dají vytvářet na základě zkušeností z minulých incidentů, ověřených zdrojů komunity a nástrojů určených k vygenerování a vyčištění výstrah odmítnutím a korelacemi různých zdrojů signálu. 

Azure Security Center poskytuje vysoce kvalitní výstrahy napříč mnoha prostředky Azure. K streamování výstrah do Azure Sentinel můžete použít datový konektor ASC. Azure Sentinel umožňuje vytvářet Pokročilá pravidla výstrah pro automatické generování incidentů pro účely šetření. 

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

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Vytvoření zabezpečených konfigurací pro služby Azure 

**Pokyny**: Azure Lighthouse podporuje níže uvedené zásady pro konkrétní služby, které jsou k dispozici v Azure Security Center k auditování a prosazování konfigurací vašich prostředků Azure. Tato možnost se dá nakonfigurovat v Azure Security Center nebo Azure Policy iniciativ.

- Povolení správy ID tenantů prostřednictvím Azure Lighthouse

- Auditovat delegování oborů do spravovaného tenanta

Plány Azure můžete použít k automatizaci nasazení a konfigurace služeb a prostředí aplikací, včetně šablon Azure Resource Manager, řízení a zásad Azure RBAC a zásad v jediné definici podrobného plánu.

- [Zásady Azure Lighthouse](samples/policy-reference.md)

- [Kurz – vytváření a Správa zásad pro prosazování dodržování předpisů](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprint](../governance/blueprints/overview.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: udržování zabezpečených konfigurací pro služby Azure

**Pokyny**: Azure Lighthouse podporuje níže uvedené zásady pro konkrétní služby, které jsou k dispozici v Azure Security Center k auditování a prosazování konfigurací vašich prostředků Azure. Tato možnost se dá nakonfigurovat v Azure Security Center nebo Azure Policy iniciativ.

- [Zásady Azure Lighthouse](samples/policy-reference.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>Souč_hod-3: Vytvoření zabezpečených konfigurací pro výpočetní prostředky

**Pokyny**: pomocí Azure Security Center a Azure Policy navažte zabezpečené konfigurace na všech výpočetních prostředcích, včetně virtuálních počítačů, kontejnerů a dalších.

- [Jak monitorovat Azure Security Center doporučení](../security-center/security-center-recommendations.md) 

- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: provedení pravidelné simulace útoků

**Doprovodné** materiály: jak je potřeba, proveďte testování průniku nebo červené týmové aktivity na svých prostředcích Azure a zajistěte nápravu všech kritických zjištění zabezpečení.
Postupujte podle pravidel pro testování průniku Microsoft Cloud a zajistěte, aby testy průniku nenarušily zásady Microsoftu. Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft.

- [Testování průniku v Azure](../security/fundamentals/pen-testing.md)

- [Pravidla testování průniku pro zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center**: nelze použít

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

-   Použití nativních funkcí Azure a ochrany dat třetích stran

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
