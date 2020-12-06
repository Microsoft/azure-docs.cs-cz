---
title: Přihlášení k bezpečnostnímu klíči s nehesly do Windows – Azure Active Directory
description: Naučte se, jak povolit přihlašování k bezpečnostnímu klíči s neFIDO2mi heslem pro Azure Active Directory pomocí klíčů zabezpečení (Preview).
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/24/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04a46a691b2f629b64cfe09c22813b05c593af1c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743458"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Povolení přihlášení k bezpečnostnímu klíči bez hesla pro zařízení s Windows 10 pomocí Azure Active Directory (Preview)

Tento dokument se zaměřuje na povolení ověřování bez hesla založeného na klíčích zabezpečení FIDO2 u zařízení s Windows 10. Na konci tohoto článku se budete moct přihlásit k Azure AD i k hybridním zařízením s Windows 10 připojeným k Azure AD pomocí svého účtu služby Azure AD s použitím bezpečnostního klíče FIDO2.

> [!NOTE]
> Bezpečnostní klíče FIDO2 jsou funkcí veřejné verze Preview Azure Active Directory. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Požadavky

| Typ zařízení | Připojené k Azure AD | k hybridní službě Azure AD. |
| --- | --- | --- |
| [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [Souhrnná registrace informací o zabezpečení ve verzi Preview](concept-registration-mfa-sspr-combined.md) | X | X |
| Kompatibilní [klíče zabezpečení FIDO2](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| Operace WebAuthN vyžaduje Windows 10 verze 1903 nebo vyšší. | X | X |
| [Zařízení připojená k Azure AD](../devices/concept-azure-ad-join.md) vyžadují Windows 10 verze 1909 nebo vyšší. | X |   |
| [Zařízení připojená k hybridní službě Azure AD](../devices/concept-azure-ad-join-hybrid.md) vyžadují Windows 10 verze 2004 nebo vyšší. |   | X |
| Plně opravené řadiče domény se systémem Windows Server 2016/2019. |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) verze 1.4.32.0 nebo novější |   | X |
| [Microsoft Intune](/intune/fundamentals/what-is-intune) (volitelné) | X | X |
| Zřizovací balíček (volitelné) | X | X |
| Zásady skupiny (volitelné) |   | X |

### <a name="unsupported-scenarios"></a>Nepodporované scénáře

Následující scénáře nejsou podporovány:

- Nasazení Windows serveru Active Directory Domain Services (služba AD DS) připojeného k doméně (jenom místní zařízení).
- Scénáře RDP, VDI a Citrix využívají bezpečnostní klíč.
- S/MIME používá bezpečnostní klíč.
- "Spustit jako" pomocí bezpečnostního klíče.
- Přihlaste se k serveru pomocí bezpečnostního klíče.
- Pokud jste svůj bezpečnostní klíč nepoužili k přihlášení k zařízení v online režimu, nemůžete ho použít k přihlášení nebo odemknutí offline.
- Přihlášení nebo odemknutí zařízení s Windows 10 klíčem zabezpečení, který obsahuje několik účtů Azure AD. Tento scénář využívá Poslední účet přidaný do klíče zabezpečení. Operace WebAuthN umožňuje uživatelům zvolit účet, který chtějí použít.
- Odemkněte zařízení s Windows 10 verze 1809. Pro dosažení optimálního prostředí použijte Windows 10 verze 1903 nebo vyšší.

## <a name="prepare-devices-for-preview"></a>Příprava zařízení pro verzi Preview

Zařízení připojená ke službě Azure AD, která vytváříte za verzi Preview funkce, musí používat Windows 10 verze 1909 nebo vyšší.

Hybridní zařízení připojená k Azure AD musí používat Windows 10 verze 2004 nebo novější.

## <a name="enable-security-keys-for-windows-sign-in"></a>Povolit klíče zabezpečení pro přihlášení k Windows

Organizace se můžou rozhodnout použít jednu nebo více následujících metod k povolení použití klíčů zabezpečení pro přihlášení Windows na základě požadavků organizace:

- [Povolit s Intune](#enable-with-intune)
- [Cílené nasazení Intune](#targeted-intune-deployment)
- [Povolit pomocí zřizovacího balíčku](#enable-with-a-provisioning-package)
- [Povolit s Zásady skupiny (jenom zařízení připojená k hybridní službě Azure AD)](#enable-with-group-policy)

> [!IMPORTANT]
> Organizace s **hybridními zařízeními připojenými k Azure AD** musí **také** provést kroky v tomto článku, [Povolit ověřování FIDO2 pro místní prostředky](howto-authentication-passwordless-security-key-on-premises.md) , než funguje ověřování pomocí klíče zabezpečení Windows 10 FIDO2.
>
> Organizace se **zařízeními připojenými k Azure AD** musí tuto funkci provést předtím, než se jejich zařízení bude moci ověřit u místních prostředků pomocí klíčů zabezpečení FIDO2.

### <a name="enable-with-intune"></a>Povolit s Intune

Pokud chcete povolit použití klíčů zabezpečení pomocí Intune, proveďte následující kroky:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Přejděte na **Microsoft Intune**  >  **registrace zařízení registrace zařízení** s  >  **Windows registrace**  >  **Windows Hello pro firmy**–  >  **vlastnosti**.
1. V části **Nastavení** nastavte **možnost použít klíče zabezpečení pro přihlášení** a **Povolit**.

Konfigurace klíčů zabezpečení pro přihlášení nezávisí na konfiguraci Windows Hello pro firmy.

### <a name="targeted-intune-deployment"></a>Cílené nasazení Intune

Pokud chcete cílit na konkrétní skupiny zařízení a povolit poskytovatele přihlašovacích údajů, použijte následující vlastní nastavení přes Intune:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Přejděte na **Microsoft Intune**  >  **konfigurační**  >  **profily** zařízení  >  **vytvořit profil**.
1. Nakonfigurujte nový profil s následujícím nastavením:
   - Název: bezpečnostní klíče pro Windows Sign-In
   - Popis: povolí použití klíčů zabezpečení FIDO během přihlašování Windows.
   - Platforma: Windows 10 a novější
   - Typ profilu: vlastní
   - Vlastní nastavení OMA-URI:
      - Název: Zapněte FIDO klíče zabezpečení pro Windows Sign-In
      - OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Datový typ: celé číslo
      - Hodnota: 1
1. Tato zásada se dá přiřadit konkrétním uživatelům, zařízením nebo skupinám. Další informace najdete v tématu [přiřazení profilů uživatelů a zařízení v Microsoft Intune](/intune/device-profile-assign).

![Vytvoření vlastní zásady konfigurace zařízení v Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Povolit pomocí zřizovacího balíčku

Pro zařízení, která nespravuje služba Intune, je možné nainstalovat zřizovací balíček, který tuto funkci povolí. Aplikaci Windows Configuration Designer můžete nainstalovat z [Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22). Provedením následujících kroků vytvořte zřizovací balíček:

1. Spusťte Windows Configuration Designer.
1. Vyberte **soubor**  >  **Nový projekt**.
1. Dejte vašemu projektu název a poznamenejte si cestu, kde je projekt vytvořený, a pak vyberte **Další**.
1. Nechejte *zřizovací balíček* vybraný jako **vybraný pracovní postup projektu** a vyberte **Další**.
1. V části **Zvolte nastavení, které chcete zobrazit a konfigurovat**, vyberte *všechny edice Windows desktopu* a pak vyberte **Další**.
1. Vyberte **Dokončit**.
1. V nově vytvořeném projektu přejděte do **nastavení modulu runtime**  >  **WindowsHelloForBusiness**  >  **SecurityKeys**  >  **UseSecurityKeyForSignIn**.
1. Nastavte **UseSecurityKeyForSignIn** na *povoleno*.
1. Vyberte **exportovat**  >  **zřizovací balíček** .
1. V okně **sestavení** ponechte výchozí nastavení v části **Popis zřizovacího balíčku** a pak vyberte **Další**.
1. V okně **sestavení** v části **Vybrat podrobnosti zabezpečení pro zřizovací balíček** ponechte výchozí nastavení a vyberte **Další**.
1. Poznamenejte si nebo změňte cestu v oknech **sestavení** v části **Vyberte místo, kde se má zřizovací balíček uložit** , a vyberte **Další**.
1. Na stránce **sestavení balíčku pro zřizování** vyberte **sestavit** .
1. Uložte dva vytvořené soubory (*ppkg* a *Cat*) do umístění, kde je můžete použít na počítače později.
1. Pokud chcete použít zřizovací balíček, který jste vytvořili, přečtěte si téma [použití zřizovacího balíčku](/windows/configuration/provisioning-packages/provisioning-apply-package).

> [!NOTE]
> Zařízení se systémem Windows 10 verze 1903 musí umožňovat také režim sdíleného osobního počítače (*EnableSharedPCMode*). Další informace o povolení této funkce najdete v tématu [nastavení sdíleného nebo hostovaného počítače s Windows 10](/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Povolit s Zásady skupiny

Pro **zařízení připojená k hybridní službě Azure AD** můžou organizace nakonfigurovat následující nastavení zásady skupiny, aby se povolilo přihlášení k bezpečnostnímu klíči Fido. Toto nastavení najdete v části **Konfigurace počítače**  >  **šablony pro správu** přihlášení  >  **System**  >  **Logon**  >  **k nástroji zapnout bezpečnostní klíč**:

- Nastavením této zásady na **povoleno** umožňuje uživatelům přihlašovat se pomocí bezpečnostních klíčů.
- Nastavení této zásady na **zakázáno** nebo **Nenakonfigurováno** zabrání uživatelům v přihlašování pomocí bezpečnostních klíčů.

Toto nastavení Zásady skupiny vyžaduje aktualizovanou verzi `CredentialProviders.admx` šablony Zásady skupiny. Tato nová šablona je k dispozici v další verzi Windows serveru a ve Windows 10 20H1. Toto nastavení lze spravovat pomocí zařízení s jednou z těchto novějších verzí systému Windows nebo centrálně podle pokynů v tématu Podpora, [jak vytvořit a spravovat centrální úložiště pro Zásady skupiny šablony pro správu ve Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Přihlaste se pomocí bezpečnostního klíče FIDO2.

V níže uvedeném příkladu již uživatel s názvem Bala Sandhu zřídil svůj klíč zabezpečení FIDO2 pomocí postupu v předchozím článku, který [umožňuje přihlášení k neheslovým klíčům zabezpečení bez hesla](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). U zařízení připojených k hybridní službě Azure AD se ujistěte, že jste taky [povolili přihlášení k bezpečnostnímu klíči bez hesla k místním prostředkům](howto-authentication-passwordless-security-key-on-premises.md). Bala může zvolit poskytovatele pověření bezpečnostního klíče z zamykací obrazovky Windows 10 a vložit bezpečnostní klíč pro přihlášení do Windows.

![Přihlášení k bezpečnostnímu klíči na zamykací obrazovce Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Správa klíčového bezpečnostního biometriky, PIN kódu nebo resetování klíče zabezpečení

* Windows 10 verze 1903 nebo vyšší
   * Uživatelé můžou otevřít **nastavení Windows** na svém zařízení > **Accounts**  >  **klíč zabezpečení** účty.
   * Uživatelé můžou změnit kód PIN, aktualizovat biometrika nebo obnovit svůj bezpečnostní klíč.

## <a name="troubleshooting-and-feedback"></a>Řešení potíží a zpětná vazba

Pokud byste chtěli sdílet zpětnou vazbu nebo narazit na problémy při zobrazení náhledu této funkce, sdílejte ji přes aplikaci Windows Feedback Center pomocí následujících kroků:

1. Spusťte **Centrum zpětné vazby** a ujistěte se, že jste přihlášení.
1. Odeslat názor v rámci následující kategorizace:
   - Kategorie: zabezpečení a ochrana osobních údajů
   - Subcategory: FIDO
1. Pokud chcete zaznamenávat protokoly, použijte možnost pro **opětovné vytvoření problému** .

## <a name="next-steps"></a>Další kroky

[Povolení přístupu k místním prostředkům pro zařízení připojená k Azure AD a k hybridním zařízením připojeným k Azure AD](howto-authentication-passwordless-security-key-on-premises.md)

[Další informace o registraci zařízení](../devices/overview.md)

[Další informace o Multi-Factor Authentication Azure AD](../authentication/howto-mfa-getstarted.md)