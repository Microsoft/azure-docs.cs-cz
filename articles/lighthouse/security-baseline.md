---
title: Základní hodnoty zabezpečení Azure pro Azure Lighthouse
description: Základní plán zabezpečení Azure Lighthouse poskytuje pokyny a materiály k postupům pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a6a1b41dd4ede055ba81c069c2164bb36da99876
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602304"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>Základní hodnoty zabezpečení Azure pro Azure Lighthouse

Tato základní hodnota zabezpečení se vztahuje na pokyny od [verze Azure Security test 2,0](../security/benchmarks/overview.md) pro Azure Lighthouse. Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah se seskupuje podle **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů vztahujících se na Azure Lighthouse. **Ovládací prvky** , které se nevztahují na Azure Lighthouse, se vyloučily.

Pokud chcete zjistit, jak Azure Lighthouse kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení Azure Lighthouse](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [Azure Security Benchmark: správa identit](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizace Azure Active Directory jako centrálního systému pro identifikaci a ověřování

**Pokyny**: Azure Lighthouse používá jako výchozí službu pro správu identit a přístupu Azure Active Directory (Azure AD). Standardizace Azure AD pro řízení identity a správy přístupu vaší organizace v nástroji:
- Microsoft Cloud prostředky, jako jsou Azure Portal, Azure Storage, virtuální počítače Azure (Linux a Windows), Azure Key Vault, PaaS a SaaS aplikace.
- prostředky vaší organizace, jako jsou aplikace v Azure nebo prostředky vaší podnikové sítě

V případě Azure Lighthouse mají určení uživatelé ve spravovaném tenantovi integrovanou roli Azure, která jim umožňuje přístup k delegovaným předplatným nebo skupinám prostředků v tenantovi zákazníka. Všechny předdefinované role jsou v současné době podporovány s výjimkou vlastník nebo jakýchkoli předdefinovaných rolí s oprávněním dataactions. Role správce přístupu uživatelů je podporovaná jenom pro omezené použití při přiřazování rolí ke spravovaným identitám. Vlastní role a role správců pro klasický odběr nejsou podporovány.

- [Architektura tenantů v Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Použití externích zprostředkovatelů identity pro aplikaci](../active-directory/external-identities/identity-providers.md) 

- [Co je skóre zabezpečení identity v Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Zabezpečená a automatická správa identit aplikací

**Pokyny**: spravované identity Azure se můžou ověřit u služeb a prostředků Azure, které podporují ověřování Azure AD. Ověřování je povoleno prostřednictvím předdefinovaných pravidel udělení přístupu, což vyloučí pevně zakódované přihlašovací údaje ve zdrojovém kódu nebo konfiguračních souborech. S Azure Lighthouse můžou uživatelé s rolí správce přístupu uživatele v předplatném zákazníka vytvořit spravovanou identitu v tenantovi daného zákazníka. I když tato role není pro Azure Lighthouse všeobecně podporovaná, dá se použít v tomto konkrétním scénáři, takže uživatelé s tímto oprávněním můžou k spravovaným identitám přiřadit jednu nebo více specifických rolí.

Pro služby, které nepodporují spravované identity, použijte službu Azure AD k vytvoření instančního objektu s omezenými oprávněními na úrovni prostředků. Azure Lighthouse umožňuje instančním objektům přístup k zákaznickým prostředkům v závislosti na rolích, které jsou během procesu připojování uděleny. Doporučuje se nakonfigurovat instanční objekty s přihlašovacími údaji certifikátu a vrátit se k klientským tajným klíčům. V obou případech se Azure Key Vault dá použít společně se spravovanými identitami Azure, takže běhové prostředí (například funkce Azure) může přihlašovací údaje načíst z trezoru klíčů.

- [Spravované identity Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Instanční objekt Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Použití Azure Key Vault k registraci objektu zabezpečení](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [Vytvoření uživatele, který může přiřadit role ke spravované identitě v tenantovi zákazníka](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Použití řídicích prvků silného ověřování pro veškerý přístup založený na Azure Active Directory

**Pokyny**: vyžadovat Multi-Factor Authentication (MFA) pro všechny uživatele ve vašem spravovaném tenantovi, včetně uživatelů, kteří budou mít přístup k delegovaným zákaznickým prostředkům. Doporučujeme, abyste zákazníkům požádali o implementaci vícefaktorového ověřování ve svých klientech.

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorování a upozornění na anomálie účtů

**Doprovodné** materiály: Azure AD poskytuje následující zdroje dat: 

-   Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů.

-   Protokoly auditu – poskytuje sledovatelnost prostřednictvím protokolů pro všechny změny provedené prostřednictvím různých funkcí služby Azure AD. Příklady protokolů auditu v protokolovaných změnách zahrnují přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.

-   Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

-   Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Tyto zdroje dat je možné integrovat s Azure Monitor, s Sentinelou v Azure a systémy SIEM třetích stran.

Poskytovatelé služeb pomocí Azure Lighthouse můžou přesměrovat protokoly Azure AD do Azure Sentinel a zobrazovat nebo nastavovat výstrahy napříč klienty, aby mohli sledovat anomálie účtů a upozorňovat na ně.

- [Sestavy aktivit auditu v Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Správa pracovních prostorů Sentinel Azure ve velkém měřítku](how-to/manage-sentinel-workspaces.md)

- [Připojení dat z Azure AD k Azure Sentinel](../sentinel/connect-azure-active-directory.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Omezení přístupu k prostředkům Azure na základě podmínek

**Pokyny**: Azure Lighthouse nepodporuje funkci podmíněného přístupu pro delegované prostředky zákazníků. V části Správa tenanta použijte podmíněný přístup Azure AD pro přesnější řízení přístupu na základě uživatelem definovaných podmínek, jako je třeba vyžadování přihlášení uživatelů z určitých rozsahů IP adres pro použití Multi-Factor Authentication (MFA). Pomocí zásad podmíněného přístupu Azure AD pro různé případy použití lze také použít podrobnou správu relace ověřování. 

Vícefaktorové ověřování byste měli vyžadovat pro všechny uživatele ve vašem spravovaném tenantovi, včetně uživatelů, kteří budou mít přístup k delegovaným zákaznickým prostředkům. Doporučujeme, abyste zákazníkům požádali o implementaci vícefaktorového ověřování ve svých klientech.

- [Přehled podmíněného přístupu Azure](../active-directory/conditional-access/overview.md)

- [Společné zásady podmíněného přístupu](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurace správy relací ověřování pomocí podmíněného přístupu](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Azure Security Benchmark: privilegovaný přístup](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Ochrana a omezení vysoce privilegovaných uživatelů

**Doprovodné** materiály: Omezte počet vysoce privilegovaných uživatelských účtů a chraňte tyto účty na vyšší úrovni. Pro povolení a používání Azure Lighthouse se nevyžaduje účet globálního správce.

Aby bylo možné získat přístup k datům protokolu aktivit na úrovni tenanta, musí být k účtu přiřazená integrovaná role Azure Monitoring Reader v kořenovém oboru (/). Vzhledem k tomu, že role čtenář monitorování v kořenovém oboru je širokou úrovní přístupu, doporučujeme přiřadit tuto roli k hlavnímu účtu služby a nikoli jednotlivým uživatelům nebo skupině. Toto přiřazení musí provést uživatel, který má roli globálního správce s dalšími oprávněními vyšší úrovně přístupu. Tento zvýšený přístup by se měl přidat hned před provedením přiřazení role a pak se po dokončení přiřazení odebere.

- [Oprávnění role správce v Azure AD](../active-directory/roles/permissions-reference.md)

- [Přiřazení přístupu k monitorování dat protokolu aktivit na úrovni tenanta](how-to/monitor-delegation-changes.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Omezení přístupu pro správu k nejdůležitějším podnikovým systémům

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

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Pravidelná kontrola a sjednocování přístupu uživatelů

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

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Nastavení nouzového přístupu v Azure AD

**Doprovodné** materiály: Azure Lighthouse je integrovaný s Azure Active Directory pro správu prostředků. Pokud se chcete vyhnout náhodnému uzamčení vaší organizace Azure AD, nastavte účet pro nouzový přístup pro přístup, když nebudete moct použít normální účty pro správu. Účty pro nouzový přístup jsou obvykle vysoce privilegované a neměly by se přiřazovat konkrétním jednotlivcům. Jejich použití je omezené na nouzové nebo kritické situace, kdy se nedají použít běžné účty pro správu.

Měli byste zajistit, aby přihlašovací údaje (například heslo, certifikát nebo čipová karta) pro účty pro nouzový přístup byly zabezpečené a aby o nich věděli jenom ti, kteří jsou oprávněni je použít jenom v případě nouze.

- [Správa účtů pro nouzový přístup v Azure AD](../active-directory/roles/security-emergency-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizace správy nároků 

**Pokyny**: Služba Azure Lighthouse je integrovaná s Azure Active Directory (Azure AD) pro správu prostředků. Pomocí funkcí správy opravňujících ke službě Azure AD můžete automatizovat pracovní postupy pro žádosti o přístup, včetně přiřazení přístupu, revizí a vypršení platnosti. Podporuje se také duální nebo vícenásobné schválení.

- [Co jsou kontroly přístupu Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Co je Správa nároků Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Použití pracovních stanic s privilegovaným přístupem

**Pokyny:** Zabezpečené, izolované pracovní stanice jsou nesmírně důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a obsluha nejdůležitějších služeb. V závislosti na vašich požadavcích můžete pomocí vysoce zabezpečených uživatelských pracovních stanic nebo Azure bastionu provádět úlohy správy s Azure Lighthouse v produkčních prostředích. K nasazení zabezpečené a spravované uživatelské pracovní stanice pro úlohy správy použijte Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) nebo Microsoft Intune. Zabezpečená pracovní stanice se dají centrálně spravovat tak, aby vynutila zabezpečenou konfiguraci, včetně silného ověřování, standardních hodnot softwaru a hardwaru a omezeného logického a síťového přístupu. 

- [Pochopení pracovních stanic s privilegovaným přístupem](../active-directory/devices/concept-azure-managed-workstation.md)

- [Nasazení pracovní stanice s privilegovaným přístupem](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorování služby Azure Security Center:** Nelze použít

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

**Pokyny**: týmy zabezpečení pro zákazníky mohou kontrolovat protokoly aktivit a zobrazit tak aktivity prováděné poskytovateli služeb, kteří používají Azure Lighthouse. 

Pokud chce poskytovatel pro zabezpečení zkontrolovat prostředky delegovaného zákazníka, musí mít autorizaci bezpečnostního týmu zabudovanou roli čtenáře.

- [Jak zákazník může zkontrolovat aktivitu poskytovatele služeb](how-to/view-service-provider-activity.md)

- [Určení rolí při připojování zákazníka k Azure Lighthouse](how-to/onboard-customer.md#define-roles-and-permissions)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používání jenom schválených služeb Azure

**Doprovodné** materiály: pomocí Azure Policy můžete auditovat a omezovat, které služby můžou uživatelé zřídit ve vašem prostředí. Pomocí Azure Resource Graphu se můžete dotazovat na prostředky v rámci jejich předplatných a zjišťovat je. Pomocí Azure Monitoru můžete také vytvořit pravidla pro aktivaci upozornění při zjištění neschválené služby.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

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

*Další informace najdete v tématu [Azure Security Benchmark: protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: povolení detekce hrozeb pro prostředky Azure

**Doprovodné** materiály: prostřednictvím Azure Lighthouse můžete monitorovat prostředky Azure vašich zákazníků na potenciální hrozby a anomálie. Zaměřte se na získávání vysoce kvalitních výstrah, které omezí falešně pozitivní výsledky pro analytiky k řazení. Výstrahy se dají nacházet z dat protokolů, agentů nebo jiných dat.

Využijte Azure Security Center integrovanou funkci detekce hrozeb, která vychází z monitorování telemetrie služeb Azure a analýzy protokolů služby. Data se shromažďují pomocí Log Analytics agenta, který čte různé konfigurace a protokoly událostí související se zabezpečením ze systému a kopíruje data do vašeho pracovního prostoru pro účely analýzy. 

Kromě toho použijte Azure Sentinel k sestavování analytických pravidel, která navzájemují hrozby, které odpovídají konkrétním kritériím v prostředí zákazníka. Pravidla generují incidenty, když jsou kritéria shodná, takže můžete prozkoumat jednotlivé incidenty. Funkce Sentinel Azure může taky importovat analýzy hrozeb jiných výrobců, aby se zlepšila její schopnost detekce hrozeb. 

- [Ochrana před hrozbami v Azure Security Center](../security-center/azure-defender.md)

- [Referenční příručka pro Azure Security Center výstrahy zabezpečení](../security-center/alerts-reference.md)

- [Vytváření vlastních pravidel analýzy pro detekci hrozeb](../sentinel/tutorial-detect-threats-custom.md)

- [Správa pracovních prostorů Sentinel Azure ve velkém měřítku](how-to/manage-sentinel-workspaces.md)

**Monitorování služby Azure Security Center:** Nelze použít

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

- [Ochrana před hrozbami v Azure Security Center](../security-center/azure-defender.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Povolení protokolování pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit, které jsou automaticky k dispozici, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky Azure Lighthouse s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

S Azure Lighthouse můžete protokolovat pomocí Azure Monitor škálovatelným způsobem napříč klienty zákazníka, které spravujete. Vytvářejte Log Analytics pracovní prostory přímo v klientech zákazníka, aby zákaznická data zůstala ve svých klientech a nemuseli je exportovat. Díky tomu je možné centralizované monitorování všech prostředků nebo služeb, které Log Analytics podporuje, a poskytuje větší flexibilitu v tom, jaké typy dat sledujete.

Zákazníci, kteří mají delegované předplatné pro Azure Lighthouse, mohou zobrazit data protokolu aktivit Azure, aby viděli všechny akce, které provedli. To zákazníkům poskytuje přehled o operacích, které poskytovatelé služeb provádějí, spolu s operacemi prováděnými uživateli v rámci vlastního klienta Azure Active Directory (Azure AD) zákazníka.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Monitorování delegovaných prostředků ve velkém měřítku](how-to/monitor-at-scale.md)

- [Zobrazení aktivity poskytovatele služeb](how-to/view-service-provider-activity.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizace správy a analýz protokolu zabezpečení

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

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurace uchovávání úložiště protokolů

**Doprovodné** materiály: Azure Lighthouse aktuálně nevytváří žádné protokoly související se zabezpečením. Zákazníci, kteří chtějí zobrazit aktivitu poskytovatele služeb, mohou konfigurovat uchovávání protokolů podle dodržování předpisů, nařízení a obchodních požadavků. 

V Azure Monitor můžete nastavit dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte Azure Storage, Data Lake nebo Log Analytics účty pracovního prostoru pro dlouhodobé a archivační úložiště.

- [Změnit dobu uchovávání dat v Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center výstrahy a doporučení exportovat](../security-center/continuous-export.md) konfiguraci a zákazník nemůže nastavit žádné uchovávání protokolu.

- [Jak zákazník může zkontrolovat data protokolu aktivit pro poskytovatele služeb](how-to/view-service-provider-activity.md)

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

*Další informace najdete v tématu [Azure Security Benchmark: stav a správa ohrožení zabezpečení](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Zřízení zabezpečených konfigurací pro služby Azure 

**Pokyny**: Azure Lighthouse podporuje níže uvedené zásady pro konkrétní služby, které jsou k dispozici v Azure Security Center k auditování a prosazování konfigurací vašich prostředků Azure. Tato možnost se dá nakonfigurovat v Azure Security Center nebo Azure Policy iniciativ.

- Povolení správy ID tenantů prostřednictvím Azure Lighthouse

- Auditovat delegování oborů do spravovaného tenanta

Plány Azure můžete použít k automatizaci nasazení a konfigurace služeb a prostředí aplikací, včetně šablon Azure Resource Manager, řízení a zásad Azure RBAC a zásad v jediné definici podrobného plánu.

- [Zásady Azure Lighthouse](samples/policy-reference.md)

- [Kurz – vytváření a Správa zásad pro prosazování dodržování předpisů](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprint](../governance/blueprints/overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Udržování zabezpečených konfigurací pro služby Azure

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

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Provádění pravidelné simulace útoku

**Pokyny:** Podle potřeby provádějte testování průniku nebo aktivity červeného týmu na svých prostředcích Azure a zajistěte nápravu všech kritických zjištění v oblasti zabezpečení.
Postupujte podle pravidel zapojení testování průniku cloudových služeb Microsoftu (Microsoft Cloud Penetration Testing), abyste měli jistotu, že testy průniku neporušují zásady Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem.

- [Testování průniku v Azure](../security/fundamentals/pen-testing.md)

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

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

-   Požadovaná strategie řízení přístupu v souladu s klasifikací dat organizace

-   Použití funkcí pro ochranu dat nativních pro Azure i třetích stran

-   Požadavky na šifrování dat pro případy použití v přenosech a u neaktivních uložených dat

-   Vhodné kryptografické standardy

Další informace najdete v následujících referenčních materiálech:
- [Doporučení architektury zabezpečení Azure – úložiště, data a šifrování](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Základy zabezpečení Azure – zabezpečení, šifrování a úložiště dat Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – osvědčené postupy zabezpečení a šifrování dat Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – správa prostředků](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure Security Benchmark – ochrana dat](/azure/security/benchmarks/security-controls-v2-data-protection)

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

- [Azure Security Benchmark – správa stavu a ohrožení zabezpečení](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

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

-   Strategie pro Internet Edge a příchozí a odchozí/Azure/Security/benchmarks/Security-Controls-v2-Logging-Threat-Detection
-   Strategie vzájemného propojení hybridního cloudu a místního připojení

-   Aktuální artefakty zabezpečení sítě (například síťové diagramy, referenční síťová architektura)

Další informace najdete v následujících referenčních materiálech:
- [Osvědčený postup zabezpečení Azure 11 – Architektura: jednotná strategie zabezpečení](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – zabezpečení sítě](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

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

- [Azure Security Benchmark – správa identit](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure Security Benchmark – privilegovaný přístup](/azure/security/benchmarks/security-controls-v2-privileged-access)

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

- [Azure Security Benchmark – protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark – reakce na incidenty](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Osvědčený postup zabezpečení Azure 4 – Proces: aktualizace procesů reakcí na incidenty pro cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Architektura přechodu na Azure a průvodce rozhodováním o protokolování a vytváření sestav](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Škálování, správa a monitorování Azure na podnikové úrovni](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](../security/benchmarks/overview.md).
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)