---
title: Povolit přihlašování k neheslovým klíčům zabezpečení pro Azure AD (Preview) – Azure Active Directory
description: Povolit klíč zabezpečení s nezabezpečenými hesly do Azure AD pomocí klíčů zabezpečení FIDO2 (Preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 316a523a6216354ae5b6166be55e183a4e050766
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305080"
---
# <a name="enable-passwordless-security-key-sign-in-for-azure-ad-preview"></a>Povolit přihlašování k neheslovým klíčům zabezpečení pro Azure AD (Preview)

## <a name="requirements"></a>Požadavky

* Azure Multi-Factor Authentication
* Kombinovaná registrace ve verzi Preview s povolenými uživateli pro SSPR
* FIDO2 Security Key Preview vyžaduje kompatibilní klíče zabezpečení FIDO2.
* Operace WebAuthN vyžaduje Microsoft Edge ve Windows 10 verze 1809 nebo vyšší.
* Přihlášení systému Windows založené na FIDO2 vyžaduje Windows 10 připojená k Azure AD verze 1809 nebo vyšší.

## <a name="prepare-devices-for-preview"></a>Příprava zařízení pro verzi Preview

V zařízeních, ve kterých budete pilotní nasazení, musí běžet Windows 10 verze 1809 nebo vyšší. Nejlepší prostředí je ve Windows 10 verze 1903 nebo vyšší.

## <a name="enable-security-keys-for-windows-sign-in"></a>Povolit klíče zabezpečení pro přihlášení k systému Windows

Organizace se můžou rozhodnout použít jednu nebo více následujících metod, které umožňují použití klíčů zabezpečení pro přihlášení k systému Windows.

### <a name="enable-credential-provider-via-intune"></a>Povolení poskytovatele přihlašovacích údajů přes Intune

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte na **Microsoft Intune** > **registrace zařízení registrace** > zařízení s**Windows registrace** > **Windows Hello pro firmy** > –**vlastnosti**.
1. V části **Nastavení** nastavení **použít klíče zabezpečení pro přihlášení** na **povoleno**.

Konfigurace klíčů zabezpečení pro přihlášení nezávisí na konfiguraci Windows Hello pro firmy.

#### <a name="enable-targeted-intune-deployment"></a>Povolit cílené nasazení Intune

Pokud chcete cílit na konkrétní skupiny zařízení a povolit poskytovatele přihlašovacích údajů, použijte následující vlastní nastavení přes Intune. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte na **Microsoft Intune** > **konfigurační** > profilyzařízení > **vytvořit profil**.
1. Nakonfigurujte nový profil s následujícím nastavením.
   1. Název: Klíče zabezpečení pro přihlášení k Windows
   1. Popis: Povolí použití klíčů zabezpečení FIDO během přihlašování Windows.
   1. Platforma: Windows 10 a novější
   1. Typ profilu: Vlastní
   1. Vlastní nastavení OMA-URI:
      1. Název: Zapnout přihlášení k Windows FIDO klíče zabezpečení
      1. OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Datový typ: Integer
      1. Osa 1 
1. Tato zásada se dá přiřadit konkrétním uživatelům, zařízením nebo skupinám. Další informace najdete v článku [přiřazení profilů uživatelů a zařízení v Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Vytvoření vlastní zásady konfigurace zařízení v Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>Povolení poskytovatele přihlašovacích údajů prostřednictvím zřizovacího balíčku

Pro zařízení, která nespravuje služba Intune, je možné nainstalovat zřizovací balíček, který tuto funkci povolí. Aplikaci Windows Configuration Designer můžete nainstalovat z [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Spusťte Windows Configuration Designer.
1. Vyberte **soubor** > **Nový projekt**.
1. Dejte svému projektu název a poznamenejte si cestu, kde je projekt vytvořen.
1. Vyberte **Další**.
1. Nechejte **zřizovací balíček** vybraný jako **vybraný pracovní postup projektu** a vyberte **Další**.
1. V části **Zvolte nastavení, které chcete zobrazit a konfigurovat** a vyberte možnost **Další**, vyberte **všechny edice Windows Desktop** .
1. Vyberte **Finish** (Dokončit).
1. V nově vytvořeném projektu přejděte do >  **nastavení modulu runtime** **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Nastavte **UseSecurityKeyForSignIn** na **povoleno**.
1. Vyberte **exportovat** > **zřizovací balíček** .
1. V okně **sestavení** ponechte výchozí nastavení v části **Popis zřizovacího balíčku** a vyberte **Další**.
1. V okně **sestavení** v části **Vybrat podrobnosti zabezpečení pro zřizovací balíček** ponechte výchozí nastavení a vyberte **Další**.
1. Poznamenejte si nebo změňte cestu v oknech **sestavení** v části **Vyberte místo, kde se má zřizovací balíček uložit** , a vyberte **Další**.
1. Na stránce **sestavení balíčku pro zřizování** vyberte **sestavit** .
1. Uložte dva vytvořené soubory (ppkg a CAT) do umístění, kde je můžete použít na počítače později.
1. Podle pokynů v článku [použití zřizovacího](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)balíčku použijte zřizovací balíček, který jste vytvořili.

## <a name="obtain-fido2-security-keys"></a>Získat klíče zabezpečení FIDO2

Další informace o podporovaných klíčích a výrobci najdete v části FIDO2 Security Keys v článku [co je bez hesla](concept-authentication-passwordless.md) .

> [!NOTE]
> Pokud si koupíte a plánujete použít bezpečnostní klíče založené na NFC, budete potřebovat podporovanou čtečku NFC.

## <a name="enable-passwordless-authentication-method"></a>Povolit metodu ověřování nejenom pro hesla

### <a name="enable-the-combined-registration-experience"></a>Povolit kombinované možnosti registrace

Funkce registrace pro metody ověřování nevyužívající hesla jsou závislé na kombinované registraci ve verzi Preview. Postupujte podle kroků v článku [Povolení registrace kombinovaných zabezpečení (Preview)](howto-registration-mfa-sspr-combined.md), aby se aktivovala kombinovaná registrační verze.

### <a name="enable-new-passwordless-authentication-method"></a>Povolit novou metodu ověřování s nehesly

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Přejít na **Azure Active Directory** > zásadyověřování > metody ověřování **(Preview)**  > 
1. V části jednotlivé **metody**vyberte následující možnosti.
   1. **Povolit** – Ano nebo ne
   1. **Cíl** – všichni uživatelé nebo vybrat uživatele
1. **Uložit** každou metodu

> [!WARNING]
> Zásady omezení klíčů FIDO2 ještě nefungují. Tato funkce bude k dispozici před obecnou dostupností. neměňte prosím tyto zásady ve výchozím nastavení.

> [!NOTE]
> Nemusíte se přihlásit ke oběma metodám bez hesla (Pokud chcete zobrazit jenom jednu metodu bez hesla), můžete povolit jenom tuto metodu). Doporučujeme, abyste si vyzkoušeli obě metody, protože obě mají své výhody.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registrace uživatelů a Správa klíčů zabezpečení FIDO2

1. Přejít na[https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. Přihlásit se, pokud ještě není
1. Kliknout na **zabezpečení – informace**
   1. Pokud má uživatel již alespoň jednu zaregistrovanou metodu Azure Multi-Factor Authentication, může ihned zaregistrovat klíč zabezpečení FIDO2.
   1. Pokud nemají zaregistrovanou alespoň jednu metodu služby Azure Multi-Factor Authentication, musí ji přidat.
1. Přidejte klíč zabezpečení FIDO2, a to tak, že kliknete na **Přidat metodu** a zvolíte **klíč zabezpečení** .
1. Zvolit **zařízení USB** nebo **zařízení NFC**
1. Připravte si klíč a vyberte **Další** .
1. Zobrazí se okno s žádostí o vytvoření nebo zadání kódu PIN pro klíč zabezpečení a potom pro svůj klíč použijte možnost biometrika nebo dotykové ovládání.
1. Vrátíte se do kombinovaného prostředí pro registraci a budete požádáni o poskytnutí smysluplného názvu pro váš token, abyste mohli zjistit, který z nich máte, pokud máte víc. Klikněte na **Další**.
1. Kliknutím na **Hotovo** dokončete proces.

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Správa klíčového bezpečnostního biometriky, PIN kódu nebo resetování klíče zabezpečení

* Windows 10 verze 1809
   * Je nutný doprovodný software od dodavatele bezpečnostního klíče.
* Windows 10 verze 1903 nebo vyšší
   * Uživatelé můžou otevřít **nastavení Windows** na svém zařízení >**klíč zabezpečení** **účty** > .
   * Uživatelé můžou změnit kód PIN, aktualizovat biometrika nebo obnovit svůj bezpečnostní klíč.

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registrace a Správa uživatelů aplikace Microsoft Authenticator

Pokud chcete nakonfigurovat aplikaci Microsoft Authenticator pro přihlašování telefonem, postupujte podle pokynů v článku [přihlášení k vašim účtům pomocí aplikace Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

## <a name="sign-in-with-passwordless-credential"></a>Přihlášení pomocí přihlašovacích údajů bez hesla

### <a name="sign-in-at-the-lock-screen"></a>Přihlaste se na zamykací obrazovce.

V příkladu níže už uživatel Bala Sandhu zřídil svůj bezpečnostní klíč FIDO2. Bala může zvolit poskytovatele pověření bezpečnostního klíče z zamykací obrazovky Windows 10 a vložit bezpečnostní klíč pro přihlášení do Windows.

![Přihlášení k bezpečnostnímu klíči na zamykací obrazovce Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>Přihlášení na webu

V následujícím příkladu už uživatel zřídil svůj bezpečnostní klíč FIDO2. Uživatel se může přihlásit na webu pomocí klíče zabezpečení FIDO2 v prohlížeči Microsoft Edge ve Windows 10 verze 1809 nebo vyšší.

![Přihlášení k bezpečnostnímu klíči Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="known-issues"></a>Známé problémy

### <a name="security-key-provisioning"></a>Zřizování klíče zabezpečení

Ve verzi Public Preview není k dispozici zřízení a zrušení zřizování správců klíčů zabezpečení.

### <a name="hybrid-azure-ad-join"></a>Připojení k hybridní službě Azure AD

Uživatelé, kteří se spoléhají na jednotné přihlašování, které používá spravované přihlašovací údaje, jako jsou FIDO2 zabezpečovací klíče nebo přihlašování bez hesla pomocí aplikace Microsoft Authenticator, potřebují ke hybridnímu připojení ve Windows 10 a získat výhody jednotného přihlašování. Bezpečnostní klíče ale fungují jenom pro teď Azure Active Directory připojené počítače. Doporučujeme, abyste si pro zamykací obrazovku Windows na počítačích s čistým Azure Active Directoryi vyzkoušeli jenom bezpečnostní klíče pro FIDO2. Toto omezení se nevztahuje na web.

### <a name="upn-changes"></a>Změny UPN

Pracujeme na podpoře funkce, která umožňuje změnit hlavní název uživatele (UPN) na hybridních AADJ a AADJ zařízeních. Pokud se změní hlavní název uživatele (UPN), nebudete už moct měnit bezpečnostní klíče FIDO2 k tomuto účtu. Jediným přístupem je resetování zařízení a uživatel se musí znovu zaregistrovat.

## <a name="next-steps"></a>Další postup

[Další informace o registraci zařízení](../devices/overview.md)

[Přečtěte si o Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
