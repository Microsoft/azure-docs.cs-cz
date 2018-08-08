---
title: Jak spravovat nastavení aktivace rolí | Dokumentace Microsoftu
description: Zjistěte, jak změnit výchozí nastavení pro privilegované identity pomocí rozšíření Azure Active Directory Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 4ca74c001ba379b287c0c9799d90336eb187b2c2
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619596"
---
# <a name="how-to-manage-role-activation-settings-in-azure-ad-privileged-identity-management"></a>Jak spravovat nastavení aktivace rolí v Azure AD Privileged Identity Management
Správce privilegovaných rolí, odkud můžete přizpůsobit Azure AD Privileged Identity Management (PIM) ve své organizaci, včetně změnou prostředí pro uživatele, který je aktivaci přiřazení oprávněné role.

## <a name="manage-the-role-activation-settings"></a>Spravovat nastavení aktivace rolí
1. Přejděte [webu Azure portal](https://portal.azure.com) a vyberte **Azure AD Privileged Identity Management** aplikace z řídicího panelu.
2. Vyberte **spravovat privilegované role** > **nastavení** > **privilegované role**.
3. Zvolte roli, jehož nastavení chcete spravovat.

Na stránce nastavení pro každou roli jsou celou řadu nastavení, která můžete konfigurovat. Tato nastavení ovlivňují jenom uživatelé, kteří jsou oprávnění správci, ne trvalých správců.

**Počet aktivací**: doba v hodinách, které role zůstane aktivní, než vyprší její platnost. To může být v rozmezí 1 až 72 hodin.

**Oznámení**: můžete zvolit, zda systém odešle e-mailů do správci potvrzení, že jejich aktivaci role. To může být užitečné pro zjišťování nezákonných nebo neoprávněné aktivace.

**Lístek incidentu nebo žádosti**: můžete zvolit, jestli se má vyžadovat oprávnění správci zahrnovat číslo lístku, pokud se aktivovat svoje role. To může být užitečné, když provádíte auditování přístupu k roli.

**Ověřování službou Multi-Factor Authentication**: můžete zvolit, jestli se má vyžadovat, aby uživatelé ověřit svoji identitu pomocí MFA, před aktivací jejich role. Mají pouze k ověření tohoto jednou na relaci, není vždy, když se aktivovat roli. Existují dva tipy, jak vzít v úvahu při povolení vícefaktorového ověřování:

* Uživatelé, kteří mají účty Microsoft pro jejich e-mailové adresy (obvykle @outlook.com, ale ne vždy) nejde zaregistrovat pro Azure MFA. Pokud chcete přiřazení rolí uživatelům s účty Microsoft, by měl daly trvalých správců nebo zakázání MFA pro danou roli.
* Vícefaktorové ověřování pro vysoce privilegované role nelze zakázat pro službu Azure AD a Office 365. Toto je funkce bezpečný přístup z více, protože pracovníci v těchto rolích by měly být pečlivě chráněné:  
  
  * Správce aplikace
  * Správce serveru Proxy aplikací
  * Správce fakturace  
  * Správce dodržování předpisů  
  * Správce služby CRM
  * Schvalovatel přístupu Lockboxu zákazníka
  * Zapisovač adresáře  
  * Správce Exchange  
  * Globální správce
  * Správce služby Intune
  * Správce poštovní schránky  
  * Partnerská podpora úrovně 1  
  * Partnerská podpora úrovně 2  
  * Správce privilegovaných rolí   
  * Správce zabezpečení  
  * Správce SharePointu  
  * Správce Skypu pro firmy  
  * Správce uživatelských účtů  

Další informace o použití vícefaktorového ověřování pomocí služby PIM naleznete v tématu [jak vyžadovat vícefaktorové ověřování](pim-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

