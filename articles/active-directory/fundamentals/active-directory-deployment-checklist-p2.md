---
title: Azure AD kontrolní seznam nasazení 30 dnů, 90 dnů a novější
description: Kontrolní seznam nasazení funkce Azure Active Directory Premium P2
services: active-directory
ms.service: active-directory
ms.component: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: ''
ms.openlocfilehash: 86561cd835a36282ca1b38638ab4372c6b360617
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705647"
---
# <a name="azure-active-directory-premium-p2-licensing-feature-checklist"></a>Azure Active Directory Premium P2 licencování funkce kontrolní seznam

Může to působit příliš složitě k nasazení služby Azure Active Directory (Azure AD) pro vaši organizaci a zajistili jeho zabezpečení. Tento článek identifikuje některé běžné úlohy, které zákazníkům užitečné. Zákazníci obvykle dokončení těchto úloh v průběhu 30 dnů, 90 dnů, maximálně zvýšit jejich stavu zabezpečení. Dokonce i organizace, kteří už mají nasazenou Azure AD můžete použít tento kontrolní seznam, abyste měli jistotu, že výhodný využívat své investice na maximum.

Infrastruktura promyšlených a jsou prováděny identit usnadní cestu pro lepší zabezpečení přístupu k data pouze pomocí ověřených uživatelů a zařízení a produktivity úloh.

## <a name="prerequisites"></a>Požadavky

Tento průvodce to předpokládá, že máte licence Azure AD Premium P2, Enterprise Mobility + Security E5, Microsoft 365 E5 nebo podobné sadě licence.

[Licencování Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)

[Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)

[Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)

## <a name="plan-and-deploy-day-1-30"></a>Plánování a nasazení: den 1-30

- Určení více než jednoho globálního správce (konec pohotovostní účet)
   - [Spravovat účty pro správu nouzovou přístup ve službě Azure AD](../users-groups-roles/directory-emergency-access.md)
- Zapnout Azure AD Privileged Identity Management (PIM) Chcete-li zobrazit sestavy
   - [Zahájení práce s PIM](../privileged-identity-management/pim-getting-started.md)
- Kde je to možné, použijte platformou pro správu rolí.
   - [Přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
- Authentication
   - [Zavedení samoobslužného resetování hesla](../authentication/howto-sspr-deployment.md)
   - Nasazení ochrany hesel služby Azure AD (preview)
      - [Eliminuje chybná hesla ve vaší organizaci](../authentication/concept-password-ban-bad.md)
      - [Vynucení ochrany hesla Azure AD pro Windows Server Active Directory](../authentication/concept-password-ban-bad-on-premises.md)
   - Konfigurace zásady uzamčení účtů
      - [Azure Active Directory inteligentní uzamčení](../authentication/howto-password-smart-lockout.md)
      - [Uzamčení extranetu a inteligentní uzamčení extranetu služby AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)
   - [Nasazení Azure Multi-Factor Authentication AD pomocí zásad podmíněného přístupu](../authentication/howto-mfa-getstarted.md)
   - [Povolit sblížené registrace pro samoobslužné resetování hesla a Vícefaktorové ověřování Azure AD (preview)](../authentication/concept-registration-mfa-sspr-converged.md)
   - [Povolit Azure Active Directory Identity Protection](../identity-protection/enable.md)
      - [Pomocí aktivační události služby Multi-Factor Authentication a změn hesel rizikových událostí](../authentication/tutorial-risk-based-sspr-mfa.md)
   - [Pokyny k heslo](https://www.microsoft.com/research/publication/password-guidance/)
      - Udržovat požadavek na minimální délku osmi znaků, již není nutně lepší.
      - Odstranit znak složení požadavky.
      - [Odstranění obnovení povinné pravidelné hesla pro uživatelské účty.](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire)
- Synchronizovat uživatele z místní služby Active Directory
   - [Instalace služby Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md)
   - [Implementace synchronizace hodnot Hash hesel](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)
   - [Implementace zpětného zápisu hesla](../authentication/howto-sspr-writeback.md)
   - [Implementace služby Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)
- [Přiřazení licencí pro uživatele na základě členství ve skupinách v Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)

## <a name="plan-and-deploy-day-31-90"></a>Plánování a nasazení: dne 31 až 90

- [Plánování přístupu uživatelů typu Host](../b2b/what-is-b2b.md)
   - [Přidat uživatele spolupráce Azure Active Directory B2B na webu Azure Portal](../b2b/add-users-administrator.md)
   - [Povolení nebo blokování pozvánek uživatelů B2B z konkrétních organizací](../b2b/allow-deny-list.md)
   - [Uživatelům udělit B2B v Azure AD přístup k místním aplikacím](../b2b/hybrid-cloud-to-on-premises.md)
- Rozhodování o strategie správy životního cyklu uživatele
- [Při rozhodování o strategie správy zařízení](../devices/overview.md)
   - [Scénáře použití a aspekty nasazení pro službu Azure AD Join](../devices/azureadjoin-plan.md)
- [Správa Windows Hello pro firmy ve vaší organizaci](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

## <a name="plan-and-deploy-day-90-and-beyond"></a>Plánování a nasazení: den 90 a novější

- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md)
   - [Konfigurace nastavení role adresáře Azure AD v PIM](../privileged-identity-management/pim-how-to-change-default-settings.md)
   - [Přiřazení role adresáře Azure AD v PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md)
- [Dokončení kontroly přístupu pro role adresáře Azure AD v PIM](../privileged-identity-management/pim-how-to-start-security-review.md)
- Správa životního cyklu uživatele holistické
   - Azure AD má přístup ke správě životního cyklu identit
   - Odebrání vyžadováno provedení ručních kroků životního cyklu pro účet vaše zaměstnance, aby se zabránilo neoprávněnému přístupu:
      - Synchronizaci identit mezi vaším zdrojem pravdivých informací (HR systému) do služby Azure AD. odkaz na podporované systémy oddělení Personalistiky)
      - [Umožňuje automaticky přiřadit uživatele do skupin na základě jejich atributů z personálního oddělení (nebo zdroji pravdivých informací), jako je například oddělení, název, oblast a další atributy dynamické skupiny.](../users-groups-roles/groups-dynamic-membership.md)
      - [Pomocí přístupu na základě skupiny správy zřizování, které umožňuje automaticky zřizovat uživatele pro aplikace SaaS.](../manage-apps/what-is-access-management.md)

## <a name="next-steps"></a>Další postup

[Konfigurace přístupu k identity a zařízení](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Běžné doporučené zásady přístupu identity a zařízení](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
