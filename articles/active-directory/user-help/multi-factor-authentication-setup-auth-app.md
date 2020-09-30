---
title: Nastavení ověřovací aplikace jako metody dvojúrovňové ověřování – Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak nastavit aplikaci Microsoft Authenticator jako metodu ověřování dvou faktorů.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 10b6b788077eccda522c9e8c6e631939ca1819fc
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530336"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>Nastavení ověřovací aplikace jako metody ověřování dvou faktorů

Můžete nastavit ověřovací aplikaci, která odešle oznámení na mobilní zařízení nebo pošle ověřovací kód jako metodu ověření zabezpečení. Nemusíte používat aplikaci Microsoft Authenticator a během procesu nastavování můžete vybrat jinou aplikaci. Tento článek však používá aplikaci Microsoft Authenticator.

>[!Important]
>Než budete moct přidat svůj účet, musíte si stáhnout a nainstalovat aplikaci Microsoft Authenticator. Pokud jste to ještě neudělali, postupujte podle kroků v článku [Stažení a instalace aplikace](user-help-auth-app-download-install.md) .

>[!Note]
> Pokud je možnost mobilní aplikace šedá, je možné, že vaše organizace neumožňuje použít ověřovací aplikaci k ověření. V takovém případě budete muset vybrat jinou metodu nebo požádat správce o další nápovědu.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>Nastavení aplikace Microsoft Authenticator k odesílání oznámení

1. Na [stránce další ověření zabezpečení](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)vyberte **mobilní aplikace** z **kroku 1: jak vás máme kontaktovat vaši** oblastí.

