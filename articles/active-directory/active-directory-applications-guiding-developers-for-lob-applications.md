---
title: Registrace aplikace pomocí Azure Active Directory | Microsoft Docs
description: Napsané pro IT specialisty, tento článek obsahuje pokyny k integraci aplikací Azure se službou Active Directory.
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2018
ms.author: kgremban
ms.custom: seohack1
ms.openlocfilehash: 10517f5684774c47ccc96c773287476c2614613f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292503"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Vyvíjet-obchodní aplikace pro Azure Active Directory
Tato příručka poskytuje přehled vývoje-obchodní aplikace (LoB) pro Azure Active Directory (AD). Předpokládanou cílovou skupinou je globální správci Active Directory nebo Office 365.

## <a name="overview"></a>Přehled
Vytváření aplikací, které jsou integrované s Azure AD poskytuje uživatelům ve vaší organizaci jednotné přihlašování s Office 365. S aplikace ve službě Azure AD umožňuje spravovat přes zásady ověřování pro aplikaci. Další informace o podmíněný přístup a ochrana aplikací pomocí služby Multi-Factor authentication (MFA) najdete v tématu [pravidla přístupu k konfigurace](active-directory-conditional-access-azure-portal-get-started.md).

Registrace aplikace pomocí Azure Active Directory. Registrace aplikace znamená, že vaše vývojáři použít Azure AD ověřuje uživatele a požádat o přístup k prostředkům uživatele, například e-mailu, kalendáři a dokumenty.

Každý člen adresáři (ne Hosté) můžete zaregistrovat aplikaci, v opačném případě se označuje jako *vytvoření objektu aplikace*.

Registrace aplikace umožňuje všem uživatelům postupujte takto:

* Získat identity pro svou aplikaci, který rozpoznává Azure AD
* Získat jeden nebo více tajné klíče nebo klíče, které aplikace můžete použít k vlastnímu ověření AD
* Značky aplikace na portálu Azure s vlastní název, logem, atd.
* Použití funkce autorizace Azure AD do své aplikace, včetně:

  * Řízení přístupu na základě role (RBAC)
  * Azure Active Directory jako server autorizace oAuth (zabezpečení rozhraní API zveřejněný prostřednictvím aplikace)
* Deklarujte požadovaná oprávnění nutné pro funkce aplikace podle očekávání, včetně:

      - Oprávnění aplikací (jenom globální správci). Příklad: Role členství v jiné službě Azure AD aplikace nebo role členství relativně k prostředku Azure, skupiny prostředků nebo předplatného
      - Přidělená oprávnění (všechny uživatele). Příklad: Azure AD, přihlášení a čtení profilu

> [!NOTE]
> Ve výchozím nastavení může každý člen zaregistrovat aplikaci. Zjistěte, jak omezit oprávnění pro registraci aplikace do konkrétních členů, najdete v tématu [jak se aplikace přidávají do služby Azure AD](develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Tady je co, globální správce, musíte udělat, což vývojářům Zkontrolujte své aplikace připravené pro produkci:

* Konfigurace pravidel přístupu (zásady přístupu vícefaktorového ověřování)
* Nakonfiguruje aplikaci, aby vyžadují přiřazení uživatelů a přiřazení uživatelů
* Potlačit výchozí uživatelské souhlasu prostředí

## <a name="configure-access-rules"></a>Nakonfigurovat pravidla přístupu
Nakonfigurujte každou aplikaci přístup pravidla pro aplikace SaaS. Můžete například vyžadovat vícefaktorové ověřování nebo přístup k uživatelům povolit jenom v důvěryhodných sítích. Podrobnosti o to jsou k dispozici v dokumentu [pravidla přístupu k konfigurace](active-directory-conditional-access-azure-portal-get-started.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Nakonfiguruje aplikaci, aby vyžadují přiřazení uživatelů a přiřazení uživatelů
Ve výchozím nastavení uživatelé mohou využívat aplikace bez přiřazení. Ale pokud aplikace zpřístupní role nebo pokud chcete aplikace se objeví na panel přístupu uživatele, byste měli vyžadovat přiřazení uživatelů.

[Vyžádání přiřazení uživatele](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Pokud jste Azure AD Premium nebo Enterprise Mobility Suite (EMS) odběratele, důrazně doporučujeme použití skupin. Přiřazení skupiny k aplikaci, můžete delegovat správu probíhající přístupu vlastníkovi skupiny. Můžete vytvořit skupinu, nebo požádejte zodpovědná strany ve vaší organizaci vytvoření skupiny pomocí vaše skupiny správy zařízení.

[Přiřazování uživatelů k aplikaci](active-directory-applications-guiding-developers-assigning-users.md)  
[Přiřazování skupin k aplikaci](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Potlačit souhlas uživatele
Ve výchozím nastavení každý uživatel prochází souhlasu prostředí pro přihlášení. Prostředí souhlasu, požádat uživatele udělit oprávnění k aplikaci, může být zneklidňovat pro uživatele, kteří jsou obeznámeni s rozhodování.

Pro aplikace, které důvěřujete můžete zjednodušit uživatelské prostředí tím souhlas k aplikaci jménem vaší organizace.

Další informace o souhlas uživatele a souhlasu prostředí v Azure, najdete v části [integrace aplikací s Azure Active Directory](active-directory-integrating-applications.md).

## <a name="related-articles"></a>Související články
* [Povolit zabezpečený vzdálený přístup k místním aplikacím s proxy aplikace služby Azure AD](manage-apps/application-proxy.md)
* [Verze Preview Azure podmíněného přístupu pro aplikace SaaS](active-directory-conditional-access-azure-portal-get-started.md)
* [Správa přístupu k aplikacím s Azure AD](manage-apps/what-is-access-management.md)
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](active-directory-apps-index.md)
