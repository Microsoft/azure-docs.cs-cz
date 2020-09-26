---
title: Azure Security benchmark v2 – privilegovaný přístup
description: Privilegovaný přístup k Azure Security test v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b5315b4d2ec8b757f7fa1075a438419679f5e798
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317969"
---
# <a name="security-control-v2-privileged-access"></a>Řízení zabezpečení v2: privilegovaný přístup

Privilegovaný přístup pokrývá ovládací prvky pro ochranu privilegovaného přístupu k vašemu tenantovi a prostředkům Azure. To zahrnuje řadu ovládacích prvků pro ochranu modelu správy, účtů pro správu a pracovních stanic s privilegovaným přístupem proti úmyslnému a neúmyslnému riziku.

## <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: ochrana a omezení vysoce privilegovaných uživatelů

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC – 2 |

Omezte počet vysoce privilegovaných uživatelských účtů a chraňte tyto účty na vyšší úrovni. Nejdůležitější předdefinované role v Azure AD jsou globální správce a správce privilegovaných rolí, protože uživatelé přiřazení k těmto dvěma rolím můžou delegovat role správce. Díky těmto oprávněním můžou uživatelé přímo nebo nepřímo číst a upravovat všechny prostředky v prostředí Azure:

- Globální správce/správce společnosti: uživatelé s touto rolí mají přístup ke všem funkcím pro správu ve službě Azure AD a službám, které používají identity Azure AD.

- Správce privilegovaných rolí: uživatelé s touto rolí můžou spravovat přiřazení rolí v Azure AD i v Azure AD Privileged Identity Management (PIM). Tato role navíc umožňuje správu všech aspektů PIM a administrativních jednotek.

Poznámka: Pokud používáte vlastní role s přiřazenými konkrétními privilegovanými oprávněními, můžete mít i jiné kritické role, které je potřeba řídit. A můžete také chtít použít podobné ovládací prvky pro účet správce důležitých obchodních prostředků.  

Přístup k prostředkům Azure a Azure AD pomocí Azure AD Privileged Identity Management (PIM) můžete povolit pomocí privilegovaného přístupu JIT (just-in-time). Kompilátor JIT uděluje dočasná oprávnění k provádění privilegovaných úloh pouze v případě, že ji uživatelé potřebují. PIM může také generovat výstrahy zabezpečení v případě podezřelé nebo nebezpečné aktivity ve vaší organizaci Azure AD.

