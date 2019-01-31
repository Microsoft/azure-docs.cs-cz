---
title: Obsah role správce pro Microsoft 365 úlohy – Azure AD | Dokumentace Microsoftu
description: Vyhledání obsahu a Reference k rozhraní API pro role správce pro úlohy Microsoft 365 ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 9ef400a5c7b42f6782fefa28e2351b09d8667861
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470242"
---
# <a name="administrator-roles-for-microsoft-365-workloads"></a>Role správce pro úlohy Microsoft 365

Všechny produkty v Microsoftu 365 je možné spravovat pomocí Správce rolí ve službě Azure AD. Některé produkty také poskytují další role, které jsou specifické pro tento produkt. Informace o rolích nepodporuje každého produktu najdete v následující tabulce. Obecné diskuse o problémů s delegováním lze nalézt v [delegování Role v Azure Active Directory plánování](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Kde se mají hledat obsah

Microsoft 365 workload | Obsah role | Obsah rozhraní API
---------------------- | ------------------ | -----------------
Rolí správce v Office 365 a Microsoft 365 obchodní plány | [Role správce Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Není k dispozici.
Azure Active Directory (Azure AD) a Azure AD Identity Protection| [Role Správce služby Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení role](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Řízení přístupu podle role serveru Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [Prostředí PowerShell pro server Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Načíst přiřazení role](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Role Správce služby Azure AD](directory-assign-admin-roles.md)<br>Také [o Sharepointu role správce v Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení role](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Týmy a Skype pro firmy | [Role Správce služby Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení role](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Zabezpečení a dodržování předpisů (Office 365 Advanced Threat Protection, Exchange Online Protection Information Protection) | [Role správce Office 365](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange Powershellu](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Načíst přiřazení role](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Skóre zabezpečení | [Role Správce služby Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení role](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Správce dodržování předpisů | [Role správce dodržování předpisů](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Není k dispozici.
Azure Information Protection | [Role Správce služby Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení role](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Řízení přístupu na základě rolí](https://docs.microsoft.com/cloud-app-security/manage-admins) | [Referenční materiály k rozhraní API](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Rozšířená ochrana před internetovými útoky Azure | [Skupiny rolí Azure ATP](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Není k dispozici.
Rozšířená ochrana před internetovými útoky v programu Windows Defender | [Řízení přístupu na základě rolí ochrany ATP v programu Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Není k dispozici.
Privileged Identity Management | [Role Správce služby Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení role](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Řízení přístupu na základě rolí Intune](https://docs.microsoft.com/intune/role-based-access-control) | [Graph API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Načíst přiřazení role](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Stolní počítač | [Role Správce služby Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Načíst přiřazení role](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Další postup

* [Postup přiřazení nebo odebrání role správce Azure AD](directory-manage-roles-portal.md)
* [Odkazovat na role Správce služby Azure AD](directory-assign-admin-roles.md)