2. Vyberte možnost **přijímat oznámení pro ověření** z části **jak chcete použít oblast mobilní aplikace** a pak vyberte možnost **nastavit**.

    ![Snímek obrazovky se stránkou "další ověření zabezpečení", s vybranou možnost "mobilní aplikace" a "dostávat oznámení k ověření".](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    Zobrazí se stránka **Konfigurace mobilní aplikace** .

    ![Obrazovka, která poskytuje kód QR](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Otevřete aplikaci Microsoft Authenticator, v pravém horním rohu vyberte **Přidat účet** **a potom** vyberte **pracovní nebo školní účet**.

    >[!Note]
    >Pokud Microsoft Authenticator aplikaci nakonfigurujete poprvé, může se zobrazit výzva s dotazem, jestli chcete, aby aplikace měla přístup k vaší kameře (iOS), nebo aby aplikaci mohla pořizovat snímky a nahrávat video (Android). Je nutné vybrat možnost **umožnit** , aby mohla aplikace ověřovatele získat přístup k fotoaparátu, aby pomohlo vytvořit obrázek kódu QR v dalším kroku. Pokud fotoaparát nepovolíte, můžete přesto nastavit ověřovací aplikaci, ale budete muset informace o kódu přidat ručně. Informace o tom, jak kód přidat ručně, najdete v tématu věnovaném [ručnímu přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

4. Pomocí kamery zařízení Naskenujte kód QR z obrazovky **Konfigurace mobilní aplikace** na vašem počítači a pak zvolte **Další**.

5. Vraťte se do počítače a na stránku **Další ověření zabezpečení** , zkontrolujte, že se zobrazí zpráva s informacemi o tom, že konfigurace byla úspěšná, a pak vyberte **Další**.

    ![Snímek obrazovky se stránkou "další ověření zabezpečení", která obsahuje "mobilní aplikace byla nakonfigurována..." Zpráva o úspěchu byla zvýrazněna.](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    Aplikace ověřovatele pošle oznámení na vaše mobilní zařízení jako test.

6. Na mobilním zařízení vyberte **schválit**.

7. V počítači přidejte do kroku 3 telefonní číslo mobilního zařízení: pro **případ, že ztratíte přístup k oblasti mobilní aplikace** , a pak vyberte **Další**.

    Důrazně doporučujeme přidat telefonní číslo mobilního zařízení, které se bude chovat jako záloha, pokud k tomu nemůžete získat přístup k mobilní aplikaci nebo ji z nějakého důvodu použít.

8. V části **Krok 4: pokračujte v oblasti používání stávajících aplikací** , zkopírujte zadané heslo aplikace a vložte ho někam do trezoru.

    ![Oblast hesel aplikací na stránce dodatečného ověření zabezpečení](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Informace o tom, jak používat heslo aplikace pro vaše starší aplikace, najdete v tématu [Správa hesel aplikací](multi-factor-authentication-end-user-app-passwords.md). Pokud budete nadále používat starší aplikace, které nepodporují dvojúrovňové ověřování, je třeba použít hesla aplikací.

9. Vyberte **Hotovo**.

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>Nastavení aplikace Microsoft Authenticator pro použití ověřovacích kódů

1. Na stránce **Další ověření zabezpečení** vyberte **mobilní aplikace** z **kroku 1: jak vás máme kontaktovat vaši** oblastí.

2. Vyberte možnost **použít ověřovací kód** z části **jak chcete použít oblast mobilní aplikace** a pak vyberte možnost **nastavit**.

    ![Stránka Další ověření zabezpečení s možností mobilní aplikace a oznámení](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    Zobrazí se stránka **Konfigurace mobilní aplikace** .

    ![Obrazovka, která poskytuje kód QR](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Otevřete aplikaci Microsoft Authenticator, v pravém horním rohu vyberte **Přidat účet** **a potom** vyberte **pracovní nebo školní účet**.

    >[!Note]
    >Pokud Microsoft Authenticator aplikaci nakonfigurujete poprvé, může se zobrazit výzva s dotazem, jestli chcete, aby aplikace měla přístup k vaší kameře (iOS), nebo aby aplikaci mohla pořizovat snímky a nahrávat video (Android). Je nutné vybrat možnost **umožnit** , aby mohla aplikace ověřovatele získat přístup k fotoaparátu, aby pomohlo vytvořit obrázek kódu QR v dalším kroku. Pokud fotoaparát nepovolíte, můžete přesto nastavit ověřovací aplikaci, ale budete muset informace o kódu přidat ručně. Informace o tom, jak kód přidat ručně, najdete v tématu věnovaném [ručnímu přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

4. Pomocí kamery zařízení Naskenujte kód QR z obrazovky **Konfigurace mobilní aplikace** na vašem počítači a pak zvolte **Další**.

5. Vraťte se do počítače a na stránku **Další ověření zabezpečení** , zkontrolujte, že se zobrazí zpráva s informacemi o tom, že konfigurace byla úspěšná, a pak vyberte **Další**.

    ![Stránka dodatečného ověření zabezpečení se zprávou o úspěchu](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    Ověřovací aplikace se zeptá na ověřovací kód jako test.

6. V aplikaci Microsoft Authenticator se posuňte dolů k pracovnímu nebo školnímu účtu, zkopírujte a vložte 6-číslice kód z aplikace do části **Krok 2: zadejte ověřovací kód z pole mobilní aplikace** v počítači a pak vyberte **ověřit**.

    ![Stránka dodatečného ověření zabezpečení s testem ověřovacího kódu](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. V počítači přidejte do kroku 3 telefonní číslo mobilního zařízení: pro **případ, že ztratíte přístup k oblasti mobilní aplikace** , a pak vyberte **Další**.

    Důrazně doporučujeme přidat telefonní číslo mobilního zařízení, které se bude chovat jako záloha, pokud k tomu nemůžete získat přístup k mobilní aplikaci nebo ji z nějakého důvodu použít.

8. V části **Krok 4: pokračujte v oblasti používání stávajících aplikací** , zkopírujte zadané heslo aplikace a vložte ho někam do trezoru.

    ![Oblast hesel aplikací na stránce dodatečného ověření zabezpečení](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Informace o tom, jak používat heslo aplikace pro vaše starší aplikace, najdete v tématu [Správa hesel aplikací](multi-factor-authentication-end-user-app-passwords.md). Pokud budete nadále používat starší aplikace, které nepodporují dvojúrovňové ověřování, je třeba použít hesla aplikací.

9. Vyberte **Hotovo**.

## <a name="next-steps"></a>Další kroky

Po nastavení dvojúrovňové metody ověřování můžete přidat další metody, spravovat nastavení a hesla aplikací, přihlašovat nebo získat pomoc s některými běžnými problémy souvisejícími s ověřováním dvou faktorů.

- [Správa nastavení dvojúrovňové metody ověřování](multi-factor-authentication-end-user-manage-settings.md)

- [Správa hesel aplikací](multi-factor-authentication-end-user-app-passwords.md)

- [Přihlášení Pomocí dvojúrovňového ověřování](multi-factor-authentication-end-user-signin.md)

- [Získat pomoc se dvojúrovňovém ověřováním](multi-factor-authentication-end-user-troubleshoot.md)
