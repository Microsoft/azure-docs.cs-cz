---
title: Dokumenty rolí správce ve službách Microsoft 365 – Azure AD | Dokumenty společnosti Microsoft
description: Vyhledání obsahu a odkazů na rozhraní API pro role správce pro služby Microsoft 365 ve službě Azure Active Directory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024463"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Role správce pro služby Microsoft 365

Všechny produkty v Microsoftu 365 se dá spravovat s rolemi správy ve službě Azure AD. Některé produkty také poskytují další role, které jsou specifické pro tento produkt. Informace o rolích podporovaných jednotlivými produkty naleznete v následující tabulce. Obecné diskuse o otázkách delegování najdete v [plánování delegování rolí ve službě Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Kde najít obsah

Služba Microsoft 365 | Obsah role | Obsah rozhraní API
---------------------- | ------------------ | -----------------
Role správců v plánech Office 365 a Microsoft 365 pro firmy | [Role správce Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Není k dispozici.
Azure Active Directory (Azure AD) a Ochrana identit Azure AD| [Role správce Azure AD](directory-assign-admin-roles.md) | [Rozhraní API pro grafy](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načtení přiřazení rolí](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Řízení přístupu založené na rolích serveru Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [Prostředí PowerShell pro exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Načtení přiřazení rolí](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Role správce Azure AD](directory-assign-admin-roles.md)<br>Taky [o roli správce SharePointu v Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Rozhraní API pro grafy](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načtení přiřazení rolí](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Týmy/Skype pro firmy | [Role správce Azure AD](directory-assign-admin-roles.md) | [Rozhraní API pro grafy](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načtení přiřazení rolí](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Centrum dodržování předpisů pro zabezpečení & (Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Role správce Office 365](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Načtení přiřazení rolí](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Bezpečnostní skóre | [Role správce Azure AD](directory-assign-admin-roles.md) | [Rozhraní API pro grafy](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načtení přiřazení rolí](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Správce dodržování předpisů | [Role správce dodržování předpisů](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Není k dispozici.
Azure Information Protection | [Role správce Azure AD](directory-assign-admin-roles.md) | [Rozhraní API pro grafy](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načtení přiřazení rolí](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Řízení přístupu založené na rolích](https://docs.microsoft.com/cloud-app-security/manage-admins) | [Odkaz na rozhraní API](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Rozšířená ochrana před internetovými útoky Azure | [Skupiny rolí služby Azure ATP](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Není k dispozici.
Rozšířená ochrana před internetovými útoky v programu Windows Defender | [Řízení přístupu založené na rolích ochrany ATP v programu Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Není k dispozici.
Privileged Identity Management | [Role správce Azure AD](directory-assign-admin-roles.md) | [Rozhraní API pro grafy](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načtení přiřazení rolí](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Řízení přístupu založené na rolích Intune](https://docs.microsoft.com/intune/role-based-access-control) | [Rozhraní API pro grafy](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Načtení přiřazení rolí](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Spravovaná plocha | [Role správce Azure AD](directory-assign-admin-roles.md) | [Rozhraní API pro grafy](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načtení přiřazení rolí](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Další kroky

* [Jak přiřadit nebo odebrat role správce Azure AD](directory-manage-roles-portal.md)
* [Odkaz na role správce Azure AD](directory-assign-admin-roles.md)
