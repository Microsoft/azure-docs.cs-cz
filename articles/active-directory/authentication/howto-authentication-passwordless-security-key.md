---
title: Přihlášení k bezpečnostnímu klíči s nezabezpečenými hesly – Azure Active Directory
description: Povolení nezabezpečeného přihlášení k bezpečnostnímu klíči pro službu Azure AD pomocí klíčů zabezpečení FIDO2
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71a29a2a7e379e253b52813eb7a76f669abbf668
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653826"
---
# <a name="enable-passwordless-security-key-sign-in"></a>Povolit přihlašování k bezpečnostnímu klíči s nezabezpečenými hesly 

Pro podniky, které používají hesla dnes a mají prostředí pro sdílené počítače, poskytují bezpečnostní klíče bezproblémové možnosti, jak se pracovníkům ověřovat bez zadání uživatelského jména nebo hesla. Klíče zabezpečení poskytují lepší produktivitu pro pracovníky a mají lepší zabezpečení.

Tento dokument se zaměřuje na povolení ověřování pomocí hesla založeného na klíčích zabezpečení. Na konci tohoto článku se budete moci přihlásit k webovým aplikacím pomocí účtu Azure AD pomocí bezpečnostního klíče FIDO2.

## <a name="requirements"></a>Požadavky

- [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md)
- Povolit [registraci kombinovaných zabezpečení informací](concept-registration-mfa-sspr-combined.md)
- Kompatibilní [klíče zabezpečení FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
- Operace WebAuthN vyžaduje Windows 10 verze 1903 nebo vyšší * *

Chcete-li použít klíče zabezpečení pro přihlášení k webovým aplikacím a službám, je nutné mít prohlížeč, který podporuje protokol WebAuthN. Mezi ně patří Microsoft Edge, Chrome, Firefox a Safari.

## <a name="prepare-devices"></a>Příprava zařízení

Pro zařízení připojená k Azure AD je nejlepší prostředí ve Windows 10 verze 1903 nebo vyšší.

Hybridní zařízení připojená k Azure AD musí používat Windows 10 verze 2004 nebo vyšší.

## <a name="enable-passwordless-authentication-method"></a>Povolit metodu ověřování nejenom pro hesla

### <a name="enable-the-combined-registration-experience"></a>Povolit kombinované možnosti registrace

Funkce registrace pro metody ověřování nevyužívající hesla se spoléhají na funkci kombinované registrace. Postupujte podle kroků v článku [Povolení registrace kombinovaných informací o zabezpečení](howto-registration-mfa-sspr-combined.md), aby se aktivovala kombinovaná registrace.

### <a name="enable-fido2-security-key-method"></a>Povolit metodu klíče zabezpečení FIDO2

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejděte na **Azure Active Directory**  >    >  zásady ověřování **metody** ověřování zabezpečení  >  .
1. V části **klíč zabezpečení FIDO2** metody vyberte následující možnosti:
   1. **Povolit** – Ano nebo ne
   1. **Cíl** – všichni uživatelé nebo vybrat uživatele
1. **Uložte** konfiguraci.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registrace uživatelů a Správa klíčů zabezpečení FIDO2

1. Přejděte na [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Přihlaste se, pokud ještě není.
1. Klikněte na **informace o zabezpečení**.
   1. Pokud má uživatel již alespoň jednu registrovanou metodu služby Azure AD Multi-Factor Authentication, může ihned zaregistrovat klíč zabezpečení FIDO2.
   1. Pokud nemají zaregistrovanou alespoň jednu metodu služby Azure AD Multi-Factor Authentication, musí ji přidat.
1. Přidejte klíč zabezpečení FIDO2, a to tak, že kliknete na **Přidat metodu** a zvolíte **klíč zabezpečení**.
1. Vyberte **zařízení USB** nebo **zařízení NFC**.
1. Připravte si klíč a klikněte na tlačítko **Další**.
1. Zobrazí se pole a požádá uživatele o vytvoření nebo zadání kódu PIN pro klíč zabezpečení a pak pro tento klíč provede požadované gesto, ať už je to biometrika nebo dotykové ovládání.
1. Uživatel bude vrácen do kombinovaného prostředí pro registraci a požádán o poskytnutí smysluplného názvu pro klíč, aby uživatel mohl zjistit, který z nich je v případě, že má více. Klikněte na **Next** (Další).
1. Kliknutím na **Hotovo** proces dokončete.

## <a name="sign-in-with-passwordless-credential"></a>Přihlášení pomocí přihlašovacích údajů bez hesla

V následujícím příkladu už uživatel zřídil svůj bezpečnostní klíč FIDO2. Uživatel se může přihlásit na webu pomocí klíče zabezpečení FIDO2 v podporovaném prohlížeči ve Windows 10 verze 1903 nebo vyšší.

![Přihlášení k bezpečnostnímu klíči Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Řešení potíží a zpětná vazba

Pokud chcete sdílet zpětnou vazbu nebo narazit na problémy s touto funkcí, sdílejte ji přes aplikaci Windows Feedback Center pomocí následujících kroků:

1. Spusťte **Centrum zpětné vazby** a ujistěte se, že jste přihlášení.
1. Odeslat názor v rámci následující kategorizace:
   - Kategorie: zabezpečení a ochrana osobních údajů
   - Subcategory: FIDO
1. Chcete-li zachytit protokoly, použijte možnost k **opětovnému vytvoření problému**.

## <a name="known-issues"></a>Známé problémy

### <a name="security-key-provisioning"></a>Zřizování klíče zabezpečení

Bezpečnostní klíče zřizování a zrušení zřizování nejsou k dispozici.

### <a name="upn-changes"></a>Změny hlavního názvu uživatele (UPN)

Pracujeme na podpoře funkce, která umožňuje změnu UPN na hybridních připojených zařízeních Azure AD a na zařízeních připojených k Azure AD. Pokud se změní hlavní název uživatele (UPN), nemůžete už upravovat bezpečnostní klíče FIDO2, aby se změna projevila. Řešením je resetování zařízení a uživatel se musí znovu zaregistrovat.

## <a name="next-steps"></a>Další kroky

[FIDO2 bezpečnostní klíč Windows 10 – přihlášení](howto-authentication-passwordless-security-key-windows.md)

[Povolit ověřování FIDO2 pro místní prostředky](howto-authentication-passwordless-security-key-on-premises.md)

[Další informace o registraci zařízení](../devices/overview.md)

[Další informace o Multi-Factor Authentication Azure AD](../authentication/howto-mfa-getstarted.md)
