---
title: Základní hodnoty zabezpečení Azure pro Azure Lighthouse
description: Základní plán zabezpečení Azure Lighthouse poskytuje pokyny a materiály k postupům pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: acc98cd2a724abc779954a5f22c73a5a7c6b9db4
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302442"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>Základní hodnoty zabezpečení Azure pro Azure Lighthouse

Tato základní hodnota zabezpečení se vztahuje na pokyny od [verze Azure Security test 2,0](../security/benchmarks/overview.md) pro Azure Lighthouse. Srovnávací test zabezpečení Azure nabízí doporučení k zabezpečení cloudových řešení v Azure. Obsah se seskupuje podle **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů vztahujících se na Azure Lighthouse. **Ovládací prvky** , které se nevztahují na Azure Lighthouse, se vyloučily.

Pokud chcete zjistit, jak Azure Lighthouse kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení Azure Lighthouse](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Správa identit](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizace Azure Active Directory jako centrálního systému identit a ověřování

**Pokyny**: Azure Lighthouse používá jako výchozí službu pro správu identit a přístupu Azure Active Directory (Azure AD). Standardizace Azure AD pro řízení identity a správy přístupu vaší organizace v nástroji:
- Microsoft Cloud prostředky, jako jsou Azure Portal, Azure Storage, virtuální počítače Azure (Linux a Windows), Azure Key Vault, PaaS a SaaS aplikace.
- prostředky vaší organizace, jako jsou aplikace v Azure nebo prostředky vaší podnikové sítě

V případě Azure Lighthouse mají určení uživatelé ve spravovaném tenantovi integrovanou roli Azure, která jim umožňuje přístup k delegovaným předplatným nebo skupinám prostředků v tenantovi zákazníka. Všechny předdefinované role jsou v současné době podporovány s výjimkou vlastník nebo jakýchkoli předdefinovaných rolí s oprávněním dataactions. Role správce přístupu uživatelů je podporovaná jenom pro omezené použití při přiřazování rolí ke spravovaným identitám. Vlastní role a role správců pro klasický odběr nejsou podporovány.

- [Tenantů v Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Vytvoření a konfigurace instance Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Použít externí zprostředkovatele identity pro aplikaci](/azure/active-directory/b2b/identity-providers) 

- [Jaké je bezpečné skóre identity v Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: zabezpečená a Automatická správa identit aplikací

**Pokyny**: spravované identity Azure se můžou ověřit u služeb a prostředků Azure, které podporují ověřování Azure AD. Ověřování je povoleno prostřednictvím předdefinovaných pravidel udělení přístupu, což vyloučí pevně zakódované přihlašovací údaje ve zdrojovém kódu nebo konfiguračních souborech. S Azure Lighthouse můžou uživatelé s rolí správce přístupu uživatele v předplatném zákazníka vytvořit spravovanou identitu v tenantovi daného zákazníka. I když tato role není pro Azure Lighthouse všeobecně podporovaná, dá se použít v tomto konkrétním scénáři, takže uživatelé s tímto oprávněním můžou k spravovaným identitám přiřadit jednu nebo více specifických rolí.

Pro služby, které nepodporují spravované identity, použijte službu Azure AD k vytvoření instančního objektu s omezenými oprávněními na úrovni prostředků. Azure Lighthouse umožňuje instančním objektům přístup k zákaznickým prostředkům v závislosti na rolích, které jsou během procesu připojování uděleny. Doporučuje se nakonfigurovat instanční objekty s přihlašovacími údaji certifikátu a vrátit se k klientským tajným klíčům. V obou případech se Azure Key Vault dá použít společně se spravovanými identitami Azure, takže běhové prostředí (například funkce Azure) může přihlašovací údaje načíst z trezoru klíčů.

- [Spravované identity Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Instanční objekt Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Použití Azure Key Vault k registraci objektu zabezpečení](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [Vytvoření uživatele, který může přiřadit role ke spravované identitě v tenantovi zákazníka](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Používání kontrolních mechanismů silného ověřování pro veškerý přístup na základě Azure Active Directory

**Pokyny**: vyžadovat Multi-Factor Authentication (MFA) pro všechny uživatele ve vašem spravovaném tenantovi, včetně uživatelů, kteří budou mít přístup k delegovaným zákaznickým prostředkům. Doporučujeme, abyste zákazníkům požádali o implementaci vícefaktorového ověřování ve svých klientech.

- [Povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorování anomálií u účtů a upozorňování na ně

**Doprovodné** materiály: Azure AD poskytuje následující zdroje dat: 

-   Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů.

-   Protokoly auditu – poskytuje sledovatelnost prostřednictvím protokolů pro všechny změny provedené prostřednictvím různých funkcí služby Azure AD. Příklady protokolů auditu v protokolovaných změnách zahrnují přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.

-   Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

-   Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Tyto zdroje dat je možné integrovat s Azure Monitor, s Sentinelou v Azure a systémy SIEM třetích stran.

Poskytovatelé služeb pomocí Azure Lighthouse můžou přesměrovat protokoly Azure AD do Azure Sentinel a zobrazovat nebo nastavovat výstrahy napříč klienty, aby mohli sledovat anomálie účtů a upozorňovat na ně.

- [Sestavy aktivit auditu v Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Zobrazení rizikových přihlášení Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Správa pracovních prostorů Sentinel Azure ve velkém měřítku](how-to/manage-sentinel-workspaces.md)

- [Připojení dat z Azure AD k Azure Sentinel](../sentinel/connect-azure-active-directory.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM – 6: omezit přístup k prostředkům Azure na základě podmínek

**Pokyny**: Azure Lighthouse nepodporuje funkci podmíněného přístupu pro delegované prostředky zákazníků. V části Správa tenanta použijte podmíněný přístup Azure AD pro přesnější řízení přístupu na základě uživatelem definovaných podmínek, jako je třeba vyžadování přihlášení uživatelů z určitých rozsahů IP adres pro použití Multi-Factor Authentication (MFA). Pomocí zásad podmíněného přístupu Azure AD pro různé případy použití lze také použít podrobnou správu relace ověřování. 

Vícefaktorové ověřování byste měli vyžadovat pro všechny uživatele ve vašem spravovaném tenantovi, včetně uživatelů, kteří budou mít přístup k delegovaným zákaznickým prostředkům. Doporučujeme, abyste zákazníkům požádali o implementaci vícefaktorového ověřování ve svých klientech.

- [Přehled podmíněného přístupu Azure](../active-directory/conditional-access/overview.md)

- [Běžné zásady podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurace správy relací ověřování pomocí podmíněného přístupu](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Privilegovaný přístup](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Ochrana a omezení vysoce privilegovaných uživatelů

**Doprovodné** materiály: Omezte počet vysoce privilegovaných uživatelských účtů a chraňte tyto účty na vyšší úrovni. Pro povolení a používání Azure Lighthouse se nevyžaduje účet globálního správce.

Aby bylo možné získat přístup k datům protokolu aktivit na úrovni tenanta, musí být k účtu přiřazená integrovaná role Azure Monitoring Reader v kořenovém oboru (/). Vzhledem k tomu, že role čtenář monitorování v kořenovém oboru je širokou úrovní přístupu, doporučujeme přiřadit tuto roli k hlavnímu účtu služby a nikoli jednotlivým uživatelům nebo skupině. Toto přiřazení musí provést uživatel, který má roli globálního správce s dalšími oprávněními vyšší úrovně přístupu. Tento zvýšený přístup by se měl přidat hned před provedením přiřazení role a pak se po dokončení přiřazení odebere.

- [Oprávnění role správce v Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Přiřazení přístupu k monitorování dat protokolu aktivit na úrovni tenanta](how-to/monitor-delegation-changes.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: omezení přístupu pro správu na systémy kritické pro podnikání

**Pokyny**: Azure Lighthouse používá řízení přístupu na základě role Azure (Azure RBAC) k izolaci přístupu k podnikovým systémům tím, že omezuje přístup k důležitým systémům tím, že omezí účty, ve kterých jsou udělen privilegovaný přístup k předplatným a skupinám pro správu.

Nezapomeňte postupovat podle principu minimálního oprávnění, aby uživatelé měli jenom oprávnění potřebná k provádění jejich konkrétních úloh.

Ujistěte se, že také omezíte přístup k systémům pro správu, identitu a zabezpečení, které mají přístup pro správu k vašemu podnikovému přístupu, jako jsou Doména služby Active Directory Controllers (DCs), nástroje zabezpečení a nástroje pro správu systému s agenty nainstalovanými v důležitých podnikových systémech. Útočníci, kteří tyto systémy pro správu a zabezpečení ohrožují, si je můžou hned weaponize, aby vyzrazeni důležité obchodní prostředky.

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu.

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Přístup ke skupině pro správu](../governance/management-groups/overview.md#management-group-access)

- [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Osvědčené postupy pro definování uživatelů a rolí pro Azure Lighthouse](concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Pravidelné kontroly a sjednocování uživatelského přístupu

**Pokyny**: Azure Lighthouse používá ke správě svých prostředků účty služby Azure Active Directory (Azure AD), pravidelně kontrolují uživatelské účty a přiřazení přístupu, aby bylo zajištěno, že účty a jejich přístup jsou platné. Kontroly přístupu Azure AD můžete použít ke kontrole členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí. Vytváření sestav Azure AD může poskytovat protokoly, které vám pomůžou zjistit zastaralé účty. K usnadnění procesu kontroly můžete použít také Azure AD Privileged Identity Management k vytvoření pracovního postupu sestavy kontroly přístupu.

Zákazníci si můžou na Azure Portal zkontrolovat úroveň přístupu udělených uživatelům v rámci správy tenanta prostřednictvím Azure Lighthouse. Tito uživatelé můžou tento přístup kdykoli odebrat.

Kromě toho je možné Azure Privileged Identity Management nakonfigurovat tak, aby výstrahu při vytváření nadměrného počtu účtů správců a identifikaci účtů správce, které jsou zastaralé nebo nesprávně nakonfigurované.

Poznámka: některé služby Azure podporují místní uživatele a role, které se nespravují prostřednictvím služby Azure AD. Tyto uživatele budete muset spravovat samostatně.

- [Vytvoření kontroly přístupu pro role prostředků Azure v Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Odebrání přístupu k delegování](how-to/remove-delegation.md)

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Zobrazení stránky poskytovatelé služeb v Azure Portal](how-to/view-manage-service-providers.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: nastavení nouzového přístupu v Azure AD

**Doprovodné** materiály: Azure Lighthouse je integrovaný s Azure Active Directory pro správu prostředků. Pokud se chcete vyhnout náhodnému uzamčení vaší organizace Azure AD, nastavte účet pro nouzový přístup pro přístup, když nebudete moct použít normální účty pro správu. Účty pro nouzový přístup jsou obvykle vysoce privilegované a neměly by být přiřazeny konkrétním jednotlivcům. Účty pro nouzový přístup jsou omezené na nouzové nebo "oddělovací sklo", kde se nedají použít běžné účty pro správu.

Měli byste zajistit, aby pověření (například heslo, certifikát nebo čipová karta) pro účty pro nouzový přístup byla zabezpečená a známá jenom jednotlivcům, kteří jsou oprávněni je používat jenom v nouzi.

- [Správa účtů pro nouzový přístup v Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizace správy nároků 

**Pokyny**: Služba Azure Lighthouse je integrovaná s Azure Active Directory (Azure AD) pro správu prostředků. Pomocí funkcí správy opravňujících ke službě Azure AD můžete automatizovat pracovní postupy pro žádosti o přístup, včetně přiřazení přístupu, revizí a vypršení platnosti. Podporuje se také duální nebo vícenásobné schválení.

- [Co jsou kontroly přístupu Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Co je Správa nároků Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: použití pracovních stanic s privilegovaným přístupem

**Doprovodné** materiály: zabezpečené, izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a kritické operátory služby. V závislosti na vašich požadavcích můžete pomocí vysoce zabezpečených uživatelských pracovních stanic nebo Azure bastionu provádět úlohy správy s Azure Lighthouse v produkčních prostředích. Pomocí Azure Active Directory, programu Microsoft Defender Advanced Threat Protection (ATP) a/nebo Microsoft Intune nasaďte zabezpečenou a spravovanou pracovní stanici uživatelů pro úlohy správy. Zabezpečená pracovní stanice se dají centrálně spravovat tak, aby vynutila zabezpečenou konfiguraci, včetně silného ověřování, standardních hodnot softwaru a hardwaru a omezeného logického a síťového přístupu. 

- [Pochopení pracovních stanic s privilegovaným přístupem](../active-directory/devices/concept-azure-managed-workstation.md)

- [Nasazení privilegované pracovní stanice přístupu](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Využívání správy jen v potřebném rozsahu (princip nejnižší úrovně nutných oprávnění) 

**Pokyny**: Azure Lighthouse je integrovaný s řízením přístupu na základě role (RBAC) Azure pro správu prostředků. Azure RBAC umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto předdefinované role můžete přiřadit uživatelům, skupinám, objektům služby a spravovaným identitám. Pro určité prostředky existují předdefinované role, které je možné inventarizovat nebo dotazovat pomocí nástrojů, jako jsou Azure CLI, Azure PowerShell nebo Azure Portal. Oprávnění k prostředkům přiřazená prostřednictvím Azure RBAC by vždy měla být omezená pouze na to, co konkrétní role vyžadují. Tento přístup doplňuje přístup založený na přístupu podle potřeby (JIT) ve službě Azure AD Privileged Identity Management (PIM) a měl by se pravidelně kontrolovat. Pomocí integrovaných rolí můžete přidělit oprávnění a v případě potřeby vytvářet pouze vlastní role.

Azure Lighthouse umožňuje přístup k delegovaným zákaznickým prostředkům pomocí integrovaných rolí Azure. Ve většině případů budete chtít přiřadit tyto role k objektu skupiny nebo instančnímu objektu, a ne k mnoha jednotlivým uživatelským účtům. To vám umožní přidat nebo odebrat přístup pro jednotlivé uživatele bez nutnosti aktualizace a opětovného publikování plánu, když se změní vaše požadavky na přístup.

Pro delegování zákaznických prostředků do spravovaného tenanta musí být nasazení provedeno účtem bez hosta v tenantovi zákazníka, který má předdefinovanou roli předplatného pro odběr (nebo který obsahuje skupiny prostředků, které jsou připojené).

- [Pochopení tenantů, uživatelů a rolí v Azure Lighthouse](concepts/tenants-users-roles.md)

- [Jak použít princip nejnižších oprávnění pro Azure Lighthouse](concepts/recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)

- [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="asset-management"></a>Správa aktiv

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Správa prostředků](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Zajištění přehledu bezpečnostního týmu o rizicích pro prostředky

**Pokyny:** Zajistěte udělení oprávnění Čtenář zabezpečení ve vašem tenantovi Azure a vašich předplatných bezpečnostním týmům, aby mohly monitorovat bezpečnostní rizika s využitím služby Azure Security Center. 

V závislosti na nastavení struktury odpovědnosti bezpečnostních týmů může být monitorování bezpečnostních rizik odpovědností centrálního nebo místního bezpečnostního týmu. Přehledy zabezpečení a bezpečnostní rizika se však v rámci organizace musí vždy agregovat centrálně. 

Oprávnění Čtenář zabezpečení je možné přidělit pro celého tenanta (kořenová skupina pro správu) nebo je vymezit na konkrétní skupiny pro správu nebo konkrétní předplatná. 

Poznámka: K získání přehledu o úlohách a službách se můžou vyžadovat další oprávnění. 

- [Přehled role Čtenář zabezpečení](../role-based-access-control/built-in-roles.md#security-reader)

- [Přehled skupin pro správu Azure](../governance/management-groups/overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>DOP. 2: Ujistěte se, že má tým zabezpečení přístup k inventáři prostředků a metadatům.

**Pokyny**: týmy zabezpečení pro zákazníky mohou kontrolovat protokoly aktivit a zobrazit tak aktivity prováděné poskytovateli služeb, kteří používají Azure Lighthouse. 

Pokud chce poskytovatel pro zabezpečení zkontrolovat prostředky delegovaného zákazníka, musí mít autorizaci bezpečnostního týmu zabudovanou roli čtenáře.

- [Jak zákazník může zkontrolovat aktivitu poskytovatele služeb](how-to/view-service-provider-activity.md)

- [Určení rolí při připojování zákazníka k Azure Lighthouse](how-to/onboard-customer.md#define-roles-and-permissions)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: pomocí Azure Policy můžete auditovat a omezovat, které služby můžou uživatelé zřídit ve vašem prostředí. Pomocí Azure Resource graphu se můžete dotazovat a zjišťovat prostředky v rámci svých předplatných. Pomocí Azure Monitor můžete také vytvořit pravidla pro aktivaci výstrah při zjištění neschválené služby.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Zajištění zabezpečení správy životního cyklu prostředků

**Pokyny**: Odeberte přístup k prostředkům, které byly delegovány prostřednictvím Azure Lighthouse, jakmile už nejsou potřeba, aby poskytovatelé služeb už nemuseli mít přístup. Přístup může odebrat buď zákazník, nebo poskytovatel služeb. 

- [Odebrání přístupu k delegování](how-to/remove-delegation.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Doprovodné** materiály: Služba Azure Lighthouse je integrovaná s Azure Active Directory (Azure AD) pro identitu a ověřování. Pomocí podmíněného přístupu Azure můžete omezit schopnost uživatelů pracovat se správcem prostředků Azure tak, že nakonfigurujete "blokování přístupu" pro aplikaci Microsoft Azure Management.

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: povolení detekce hrozeb pro prostředky Azure

**Doprovodné** materiály: prostřednictvím Azure Lighthouse můžete monitorovat prostředky Azure vašich zákazníků na potenciální hrozby a anomálie. Zaměřte se na získávání vysoce kvalitních výstrah, které omezí falešně pozitivní výsledky pro analytiky k řazení. Výstrahy se dají nacházet z dat protokolů, agentů nebo jiných dat.

Využijte Azure Security Center integrovanou funkci detekce hrozeb, která vychází z monitorování telemetrie služeb Azure a analýzy protokolů služby. Data se shromažďují pomocí Log Analytics agenta, který čte různé konfigurace a protokoly událostí související se zabezpečením ze systému a kopíruje data do vašeho pracovního prostoru pro účely analýzy. 

Kromě toho použijte Azure Sentinel k sestavování analytických pravidel, která navzájemují hrozby, které odpovídají konkrétním kritériím v prostředí zákazníka. Pravidla generují incidenty, když jsou kritéria shodná, takže můžete prozkoumat jednotlivé incidenty. Funkce Sentinel Azure může taky importovat analýzy hrozeb jiných výrobců, aby se zlepšila její schopnost detekce hrozeb. 

- [Ochrana před hrozbami v Azure Security Center](/azure/security-center/threat-protection)

- [Referenční příručka pro Azure Security Center výstrahy zabezpečení](../security-center/alerts-reference.md)

- [Vytváření vlastních pravidel analýzy pro detekci hrozeb](../sentinel/tutorial-detect-threats-custom.md)

- [Správa pracovních prostorů Sentinel Azure ve velkém měřítku](how-to/manage-sentinel-workspaces.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Povolení detekce hrozeb pro správu identit a přístupu v Azure

**Doprovodné** materiály: prostřednictvím Azure Lighthouse můžete pomocí Azure Security Center upozorňovat na určité podezřelé aktivity v klientech zákazníků, které spravujete, jako je například nadměrný počet neúspěšných pokusů o ověření a zastaralé účty v rámci předplatného.

Azure Active Directory (Azure AD) poskytuje následující protokoly uživatelů, které se dají zobrazit v sestavách Azure AD nebo v nich integrovat s využitím Azure Monitor, Azure Sentinel nebo jiné nástroje SIEM/Monitoring pro výkonnější monitorování a analytické účely:
- Přihlášení – sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.
- Rizikové přihlášení – rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.
- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako jsou nadměrný počet neúspěšných pokusů o ověření nebo zastaralé účty v předplatném. Kromě základního monitorování hygieny zabezpečení může modul ochrany před hrozbami služby Azure Security Center také shromažďovat podrobnější upozornění zabezpečení z jednotlivých výpočetních prostředků Azure (virtuální počítače, kontejnery, App Service), datových prostředků (databáze SQL a úložiště) a vrstev služeb Azure. Tato funkce poskytuje přehled o anomáliích v účtech v jednotlivých prostředcích. 

- [Vylepšené služby a scénáře s využitím Azure Lighthouse](concepts/cross-tenant-management-experience.md#enhanced-services-and-scenarios)

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Povolení ochrany identit Azure](../active-directory/identity-protection/overview-identity-protection.md) 

- [Ochrana před hrozbami v Azure Security Center](/azure/security-center/threat-protection)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: povolení protokolování pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit, které jsou automaticky k dispozici, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky Azure Lighthouse s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

S Azure Lighthouse můžete protokolovat pomocí Azure Monitor škálovatelným způsobem napříč klienty zákazníka, které spravujete. Vytvářejte Log Analytics pracovní prostory přímo v klientech zákazníka, aby zákaznická data zůstala ve svých klientech a nemuseli je exportovat. Díky tomu je možné centralizované monitorování všech prostředků nebo služeb, které Log Analytics podporuje, a poskytuje větší flexibilitu v tom, jaké typy dat sledujete.

Zákazníci, kteří mají delegované předplatné pro Azure Lighthouse, mohou zobrazit data protokolu aktivit Azure, aby viděli všechny akce, které provedli. To zákazníkům poskytuje přehled o operacích, které poskytovatelé služeb provádějí, spolu s operacemi prováděnými uživateli v rámci vlastního klienta Azure Active Directory (Azure AD) zákazníka.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Monitorování delegovaných prostředků ve velkém měřítku](how-to/monitor-at-scale.md)

- [Zobrazení aktivity poskytovatele služeb](how-to/view-service-provider-activity.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílená

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

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurace uchovávání úložiště protokolu

**Doprovodné** materiály: Azure Lighthouse aktuálně nevytváří žádné protokoly související se zabezpečením. Zákazníci, kteří chtějí zobrazit aktivitu poskytovatele služeb, mohou konfigurovat uchovávání protokolů podle dodržování předpisů, nařízení a obchodních požadavků. 

V Azure Monitor můžete nastavit dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte Azure Storage, Data Lake nebo Log Analytics účty pracovního prostoru pro dlouhodobé a archivační úložiště.

- [Změnit dobu uchovávání dat v Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center výstrahy a doporučení exportovat](../security-center/continuous-export.md) konfiguraci a zákazník nemůže nastavit žádné uchovávání protokolu.

- [Jak zákazník může zkontrolovat data protokolu aktivit pro poskytovatele služeb](how-to/view-service-provider-activity.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Reakce na incidenty](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Příprava – Aktualizace procesu reakce na incidenty pro Azure

**Pokyny:** Ujistěte se, že jsou ve vaší organizaci nastavené procesy pro reakci na incidenty zabezpečení, jsou aktualizované pro Azure a pravidelně se uplatňují, aby se zajistila připravenost na tyto incidenty.

- [Implementace zabezpečení v podnikovém prostředí](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referenční příručka k reakci na incidenty](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Příprava – Nastavení oznámení o incidentech

**Pokyny:** Nastavte ve službě Azure Security Center kontaktní informace pro incidenty zabezpečení. Prostřednictvím těchto kontaktních informací vás bude Microsoft kontaktovat, pokud středisko Microsoft Security Response Center (MSRC) zjistí, že k vašim datům nezákonně nebo neoprávněně přistupovala třetí strana. Můžete také upravit upozornění na incidenty a oznámení o incidentech v různých službách Azure podle vašich požadavků na reakci na incidenty. 

- [Nastavení kontaktu ve službě Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detekce a analýza – Vytváření incidentů na základě vysoce kvalitních upozornění

**Doprovodné** materiály: Ujistěte se, že máte proces pro vytváření vysoce kvalitních výstrah a měření kvality výstrah. To vám umožní učit se z minulých incidentů a určovat prioritu upozornění pro analytiky, aby neztráceli čas falešně pozitivními výsledky. 

Vysoce kvalitní výstrahy se dají vytvářet na základě zkušeností z minulých incidentů, ověřených zdrojů komunity a nástrojů určených k vygenerování a vyčištění výstrah odmítnutím a korelacemi různých zdrojů signálu. 

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

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detekce a analýza – Určování priority incidentů

**Pokyny:** Poskytněte analytikům kontext, aby věděli, na jaké incidenty se zaměřit jako první v závislosti na závažnosti upozornění a citlivosti prostředků. 

Azure Security Center jednotlivým upozorněním přiřazuje závažnost, která vám pomůže určit, jaká upozornění by se měla vyšetřit jako první. Závažnost vychází z míry důvěry služby Security Center v závěr nebo analýzu, na základě kterých se upozornění vygenerovalo, a také z úrovně spolehlivosti, že za aktivitou, která vedla k upozornění, byl škodlivý záměr.

Kromě toho můžete prostředky označit pomocí značek a vytvořit systém vytváření názvů, který vám pomůže identifikovat a kategorizovat prostředky Azure, zejména prostředky zpracovávající citlivá data.  Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Izolace, odstranění a obnovení – Automatizace řešení incidentů

**Pokyny:** Zrychlete reakci a snižte zátěž analytiků automatizací opakovaných ručních úloh. Provádění ručních úloh trvá déle, což zpomaluje řešení jednotlivých incidentů a snižuje počet incidentů, které můžou jednotliví analytici vyřešit. Ruční úlohy také analytiky více unavují, což zvyšuje riziko zpoždění v důsledku lidských chyb a snižuje schopnost analytiků efektivně se soustředit na složité úlohy. S využitím funkcí automatizace pracovních postupů ve službách Azure Security Center a Azure Sentinel můžete v reakci na příchozí upozornění zabezpečení automaticky aktivovat akce nebo spouštět playbooky. Playbook provádí akce, jako jsou odesílání oznámení, zakazování účtů nebo izolace problematických sítí. 

- [Konfigurace automatizace pracovních postupů ve službě Security Center](../security-center/workflow-automation.md)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="posture-and-vulnerability-management"></a>Správa stavu a ohrožení zabezpečení

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Správa stavu a ohrožení zabezpečení](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Vytvoření zabezpečených konfigurací pro služby Azure 

**Pokyny**: Azure Lighthouse podporuje níže uvedené zásady pro konkrétní služby, které jsou k dispozici v Azure Security Center k auditování a prosazování konfigurací vašich prostředků Azure. Tato možnost se dá nakonfigurovat v Azure Security Center nebo Azure Policy iniciativ.

- Povolení správy ID tenantů prostřednictvím Azure Lighthouse

- Auditovat delegování oborů do spravovaného tenanta

Plány Azure můžete použít k automatizaci nasazení a konfigurace služeb a prostředí aplikací, včetně šablon Azure Resource Manager, řízení a zásad Azure RBAC a zásad v jediné definici podrobného plánu.

- [Zásady Azure Lighthouse](samples/policy-reference.md)

- [Kurz – vytváření a Správa zásad pro prosazování dodržování předpisů](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprint](../governance/blueprints/overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: udržování zabezpečených konfigurací pro služby Azure

**Pokyny**: Azure Lighthouse podporuje níže uvedené zásady pro konkrétní služby, které jsou k dispozici v Azure Security Center k auditování a prosazování konfigurací vašich prostředků Azure. Tato možnost se dá nakonfigurovat v Azure Security Center nebo Azure Policy iniciativ.

- [Zásady Azure Lighthouse](samples/policy-reference.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>Souč_hod-3: Vytvoření zabezpečených konfigurací pro výpočetní prostředky

**Pokyny**: pomocí Azure Security Center a Azure Policy navažte zabezpečené konfigurace na všech výpočetních prostředcích, včetně virtuálních počítačů, kontejnerů a dalších.

- [Jak monitorovat Azure Security Center doporučení](../security-center/security-center-recommendations.md) 

- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Provádění pravidelných simulací útoku

**Pokyny:** Provádějte u vašich prostředků Azure podle potřeby penetrační testování nebo aktivity etického testování průniku a zajistěte nápravu všech kritických zjištění o zabezpečení.
Postupujte podle pravidel penetračního testování cloudu Microsoftu, abyste zajistili, že vaše penetrační testy nejsou v rozporu se zásadami Microsoftu. Využijte strategii Microsoftu a provádění etického testování průniku a penetračního testování živých webů proti cloudové infrastruktuře, službám a aplikacím spravovaným Microsoftem.

- [Penetrační testování v Azure](../security/fundamentals/pen-testing.md)

- [Pravidla penetračního testování](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Etické testování průniku do cloudu Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Sdílená

## <a name="governance-and-strategy"></a>Zásady správného řízení a strategie

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Zásady správného řízení a strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definování strategie správy prostředků a ochrany dat 

**Pokyny:** Zajistěte zdokumentování a komunikaci jasné strategie průběžného monitorování a ochrany systémů a dat. Jako prioritu si stanovte zjišťování, hodnocení, ochranu a monitorování důležitých obchodních dat a systémů. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující aspekty: 

-   Standard klasifikace dat podle obchodních rizik

-   Přehled bezpečnostní organizace o rizicích a inventáři prostředků 

-   Schvalování použití služeb Azure ze strany bezpečnostní organizace 

-   Zabezpečení prostředků v rámci jejich životního cyklu

-   Požadovaná strategie řízení přístupu podle klasifikace dat organizace

-   Použití nativních funkcí Azure a ochrany dat třetích stran

-   Požadavky na šifrování přenášených a neaktivních uložených dat

-   Příslušné kryptografické standardy

Další informace najdete na následujících odkazech:
- [Doporučení pro architekturu zabezpečení Azure – Úložiště, data a šifrování](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Základy zabezpečení Azure – Zabezpečení dat v Azure, šifrování a úložiště](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – Osvědčené postupy šifrování a zabezpečení dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Srovnávací test zabezpečení Azure – Správa prostředků](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Srovnávací test zabezpečení Azure – Ochrana dat](/azure/security/benchmarks/security-controls-v2-data-protection)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definování podnikové strategie segmentace 

**Pokyny:** Vytvořte celopodnikovou strategii segmentace přístupu k prostředkům s využitím kombinace identity, sítě, aplikace, předplatného, skupiny pro správu a dalších kontrolních mechanismů.

Opatrně najděte rovnováhu mezi potřebou oddělit zabezpečení a potřebou zajistit denní provoz systémů, které potřebují vzájemně komunikovat a přistupovat k datům.

Zajistěte konzistentní implementaci této strategie segmentace napříč různými typy kontrolních mechanismů, včetně zabezpečení sítě, modelů identit a přístupu, modelů oprávnění a přístupu k aplikacím a kontrolních mechanismů lidských procesů.

- [Pokyny ke strategii segmentace v Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Pokyny ke strategii segmentace v Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Sladění segmentace sítě s podnikovou strategií segmentace](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definování strategie správy stavu zabezpečení

**Pokyny:** Průběžně měřte a zmírňujte rizika pro jednotlivé prostředky a prostředí, ve kterém jsou hostované. Jako prioritu si stanovte prostředky s vysokou hodnotou a vysoce exponované oblasti útoku, jako jsou publikované aplikace, vstupní a výstupní body sítě, koncové body uživatelů a správců atd.

- [Srovnávací test zabezpečení Azure – Správa stavu a ohrožení zabezpečení](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Sladění rolí, povinností a odpovědnosti v rámci organizace

**Pokyny:** Zajistěte zdokumentování a komunikaci jasné strategie pro role a odpovědnost ve vaší bezpečnostní organizaci. Jako prioritu si stanovte zajištění jasné odpovědnosti za rozhodnutí ohledně zabezpečení, informování všech o modelu sdílené odpovědnosti a informování technických týmů o technologiích pro zabezpečení cloudu.

- [Osvědčený postup zabezpečení Azure 1 – Lidé: Informování týmů o cestě ke cloudovému zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Osvědčený postup zabezpečení Azure 2 – Lidé: Informování týmů o technologiích cloudového zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Osvědčený postup zabezpečení Azure 3 – Procesy: Přiřazení odpovědnosti za rozhodnutí ohledně cloudového zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definování strategie zabezpečení sítě

**Pokyny:** Nastavte v rámci celkové bezpečnostní strategie řízení přístupu vaší organizace přístup k zabezpečení sítě Azure.  

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující aspekty: 

-   Centralizovaná správa sítě a odpovědnost za zabezpečení

-   Model segmentace virtuálních sítí v souladu s podnikovou strategií segmentace

-   Strategie náprav v různých scénářích hrozeb a útoků

-   Strategie pro Internet Edge a příchozí a odchozí/Azure/Security/benchmarks/Security-Controls-v2-Logging-Threat-Protection
-   Strategie propojení hybridního cloudu a místního prostředí

-   Aktuální artefakty zabezpečení sítě (např. síťové diagramy, referenční síťová architektura)

Další informace najdete na následujících odkazech:
- [Osvědčený postup zabezpečení Azure 11 – Architektura: Jednotná bezpečnostní strategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Srovnávací test zabezpečení Azure – Zabezpečení sítě](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Přehled zabezpečení sítě Azure](../security/fundamentals/network-overview.md)

- [Strategie pro architekturu podnikové sítě](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definování strategie privilegovaného přístupu a identit

**Pokyny:** Nastavte v rámci celkové bezpečnostní strategie řízení přístupu vaší organizace přístupy k privilegovanému přístupu a identitám Azure.  

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující aspekty: 

-   Centralizovaný systém identit a ověřování a jeho propojení s dalšími interními a externími systémy identit

-   Metody silného ověřování v různých případech použití a podmínkách

-   Ochrana vysoce privilegovaných uživatelů

-   Monitorování a zpracování neobvyklých aktivit uživatelů  

-   Proces kontroly a odsouhlasení identit a přístupu uživatelů

Další informace najdete na následujících odkazech:

- [Srovnávací test zabezpečení Azure – Správa identit](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Srovnávací test zabezpečení Azure – Privilegovaný přístup](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Osvědčený postup zabezpečení Azure 11 – Architektura: Jednotná bezpečnostní strategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Přehled zabezpečení správy identit Azure](../security/fundamentals/identity-management-overview.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definování strategie protokolování a reakce na hrozby

**Pokyny:** Nastavte strategii protokolování a reakce na hrozby, abyste mohli rychle detekovat a zmírňovat hrozby při současném splnění požadavků na dodržování předpisů. Určete prioritu poskytování analytiků s vysoce kvalitními výstrahami a bezproblémové prostředí, aby se mohly soustředit na hrozby, nikoli na integraci a ruční kroky. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující aspekty: 

-   Role a odpovědnost organizace s ohledem na operace zabezpečení (SecOps) 

-   Jasně definovaný proces reakce na incidenty, který je v souladu s NIST nebo jiným průmyslovým rámcem 

-   Zaznamenávání a uchovávání protokolů pro potřeby detekce hrozeb, reakce na incidenty a zajištění dodržování předpisů

-   Centralizovaný přehled a korelace informací o hrozbách s využitím řešení SIEM, nativních funkcí Azure a dalších zdrojů 

-   Plán komunikace a oznamování pro vaše zákazníky, dodavatele a zúčastněné strany z řad veřejnosti

-   Používání nativních platforem Azure a platforem třetích stran při řešení incidentů, například pro účely protokolování a detekce hrozeb, zmírňování a likvidace útoků a forenzní účely

-   Procesy pro řešení incidentů a aktivit po incidentech, jako je uchovávání poznatků, zkušeností a důkazů

Další informace najdete na následujících odkazech:

- [Srovnávací test zabezpečení Azure – Protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Srovnávací test zabezpečení Azure – Reakce na incidenty](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Osvědčený postup zabezpečení Azure 4 – Procesy: Aktualizace procesů reakce na incidenty pro cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Cloud Adoption Framework – Průvodce rozhodováním ohledně protokolování a generování sestav](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Správa a monitorování na podnikové úrovni v Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="next-steps"></a>Další kroky

- Projděte si [přehled srovnávacího testu zabezpečení Azure v2](/azure/security/benchmarks/overview).
- Přečtěte si další informace o [standardních hodnotách zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview).
