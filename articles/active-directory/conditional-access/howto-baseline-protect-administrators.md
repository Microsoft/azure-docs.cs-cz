---
title: Základní zásady vyžadují MFA pro správce – Azure Active Directory
description: Zásada podmíněného přístupu, která vyžaduje službu Multi-Factor Authentication pro správce
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 213540a5b6c77146155365133f2cca08eea25351
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608156"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>Základní zásady: Vyžadovat MFA pro správce (Preview)

Uživatelé s přístupem k privilegovaným účtům mají neomezený přístup k vašemu prostředí. Vzhledem k napájení těchto účtů byste je měli považovat za zvláštní péči. Jednou z běžných metod, jak zlepšit ochranu privilegovaných účtů, je vyžadovat při přihlášení silnější formu ověření účtu. V Azure Active Directory můžete získat silnější ověření účtu tím, že budete vyžadovat vícefaktorové ověřování (MFA).

**Vyžadovat MFA pro správce (Preview)**  je [základní zásada](concept-baseline-protection.md) , která vyžaduje MFA pokaždé, když se přihlásí jedna z následujících rolí privilegovaného správce:

* Globální správce
* Správce SharePointu
* Správce Exchange
* Správce podmíněného přístupu
* Správce zabezpečení
* Správce helpdesku/správce hesel
* Správce fakturace
* Správce uživatelů

Když se povolí zásada vyžadovat MFA pro správce, bude se při registraci MFA pomocí ověřovací aplikace vyžadovat výše uvedené devět rolí správce. Po dokončení registrace MFA budou muset správci provádět vícefaktorové ověřování při každém přihlášení.

## <a name="deployment-considerations"></a>Aspekty nasazování

Vzhledem k tomu, že zásady **vyžadovat MFA pro správce (Preview)** platí pro všechny kritické správce, je potřeba provést několik důležitých informací, aby bylo zajištěno hladké nasazení. Mezi tyto požadavky patří určení uživatelů a zásad služeb ve službě Azure AD, které nemůžou nebo by neměly provádět MFA, a také aplikace a klienty používané ve vaší organizaci, které nepodporují moderní ověřování.

### <a name="legacy-protocols"></a>Starší protokoly

Starší verze ověřovacích protokolů (IMAP, SMTP, POP3 atd.) jsou používány e-mailové klienty k provádění požadavků na ověření. Tyto protokoly vícefaktorové ověřování nepodporují. Většina kompromisů, které společnost Microsoft uvidí, je způsobena nesprávnými aktéry, které provádějí útoky proti starším protokolům, které se pokoušejí obejít MFA. Aby se zajistilo, že MFA se při přihlašování k účtu pro správu vyžaduje, a nedaří se jim tak obejít MFA, tato zásada blokuje všechny požadavky na ověření provedené pro účty správců ze starších protokolů.

> [!WARNING]
> Než povolíte tuto zásadu, zajistěte, aby vaši správci nepoužívali starší protokoly pro ověřování. Informace najdete v [článku Postupy: Zablokovat starší ověřování do Azure AD s](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) podmíněným přístupem pro další informace.

## <a name="enable-the-baseline-policy"></a>Povolit základní zásady

Zásady standardních **hodnot zásad: Vyžadovat MFA pro správce (Preview)** je předem nakonfigurovaný a při přechodu do okna podmíněný přístup v Azure Portal se zobrazí v horní části.

Chcete-li povolit tuto zásadu a chránit správce:

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na **Azure Active Directory** > **podmíněný přístup**.
1. V seznamu zásad vyberte **základní zásady: Vyžadovat MFA pro správce (Preview)** .
1. Nastavte **Povolit zásadu** pro **okamžité použití zásad**.
1. Klikněte na **Uložit**.

> [!WARNING]
> V budoucnu existovala možnost **automaticky povolit zásady** , pokud byla tato zásada ve verzi Preview. Tuto možnost jsme odebrali kvůli minimalizaci náhlého dopadu na uživatele. Pokud jste tuto možnost vybrali, když byla k dispozici, není nyní automaticky vybraná možnost **Nepoužívat zásady** . Pokud chtějí použít tuto základní zásady, můžete ji povolit v části výše uvedené kroky.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v tématu:

* [Zásady ochrany základní úrovně přístupu pro podmíněný přístup](concept-baseline-protection.md)
* [Pět kroků pro zabezpečení infrastruktury identity](../../security/fundamentals/steps-secure-identity.md)
* [Co je podmíněný přístup v Azure Active Directory?](overview.md)
