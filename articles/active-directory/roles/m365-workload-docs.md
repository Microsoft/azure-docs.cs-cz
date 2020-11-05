---
title: Dokumentace k rolím Správce v rámci služby Microsoft 365 Services – Azure AD | Microsoft Docs
description: Hledání obsahu a odkazů rozhraní API pro role správců pro Microsoft 365 služby v Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8178f0753cd6a93caac2f9dece62f21e1c1b311d
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378886"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Role správce pro služby Microsoft 365 Services

Všechny produkty v Microsoft 365 můžete spravovat pomocí administrativních rolí v Azure AD. Některé produkty také poskytují další role, které jsou specifické pro daný produkt. Informace o rolích podporovaných jednotlivými produkty najdete v následující tabulce. Obecné diskuze o potížích s delegováním najdete v části [plánování delegování rolí v Azure Active Directory](concept-delegation.md).

## <a name="where-to-find-content"></a>Kde najít obsah

Služba Microsoft 365 | Obsah role | Obsah rozhraní API
---------------------- | ------------------ | -----------------
Role správců v Office 365 a Microsoft 365 obchodních plánů | [Microsoft 365 role správců](/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Není k dispozici
Azure Active Directory (Azure AD) a Azure AD Identity Protection| [Role správce Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení rolí](/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Řízení přístupu na základě role serveru Exchange](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell pro Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Načíst přiřazení rolí](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Role správce Azure AD](permissions-reference.md)<br>Také [o roli správce služby SharePoint v Microsoft 365](/sharepoint/sharepoint-admin-role) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení rolí](/graph/api/directoryrole-list?view=graph-rest-1.0)
Týmy/Skype pro firmy | [Role správce Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení rolí](/graph/api/directoryrole-list?view=graph-rest-1.0)
Security & – centrum dodržování předpisů (Rozšířená ochrana před internetovými útoky pro Office 365, ochrana Exchange Online, Information Protection) | [Role správce Office 365](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Prostředí Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Načíst přiřazení rolí](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Bezpečné skóre | [Role správce Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení rolí](/graph/api/directoryrole-list?view=graph-rest-1.0)
Správce dodržování předpisů | [Role správce dodržování předpisů](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Není k dispozici
Azure Information Protection | [Role správce Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení rolí](/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Řízení přístupu na základě role](/cloud-app-security/manage-admins) | [Referenční materiály k rozhraní API](/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Skupiny rolí služby Azure ATP](/azure-advanced-threat-protection/atp-role-groups) | Není k dispozici
Rozšířená ochrana před internetovými útoky v programu Windows Defender | [Řízení přístupu na základě role ATP v programu Windows Defender](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Není k dispozici
Privileged Identity Management | [Role správce Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení rolí](/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Řízení přístupu na základě role v Intune](/intune/role-based-access-control) | [Graph API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Načíst přiřazení rolí](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Spravovaná plocha | [Role správce Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení rolí](/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Další kroky

* [Jak přiřadit nebo odebrat role správce Azure AD](manage-roles-portal.md)
* [Reference k rolím správce Azure AD](permissions-reference.md)