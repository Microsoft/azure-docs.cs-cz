---
title: Přihlášení k bezpečnostnímu klíči s nehesly do Windows – Azure Active Directory
description: Povolení nezabezpečeného přihlášení klíče zabezpečení do Azure AD pomocí klíčů zabezpečení FIDO2 (Preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce2b420c2124c86610058ce2f31cd6d7bf620a97
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848455"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-preview"></a>Povolit pro zařízení s Windows 10 přihlášení ke klíčům zabezpečení s nezabezpečenými hesly (Preview)

Tento dokument se zaměřuje na povolení ověřování bez hesla založeného na klíčích zabezpečení FIDO2 u zařízení s Windows 10. Na konci tohoto článku budete moct přihlašovat webové aplikace i zařízení s Windows 10 připojená k Azure AD k vašemu účtu Azure AD pomocí bezpečnostního klíče FIDO2.

|     |
| --- |
| Bezpečnostní klíče FIDO2 jsou funkcí veřejné verze Preview Azure Active Directory. Další informace o verzích Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview.|
|     |

## <a name="requirements"></a>Požadavky

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Souhrnná registrace informací o zabezpečení ve verzi Preview](concept-registration-mfa-sspr-combined.md)
- Kompatibilní [klíče zabezpečení FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
- Operace WebAuthN vyžaduje Windows 10 verze 1809 nebo vyšší.
- [Zařízení připojená k Azure AD](../devices/concept-azure-ad-join.md) vyžadují Windows 10 verze 1809 nebo vyšší.
- [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (volitelné)
- Zřizovací balíček (volitelné)

### <a name="unsupported-scenarios"></a>Nepodporované scénáře

- Nasazení Windows Server Active Directory Domain Services (služba AD DS) připojené k doméně (jenom místní zařízení) **se**nepodporuje.
- Scénáře RDP, VDI a Citrix nejsou **podporovány** pomocí klíče zabezpečení.
- S/MIME není **podporována** použití klíče zabezpečení.
- Příkaz Spustit jako není **podporován** pomocí klíče zabezpečení.
- Přihlášení k serveru pomocí klíče zabezpečení není **podporováno**.
- Pokud jste zabezpečovací klíč nepoužívali k přihlášení k zařízení v online režimu, nebudete ho moct použít k přihlášení nebo odemčení offline.
- Přihlášení nebo odemknutí zařízení s Windows 10 klíčem zabezpečení, který obsahuje několik účtů Azure AD. Tento scénář bude využívat Poslední účet přidaný do klíče zabezpečení. Operace WebAuthN umožní uživatelům zvolit účet, který chtějí použít.

## <a name="prepare-devices-for-preview"></a>Příprava zařízení pro verzi Preview

Zařízení připojená k Azure AD, na kterých budete pilotní nasazení, musí používat Windows 10 verze 1809 nebo vyšší. Nejlepší prostředí je ve Windows 10 verze 1903 nebo vyšší.

## <a name="enable-security-keys-for-windows-sign-in"></a>Povolit klíče zabezpečení pro přihlášení k Windows

Organizace se můžou rozhodnout použít jednu nebo více následujících metod k povolení použití klíčů zabezpečení pro přihlášení Windows na základě požadavků organizace.

- [Povolit s Intune](#enable-with-intune)
- [Cílené nasazení Intune](#targeted-intune-deployment)
- [Povolit pomocí zřizovacího balíčku](#enable-with-a-provisioning-package)

### <a name="enable-with-intune"></a>Povolit s Intune

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Přejděte na **Microsoft Intune** > **registrace zařízení** > **vlastnosti** **registrace** zařízení > **Windows Hello pro firmy** > .
1. V části **Nastavení** nastavení **použít klíče zabezpečení pro přihlášení** na **povoleno**.

Konfigurace klíčů zabezpečení pro přihlášení nezávisí na konfiguraci Windows Hello pro firmy.

### <a name="targeted-intune-deployment"></a>Cílené nasazení Intune

Pokud chcete cílit na konkrétní skupiny zařízení a povolit poskytovatele přihlašovacích údajů, použijte následující vlastní nastavení přes Intune.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Přejděte na **Microsoft Intune** > **Konfigurace zařízení** > **profily** > **vytvořit profil**.
1. Nakonfigurujte nový profil s následujícím nastavením.
   1. Název: bezpečnostní klíče pro přihlášení k Windows
   1. Popis: povolí použití klíčů zabezpečení FIDO během přihlašování Windows.
   1. Platforma: Windows 10 a novější
   1. Typ profilu: vlastní
   1. Vlastní nastavení OMA-URI:
      1. Name (název): zapnout FIDO klíče zabezpečení pro přihlášení k Windows
      1. OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Datový typ: celé číslo
      1. Hodnota: 1
1. Tato zásada se dá přiřadit konkrétním uživatelům, zařízením nebo skupinám. Další informace najdete v článku [přiřazení profilů uživatelů a zařízení v Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Vytvoření vlastní zásady konfigurace zařízení v Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Povolit pomocí zřizovacího balíčku

Pro zařízení, která nespravuje služba Intune, je možné nainstalovat zřizovací balíček, který tuto funkci povolí. Aplikaci Windows Configuration Designer můžete nainstalovat z [Microsoft Store](https://www.microsoft.com/en-us/p/windows-configuration-designer/9nblggh4tx22).

1. Spusťte Windows Configuration Designer.
1. Vyberte **soubor** > **Nový projekt**.
1. Dejte svému projektu název a poznamenejte si cestu, kde je projekt vytvořen.
1. Vyberte **Další**.
1. Nechejte **zřizovací balíček** vybraný jako **vybraný pracovní postup projektu** a vyberte **Další**.
1. V části **Zvolte nastavení, které chcete zobrazit a konfigurovat** a vyberte možnost **Další**, vyberte **všechny edice Windows Desktop** .
1. Vyberte **Finish** (Dokončit).
1. V nově vytvořeném projektu přejděte do **nastavení modulu Runtime** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Nastavte **UseSecurityKeyForSignIn** na **povoleno**.
1. Vyberte **exportovat** > **zřizovací balíček** .
1. V okně **sestavení** ponechte výchozí nastavení v části **Popis zřizovacího balíčku** a vyberte **Další**.
1. V okně **sestavení** v části **Vybrat podrobnosti zabezpečení pro zřizovací balíček** ponechte výchozí nastavení a vyberte **Další**.
1. Poznamenejte si nebo změňte cestu v oknech **sestavení** v části **Vyberte místo, kde se má zřizovací balíček uložit** , a vyberte **Další**.
1. Na stránce **sestavení balíčku pro zřizování** vyberte **sestavit** .
1. Uložte dva vytvořené soubory (ppkg a CAT) do umístění, kde je můžete použít na počítače později.
1. Podle pokynů v článku [použití zřizovacího](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)balíčku použijte zřizovací balíček, který jste vytvořili.

> [!NOTE]
> Zařízení se systémem Windows 10 verze 1809 musí umožňovat také režim sdíleného osobního počítače (EnableSharedPCMode). Informace o povolení tohoto funkci najdete v článku [nastavení sdíleného nebo hostovaného počítače s Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

## <a name="sign-in-to-windows-with-a-fido2-security-key"></a>Přihlaste se k Windows pomocí bezpečnostního klíče FIDO2.

V níže uvedeném příkladu už uživatel Bala Sandhu zřídil svůj klíč zabezpečení FIDO2 pomocí postupu v předchozím článku, který [umožňuje přihlásit klíč zabezpečení bez hesla](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Bala může zvolit poskytovatele pověření bezpečnostního klíče z zamykací obrazovky Windows 10 a vložit bezpečnostní klíč pro přihlášení do Windows.

![Přihlášení k bezpečnostnímu klíči na zamykací obrazovce Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Správa klíčového bezpečnostního biometriky, PIN kódu nebo resetování klíče zabezpečení

* Windows 10 verze 1903 nebo vyšší
   * Uživatelé můžou otevřít **nastavení Windows** na svém zařízení > **účty** > **bezpečnostní klíč** .
   * Uživatelé můžou změnit kód PIN, aktualizovat biometrika nebo obnovit svůj bezpečnostní klíč.

## <a name="troubleshooting-and-feedback"></a>Řešení potíží a zpětná vazba

Pokud chcete sdílet zpětnou vazbu nebo narazit na problémy při prohlížení této funkce, sdílejte ji prosím přes aplikaci Centrum zpětné vazby z Windows.

1. Spusťte **Centrum zpětné vazby** a ujistěte se, že jste přihlášení.
1. Odeslat názor v rámci následující kategorizace:
   1. Kategorie: zabezpečení a ochrana osobních údajů
   1. Subcategory: FIDO
1. Pro zachycení protokolů použijte možnost: **znovu vytvořit můj problém**

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="does-this-work-in-my-on-premises-environment"></a>Funguje to v místním prostředí?

Tato funkce nefunguje pro čistě místní prostředí Active Directory Domain Services (služba AD DS).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Má moje organizace pro přístup k prostředkům dva faktory ověřování, co můžu udělat pro podporu tohoto požadavku?

Bezpečnostní klíče přicházejí v nejrůznějších faktorech formy. Obraťte se na výrobce zařízení, aby pomohly pojednávat, jak je možné jejich zařízení povolit pomocí PIN kódu nebo biometriky jako druhý faktor.

### <a name="can-admins-set-up-security-keys"></a>Můžou správci nastavit klíče zabezpečení?

Pracujeme na této funkci pro obecnou dostupnost této funkce (GA).

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Kde můžu přejít k hledání odpovídajících bezpečnostních klíčů?

[FIDO2 klíče zabezpečení](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Jak mám postupovat, když ztratím svůj bezpečnostní klíč?

Klíče můžete z Azure Portal odebrat tak, že přejdete na stránku bezpečnostní údaje a odeberete klíč zabezpečení.

## <a name="next-steps"></a>Další kroky

[Další informace o registraci zařízení](../devices/overview.md)

[Další informace o Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
