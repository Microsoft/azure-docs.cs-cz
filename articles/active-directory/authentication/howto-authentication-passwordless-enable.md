---
title: Konfigurace Azure Active Directory passwordless přihlášení (preview)
description: Povolit passwordless přihlášení ke službě Azure AD pomocí klíčů zabezpečení FIDO2 nebo aplikace Microsoft Authenticator (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ba2545467ebfbd032408aeee25b82b92a628f2a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712069"
---
# <a name="enable-passwordless-sign-in-for-azure-ad-preview"></a>Povolit passwordless přihlášení pro službu Azure AD (preview)

## <a name="requirements"></a>Požadavky

* Azure Multi-Factor Authentication
* Kombinované registraci ve verzi preview
* Vyžaduje kompatibilní klíčů zabezpečení FIDO2 FIDO2 zabezpečení klíčů ve verzi preview
* WebAuthN vyžaduje Microsoft Edge ve Windows 10 verze 1809 nebo vyšší
* Přihlášení založené na Windows vyžaduje Azure AD FIDO2 připojený k Windows 10 verze 1809 nebo vyšší

## <a name="prepare-devices-for-preview"></a>Příprava zařízení pro verzi preview

Zařízení, která bude mít pilotní nasazení s musí být spuštěn Windows 10 verze 1809 nebo vyšší. Dosažení co nejlepších výsledků je ve Windows 10 verze 1903 nebo vyšší.

## <a name="enable-security-keys-for-windows-sign-in"></a>Povolit zabezpečení klíčů pro Windows přihlášení

Organizace se mohou rozhodnout pro použití jedné nebo více z následujících metod pro povolení použití klíčů zabezpečení pro Windows přihlášení.

### <a name="enable-credential-provider-via-intune"></a>Povolit poskytovatele přihlašovacích údajů prostřednictvím Intune

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do **Microsoft Intune** > **registrace zařízení** > **registrace Windows** > **Windows Hello pro firmy** > **vlastnosti**.
1. V části **nastavení** nastavit **pomocí kláves se zabezpečení pro přihlášení do** k **povoleno**.

Konfigurace klíčů zabezpečení pro přihlášení, není závislá na konfiguraci Windows Hello pro firmy.

#### <a name="enable-targeted-intune-deployment"></a>Povolit cíleného nasazení Intune

Cílit na konkrétním skupinám zařízení chcete povolit poskytovatele přihlašovacích údajů, použijte následující vlastní nastavení prostřednictvím Intune. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do **Microsoft Intune** > **konfigurace zařízení** > **profily** > **vytvořit profil**.
1. Nový profil konfigurace s následujícími nastaveními
   1. Název: Zabezpečení klíčů pro přihlášení k Windows
   1. Popis: Umožňuje klíčů zabezpečení FIDO se použije při přihlášení k Windows
   1. Platforma: Windows 10 a novější
   1. Typ platformy: Vlastní
   1. Nastavení vlastní OMA-URI:
      1. Název: Zapnout klíčů zabezpečení FIDO pro přihlášení k Windows
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Datový typ: Integer
      1. Hodnota: 1 
1. Tuto zásadu lze přiřadit pro konkrétní uživatele, zařízení nebo skupiny. Další informace najdete v článku [přiřazení profilů uživatelů a zařízení v Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Vytvoření zásad konfigurace Intune vlastní zařízení](./media/howto-authentication-passwordless-enable/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>Povolit poskytovatele přihlašovacích údajů prostřednictvím zřizovací balíček

Pro zařízení nespravuje Intune můžete nainstalovat zřizovací balíček k povolení funkcí. Aplikaci Windows Configuration Designer lze nainstalovat z [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Spusťte Windows Configuration Designer.
1. Vyberte **souboru** > **nový projekt**.
1. Pojmenujte svůj projekt a poznamenejte si cesta kde se váš projekt vytvoří.
1. Vyberte **Další**.
1. Ponechte **zřizování balíček** vybrané jako **vybraný projekt pracovního postupu** a vyberte **Další**.
1. Vyberte **klasické pracovní plochy Windows všechny edice** pod **zvolte nastavení, které chcete zobrazit a konfigurovat** a vyberte **Další**.
1. Vyberte **Finish** (Dokončit).
1. V nově vytvořený projekt, přejděte do **nastavení běhového prostředí** > **WindowsHelloForBusiness** > **SecurityKeys**  >  **UseSecurityKeyForSignIn**.
1. Nastavte **UseSecurityKeyForSignIn** k **povolené**.
1. Vyberte **exportovat** > **zřizování balíčku**
1. Ponechte výchozí nastavení v **sestavení** okně v části **popisují zřizovací balíček** a vyberte **Další**.
1. Ponechte výchozí nastavení v **sestavení** okně v části **vyberte podrobnosti o zabezpečení pro zřizovací balíček** a vyberte **Další**.
1. Poznamenejte si hodnotu nebo změnit cestu v **sestavení** windows v rámci **určete, kam chcete uložit zřizovací balíček** a vyberte **Další**.
1. Vyberte **sestavení** na **vytvoření zřizovacího balíčku** stránky.
1. Dva soubory vytvořené (ppkg a cat) uložte do umístění, kde je lze následně použít na počítače později.
1. Postupujte podle pokynů v článku [aplikování zřizovacího balíčku](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package), aby se zavedly zřizovací balíček, který jste vytvořili.

## <a name="obtain-fido2-security-keys"></a>Získání klíčů FIDO2 zabezpečení

Najdete v článku v části FIDO2 klíčů zabezpečení, [novinky passwordless?](concept-authentication-passwordless.md) Další informace o podporovaných klíče a výrobce.

> [!NOTE]
> Pokud zakoupíte a plánujete používat klíče zabezpečení NFC na základě budete potřebovat podporovanou čtečky NFC.

## <a name="enable-passwordless-authentication-methods"></a>Povolit metody passwordless ověřování

### <a name="enable-the-combined-registration-experience"></a>Povolit prostředí kombinovaná registrace

Registrace funkce pro zabezpečení klíčů FIDO2 spoléhají na kombinované registraci ve verzi preview. Postupem uvedeným níže povolte kombinované registraci ve verzi preview.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Přejděte do **Azure Active Directory** > **uživatelská nastavení**
   1. Klikněte na **spravovat nastavení pro funkce ve verzi preview panel přístupu**
   1. V části **uživatelé můžou používat funkce verze preview pro registraci a správu bezpečnostních údajů - rozšířeného**.
      1. Zvolte **vybrané** a vyberte skupinu uživatelů, kteří budou účast ve verzi preview.
      1. Nebo zvolte **všechny** povolit pro všechny uživatele ve vašem adresáři.
1. Klikněte na **Uložit**.

### <a name="enable-new-passwordless-authentication-methods"></a>Povolit nové metody passwordless ověřování

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Přejděte do **Azure Active Directory** > **metody ověřování** > **zásad ověřování – metoda (Preview)**
1. U každého **metoda**, vyberte následující možnosti
   1. **Povolit** – Ano nebo ne
   1. **Cíl** – všechny uživatele nebo vybrat uživatele
1. **Uložit** každý – metoda

> [!WARNING]
> FIDO2 "Klíč zásady omezení", nebudou fungovat ještě. Tato funkce bude k dispozici před obecnou dostupnost, neměňte tyto zásady z výchozí hodnoty.

> [!NOTE]
> Není nutné vyjádřit výslovný souhlas pro obě metody passwordless (Pokud chcete zobrazit náhled jenom jedna metoda passwordless, můžete povolit pouze metody). Doporučujeme vám vyzkoušet obě metody, protože obě mají své vlastní výhody.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registrace uživatelů a správu FIDO2 zabezpečení klíčů

1. Přejděte na [https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. Podepsat Nepřihlášený
1. Klikněte na tlačítko **bezpečnostní údaje**
   1. Pokud uživatel už má alespoň jednu metodu ověřování Azure Multi-Factor Authentication zaregistrované, aby se okamžitě zaregistrovali FIDO2 bezpečnostní klíč.
   1. Pokud nemají alespoň jednu metodu ověřování Azure Multi-Factor Authentication zaregistrovaný, že musíte přidat.
1. Kliknutím na Přidat klíč zabezpečení FIDO2 **přidejte metodu** a zvolíte **klíč zabezpečení**
1. Zvolte **zařízení USB** nebo **zařízení přes NFC**
1. Klíč připravené a zvolte možnost máte **další**
1. Pole se zobrazí a vás vyzve k vytvoření nebo zadání kódu PIN pro klíč zabezpečení, a poté proveďte požadované gesta pro klíč buď biometrika nebo dotykového ovládání.
1. Můžete se vrátit do prostředí kombinovaná registrace a vyzváni k zadání smysluplný název tokenu, který z nich mohli identifikovat, pokud máte více. Klikněte na **Další**.
1. Klikněte na tlačítko **provádí** dokončete proces

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Správa zabezpečení klíče biometrické, PIN kód, nebo obnovit klíč zabezpečení

* Windows 10 version 1809
   * Doprovodná softwaru od dodavatele zabezpečení klíčů je povinný
* Windows 10 verze 1903 nebo vyšší
   * Uživatelé můžou otevírat **nastavení Windows** na svém zařízení > **účty** > **klíč zabezpečení**
   * Uživatelé mohou změnit kód PIN, aktualizovat biometrika nebo obnovit svůj klíč zabezpečení

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Registrace uživatele a Správa aplikace Microsoft Authenticator

Pokud chcete nakonfigurovat aplikace Microsoft Authenticator pro přihlášení telefonem, postupujte podle pokynů v článku [přihlášení k účtům pomocí aplikace Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

## <a name="sign-in-with-passwordless-credentials"></a>Přihlaste se pomocí přihlašovacích údajů passwordless

### <a name="sign-in-at-the-lock-screen"></a>Přihlaste se na zamykací obrazovce

V příkladu níže uživatele Bala Sandhu zřídila už jejich FIDO2 klíč zabezpečení. Bala můžete vybrat zprostředkovatele zabezpečení přihlašovací údaje klíče ze zamykací obrazovky Windows 10 a vložte klíč zabezpečení k přihlašování do Windows.

![Klíč zabezpečení přihlášení na zamykací obrazovce Windows 10](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>Přihlaste se na webu

V příkladu níže uživatel už zřídil jejich FIDO2 klíč zabezpečení. Uživatel můžete k přihlášení na webu s jejich FIDO2 zabezpečení klíče uvnitř prohlížeč Microsoft Edge ve Windows 10 verze 1809 nebo vyšší.

![Zabezpečení klíčů přihlášení Microsoft Edge](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-edge-sign-in.png)

Informace o přihlášení pomocí aplikace Microsoft Authenticator najdete v článku, [přihlášení k účtům pomocí aplikace Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

## <a name="known-issues"></a>Známé problémy

### <a name="fido2-security-keys"></a>FIDO2 zabezpečení klíčů

#### <a name="security-key-provisioning"></a>Zabezpečení klíčů zřizování

Správce zřizování a zrušení zřizování zabezpečení klíčů není k dispozici ve verzi public preview.

#### <a name="hybrid-azure-ad-join"></a>Hybridní připojení k Azure AD

Spoléhat na WIA jednotné přihlašování uživatelů, kteří používají spravované přihlašovací údaje, jako jsou klíče FIDO2 zabezpečení nebo passwordless Přihlaste se pomocí aplikace Microsoft Authenticator musí pro hybridní připojení ve Windows 10, získáte výhody jednotného přihlašování. Zabezpečení klíčů však pouze práce pro Azure Active Directory počítače připojené k teď. Doporučujeme, abyste že si jenom vyzkoušet FIDO2 zabezpečení klíčů pro zamykací obrazovce Windows na čistě Azure Active Directory počítače připojené k. Toto omezení neplatí pro web.

#### <a name="upn-changes"></a>Změny hlavního názvu uživatele

Pracujeme na podpoře funkce, která umožňuje změnit hlavní název uživatele v hybridní AADJ a AADJ zařízení. Pokud se změní uživatele (UPN), můžete upravit už FIDO2 zabezpečení klíče pro účet, který. To je ale jediný možný přístup k resetování zařízení a uživatel musí znovu zaregistrovat.

### <a name="authenticator-app"></a>Aplikace Authenticator

#### <a name="ad-fs-integration"></a>Integrace služby AD FS

Když uživatel povolí passwordless přihlašovací údaje Microsoft Authenticator, ověřování pro tohoto uživatele bude vždy ve výchozím nastavení odesílání oznámení o schválení. Tuto logiku zabraňuje uživatelům v tenantovi hybridní do AD FS pro ověření přihlášení uživatele, s ohledem na další krok směrovat do příslušných klikněte na tlačítko "Místo toho použít heslo." Tento proces bude také vynechat všechny místní zásady podmíněného přístupu a toky předávací ověřování. Výjimkou z tohoto procesu je login_hint je-li zadán, uživatel bude automaticky se službou AD FS a vynechat možnost použití passwordless přihlašovacích údajů.

#### <a name="azure-mfa-server"></a>Azure MFA serveru

Koncoví uživatelé, kteří jsou povoleny pro vícefaktorové ověřování pomocí serveru Azure MFA v místním organizace stále můžete vytvořit a použít jediné passwordless telefonní přihlašovací pověření. Pokud uživatel pokusí o upgrade více instalací (5 +) s pověřením Microsoft Authenticator, tato změna může vést k chybě.  

#### <a name="device-registration"></a>Registrace zařízení

Jednou z požadavky na vytvoření této nové silné přihlašovací údaje, je, že registrace zařízení, ve kterém se nachází v rámci tenanta Azure AD pro jednotlivé uživatele. Z důvodu omezení registrace zařízení můžete zařízení registrovat jenom v jednom tenantovi. Toto omezení znamená, že je možné povolit jenom jeden pracovní nebo školní účet v aplikaci Microsoft Authenticator pro přihlášení telefonem.

## <a name="next-steps"></a>Další postup

[Další informace o registraci zařízení](../devices/overview.md)

[Další informace o službě Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
