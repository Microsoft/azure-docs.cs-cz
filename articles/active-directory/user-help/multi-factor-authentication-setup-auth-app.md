---
title: Nastavení ověřovací aplikace jako dvoufaktorové metody ověření – Azure Active Directory | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nastavit aplikaci Microsoft Authenticator jako dvoufaktorovou metodu ověření.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 6ab1f7c97173021cc112a5f117469abd74ac954d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77062570"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>Nastavení ověřovací aplikace jako dvoufaktorové metody ověření

Můžete nastavit ověřovací aplikaci pro odeslání oznámení do mobilního zařízení nebo ověřovací kód jako metodu ověření zabezpečení. Nemusíte používat aplikaci Microsoft Authenticator a během procesu nastavení můžete vybrat jinou aplikaci. Tento článek však používá aplikaci Microsoft Authenticator.

>[!Important]
>Před přidáním účtu je nutné stáhnout a nainstalovat aplikaci Microsoft Authenticator. Pokud jste to ještě neudělali, postupujte podle pokynů v článku [Ke stažení a instalaci aplikace.](user-help-auth-app-download-install.md)

>[!Note]
> Pokud je možnost Mobilní aplikace zašedlá, je možné, že vaše organizace neumožňuje používat ověřovací aplikaci k ověření. V takovém případě budete muset vybrat jinou metodu nebo požádat správce o další pomoc.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>Nastavení aplikace Microsoft Authenticator pro odesílání oznámení

1. Na stránce **Další ověření zabezpečení** vyberte mobilní **aplikace** v **kroku 1: Jak vás máme kontaktovat** v oblasti.