- [Oprávnění role správce v Azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Použití výstrah zabezpečení Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](../../active-directory/users-groups-roles/directory-admin-roles-secure.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Správa identit a klíčů](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Operace zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: omezení přístupu pro správu na systémy kritické pro podnikání

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA – 2 | 13,2, 2,10 | AC-2, SC-3, SC-7 |

Omezte přístup k podnikovým systémům tím, že omezíte, kterým účtům se udělí privilegovaný přístup k předplatným a skupinám pro správu, ve kterých se nacházejí. Ujistěte se, že také omezíte přístup k systémům pro správu, identitu a zabezpečení, které mají přístup pro správu k důležitým prostředkům vaší firmy, jako jsou Doména služby Active Directory Controllers (DCs), nástroje zabezpečení a nástroje pro správu systému s agenty nainstalovanými v důležitých podnikových systémech. Útočníci, kteří tyto systémy pro správu a zabezpečení ohrožují, si je můžou hned weaponize, aby vyzrazeni důležité obchodní prostředky. 

Všechny typy ovládacích prvků přístupu by se měly zarovnávat na strategii segmentace vaší organizace, aby se zajistilo jednotné řízení přístupu. 

Ujistěte se, že přiřadíte samostatné privilegované účty, které se liší od standardních uživatelských účtů používaných pro úlohy e-mailu, procházení a produktivity.

- [Komponenty a referenční model Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Přístup ke skupině pro správu](../../governance/management-groups/overview.md#management-group-access)

- [Správci předplatného Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Správa identit a klíčů](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Architektura zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

## <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: pravidelně kontrolovat a sjednotit přístup uživatelů

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA-3 | 4,1, 16,9, 16,10 | AC – 2 |

Pravidelně kontrolujte uživatelské účty a přiřazení přístupu, abyste měli jistotu, že účty a jejich úroveň přístupu jsou platné. Kontroly přístupu Azure AD můžete použít ke kontrole členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí. Vytváření sestav Azure AD může poskytovat protokoly, které vám pomůžou zjistit zastaralé účty. Pomocí Azure AD Privileged Identity Management můžete také vytvořit pracovní postup sestavy kontroly přístupu, který usnadňuje proces kontroly.
Kromě toho je možné Azure Privileged Identity Management nakonfigurovat tak, aby výstrahu při vytváření nadměrného počtu účtů správců a identifikaci účtů správce, které jsou zastaralé nebo nesprávně nakonfigurované. 

Poznámka: některé služby Azure podporují místní uživatele a role, které nejsou spravované prostřednictvím služby Azure AD. Tyto uživatele je nutné spravovat samostatně.

- [Vytvoření kontroly přístupu pro role prostředků Azure v Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Jak používat kontroly identity a přístupu v Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Správa identit a klíčů](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: nastavení nouzového přístupu v Azure AD

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA-4 | 16 | AC-2, CP-2 |

Pokud se chcete vyhnout náhodnému uzamčení vaší organizace Azure AD, nastavte účet pro nouzový přístup pro přístup, když nebudete moct použít normální účty pro správu. Účty pro nouzový přístup jsou obvykle vysoce privilegované a neměly by být přiřazeny konkrétním jednotlivcům. Účty pro nouzový přístup jsou omezené na nouzové nebo "oddělovací sklo", kde se nedají použít běžné účty pro správu.
Měli byste zajistit, aby pověření (například heslo, certifikát nebo čipová karta) pro účty pro nouzový přístup byla zabezpečená a známá jenom jednotlivcům, kteří jsou oprávněni je používat jenom v nouzi.

- [Správa účtů pro nouzový přístup v Azure AD](../../active-directory/users-groups-roles/directory-emergency-access.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Správa identit a klíčů](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Operace zabezpečení (SecOps)](//azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizace správy nároků

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA-5 | 16 | AC-2, AC-5, PM-10 |

Pomocí funkcí správy opravňujících ke službě Azure AD můžete automatizovat pracovní postupy pro žádosti o přístup, včetně přiřazení přístupu, revizí a vypršení platnosti. Podporuje se také duální nebo vícenásobné schválení.
- [Co jsou kontroly přístupu Azure AD](../../active-directory/governance/access-reviews-overview.md) 

- [Co je Správa nároků Azure AD](../../active-directory/governance/entitlement-management-overview.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Správa identit a klíčů](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-6-use-privileged-access-workstations"></a>PA-6: použití pracovních stanic s privilegovaným přístupem

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA – 6 | 4,6, 11,6, 12,12 | AC-2, SC-3, SC-7 |

Zabezpečené, izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a provozovatelé důležitých služeb. Pro úlohy správy Používejte vysoce zabezpečené uživatelské pracovní stanice nebo Azure bastionu. Pomocí Azure Active Directory, programu Microsoft Defender Advanced Threat Protection (ATP) a/nebo Microsoft Intune nasaďte zabezpečenou a spravovanou pracovní stanici uživatelů pro úlohy správy. Zabezpečená pracovní stanice se dají centrálně spravovat tak, aby vynutila zabezpečenou konfiguraci, včetně silného ověřování, standardních hodnot softwaru a hardwaru a omezeného logického a síťového přístupu. 

- [Pochopení pracovních stanic s privilegovaným přístupem](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Nasazení privilegované pracovní stanice přístupu](../../active-directory/devices/howto-azure-managed-workstation.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Operace zabezpečení (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Správa identit a klíčů](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: sledování pouze dostatečné správy (princip nejnižší úrovně oprávnění)

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA-7 | 14,6 | AC-2, AC-3, SC-3 |

Řízení přístupu na základě role Azure (Azure RBAC) umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, objektům služby skupin a spravovaným identitám. Pro určité prostředky jsou předem definované předdefinované role a tyto role můžou být v inventáři nebo prostřednictvím nástrojů, jako je Azure CLI, Azure PowerShell a Azure Portal. Oprávnění, která přiřadíte k prostředkům prostřednictvím Azure RBAC, by měla být vždy omezená na to, co role vyžadují. Omezená oprávnění přidávají přístup k funkcím PIM (just in time) Azure AD Privileged Identity Management (PIM) a tato oprávnění by se měla pravidelně kontrolovat.
Pomocí integrovaných rolí můžete přidělit oprávnění a v případě potřeby vytvořit pouze vlastní roli. 

- [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md)

- [Jak nakonfigurovat službu Azure RBAC](../../role-based-access-control/role-assignments-portal.md)

- [Jak používat kontroly identity a přístupu v Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Správa identit a klíčů](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: výběr procesu schválení pro podporu Microsoftu 

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA-8 | 16 | AC-2, AC-3, AC-4 |

Ve scénářích podpory, kde Microsoft potřebuje získat přístup k zákaznickým datům, Customer Lockbox poskytuje možnost explicitně kontrolovat a schvalovat nebo odmítat žádosti o přístup k datům zákazníků.

- [Pochopení Customer Lockbox](../fundamentals/customer-lockbox-overview.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Správa identit a klíčů](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

