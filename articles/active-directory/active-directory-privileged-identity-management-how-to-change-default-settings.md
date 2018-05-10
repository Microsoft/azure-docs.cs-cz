---
title: Postup správy nastavení aktivace role | Microsoft Docs
description: Zjistěte, jak změnit výchozí nastavení pro privilegované identity pomocí rozšíření Azure Active Directory Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/06/2017
ms.author: curtand
ms.custom: pim
ms.openlocfilehash: 972fd1e322e578516073307d01548132473bc52c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-manage-role-activation-settings-in-azure-ad-privileged-identity-management"></a>Postup správy nastavení aktivace role v Azure AD Privileged Identity Management
Správce privilegovaných rolí můžete přizpůsobit Azure AD Privileged Identity Management (PIM) ve své organizaci, včetně změny prostředí pro uživatele, který je aktivace přiřazení role vhodné.

## <a name="manage-the-role-activation-settings"></a>Spravovat nastavení aktivace role
1. Přejděte na [portál Azure](https://portal.azure.com) a vyberte **Azure AD Privileged Identity Management** aplikace z řídicího panelu.
2. Vyberte **spravovat privilegované role** > **nastavení** > **privilegované role**.
3. Vyberte roli, jehož nastavení chcete spravovat.

Na stránce nastavení pro jednotlivé role existuje několik nastavení, které můžete konfigurovat. Tato nastavení ovlivňují jenom uživatelé, kteří jsou správci oprávněné, není trvalých správců.

**Aktivace**: doba, v hodinách, které role zůstává aktivní, než vyprší její platnost. To může být v rozmezí 1 až 72 hodin.

**Oznámení**: můžete zvolit, zda systém admins potvrzení, že se mají aktivovat roli odešle e-mailů. To může být užitečné pro zjišťování neoprávněným nebo nezákonných aktivací.

**Incident nebo žádost o lístek**: můžete zvolit, zda se má vyžadovat oprávněné správcům zahrnout číslo lístku aktivují jejich role. To může být užitečné při provádění auditů role přístup.

**Služba Multi-Factor Authentication**: můžete zvolit, zda budou muset uživatelé před aktivací jejich rolí, ověřovali svoji identitu pomocí vícefaktorového ověřování. Mají pouze k ověření tohoto jednou na relaci, není pokaždé, když se aktivovat roli. Existují dva typy mít na paměti, když povolíte vícefaktorového ověřování:

* Uživatelé, kteří mají účty Microsoft pro jejich e-mailové adresy (obvykle @outlook.com, ale ne vždy) nelze zaregistrovat pro Azure MFA. Pokud chcete přiřadit role pro uživatele s účty Microsoft, by je provést trvalých správců nebo zakázat MFA pro tuto roli.
* Nelze zakázat MFA pro vysoce privilegované role pro Azure AD a Office 365. Toto je funkce zabezpečení, protože tyto role je třeba pečlivě chránit:  
  
  * Správce aplikace
  * Správce serveru Proxy aplikace
  * Správce fakturace  
  * Správce dodržování předpisů  
  * Správce služeb CRM
  * Schvalovatel přístup bezpečnostního modulu zákazníka
  * Zapisovač adresáře  
  * Správce Exchange  
  * Globální správce
  * Správce služby Intune
  * Poštovní schránky správce  
  * Partnerská podpora úrovně 1  
  * Partnerská podpora úrovně 2  
  * Správce privilegovaných rolí   
  * Správce zabezpečení  
  * Správce SharePointu  
  * Správce Skypu pro firmy  
  * Správce účtu uživatele  

Další informace o použití vícefaktorového ověřování se PIM najdete v části [jak vyžadovat vícefaktorové ověřování](active-directory-privileged-identity-management-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

