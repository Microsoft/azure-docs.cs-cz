---
title: Standardní hodnoty zabezpečení Azure pro službu Cost Management
description: Standardní hodnoty zabezpečení služby Cost Management poskytují procesní pokyny a zdroje informací k implementaci doporučení k zabezpečení uvedené ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 40baab2a4c577ee96bcebeb4880235a81810b2b4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660317"
---
# <a name="azure-security-baseline-for-cost-management"></a>Standardní hodnoty zabezpečení Azure pro službu Cost Management

Tyto standardní hodnoty zabezpečení uplatňují na službu Azure Cost Management pokyny ze [srovnávacího testu zabezpečení Azure verze 2.0](../security/benchmarks/overview.md). Srovnávací test zabezpečení Azure nabízí doporučení k zabezpečení cloudových řešení v Azure. Obsah je rozdělený do kategorií podle **kontrolních mechanismů zabezpečení** definovaných srovnávacím testem zabezpečení Azure a souvisejících pokynů vztahujících se ke službě Cost Management. **Kontrolní mechanismy**, které se nevztahují ke službě Cost Management, se vyloučily.

Kompletní mapování služby Cost Management na srovnávací test zabezpečení Azure najdete v [souboru s úplným mapováním standardních hodnot zabezpečení služby Cost Management](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Správa identit

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Správa identit](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizace Azure Active Directory jako centrálního systému identit a ověřování

**Pokyny:** Služba Azure Cost Management je integrovaná s Azure Active Directory (Azure AD), což je výchozí služba Azure pro správu identit a přístupu. S využitím Azure AD byste měli standardizovat řízení správy identit a přístupu v rámci vaší organizace pro:

- cloudové prostředky Microsoftu, jako jsou Azure Portal, Azure Storage, virtuální počítače Azure (s Linuxem a Windows), Azure Key Vault a aplikace PaaS a SaaS

- prostředky vaší organizace, jako jsou aplikace v Azure nebo prostředky vaší podnikové sítě

Zabezpečení Azure AD by v rámci postupů vaší organizace v oblasti cloudového zabezpečení mělo mít vysokou prioritu. Azure AD nabízí skóre zabezpečení identit, které pomáhá vyhodnocovat stav zabezpečení identit s ohledem na doporučené osvědčené postupy Microsoftu. S využitím tohoto skóre můžete změřit, nakolik vaše konfigurace odpovídá doporučeným osvědčeným postupům, a zlepšit stav zabezpečení.

Poznámka: Azure AD podporuje externí zprostředkovatele identit, kteří umožňují uživatelům bez účtu Microsoft přihlašovat se ke svým aplikacím a prostředkům s využitím své externí identity.

- [Architektura tenantů v Azure AD](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Vytvoření a konfigurace instance Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Definování tenantů Azure AD](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [Používání externích zprostředkovatelů identit pro aplikaci](/azure/active-directory/b2b/identity-providers)

- [Co je skóre zabezpečení identit v Azure AD?](../active-directory/fundamentals/identity-secure-score.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Používání jednotného přihlašování Azure AD pro přístup k aplikacím

**Pokyny:** Azure Cost Management s využitím Azure Active Directory zajišťuje správu identit a přístupu pro prostředky Azure, cloudové aplikace a místní aplikace. To zahrnuje podnikové identity, jako jsou zaměstnanci, i externí identity, jako jsou partneři, prodejci a dodavatelé. Díky tomu je možné ke správě a zabezpečení přístupu k datům a prostředkům vaší organizace v místním prostředí a v cloudu používat jednotné přihlašování. Připojením všech uživatelů, aplikací a zařízení k Azure AD zajistíte bezproblémový a zabezpečený přístup a získáte větší přehled a kontrolu.

- [Principy jednotného přihlašování k aplikacím s využitím Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Používání kontrolních mechanismů silného ověřování pro veškerý přístup na základě Azure Active Directory

**Pokyny:** Služba Azure Cost Management je integrovaná se službou Azure AD, která podporuje kontrolní mechanismy silného ověřování prostřednictvím vícefaktorového ověřování (MFA) a metod silného ověřování bez hesla.

- Vícefaktorové ověřování – Povolte vícefaktorové ověřování Azure AD a při jeho nastavování postupujte podle osvědčených postupů v doporučeních služby Azure Security Center ke správě identit a přístupu. V závislosti na podmínkách přihlašování a rizikových faktorech je možné vícefaktorové ověřování vynucovat u všech uživatelů, u vybraných uživatelů nebo na úrovni jednotlivých uživatelů.

- Ověřování bez hesla – K dispozici jsou tři možnosti ověřování bez hesla: Windows Hello pro firmy, aplikace Microsoft Authenticator a místní metody ověřování, jako jsou čipové karty.

Zajistěte, aby se pro správce a privilegované uživatele používala nejvyšší úroveň konkrétní metody silného ověřování, a pro ostatní uživatele zaveďte odpovídající zásady silného ověřování.

- [Povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Úvod do možností ověřování bez hesla pro Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Výchozí zásady hesel Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminace špatných hesel s využitím ochrany hesel Azure AD](../active-directory/authentication/concept-password-ban-bad.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorování anomálií u účtů a upozorňování na ně

**Pokyny:** Služba Azure Cost Management je integrovaná se službou Azure Active Directory, která nabízí následující zdroje dat:

- Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů.

- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.

- Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Tyto zdroje dat je možné integrovat se službami Azure Monitor a Azure Sentinel nebo se systémy SIEM třetích stran.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako jsou nadměrný počet neúspěšných pokusů o ověření nebo zastaralé účty v předplatném.

Rozšířená ochrana před internetovými útoky (ATP) v Azure je řešení zabezpečení, které dokáže na základě signálů Active Directory identifikovat, detekovat a vyšetřovat pokročilé hrozby, ohrožené identity a škodlivé činnosti vnitřních účastníků.

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Zobrazení rizikových přihlášení Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../security-center/security-center-identity-access.md) 

- [Upozornění v modulu ochrany na základě analýzy hrozeb služby Azure Security Center](../security-center/alerts-reference.md) 

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="privileged-access"></a>Privilegovaný přístup

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Privilegovaný přístup](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Ochrana a omezení vysoce privilegovaných uživatelů

**Pokyny:** Cost Management pro smlouvy Enterprise (EA) má následující vysoce privilegovaný účet.

- Podnikový správce

Doporučujeme pravidelně kontrolovat uživatele přiřazené k rolím v rámci vaší smlouvy Enterprise (EA).

Obecně také doporučujeme omezit počet vysoce privilegovaných účtů nebo rolí a chránit tyto účty na zvýšené úrovni, protože uživatelé s tímto oprávněním můžou přímo nebo nepřímo číst a upravovat všechny prostředky ve vašem prostředí Azure.

S využitím služby Azure AD Privileged Identity Management (PIM) můžete povolit privilegovaný přístup podle potřeby (JIT) k prostředkům Azure a Azure AD. JIT uděluje dočasná oprávnění k provádění privilegovaných úloh, pouze když je uživatelé potřebují. PIM může také generovat upozornění zabezpečení v případě podezřelých nebo nebezpečných aktivit ve vaší organizaci Azure AD.

- [Správa rolí Azure Enterprise](manage/understand-ea-roles.md) 

- [Oprávnění role správce v Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [Používání upozornění zabezpečení služby Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Pravidelné kontroly a sjednocování uživatelského přístupu

**Pokyny:** Azure Cost Management spoléhá na odpovídající přístup k zobrazení a správě informací o nákladech organizace. Přístup se řídí pomocí řízení přístupu na základě role Azure (Azure RBAC) na úrovni předplatného a prostřednictvím rolí pro správu se smlouvou Enterprise (EA) nebo Smlouvou se zákazníkem Microsoftu (MCA) v případě rozsahů fakturace. Pravidelně auditujte a kontrolujte udělený přístup, abyste zajistili, že uživatelé nebo skupiny mají požadovaný přístup.

- [Správa přístupu k fakturačním údajům v Azure](manage/manage-billing-access.md)

- [Přiřazení přístupu k datům Cost Managementu](costs/assign-access-acm-data.md)

- [Správa rolí Azure Enterprise](manage/understand-ea-roles.md)

- [Vysvětlení rolí pro správu smluv se zákazníky Microsoftu v Azure](manage/understand-mca-roles.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Využívání správy jen v potřebném rozsahu (princip nejnižší úrovně nutných oprávnění) 

**Pokyny:** Azure Cost Management se za účelem správy prostředků (např. rozpočtů, uložených sestav atd.) integruje s řízením přístupu na základě role (RBAC) Azure. Azure RBAC umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, skupinám nebo instančním objektům. Pro určité prostředky existují předdefinované role, které je možné inventarizovat nebo dotazovat pomocí nástrojů, jako jsou Azure CLI, Azure PowerShell nebo Azure Portal. Oprávnění k prostředkům přiřazená prostřednictvím Azure RBAC by vždy měla být omezená pouze na to, co konkrétní role vyžadují. Tento přístup doplňuje přístup založený na přístupu podle potřeby (JIT) ve službě Azure AD Privileged Identity Management (PIM) a měl by se pravidelně kontrolovat.

Využijte k přidělování oprávnění předdefinované role a vlastní role vytvářejte pouze v případě potřeby.

Azure Cost Management nabízí předdefinované role čtenářů a přispěvatelů.

- [Čtenář služby Azure Cost Management](../role-based-access-control/built-in-roles.md#cost-management-reader)

- [Přispěvatel služby Azure Cost Management](../role-based-access-control/built-in-roles.md#cost-management-contributor)

[Co je řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Konfigurace řízení přístupu na základě role Azure](../role-based-access-control/role-assignments-portal.md) 

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Ochrana dat](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Zjišťování, klasifikace a označování citlivých dat

**Pokyny:** Doporučujeme zjišťovat, klasifikovat a označovat citlivá data, abyste mohli navrhnout odpovídající kontrolní mechanismy, které zajistí zabezpečené ukládání, zpracování a odesílání citlivých informací technologickými systémy organizace.

V případě citlivých informací v rámci dokumentů Office v Azure, místním prostředí, Office 365 a dalších umístěních můžete využít službu Azure Information Protection (a přidružený nástroj pro zjišťování).

Služba Azure SQL Information Protection vám může pomoct s klasifikací a označováním informací uložených v databázích Azure SQL.

- [Označování citlivých informací s využitím služby Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Implementace zjišťování dat Azure SQL](/azure/sql-database/sql-database-data-discovery-and-classification)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Ochrana citlivých dat

**Pokyny:** Doporučujeme chránit citlivá data omezením přístupu s využitím řízení přístupu na základě role Azure (Azure RBAC), řízení síťového přístupu a specifických kontrolních mechanismů ve službách Azure (jako je šifrování v databázích SQL a dalších databázích).

Aby se zajistilo konzistentní řízení přístupu, všechny typy řízení přístupu by měly být v souladu s vaší podnikovou strategií segmentace. Podniková strategie segmentace by měla vycházet také z umístění citlivých nebo důležitých obchodních dat a systémů.

Pro základní platformu spravovanou Microsoftem platí, že Microsoft považuje veškerý obsah zákazníků za citlivý a zajišťuje ochranu před ztrátou a vystavením zákaznických dat. V zájmu zajištění zabezpečení zákaznických dat v rámci Azure Microsoft implementovat určité výchozí kontrolní mechanismy a funkce ochrany dat.

- [Přiřazení přístupu k datům Cost Managementu](costs/assign-access-acm-data.md)

- [Řízení přístupu na základě role (RBAC) Azure](../role-based-access-control/overview.md) 

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Monitorování neautorizovaného přenosu citlivých dat

**Pokyny:** Monitorujte neautorizovaný přenos dat do umístění mimo dohled a kontrolu podniku. To obvykle zahrnuje monitorování neobvyklých aktivit (velké nebo neobvyklé přenosy), které můžou značit neautorizovanou exfiltraci dat.

Rozšířená ochrana před internetovými útoky (ATP) pro službu Azure Storage a ochrana ATP pro Azure SQL dokáží upozorňovat na neobvyklé přenosy informací, které můžou značit neautorizované přenosy citlivých informací.

Azure Information Protection (AIP) nabízí funkce monitorování důvěrných a označených informací.

Pokud to v rámci ochrany před únikem informací vyžaduje dodržování předpisů, můžete k vynucování kontrolních mechanismů detekce a prevence za účelem zajištění ochrany před exfiltrací dat využít řešení ochrany před únikem informací na hostiteli.

- [Povolení ochrany ATP pro Azure SQL](../azure-sql/database/threat-detection-overview.md) 

- [Povolení ochrany ATP pro službu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Šifrování neaktivních uložených citlivých dat

**Pokyny:** Funkce Exporty ve službě Azure Cost Management doplňuje řízení přístupu a podporuje šifrování neaktivních uložených dat služby Azure Storage, které zajišťuje ochranu před vzdálenými útoky (jako je přístup k základnímu úložišti) díky šifrování s využitím klíčů spravovaných Microsoftem. Toto výchozí nastavení pomáhá zajistit, aby útočníci nemohli snadno číst ani upravovat data.

Další informace najdete tady:

- [Šifrování služby Azure Storage pro neaktivní uložená data](../storage/common/storage-service-encryption.md)

- [Principy šifrování neaktivních uložených dat v Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

**Monitorování služby Azure Security Center:** Neuvedeno

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

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Zajištění zabezpečení správy životního cyklu prostředků

**Pokyny:** Vytvořte nebo aktualizujte zásady zabezpečení, které v rámci procesů správy životního cyklu prostředků řeší úpravy s potenciálně velkým dopadem. Mezi tyto úpravy patří změny zprostředkovatelů identit a přístupu, citlivosti dat, konfigurace sítě nebo přiřazení oprávnění správce.

Pokud už je nepotřebujete, odeberte prostředky Azure.

- [Odstranění prostředku a skupiny prostředků Azure](../azure-resource-manager/management/delete-resource-group.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="logging-and-threat-detection"></a>Protokolování a detekce hrozeb

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Protokolování a detekce hrozeb](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Povolení detekce hrozeb pro správu identit a přístupu v Azure

**Pokyny:** Azure AD nabízí následující protokoly uživatelů, které je možné zobrazit v sestavách Azure AD nebo integrovat se službami Azure Monitor a Azure Sentinel nebo dalšími řešeními SIEM nebo nástroji pro monitorování pro účely složitějšího monitorování nebo analytické případy použití:

- Přihlášení – Sestava přihlášení poskytuje informace o využití spravovaných aplikací a aktivitách přihlašování uživatelů.

- Protokoly auditu – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidání nebo odebrání uživatelů, aplikací, skupin, rolí nebo zásad.

- Riziková přihlášení – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

- Uživatelé označení příznakem rizika – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený.

Azure Security Center může také upozorňovat na určité podezřelé aktivity, jako jsou nadměrný počet neúspěšných pokusů o ověření nebo zastaralé účty v předplatném. Kromě základního monitorování hygieny zabezpečení může modul ochrany před hrozbami služby Azure Security Center také shromažďovat podrobnější upozornění zabezpečení z jednotlivých výpočetních prostředků Azure (virtuální počítače, kontejnery, App Service), datových prostředků (databáze SQL a úložiště) a vrstev služeb Azure. Tato funkce umožňuje získat přehled o anomáliích u účtů v rámci jednotlivých prostředků.

Doporučujeme také pravidelně kontrolovat uživatele přiřazené k rolím v rámci vaší smlouvy Enterprise (EA). 

- [Správa rolí Azure Enterprise](manage/understand-ea-roles.md)

- [Sestavy aktivit auditu v Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Povolení ochrany identit Azure](../active-directory/identity-protection/overview-identity-protection.md)

- [Ochrana před hrozbami v Azure Security Center](/azure/security-center/threat-protection)

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

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: Správa stavu a ohrožení zabezpečení](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Rychlá a automatická náprava ohrožení zabezpečení softwaru

**Pokyny:** Zajistěte rychlé nasazování aktualizací softwaru, které napravují ohrožení zabezpečení softwaru v operačních systémech a aplikacích.

K určování priority využijte běžný program vyhodnocování rizika (například Common Vulnerability Scoring System) nebo výchozí hodnocení rizika z nástroje pro zjišťování třetí strany a přizpůsobte je vašemu prostředí s využitím kontextových informací o tom, které aplikace představují vysoké bezpečnostní riziko a které vyžadují vysokou dostupnost.

**Monitorování služby Azure Security Center:** Neuvedeno

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

-   Využívání funkcí ochrany dat nativních pro Azure a funkcí ochrany dat třetích stran

-   Požadavky na šifrování přenášených a neaktivních uložených dat

-   Příslušné kryptografické standardy

Další informace najdete na následujících odkazech:
- [Doporučení pro architekturu zabezpečení Azure – Úložiště, data a šifrování](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Základy zabezpečení Azure – Zabezpečení dat v Azure, šifrování a úložiště](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – Osvědčené postupy šifrování a zabezpečení dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Srovnávací test zabezpečení Azure – Správa prostředků](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Srovnávací test zabezpečení Azure – Ochrana dat](/azure/security/benchmarks/security-benchmark-v2-data-protection)

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

- [Srovnávací test zabezpečení Azure – Správa stavu a ohrožení zabezpečení](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

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

-   Strategie pro hraniční, příchozí a výchozí internetový přenos dat

-   Strategie propojení hybridního cloudu a místního prostředí

-   Aktuální artefakty zabezpečení sítě (např. síťové diagramy, referenční síťová architektura)

Další informace najdete na následujících odkazech:
- [Osvědčený postup zabezpečení Azure 11 – Architektura: Jednotná bezpečnostní strategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Srovnávací test zabezpečení Azure – Zabezpečení sítě](/azure/security/benchmarks/security-benchmark-v2-network-security)

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

- [Srovnávací test zabezpečení Azure – Správa identit](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Srovnávací test zabezpečení Azure – Privilegovaný přístup](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Osvědčený postup zabezpečení Azure 11 – Architektura: Jednotná bezpečnostní strategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Přehled zabezpečení správy identit Azure](../security/fundamentals/identity-management-overview.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definování strategie protokolování a reakce na hrozby

**Pokyny:** Nastavte strategii protokolování a reakce na hrozby, abyste mohli rychle detekovat a zmírňovat hrozby při současném splnění požadavků na dodržování předpisů. Jako prioritu si stanovte poskytování vysoce kvalitních upozornění a bezproblémových prostředí analytikům, aby se mohli soustředit na hrozby, a ne na integraci a ruční kroky. 

Tato strategie by měla zahrnovat zdokumentované pokyny, zásady a standardy pro následující aspekty: 

-   Role a odpovědnost organizace s ohledem na operace zabezpečení (SecOps) 

-   Jasně definovaný proces reakce na incidenty, který je v souladu s NIST nebo jiným průmyslovým rámcem 

-   Zaznamenávání a uchovávání protokolů pro potřeby detekce hrozeb, reakce na incidenty a zajištění dodržování předpisů

-   Centralizovaný přehled a korelace informací o hrozbách s využitím řešení SIEM, nativních funkcí Azure a dalších zdrojů 

-   Plán komunikace a oznamování pro vaše zákazníky, dodavatele a zúčastněné strany z řad veřejnosti

-   Používání nativních platforem Azure a platforem třetích stran při řešení incidentů, například pro účely protokolování a detekce hrozeb, zmírňování a likvidace útoků a forenzní účely

-   Procesy pro řešení incidentů a aktivit po incidentech, jako je uchovávání poznatků, zkušeností a důkazů

Další informace najdete na následujících odkazech:

- [Srovnávací test zabezpečení Azure – Protokolování a detekce hrozeb](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Srovnávací test zabezpečení Azure – Reakce na incidenty](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Osvědčený postup zabezpečení Azure 4 – Procesy: Aktualizace procesů reakce na incidenty pro cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Cloud Adoption Framework – Průvodce rozhodováním ohledně protokolování a generování sestav](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Správa a monitorování na podnikové úrovni v Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

## <a name="next-steps"></a>Další kroky

- Projděte si [přehled srovnávacího testu zabezpečení Azure v2](/azure/security/benchmarks/overview).
- Přečtěte si další informace o [standardních hodnotách zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview).
