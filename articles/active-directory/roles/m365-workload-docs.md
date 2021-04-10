---
title: Dokumentace k rolím Správce v rámci služby Microsoft 365 Services – Azure AD | Microsoft Docs
description: Hledání obsahu a odkazů rozhraní API pro role správců pro Microsoft 365 služby v Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae5761c4a31662f447a107e6b5eb89bcf02f70c8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055531"
---
# <a name="roles-for-microsoft-365-services-in-azure-active-directory"></a>Role pro služby Microsoft 365 Services v Azure Active Directory

Všechny produkty v Microsoft 365 lze spravovat pomocí rolí správy v Azure Active Directory (Azure AD). Některé produkty také poskytují další role, které jsou specifické pro daný produkt. Informace o rolích podporovaných jednotlivými produkty najdete v následující tabulce. Pokyny k plánování zabezpečení rolí najdete v tématu [zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](security-planning.md).

## <a name="where-to-find-content"></a>Kde najít obsah

Služba Microsoft 365 | Obsah role | Obsah rozhraní API
---------------------- | ------------------ | -----------------
Role správců v Office 365 a Microsoft 365 obchodních plánů | [Microsoft 365 role správců](/office365/admin/add-users/about-admin-roles) | Není k dispozici
Azure Active Directory (Azure AD) a Azure AD Identity Protection| [Role správce Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Načíst přiřazení rolí](/graph/api/directoryrole-list)
Exchange Online| [Řízení přístupu na základě role serveru Exchange](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell pro Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[Načíst přiřazení rolí](/powershell/module/exchange/role-based-access-control/get-rolegroup)
SharePoint Online | [Role správce Azure AD](permissions-reference.md)<br>Také [o roli správce služby SharePoint v Microsoft 365](/sharepoint/sharepoint-admin-role) | [Graph API](/graph/api/overview)<br>[Načíst přiřazení rolí](/graph/api/directoryrole-list)
Týmy/Skype pro firmy | [Role správce Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Načíst přiřazení rolí](/graph/api/directoryrole-list)
Security & – centrum dodržování předpisů (Rozšířená ochrana před internetovými útoky pro Office 365, ochrana Exchange Online, Information Protection) | [Role správce Office 365](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Prostředí Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry)<br>[Načíst přiřazení rolí](/powershell/module/exchange/role-based-access-control/get-rolegroup)
Bezpečné skóre | [Role správce Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Načíst přiřazení rolí](/graph/api/directoryrole-list)
Správce dodržování předpisů | [Role správce dodržování předpisů](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Není k dispozici
Azure Information Protection | [Role správce Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Načíst přiřazení rolí](/graph/api/directoryrole-list)
Microsoft Cloud App Security | [Řízení přístupu na základě role](/cloud-app-security/manage-admins) | [Referenční materiály k rozhraní API](/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Skupiny rolí služby Azure ATP](/azure-advanced-threat-protection/atp-role-groups) | Není k dispozici
Rozšířená ochrana před internetovými útoky v programu Windows Defender | [Řízení přístupu na základě role ATP v programu Windows Defender](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Není k dispozici
Privileged Identity Management | [Role správce Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Načíst přiřazení rolí](/graph/api/directoryrole-list)
Intune | [Řízení přístupu na základě role v Intune](/intune/role-based-access-control) | [Graph API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta&preserve-view=true)<br>[Načíst přiřazení rolí](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta&preserve-view=true)
Spravovaná plocha | [Role správce Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview)<br>[Načíst přiřazení rolí](/graph/api/directoryrole-list)

## <a name="next-steps"></a>Další kroky

* [Jak přiřadit nebo odebrat role správce Azure AD](manage-roles-portal.md)
* [Reference k rolím správce Azure AD](permissions-reference.md)