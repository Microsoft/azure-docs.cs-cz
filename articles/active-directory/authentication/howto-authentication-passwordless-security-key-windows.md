---
title: Přihlášení k bezpečnostnímu klíči bez hesla Windows – Azure Active Directory
description: Zjistěte, jak povolit přihlášení klíče zabezpečení bez hesla do služby Azure Active Directory pomocí klíčů zabezpečení FIDO2 (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b8f5d6aaa96c24eb37eb78d237a489f1d25293c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653987"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Povolení přihlášení pomocí Azure Active Directory (preview) pomocí azure active directory (preview)

Tento dokument se zaměřuje na povolení ověřování bez hesla pomocí bezhesla na základě ověřovacích klíčů FIDO2 se zařízeními s Windows 10. Na konci tohoto článku se budete moct přihlásit k vašemu Azure AD i hybridnímu Azure AD, které se připojilo k zařízením s Windows 10 pomocí účtu Azure AD pomocí klíče zabezpečení FIDO2.

|     |
| --- |
| Klíče zabezpečení FIDO2 jsou veřejnou funkcí preview služby Azure Active Directory. Další informace o náhledech najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Požadavky

| Typ zařízení | Připojené k Azure AD | k hybridní službě Azure AD. |
| --- | --- | --- |
| [Azure Multi-Factor Authentication](howto-mfa-getstarted.md) | × | × |
| [Kombinovaný náhled registrace bezpečnostních informací](concept-registration-mfa-sspr-combined.md) | × | × |
| Kompatibilní [bezpečnostní klíče FIDO2](concept-authentication-passwordless.md#fido2-security-keys) | × | × |
| WebAuthN vyžaduje Windows 10 verze 1809 nebo vyšší | × | × |
| [Zařízení připojená k Azure AD](../devices/concept-azure-ad-join.md) vyžadují Windows 10 verze 1903 nebo vyšší | × |   |
| [Hybridní zařízení připojená k Azure AD](../devices/concept-azure-ad-join-hybrid.md) vyžadují Windows 10 Insider Build 18945 nebo vyšší |   | × |
| Plně opravené řadiče domény systému Windows Server 2016/2019. |   | × |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) verze 1.4.32.0 nebo novější |   | × |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (volitelné) | × | × |
| Zřizovací balíček (volitelné) | × | × |
| Zásady skupiny (volitelné) |   | × |

### <a name="unsupported-scenarios"></a>Nepodporované scénáře

Následující scénáře nejsou podporovány:

- Nasazení do domény (pouze místní zařízení) služby Windows Server Active Directory Domain Services (AD DS).
- Scénáře RDP, VDI a Citrix pomocí klíče zabezpečení.
- S/MIME pomocí bezpečnostního klíče.
- "Spustit jako" pomocí bezpečnostního klíče.
- Přihlaste se k serveru pomocí bezpečnostního klíče.
- Pokud jste bezpečnostní klíč k přihlášení k zařízení nepoužili online, nemůžete ho použít k přihlášení nebo odemknutí offline.
- Přihlášení nebo odemknutí zařízení s Windows 10 pomocí bezpečnostního klíče obsahujícího více účtů Azure AD. Tento scénář využívá poslední účet přidaný do klíče zabezpečení. WebAuthN umožňuje uživatelům vybrat si účet, který chtějí používat.
- Odemkněte zařízení s Windows 10 verze 1809. Pro nejlepší zážitek použijte Windows 10 verze 1903 nebo vyšší.

## <a name="prepare-devices-for-preview"></a>Příprava zařízení pro náhled

Azure AD připojen zařízení, která jsou pilotní během náhledu funkce s musí spustit Windows 10 verze 1809 nebo vyšší. Nejlepší zážitek je v systému Windows 10 verze 1903 nebo vyšší.

Hybridní zařízení připojená k Azure AD musí spouštět Windows 10 Insider Build 18945 nebo novější.

## <a name="enable-security-keys-for-windows-sign-in"></a>Povolení klíčů zabezpečení pro přihlášení k Systému Windows

Organizace se mohou rozhodnout použít jednu nebo více z následujících metod, které umožní použití bezpečnostních klíčů pro přihlášení systému Windows na základě požadavků jejich organizace:

- [Povolení pomocí Intune](#enable-with-intune)
- [Cílené nasazení Intune](#targeted-intune-deployment)
- [Povolit pomocí zřizovacího balíčku](#enable-with-a-provisioning-package)
- [Povolit pomocí zásad skupiny (jenom hybridní zařízení Azure AD připojená)](#enable-with-group-policy)

> [!IMPORTANT]
> Organizace s **hybridními zařízeními připojená k Azure AD** musí **také** dokončit kroky v článku [Povolit ověřování FIDO2 místním prostředkům,](howto-authentication-passwordless-security-key-on-premises.md) než funguje ověřování zabezpečovacího klíče Windows 10 FIDO2.
>
> Organizace se **zařízeními s azure ad připojení** to musí udělat před jejich zařízení můžete ověřit na místní prostředky s klíči zabezpečení FIDO2.

### <a name="enable-with-intune"></a>Povolení pomocí Intune

Chcete-li povolit použití bezpečnostních klíčů pomocí Intune, proveďte následující kroky:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte na **registraci Microsoft Intune** > **Device Windows** > **enrollment** > **Windows Hello for Business** > **Properties**.
1. V části **Nastavení**nastavte **použít klíče zabezpečení pro přihlášení** k **možnosti Povoleno**.

Konfigurace klíčů zabezpečení pro přihlášení není závislá na konfiguraci Windows Hello pro firmy.

### <a name="targeted-intune-deployment"></a>Cílené nasazení Intune

Chcete-li cílit na konkrétní skupiny zařízení, abyste povolili zprostředkovatele pověření, použijte pomocí Intune následující vlastní nastavení:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte na profil**konfigurace** > zařízení **Microsoft Intune** > **Vytvořte** > **profil**.
1. Nakonfigurujte nový profil s následujícím nastavením:
   - Název: Bezpečnostní klíče pro přihlášení k systému Windows
   - Popis: Povolí použití bezpečnostních klíčů FIDO při přihlášení k systému Windows.
   - Platforma: Windows 10 a novější
   - Typ profilu: Vlastní
   - Vlastní nastavení oma-uri:
      - Název: Zapnutí bezpečnostních klíčů FIDO pro přihlášení k systému Windows
      - OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Datový typ: Celé číslo
      - Hodnota: 1
1. Tuto zásadu lze přiřadit konkrétním uživatelům, zařízením nebo skupinám. Další informace najdete [v tématu Přiřazení profilů uživatelů a zařízení v Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Vytvoření zásad konfigurace vlastního zařízení Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Povolit pomocí zřizovacího balíčku

Pro zařízení, která není spravována Intune, lze nainstalovat zřizovací balíček, který povolí funkci. Aplikaci Windows Configuration Designer lze nainstalovat z [obchodu Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22). K vytvoření zřizovacího balíčku postupujte podle následujících kroků:

1. Spusťte Návrhář konfigurace systému Windows.
1. Vyberte možnost Nový > **projekt** **souboru**.
1. Pojmenujte projekt a poznamenejte si cestu, kde je projekt vytvořen, a pak vyberte **Další**.
1. Ponechat *balíček zřizování* vybraný jako **pracovní postup Vybraného projektu** a vybrat **další**.
1. V části Vyberte *Všechny edice windows ových ploch* v části **Vyberte nastavení, která chcete zobrazit a konfigurovat**, a pak vyberte **Další**.
1. Vyberte **Finish** (Dokončit).
1. V nově vytvořeném projektu přejděte na **nastavení** > runtime**WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Nastavte **funkci UseSecurityKeyForSignIn** na *povolenou*.
1. Vybrat **Export** > **balíček Export Provisioning**
1. Výchozí hodnoty ponechejte v okně **Sestavení** v části **Popište balíček zřizování**a pak vyberte **Další**.
1. Výchozí hodnoty ponechejte v okně **Sestavení** v **části Vyberte podrobnosti zabezpečení pro zřizovací balíček** a vyberte **Další**.
1. Poznamenejte si nebo změňte cestu v oknech **sestavení** v části **Vyberte, kam chcete uložit zřizovací balíček** a vyberte **Další**.
1. Vyberte **Sestavení** na stránce **Sestavení balíčku zřizování.**
1. Uložte dva vytvořené soubory *(ppkg* a *cat)* do umístění, kde je můžete později použít na stroje.
1. Chcete-li použít balíček zřizování, který jste vytvořili, najdete [v tématu Použití balíčku zřizování](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package).

> [!NOTE]
> Zařízení se systémem Windows 10 verze 1809 musí také povolit režim sdíleného počítače *(EnableSharedPCMode*). Další informace o povolení této funkce najdete [v tématu Nastavení sdíleného nebo hostovaného počítače s Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Povolit pomocí zásad skupiny

Pro **hybridní zařízení připojená k Azure AD**mohou organizace nakonfigurovat následující nastavení zásad skupiny, aby povolily přihlášení pomocí klíče zabezpečení FIDO. Toto nastavení najdete v části Systémové**přihlášení** > **System** > ke**složce** >  **Konfigurace** > počítače**Zapnutí přihlášení pomocí klíče zabezpečení**:

- Nastavení této zásady na **povoleno** umožňuje uživatelům přihlásit se pomocí bezpečnostních klíčů.
- Nastavení této zásady na **Zakázané** nebo **Nenakonfigurované** zabrání uživatelům v přihlášení pomocí bezpečnostních klíčů.

Toto nastavení zásad skupiny `credentialprovider.admx` vyžaduje aktualizovanou verzi šablony zásad skupiny. Tato nová šablona je k dispozici s další verzí systému Windows Server a se systémem Windows 10 20H1. Toto nastavení lze spravovat pomocí zařízení s jednou z těchto novějších verzí systému Windows nebo centrálně podle pokynů v tématu podpory Jak vytvořit a spravovat šablony správy [zásad skupiny Central Store pro šablony zásad skupiny v systému Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Přihlášení pomocí bezpečnostního klíče FIDO2

V následujícím příkladu uživatel s názvem Bala Sandhu již zřídit svůj klíč zabezpečení FIDO2 pomocí kroků v předchozím článku [Povolit zabezpečení bez hesla klíč přihlášení](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). U hybridních zařízení azure ad připojená k místním prostředkům se ujistěte, že jste také [povolili přihlášení pomocí klíče zabezpečení bez hesla k místním prostředkům](howto-authentication-passwordless-security-key-on-premises.md). Bala si může vybrat poskytovatele pověření bezpečnostního klíče ze zamykací obrazovky Windows 10 a vložit bezpečnostní klíč pro přihlášení do Windows.

![Přihlášení pomocí bezpečnostního klíče na zamykací obrazovce Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Správa biometrického klíče zabezpečení, kódu PIN nebo resetování bezpečnostního klíče

* Windows 10 verze 1903 nebo vyšší
   * Uživatelé mohou na svém zařízení otevřít **nastavení systému Windows** > **klíč** > **zabezpečení** účtů
   * Uživatelé mohou změnit svůj KÓD PIN, aktualizovat biometrické údaje nebo resetovat bezpečnostní klíč

## <a name="troubleshooting-and-feedback"></a>Poradce při potížích a zpětná vazba

Pokud chcete při náhledu této funkce sdílet zpětnou vazbu nebo se s nimi setkat, sdílejte je prostřednictvím aplikace Windows Feedback Hub pomocí následujících kroků:

1. Spusťte **Centrum Feedback hub** a ujistěte se, že jste přihlášeni.
1. Odeslat zpětnou vazbu v rámci následující kategorizace:
   - Kategorie: Bezpečnost a ochrana osobních údajů
   - Podkategorie: FIDO
1. Chcete-li zachytit protokoly, použijte možnost **znovu vytvořit můj problém**

## <a name="next-steps"></a>Další kroky

[Povolení přístupu k místním prostředkům pro zařízení Azure AD a hybridní zařízení připojená k Azure AD](howto-authentication-passwordless-security-key-on-premises.md)

[Další informace o registraci zařízení](../devices/overview.md)

[Další informace o vícefaktorovém ověřování Azure](../authentication/howto-mfa-getstarted.md)
