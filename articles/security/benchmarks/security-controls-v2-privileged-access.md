---
title: Azure Security benchmark v2 – privilegovaný přístup
description: Privilegovaný přístup k Azure Security test v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8727cbd07fad1960f4bdb33742729b6bda3a369e
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059232"
---
# <a name="security-control-privileged-access"></a>Řízení zabezpečení: privilegovaný přístup

Privilegovaný přístup pokrývá ovládací prvky pro ochranu privilegovaného přístupu k vašemu tenantovi a prostředkům Azure. To zahrnuje řadu ovládacích prvků pro ochranu modelu správy, účtů pro správu a pracovních stanic s privilegovaným přístupem proti úmyslnému a neúmyslnému riziku.

## <a name="pa-1-protect-and-limit-the-global-administrators"></a>PA-1: ochrana a omezení globálních správců

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC – 2 |

Uživatelé, kteří mají přiřazenou roli globálního správce, můžou číst a upravovat všechna nastavení správy ve vaší organizaci Azure AD. Omezte počet účtů globálních správců Azure na více než dvě pro každé předplatné. Nejdůležitější předdefinované role v Azure AD jsou globální správce a správce privilegovaných rolí, protože uživatelé přiřazení k těmto dvěma rolím můžou delegovat role správce:
- Globální správce/správce společnosti: uživatelé s touto rolí mají přístup ke všem funkcím pro správu ve službě Azure AD a službám, které používají identity Azure AD.

- Správce privilegovaných rolí: uživatelé s touto rolí můžou spravovat přiřazení rolí v Azure AD i v Azure AD Privileged Identity Management (PIM). Tato role navíc umožňuje správu všech aspektů PIM a administrativních jednotek.

Poznámka: Pokud používáte vlastní role s přiřazenými konkrétními privilegovanými oprávněními, můžete mít i jiné kritické role, které je potřeba řídit. A můžete také chtít použít podobné ovládací prvky pro účet správce důležitých obchodních prostředků.  

Přístup k prostředkům Azure a Azure AD pomocí Azure AD Privileged Identity Management (PIM) můžete povolit pomocí privilegovaného přístupu JIT (just-in-time). Kompilátor JIT uděluje dočasná oprávnění k provádění privilegovaných úloh pouze v případě, že ji uživatelé potřebují. PIM může také generovat výstrahy zabezpečení v případě podezřelé nebo nebezpečné aktivity ve vaší organizaci Azure AD.

Poznámka: Kromě ověřování Azure AD se u některých služeb Azure, jako je Azure SQL, podporuje i ověřování v místním prostředí. Tento typ ověřování pomocí místního uživatele se nespravuje přes Azure AD. Tyto uživatele budete muset spravovat samostatně.

- [Oprávnění role správce v Azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Vlastní role Azure](../../role-based-access-control/custom-roles.md)

- [Použití výstrah zabezpečení Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Monitorování identity a přístupu pomocí Azure Security Center](../../security-center/security-center-identity-access.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Identita a klíče](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="pa-2-review-and-reconcile-user-access-regularly"></a>PA-2: pravidelná kontrola a sjednocení přístupu uživatele

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA – 2 | 4,1, 16,9, 16,10 | AC – 2 |

Pravidelně kontrolujte uživatelské účty a přístup k oprávněním, aby bylo zajištěno, že uživatelské účty a jejich přístup jsou platné. 

K řízení členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí použijte kontroly přístupu a identit Azure AD. Vytváření sestav Azure AD může poskytovat protokoly, které vám pomůžou zjistit zastaralé účty. K usnadnění procesu kontroly můžete použít také Azure AD Privileged Identity Management k vytvoření pracovního postupu sestavy kontroly přístupu.

Pro uživatele s právy pro správu na úrovni služby a zatížení Azure by se měla provádět častější kontrola uživatelů a přístupu. Výstrahy zabezpečení Azure Privileged Identity Management můžete použít také k detekci, kdy jsou vytvořeny účty správců, a k vyhledání účtů správce, které jsou zastaralé nebo nesprávně nakonfigurované. 

Poznámka: některé služby Azure, jako je Azure SQL, podporují místní uživatele, kteří nejsou spravováni přes Azure AD. Tyto uživatele budete muset spravovat samostatně.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Monitorování identity a přístupu pomocí Azure Security Center](../../security-center/security-center-identity-access.md)

- [Použití výstrah zabezpečení Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Zarovnání odpovědností správy napříč týmy](/azure/cloud-adoption-framework/organize/raci-alignment) 

- [Pochopení sestav Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak používat kontroly identity a přístupu v Azure AD](../../active-directory/governance/access-reviews-overview.md)

- [Privileged Identity Management – kontrola přístupu k rolím Azure AD](../../active-directory/privileged-identity-management/pim-how-to-start-security-review.md)

