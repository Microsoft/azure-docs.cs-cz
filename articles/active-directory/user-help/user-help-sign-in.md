---
title: Přihlášení pomocí informací o ověřování identity – Azure AD
description: Přečtěte si, jak se přihlásit pomocí různých metod ověření identity v bezpečnostních informacích.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.custom: user-help
ms.openlocfilehash: 7d1d27cede6c593150d3937bb3aa0ca59394eea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062162"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Přihlášení pomocí dvoustupňového ověření nebo bezpečnostních údajů

Po nastavení dvoustupňových ověřovacích nebo bezpečnostních údajů se budete moci ke svému účtu přihlásit pomocí zadané metody ověření.

> [!Note]
> Pokud stále používáte dvoustupňové ověřování, budete muset nastavit metody ověření podle pokynů v článku [Nastavení mého účtu pro dvoustupňové ověření.](multi-factor-authentication-end-user-first-time.md)
>
> Pokud správce zapnul prostředí s bezpečnostními údaji, budete muset nastavit metody ověření pomocí těchto podrobných článků:<ul><li>[Nastavení bezpečnostních údajů pro použití ověřovací aplikace](security-info-setup-auth-app.md)</li><li>[Nastavení bezpečnostních údajů pro používání zasílání textových zpráv](security-info-setup-text-msg.md)</li><li>[Nastavení bezpečnostních údajů pro použití telefonního hovoru](security-info-setup-phone-number.md)</li><li>[Nastavení bezpečnostních údajů pro použití bezpečnostního klíče](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Přihlaste se pomocí oznámení ověřovací aplikace na mobilním zařízení

1. Přihlaste se ke svému účtu pomocí svého uživatelského jména a hesla.

2. V oznámení o schválení odeslaném do mobilního zařízení vyberte **Schválit.**

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Přihlaste se pomocí kódu ověřovací aplikace na mobilním zařízení

1. Přihlaste se ke svému účtu pomocí svého uživatelského jména a hesla.

2. Otevřete ověřovací aplikaci a do pole **Zadat kód** zadejte náhodně generovaný kód pro svůj účet.

## <a name="sign-in-using-your-phone-number"></a>Přihlášení pomocí telefonního čísla

1. Přihlaste se ke svému účtu pomocí svého uživatelského jména a hesla.

2. Zvedněte telefon a postupujte podle pokynů.

## <a name="sign-in-using-a-text-message"></a>Přihlášení pomocí textové zprávy

1. Přihlaste se ke svému účtu pomocí svého uživatelského jména a hesla.

2. Otevřete textovou zprávu a do pole **Zadat kód** zadejte kód z textové zprávy.

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>Přihlášení pomocí bezpečnostního klíče na zamykací obrazovce

1. Po registraci bezpečnostního klíče vyberte obrázek bezpečnostního klíče ze zamykací obrazovky Windows 10.

2. Vložte bezpečnostní klíč do portu USB zařízení a přihlaste se k systému Windows pomocí kódu PIN bezpečnostního klíče.

    ![Přihlášení pomocí bezpečnostního klíče na zamykací obrazovce Windows 10](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>Přihlášení pomocí bezpečnostního klíče a prohlížeče Microsoft Edge

1. Po registraci bezpečnostního klíče otevřete prohlížeč Microsoft Edge.

2. Po zobrazení výzvy k přihlášení vložte bezpečnostní klíč do portu USB zařízení a přihlaste se k systému Windows pomocí kódu PIN bezpečnostního klíče.

    ![Přihlášení pomocí klíče zabezpečení pomocí prohlížeče Microsoft Edge](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >Informace o přihlašování pomocí aplikace Microsoft Authenticator najdete v článku [Přihlášení k účtům pomocí aplikace Microsoft Authenticator](user-help-auth-app-sign-in.md).

## <a name="sign-in-using-another-verification-method"></a>Přihlášení pomocí jiné metody ověření

Pokud z nějakého důvodu nemůžete použít primární metodu přihlášení, můžete použít jinou dříve nastavenou metodu ověření.

1. Přihlaste se ke svému účtu normálně a pak zvolte odkaz **Přihlásit jiným způsobem** na stránce **Dvoufázové ověření.**

    ![Změnit způsob ověření přihlášení](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Pokud odkaz **Přihlásit** se jiným způsobem nevidíte, znamená to, že jste nenastavili žádné jiné metody ověřování a že budete muset kontaktovat správce s žádostí o pomoc s přihlášením k účtu. Až vám správce pomůže se přihlásit, nezapomeňte přidat další metody ověření. Další informace o přidávání metod ověření najdete v článku [Správa nastavení dvoukroků.](multi-factor-authentication-end-user-manage-settings.md)
    >
    >Pokud se vám zobrazí odkaz **Přihlásit se jiným způsobem,** ale i tak se vám nezobrazují žádné jiné metody ověření, budete muset požádat o pomoc se správcem s přihlášením k účtu.

2. Zvolte alternativní metodu ověření a pokračujte v procesu dvoustupňového ověření.

3. Až se vrátíte ke svému účtu, můžete v případě potřeby aktualizovat metody ověření. Další informace o přidání nebo změně metod najdete v článku [Správa nastavení dvoustupňového ověření.](multi-factor-authentication-end-user-manage-settings.md)

## <a name="next-steps"></a>Další kroky

- Informace o bezpečnostních údajích najdete v článku [Přehled bezpečnostních údajů (preview).](user-help-security-info-overview.md)

- Další informace o dvoustupňovém ověření se dozvíte v článku [Přehled dvoustupňového ověření.](user-help-two-step-verification-overview.md)

- Resetování hesla, pokud jste ho ztratili nebo zapomněli, z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/)

- V článku Nelze se [přihlásit k účtu Microsoft,](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) najdete tipy pro řešení potíží a nápovědu k problémům s přihlášením.