2. V části Jak chcete **používat** oblast mobilní aplikace vyberte **Přijímat oznámení k ověření** a pak vyberte **Nastavit**.

    ![Další stránka pro ověření zabezpečení s možností mobilní aplikace a oznámení](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    Zobrazí se stránka **Konfigurovat mobilní aplikaci.**

    ![Obrazovka, která poskytuje QR kód](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Otevřete aplikaci Microsoft Authenticator, **vyberte Přidat účet** z ikony **Přizpůsobit a řídit** v pravém horním bodě a pak vyberte Pracovní nebo **školní účet**.

    >[!Note]
    >Pokud nastavujete aplikaci Microsoft Authenticator poprvé, může se zobrazit výzva s dotazem, zda má aplikaci povolit přístup ke kameře (iOS) nebo zda aplikaci pořizovat snímky a nahrávat video (Android). Musíte vybrat **Povolit,** aby ověřovací aplikace mohla přistupovat k fotoaparátu a pořit qr kód v dalším kroku. Pokud fotoaparát nepovolíte, můžete aplikaci pro ověřování nastavit, ale budete muset přidat informace o kódu ručně. Informace o ručním přidání kódu najdete v [tématu Ruční přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

4. Pomocí fotoaparátu zařízení naskenujte QR kód z obrazovky **Konfigurace mobilní aplikace** v počítači a pak zvolte **Další**.

5. Vraťte se do počítače a na stránku **Další ověření zabezpečení,** zkontrolujte, že se zobrazí zpráva, že konfigurace byla úspěšná, a pak vyberte **další**.

    ![Další stránka ověření zabezpečení se zprávou o úspěchu](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    Ověřovací aplikace odešle oznámení do vašeho mobilního zařízení jako test.

6. Na mobilním zařízení vyberte **Schválit**.

7. V počítači přidejte telefonní číslo mobilního zařízení do **kroku 3: V případě, že ztratíte přístup k** oblasti mobilní aplikace, a pak vyberte **Další**.

    Důrazně doporučujeme přidat číslo mobilního zařízení, které bude fungovat jako záloha, pokud z jakéhokoli důvodu nebudete mít přístup k mobilní aplikaci nebo ji nebudete používat.

8. V **kroku 4: Pokračujte v používání stávající oblasti aplikací, zkopírujte** zadaný hesel aplikace a vložte jej někam do bezpečí.

    ![Oblast hesel aplikací na stránce Další ověření zabezpečení](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Informace o tom, jak používat heslo aplikace ve starších aplikacích, najdete v [tématu Správa hesel aplikací](multi-factor-authentication-end-user-app-passwords.md). Hesla aplikací stačí, když nadále používáte starší aplikace, které nepodporují dvoufaktorové ověření.

9. Vyberte **Done** (Hotovo).

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>Nastavení aplikace Microsoft Authenticator pro použití ověřovacích kódů

1. Na stránce **Další ověření zabezpečení** vyberte mobilní **aplikace** v **kroku 1: Jak vás máme kontaktovat** v oblasti.

2. V části Jak **chcete používat** oblast mobilní aplikace vyberte **Použít ověřovací kód** a pak vyberte **Nastavit**.

    ![Další stránka pro ověření zabezpečení s možností mobilní aplikace a oznámení](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    Zobrazí se stránka **Konfigurovat mobilní aplikaci.**

    ![Obrazovka, která poskytuje QR kód](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Otevřete aplikaci Microsoft Authenticator, **vyberte Přidat účet** z ikony **Přizpůsobit a řídit** v pravém horním bodě a pak vyberte Pracovní nebo **školní účet**.

    >[!Note]
    >Pokud nastavujete aplikaci Microsoft Authenticator poprvé, může se zobrazit výzva s dotazem, zda má aplikaci povolit přístup ke kameře (iOS) nebo zda aplikaci pořizovat snímky a nahrávat video (Android). Musíte vybrat **Povolit,** aby ověřovací aplikace mohla přistupovat k fotoaparátu a pořit qr kód v dalším kroku. Pokud fotoaparát nepovolíte, můžete aplikaci pro ověřování nastavit, ale budete muset přidat informace o kódu ručně. Informace o ručním přidání kódu najdete v [tématu Ruční přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

4. Pomocí fotoaparátu zařízení naskenujte QR kód z obrazovky **Konfigurace mobilní aplikace** v počítači a pak zvolte **Další**.

5. Vraťte se do počítače a na stránku **Další ověření zabezpečení,** zkontrolujte, že se zobrazí zpráva, že konfigurace byla úspěšná, a pak vyberte **další**.

    ![Další stránka ověření zabezpečení se zprávou o úspěchu](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    Ověřovací aplikace požádá o ověřovací kód jako test.

6. V aplikaci Microsoft Authenticator přejděte dolů na svůj pracovní nebo školní účet, zkopírujte a vložte 6místný kód z aplikace do **kroku 2: Zadejte ověřovací kód z** pole mobilní aplikace v počítači a pak vyberte **Ověřit**.

    ![Další stránka pro ověření zabezpečení s ověřovacím testem kódu](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. V počítači přidejte telefonní číslo mobilního zařízení do **kroku 3: V případě, že ztratíte přístup k** oblasti mobilní aplikace, a pak vyberte **Další**.

    Důrazně doporučujeme přidat číslo mobilního zařízení, které bude fungovat jako záloha, pokud z jakéhokoli důvodu nebudete mít přístup k mobilní aplikaci nebo ji nebudete používat.

8. V **kroku 4: Pokračujte v používání stávající oblasti aplikací, zkopírujte** zadaný hesel aplikace a vložte jej někam do bezpečí.

    ![Oblast hesel aplikací na stránce Další ověření zabezpečení](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Informace o tom, jak používat heslo aplikace ve starších aplikacích, najdete v [tématu Správa hesel aplikací](multi-factor-authentication-end-user-app-passwords.md). Hesla aplikací stačí, když nadále používáte starší aplikace, které nepodporují dvoufaktorové ověření.

9. Vyberte **Done** (Hotovo).

## <a name="next-steps"></a>Další kroky

Po nastavení dvoufaktorové metody ověření můžete přidat další metody, spravovat nastavení a hesla aplikací, přihlásit se nebo získat pomoc s některými běžnými problémy souvisejícími s dvoufaktorovým ověřováním.

- [Správa nastavení dvoufaktorových metod ověření](multi-factor-authentication-end-user-manage-settings.md)

- [Správa hesel aplikací](multi-factor-authentication-end-user-app-passwords.md)

- [Přihlášení pomocí dvoufaktorového ověření](multi-factor-authentication-end-user-signin.md)

- [Získání nápovědy k dvoufaktorovému ověření](multi-factor-authentication-end-user-troubleshoot.md)
