---
title: Přihlášení pomocí bezpečnostního klíče bez hesla (preview) – Azure Active Directory
description: Povolení přihlašování pomocí klíčů zabezpečení FIDO2 bez hesla (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/12/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8574fcdff12e61f7039174ed6297d0558a66dc4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653940"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Povolení přihlášení pomocí bezpečnostního klíče bez hesla (náhled)

Pro podniky, které dnes používají hesla a mají sdílené prostředí počítače, poskytují bezpečnostní klíče pracovníkům bezproblémový způsob ověřování bez zadání uživatelského jména nebo hesla. Bezpečnostní klíče poskytují pracovníkům vyšší produktivitu a mají lepší zabezpečení.

Tento dokument se zaměřuje na povolení ověřování bez hesla pomocí bezpečnostních klíčů. Na konci tohoto článku se budete moct přihlásit k webovým aplikacím pomocí účtu Azure AD pomocí klíče zabezpečení FIDO2.

|     |
| --- |
| Klíče zabezpečení FIDO2 jsou veřejnou funkcí preview služby Azure Active Directory. Další informace o náhledech najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Požadavky

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Kombinovaný náhled registrace bezpečnostních informací](concept-registration-mfa-sspr-combined.md)
- Kompatibilní [bezpečnostní klíče FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN vyžaduje Windows 10 verze 1809 nebo vyšší**

Chcete-li pro přihlášení k webovým aplikacím a službám používat bezpečnostní klíče, musíte mít prohlížeč, který podporuje protokol WebAuthN. Patří mezi ně Microsoft Edge, Chrome, Firefox a Safari.

## <a name="prepare-devices-for-preview"></a>Příprava zařízení pro náhled

Azure AD připojen zařízení, které pilotní s musí spustit Windows 10 verze 1809 nebo vyšší. Nejlepší zážitek je v systému Windows 10 verze 1903 nebo vyšší.

Hybridní zařízení připojená k Azure AD musí spouštět Windows 10 Insider Build 18945 nebo novější.

## <a name="enable-passwordless-authentication-method"></a>Povolit metodu ověřování bez hesla

### <a name="enable-the-combined-registration-experience"></a>Povolení kombinovaného registračního prostředí

Registrační funkce pro metody ověřování bez hesla spoléhají na kombinovaný náhled registrace. Postupujte podle kroků v článku [Povolit kombinovanou registraci informací o zabezpečení (náhled)](howto-registration-mfa-sspr-combined.md), chcete-li povolit kombinovaný náhled registrace.

### <a name="enable-fido2-security-key-method"></a>Povolit metodu klíče zabezpečení FIDO2

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte na**zásadu metody ověřování** > **zabezpečení** >  **služby Azure Active Directory** > **(Preview).**
1. V metodě **FIDO2 Security Key**zvolte následující možnosti:
   1. **Povolit** – ano nebo ne
   1. **Cíl** – všichni uživatelé nebo vybraní uživatelé
1. **Uložte** konfiguraci.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registrace a správa bezpečnostních klíčů FIDO2 uživatelem

1. Přejděte [https://myprofile.microsoft.com](https://myprofile.microsoft.com)na .
1. Pokud již ne, přihlaste se.
1. Klepněte na **položku Bezpečnostní údaje**.
   1. Pokud uživatel již má alespoň jednu metodu Azure Multi-Factor Authentication registrovanou, může okamžitě zaregistrovat klíč zabezpečení FIDO2.
   1. Pokud nemají zaregistrovanou alespoň jednu metodu Azure Multi-Factor Authentication, musí ji přidat.
1. Přidejte klíč zabezpečení FIDO2 klepnutím na tlačítko **Přidat metodu** a výběrem **příkazu Bezpečnostní klíč**.
1. Zvolte **zařízení USB** nebo **zařízení NFC**.
1. Připravte si klíč a zvolte **Další**.
1. Zobrazí se pole a požádá ho, aby vytvořil/zadá PIN kód bezpečnostního klíče a poté provede požadované gesto pro klíč, a to buď biometrické, nebo dotykové.
1. Uživatel bude vrácen do kombinované registrace prostředí a požádán, aby zadejte smysluplné jméno pro klíč, takže uživatel může určit, který z nich, pokud mají více. Klikněte na **Další**.
1. Chcete-li proces dokončit, klepněte na **tlačítko Hotovo.**

## <a name="sign-in-with-passwordless-credential"></a>Přihlášení pomocí přihlašovacích údajů bez hesla

V níže uvedeném příkladu uživatel již zřídit svůj klíč zabezpečení FIDO2. Uživatel se může přihlásit na webu pomocí svého bezpečnostního klíče FIDO2 v podporovaném prohlížeči v systému Windows 10 verze 1809 nebo vyšším.

![Přihlášení pomocí bezpečnostního klíče Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Poradce při potížích a zpětná vazba

Pokud chcete při náhledu této funkce sdílet zpětnou vazbu nebo se s nimi setkat, sdílejte je prostřednictvím aplikace Windows Feedback Hub pomocí následujících kroků:

1. Spusťte **Centrum Feedback hub** a ujistěte se, že jste přihlášeni.
1. Odeslat zpětnou vazbu v rámci následující kategorizace:
   - Kategorie: Bezpečnost a ochrana osobních údajů
   - Podkategorie: FIDO
1. Chcete-li zachytit protokoly, použijte možnost **znovu vytvořit můj problém**

## <a name="known-issues"></a>Známé problémy

### <a name="security-key-provisioning"></a>Zřizování bezpečnostních klíčů

Zřizování a zrušení zřizování klíčů zabezpečení správcem není ve verzi Public Preview k dispozici.

### <a name="upn-changes"></a>Změny hlavního názvu uživatele (UPN)

Pracujeme na podpoře funkce, která umožňuje změnu UPN na hybridních zařízeních Azure AD spojených a zařízeních Azure AD. Pokud se změní hlavní uživatel, již nelze upravit klíče zabezpečení FIDO2 tak, aby byly změny zohledňovány. Rozlišení je resetovat zařízení a uživatel musí znovu zaregistrovat.

## <a name="next-steps"></a>Další kroky

[Přihlašování pomocí bezpečnostního klíče FIDO2 windows 10](howto-authentication-passwordless-security-key-windows.md)

[Povolení ověřování FIDO2 k místním prostředkům](howto-authentication-passwordless-security-key-on-premises.md)

[Další informace o registraci zařízení](../devices/overview.md)

[Další informace o vícefaktorovém ověřování Azure](../authentication/howto-mfa-getstarted.md)
