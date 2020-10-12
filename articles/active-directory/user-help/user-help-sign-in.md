---
title: Přihlaste se pomocí informací o ověřování identity – Azure AD
description: Přečtěte si, jak se přihlašovat pomocí různých metod ověřování identity v informacích o zabezpečení.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.custom: user-help
ms.openlocfilehash: 42ced6632ebfa56af8fe13a02f531b9835e13df3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88799379"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Přihlášení pomocí dvoustupňového ověřování nebo bezpečnostních údajů

Až nastavíte dvoustupňové ověřování nebo bezpečnostní údaje, budete se moct přihlásit ke svému účtu pomocí zadané metody ověřování.

> [!Note]
> Pokud stále používáte dvoustupňové ověřování, budete muset nastavit metody ověřování podle pokynů v článku [Nastavení mého účtu pro dvoustupňové ověření](multi-factor-authentication-end-user-first-time.md) v článku.
>
> Pokud správce zapnul prostředí informací o zabezpečení, budete muset pomocí těchto podrobných článků nastavit metody ověřování:<ul><li>[Nastavení bezpečnostních údajů pro použití ověřovací aplikace](security-info-setup-auth-app.md)</li><li>[Nastavení bezpečnostních údajů pro používání zasílání textových zpráv](security-info-setup-text-msg.md)</li><li>[Nastavení bezpečnostních údajů pro použití telefonního hovoru](security-info-setup-phone-number.md)</li><li>[Nastavení bezpečnostních údajů pro použití bezpečnostního klíče](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Přihlaste se pomocí oznámení ověřovací aplikace na mobilním zařízení.

1. Přihlaste se ke svému účtu pomocí uživatelského jména a hesla.

2. Vyberte **schválit** z oznámení o schválení odeslaného na vaše mobilní zařízení.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Přihlaste se pomocí kódu aplikace ověřovatele na mobilním zařízení.

1. Přihlaste se ke svému účtu pomocí uživatelského jména a hesla.

2. Otevřete aplikaci ověřovatele a do pole **zadat kód** zadejte náhodně generovaný kód pro svůj účet.

## <a name="sign-in-using-your-phone-number"></a>Přihlaste se pomocí svého telefonního čísla.

1. Přihlaste se ke svému účtu pomocí uživatelského jména a hesla.

2. Odpovězte na svůj telefon a postupujte podle pokynů.

## <a name="sign-in-using-a-text-message"></a>Přihlášení pomocí textové zprávy

1. Přihlaste se ke svému účtu pomocí uživatelského jména a hesla.

2. Otevřete textovou zprávu a zadejte kód z textové zprávy do pole **Zadejte kód** .

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>Přihlášení pomocí bezpečnostního klíče na zamykací obrazovce

1. Po zaregistrování klíče zabezpečení vyberte z zamykací obrazovky Windows 10 obrázek klíče zabezpečení.

2. Vložte svůj bezpečnostní klíč do portu USB vašeho zařízení a přihlaste se k Windows pomocí PIN kódu bezpečnostního klíče.

    ![Přihlášení k bezpečnostnímu klíči na zamykací obrazovce Windows 10](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>Přihlaste se pomocí bezpečnostního klíče a prohlížeče Microsoft Edge.

1. Po zaregistrování klíče zabezpečení otevřete prohlížeč Microsoft Edge.

2. Po zobrazení výzvy k přihlášení vložte svůj bezpečnostní klíč do portu USB zařízení a přihlaste se k Windows pomocí PIN kódu bezpečnostního klíče.

    ![Přihlášení k bezpečnostnímu klíči pomocí prohlížeče Microsoft Edge](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >Informace o přihlášení pomocí aplikace Microsoft Authenticator najdete v článku o [přihlášení k účtům pomocí Microsoft Authenticator aplikace](user-help-auth-app-sign-in.md).

## <a name="sign-in-using-another-verification-method"></a>Přihlaste se pomocí jiné metody ověřování.

Pokud z nějakého důvodu nemůžete použít vaši primární metodu přihlašování, můžete použít jinou metodu ověření, kterou jste dříve nastavili.

1. Přihlaste se ke svému účtu normálně a pak na stránce **dvoustupňové ověřování** klikněte na odkaz **podepsat** jinak.

    ![Změnit metodu ověření přihlášení](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Pokud nevidíte odkaz **jiný způsob** , znamená to, že jste nenainstalovali žádné další metody ověřování a že budete muset požádat správce, aby vám pomohli přihlašovat se k vašemu účtu. Až správce pomůže přihlásit, nezapomeňte přidat další metody ověřování. Další informace o přidání metod ověřování najdete v článku [Správa nastavení pro dvoustupňové ověřování](multi-factor-authentication-end-user-manage-settings.md) .
    >
    >Pokud se zobrazí odkaz **podepsat jiným způsobem** , ale stále se nezobrazuje žádné další metody ověřování, budete se muset obrátit na správce, aby vám pomohla přihlašovat se k vašemu účtu.

2. Vyberte metodu alternativního ověřování a pokračujte v procesu dvojúrovňového ověřování.

3. Po obnovení účtu můžete aktualizovat metody ověřování (v případě potřeby). Další informace o přidání nebo změně vašich metod najdete v článku [Správa nastavení pro dvoustupňové ověřování](multi-factor-authentication-end-user-manage-settings.md) .

## <a name="next-steps"></a>Další kroky

- Informace o zabezpečení najdete v článku Přehled informací o zabezpečení [(Preview)](./security-info-setup-signin.md) .

- Další informace o dvoustupňovém ověřování najdete v článku [Přehled ověřování dvou kroků](./multi-factor-authentication-end-user-first-time.md) .

- Resetování hesla, pokud jste ho ztratili nebo zapomněli, na [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/)

- Získejte tipy pro řešení potíží a nápovědu k problémům s přihlášením v tématu se [nemůžete přihlásit k vašemu účet Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .