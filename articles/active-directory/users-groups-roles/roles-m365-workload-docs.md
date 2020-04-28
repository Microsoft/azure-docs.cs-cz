---
title: Dokumentace k rolím Správce v rámci služby Microsoft 365 Services – Azure AD | Microsoft Docs
description: Hledání obsahu a odkazů rozhraní API pro role správců pro Microsoft 365 služby v Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 430b65217da33185cd3b5c235fb148f3350e1bfe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74024463"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Role správce pro služby Microsoft 365 Services

Všechny produkty v Microsoft 365 můžete spravovat pomocí administrativních rolí v Azure AD. Některé produkty také poskytují další role, které jsou specifické pro daný produkt. Informace o rolích podporovaných jednotlivými produkty najdete v následující tabulce. Obecné diskuze o potížích s delegováním najdete v části [plánování delegování rolí v Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Kde najít obsah

Služba Microsoft 365 | Obsah role | Obsah rozhraní API
---------------------- | ------------------ | -----------------
Role správců v Office 365 a Microsoft 365 obchodních plánů | [Role správce Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Není k dispozici.
Azure Active Directory (Azure AD) a Azure AD Identity Protection| [Role správce Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení rolí](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Řízení přístupu na základě role serveru Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell pro Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Načíst přiřazení rolí](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Role správce Azure AD](directory-assign-admin-roles.md)<br>Také [o roli správce služby SharePoint v sadě Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení rolí](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Týmy/Skype pro firmy | [Role správce Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení rolí](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Security & – centrum dodržování předpisů (Rozšířená ochrana před internetovými útoky pro Office 365, ochrana Exchange Online, Information Protection) | [Role správce Office 365](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Prostředí Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Načíst přiřazení rolí](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Bezpečné skóre | [Role správce Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení rolí](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Správce dodržování předpisů | [Role správce dodržování předpisů](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Není k dispozici.
Azure Information Protection | [Role správce Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení rolí](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Řízení přístupu na základě role](https://docs.microsoft.com/cloud-app-security/manage-admins) | [referenční dokumentace k rozhraní API](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Rozšířená ochrana před internetovými útoky Azure | [Skupiny rolí služby Azure ATP](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Není k dispozici.
Rozšířená ochrana před internetovými útoky v programu Windows Defender | [Řízení přístupu na základě role ATP v programu Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Není k dispozici.
Privileged Identity Management | [Role správce Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení rolí](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Řízení přístupu na základě role v Intune](https://docs.microsoft.com/intune/role-based-access-control) | [Graph API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Načíst přiřazení rolí](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Spravovaná plocha | [Role správce Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení rolí](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Další kroky

* [Jak přiřadit nebo odebrat role správce Azure AD](directory-manage-roles-portal.md)
* [Reference k rolím správce Azure AD](directory-assign-admin-roles.md)