- [Azure Security Center – monitorování identity a přístupu](../../security-center/security-center-identity-access.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Identita a klíče](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-3-set-up-an-emergency-access-account-in-azure-ad"></a>PA-3: nastavení účtu pro nouzový přístup v Azure AD

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA-3 | 12,3 | AC – 2 |

Pokud se chcete vyhnout náhodnému uzamčení vaší organizace Azure AD, nastavte účet pro nouzový přístup pro přístup, když nebudete moct použít normální účty pro správu. Účty pro nouzový přístup jsou obvykle vysoce privilegované a neměly by být přiřazeny konkrétním jednotlivcům. Účty pro nouzový přístup jsou omezené na nouzové nebo "oddělovací sklo", kde se nedají použít běžné účty pro správu.

Měli byste zajistit, aby pověření (například heslo, certifikát nebo čipová karta) pro účty pro nouzový přístup byla zabezpečená a známá jenom jednotlivcům, kteří jsou oprávněni je používat jenom v nouzi.

- [Správa účtů pro nouzový přístup v Azure AD](../../active-directory/users-groups-roles/directory-emergency-access.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Identita a klíče](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Středisko zabezpečení cloudu (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-4-automate-azure-identity-and-access-request-workflow"></a>PA-4: automatizace pracovního postupu Azure identity a žádosti o přístup

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA-4 | – | AC-2, AC-5, PM-10 |

Pomocí funkcí správy opravňujících k Azure AD můžete automatizovat pracovní postupy pro žádosti o přístup k Azure, včetně přiřazení přístupu, kontrol a vypršení platnosti. Podporuje se také duální nebo vícenásobné schválení.  

- [Co je Správa nároků Azure AD](../../active-directory/governance/entitlement-management-overview.md)

- [Jak nastavit žádost o přístup a proces schvalování](../../active-directory/governance/entitlement-management-access-package-request-policy.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Identita a klíče](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-5-use-highly-secured-machines-for-administrative-tasks"></a>PA-5: použití vysoce zabezpečených počítačů pro úlohy správy

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA-5 | 4,6, 11,6, 12,12 | AC-2, SC-7 |

Zabezpečené, izolované pracovní stanice jsou kriticky důležité pro zabezpečení citlivých rolí, jako jsou správci, vývojáři a provozovatelé důležitých služeb. Použití vysoce zabezpečených uživatelských pracovních stanic nebo Azure bastionu pro úlohy správy:
- Pomocí Azure Active Directory, programu Microsoft Defender Advanced Threat Protection (ATP) a/nebo Microsoft Intune nasaďte zabezpečenou a spravovanou pracovní stanici uživatelů pro úlohy správy. Zabezpečená pracovní stanice se dají centrálně spravovat tak, aby vynutila zabezpečenou konfiguraci, včetně silného ověřování, standardních hodnot softwaru a hardwaru, omezeného logického a síťového přístupu. 

- Pomocí funkce Azure bastionu můžete získat zabezpečenou cestu k přístupu k virtuálním počítačům přes RDP nebo SSH. Azure bastionu je plně spravovaná služba PaaS, kterou je možné zřídit pro každou virtuální síť bez vytváření nové virtuální sítě. 

- [Pochopení zabezpečení pracovních stanic spravovaných Azure](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Nasazení zabezpečené pracovní stanice spravované v Azure](../../active-directory/devices/howto-azure-managed-workstation.md)

- [Použití hostitele Azure bastionu](../../bastion/bastion-create-host-portal.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Středisko zabezpečení cloudu (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Identita a klíče](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-6-assign-privileges-to-resources-using-azure-rbac"></a>PA-6: přiřazení oprávnění k prostředkům pomocí Azure RBAC

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA – 6 | 14,6 | AC-2, AC-3 |

Řízení přístupu na základě role (RBAC) v Azure umožňuje spravovat oprávnění k přístupu k prostředkům Azure prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, skupinám instančních objektů a spravovaným identitám. Pro určité prostředky jsou předem definované předdefinované role a tyto role můžou být v inventáři nebo prostřednictvím nástrojů, jako je Azure CLI, Azure PowerShell nebo Azure Portal. 

- [Co je řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md)

- [Jak nakonfigurovat RBAC v Azure](../../role-based-access-control/role-assignments-portal.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Identita a klíče](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-choose-approval-process-for-microsoft-support"></a>PA-7: výběr procesu schválení pro podporu Microsoftu

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| PA-7 | 16 | AC-2, AC-3, AC-4 |

Ve scénářích podpory, kde Microsoft potřebuje získat přístup k zákaznickým datům, Customer Lockbox poskytuje možnost explicitně kontrolovat a schvalovat nebo odmítat žádosti o přístup k datům zákazníků.

- [Pochopení Customer Lockbox](../fundamentals/customer-lockbox-overview.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníka**:

- [Zabezpečení aplikací a DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Identita a klíče](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

