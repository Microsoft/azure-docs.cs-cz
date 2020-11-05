---
title: Přidání účtů mimo Microsoft do aplikace Microsoft Authenticator – Azure AD
description: Přidejte účty jiné než Microsoft, například pro Google nebo Facebook do aplikace Microsoft Authenticator k ověření vaší identity při použití dvojúrovňového ověřování.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: cba8837fc738884fc187b13083487c2ab62d25f0
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392573"
---
# <a name="add-non-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Přidání jiných účtů než do aplikace Microsoft Authenticator

Přidejte k aplikaci Microsoft Authenticator své účty, jako je Google, Facebook nebo GitHub, do aplikace pro ověřování dvou faktorů. Microsoft Authenticator aplikace funguje se všemi aplikacemi, které používají dvojúrovňové ověřování, a s libovolným účtem, který podporuje standardy pro jednorázové heslo (TOTP) založené na čase.

>[!Important]
>Než budete moct přidat svůj účet, musíte si stáhnout a nainstalovat aplikaci Microsoft Authenticator. Pokud jste to ještě neudělali, postupujte podle kroků v článku [Stažení a instalace aplikace](user-help-auth-app-download-install.md) .

## <a name="add-personal-accounts"></a>Přidat osobní účty

Obecně platí, že u všech osobních účtů je třeba:

1. Přihlaste se ke svému účtu a potom pomocí zařízení nebo počítače zapněte dvojúrovňové ověřování.

2. Přidejte účet do aplikace Microsoft Authenticator. V rámci tohoto procesu se může zobrazit výzva k naskenování kódu QR.

    >[!Note]
    >Pokud Microsoft Authenticator aplikaci nakonfigurujete poprvé, může se zobrazit výzva s dotazem, jestli chcete, aby aplikace měla přístup k vaší kameře (iOS), nebo aby aplikaci mohla pořizovat snímky a nahrávat video (Android). Je nutné vybrat možnost **umožnit** , aby mohla aplikace ověřovatele získat přístup k fotoaparátu, aby pomohlo vytvořit obrázek kódu QR v dalším kroku. Pokud fotoaparát nepovolíte, můžete přesto nastavit ověřovací aplikaci, ale budete muset informace o kódu přidat ručně. Informace o tom, jak kód přidat ručně, najdete v tématu věnovaném [ručnímu přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

Tento proces poskytujeme pro účty Facebook, Google, GitHub a Amazon, ale proces je stejný i pro jiné aplikace, jako je Instagramu a Adobe.

## <a name="add-your-google-account"></a>Přidat účet Google

Přidejte svůj účet Google tím, že zapnete dvojúrovňové ověřování a potom účet přidáte do aplikace.

### <a name="turn-on-two-factor-verification"></a>Zapnout dvojúrovňové ověřování

1. V počítači klikněte na https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome , vyberte Začínáme a **Get Started** pak ověřte svoji identitu.

2. Postupujte podle pokynů na stránce, abyste zapnuli dvoustupňové ověřování pro váš osobní účet Google.

### <a name="add-your-google-account-to-the-app"></a>Přidání účtu Google do aplikace

1. Na stránce zabezpečení účtu Google na vašem počítači ( https://myaccount.google.com/security) , v části **Přidat další druhý postup ověřte, zda se jedná** o možnost **nastavit** v části **ověřovací aplikace** .

2. Na stránce **získat kódy ze ověřovací aplikace** vyberte v závislosti na typu telefonu možnost **Android** nebo **iPhone** a pak vyberte **Další**.

    Máte kód QR, který můžete použít k automatickému přidružení účtu k Microsoft Authenticator aplikaci. Nezavírejte toto okno.

3. Otevřete aplikaci Microsoft Authenticator, v pravém horním rohu vyberte **Přidat účet** v ikoně **přizpůsobení a řízení** a pak vyberte **jiný účet (Google, Facebook atd.)**.

4. Pomocí kamery zařízení Naskenujte kód QR ze stránky **nastavit ověřovací data** v počítači.

    >[!Note]
    >Pokud fotoaparát nepracuje správně, můžete zadat kód QR a adresu URL ručně.

5. Zkontrolujte stránku **účty** Microsoft Authenticator aplikace na vašem zařízení, abyste měli jistotu, že jsou informace o účtu pravé a že je k dispozici kód ověřovacího kódu se šesti číslicemi.

    Pro zvýšení zabezpečení se ověřovací kód každých 30 sekund nebrání někomu, aby používal kód několikrát.

6. V počítači klikněte na tlačítko **Další** na stránce **nastavit ověřovací data** , zadejte pro svůj účet Google kód pro ověření, který jste zadali v aplikaci, a pak vyberte **ověřit**.

7. Váš účet se ověří a můžete vybrat **Hotovo** , aby se stránka **nastavit ověřovací data** zavřela.

    >[!NOTE]
    >Další informace o dvojúrovňovém ověřování a účtu Google najdete v tématu [Zapnutí ověřování 2 kroky](https://support.google.com/accounts/answer/185839) a další [informace o 2 krocích ověřování](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Přidat účet Facebook

Přidejte svůj účet Facebook tím, že zapnete dvojúrovňové ověřování a potom do aplikace přidáte účet.

### <a name="turn-on-two-factor-verification"></a>Zapnout dvojúrovňové ověřování

1. V počítači otevřete Facebook, v pravém horním rohu vyberte rozevírací nabídku a pak přejděte na **Nastavení**  >  **zabezpečení a přihlášení**.

    Zobrazí se stránka **zabezpečení a přihlášení** .

2. V části použití dvou faktorů **ověřování** přejděte dolů na možnost **použít dvojúrovňové ověřování** a pak vyberte **Upravit**.

    Zobrazí se stránka se **dvěma faktory ověřování** .

3. Vyberte **zapnout**.

### <a name="add-your-facebook-account-to-the-app"></a>Přidání účtu Facebook do aplikace

1. Na stránce Facebook v počítači vyberte část **Přidat zálohu** a potom v oblasti **aplikace ověřování** zvolte možnost **Nastavení** .

    Máte kód QR, který můžete použít k automatickému přidružení účtu k Microsoft Authenticator aplikaci. Nezavírejte toto okno.

2. Otevřete aplikaci Microsoft Authenticator, v pravém horním rohu vyberte **Přidat účet** v ikoně **přizpůsobení a řízení** a pak vyberte **jiný účet (Google, Facebook atd.)**.

3. Pomocí kamery zařízení Naskenujte kód QR ze stránky dvojúrovňové **ověřování** na vašem počítači.

    >[!Note]
    >Pokud fotoaparát nepracuje správně, můžete zadat kód QR a adresu URL ručně.

4. Zkontrolujte stránku **účty** Microsoft Authenticator aplikace na vašem zařízení, abyste měli jistotu, že jsou informace o účtu pravé a že je k dispozici kód ověřovacího kódu se šesti číslicemi.

    Pro zvýšení zabezpečení se ověřovací kód každých 30 sekund nebrání někomu, aby používal kód několikrát.

5. V počítači klikněte na tlačítko **Další** na stránce **dvojúrovňové ověřování** a potom do pole pro váš účet Facebook zadejte čtyřmístný ověřovací kód, který jste zadali v aplikaci.

    Váš účet se ověří a teď můžete aplikaci použít k ověření vašeho účtu.

    >[!NOTE]
    >Další informace o dvojúrovňovém ověřování a účtu na Facebooku najdete v tématu [co je dvojúrovňové ověřování a jak to funguje?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>Přidat svůj účet GitHubu

Přidejte svůj účet GitHubu tím, že zapnete dvojúrovňové ověřování a potom do aplikace přidáte účet.

### <a name="turn-on-two-factor-verification"></a>Zapnout dvojúrovňové ověřování

1. V počítači otevřete GitHub, vyberte svůj obrázek z pravého horního rohu a pak vyberte **Nastavení**.

    Zobrazí se stránka se **dvěma faktory ověřování** .

2. V bočním panelu **osobní nastavení** vyberte **zabezpečení** a pak vyberte **Povolit dvojúrovňové ověřování** z oblasti **dvou-Factor Authentication** .

### <a name="add-your-github-account-to-the-app"></a>Přidání účtu GitHub do aplikace

1. Na stránce **dvojúrovňové ověřování** v počítači vyberte možnost **nastavit pomocí aplikace**.

2. Uložte si kódy obnovení, abyste se mohli vrátit ke svému účtu, pokud ztratíte přístup, a pak vyberte **Další**. 

    Své kódy můžete uložit tak, že je stáhnete do svého zařízení, vytisknete je pomocí tisku nebo je zkopírujete do nástroje Správce hesel.

3. Na stránce **dvojúrovňové ověřování** vyberte možnost **nastavit pomocí aplikace**.

    Stránka se změní, aby zobrazila kód QR. Nezavírejte tuto stránku.

4. Otevřete aplikaci Microsoft Authenticator, v pravém horním rohu vyberte **Přidat účet** z ikony pro **přizpůsobení a řízení** vyberte **jiný účet (Google, Facebook atd.)** a pak vyberte **zadat tento textový kód** z textu v horní části stránky.

    Aplikace Microsoft Authenticator nemůže naskenovat kód QR, takže je nutné kód zadat ručně.

5. Zadejte **název účtu** (například GitHub), zadejte **tajný klíč** z kroku 4 a pak vyberte **Dokončit**.

6. Na stránce **dvojúrovňové ověřovacích dat** v počítači zadejte pro svůj účet GitHubu čtyřmístný ověřovací kód, který jste zadali v aplikaci, a pak vyberte **Povolit**.

    Na stránce **účty** aplikace se zobrazí název vašeho účtu a dvanáctimístný ověřovací kód. Pro zvýšení zabezpečení se ověřovací kód každých 30 sekund nebrání někomu, aby používal kód několikrát.

    >[!NOTE]
    >Další informace o dvojúrovňovém ověřování a účtu GitHub najdete v tématu věnovaném [dvojúrovňovému ověřování](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Přidat účet Amazon

Přidejte svůj účet Amazon tím, že zapnete dvojúrovňové ověřování a potom do aplikace přidáte účet.

### <a name="turn-on-two-factor-verification"></a>Zapnout dvojúrovňové ověřování

1. V počítači otevřete službu Amazon, vyberte rozevírací nabídku **seznam & účtu** a pak vyberte **svůj účet**.

2. Vyberte **zabezpečení přihlášení &** , přihlaste se ke svému účtu Amazon a potom v oblasti **Upřesnit nastavení zabezpečení** vyberte **Upravit** .

    Zobrazí se stránka **Upřesnit nastavení zabezpečení** .

3. Vyberte **Začínáme**.

4. Vyberte možnost **ověřovací aplikace** na stránce **Zvolte způsob, jak obdržíte kódy** .

    Stránka se změní, aby zobrazila kód QR. Nezavírejte tuto stránku.

5. Otevřete aplikaci Microsoft Authenticator, v pravém horním rohu vyberte **Přidat účet** v ikoně **přizpůsobení a řízení** a pak vyberte **jiný účet (Google, Facebook atd.)**.

6. Pomocí kamery zařízení Naskenujte kód QR ze stránky **zvolit způsob, jak obdržíte kódy** na vašem počítači.

    >[!Note]
    >Pokud fotoaparát nepracuje správně, můžete zadat kód QR a adresu URL ručně.

7. Zkontrolujte stránku **účty** Microsoft Authenticator aplikace na vašem zařízení, abyste měli jistotu, že jsou informace o účtu pravé a že je k dispozici kód ověřovacího kódu se šesti číslicemi.

    Pro zvýšení zabezpečení se ověřovací kód každých 30 sekund nebrání někomu, aby používal kód několikrát.

8. Na stránce **Zvolte způsob, jak obdržíte kódy** v počítači zadejte pro svůj účet Amazon, který jste zadali v aplikaci, kód pro ověření šesti číslic a pak vyberte **ověřit kód a pokračovat**.

9. Dokončete zbývající část procesu registrace, včetně přidání metody ověřování zálohování, jako je například textová zpráva, a pak vyberte **Odeslat kód**.

10. Na stránce **Přidat způsob ověření zálohy** zadejte na svém počítači čtyřmístný ověřovací kód, který vám poskytla metoda ověření zálohování pro váš účet Amazon, a pak vyberte **ověřit kód a pokračovat**.

11. Na stránce **skoro dokončeno** rozhodněte, jestli počítač má mít důvěryhodné zařízení, a pak **ho vyberte. Zapněte Two-Step ověřování**.

    Zobrazí se stránka **Upřesnit nastavení zabezpečení** se zobrazenými aktualizovanými podrobnostmi o dvou faktorech ověřování.

    >[!NOTE]
    >Další informace o dvojúrovňovém ověřování a účtu Amazon najdete v tématu věnovaném [ověřování Two-Step](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) a [přihlašování pomocí Two-Stepho ověřování](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).

## <a name="next-steps"></a>Další kroky

- Po přidání účtů do aplikace se můžete přihlásit pomocí ověřovací aplikace na svém zařízení. Další informace najdete v tématu [přihlášení pomocí aplikace](user-help-auth-app-sign-in.md).

- U zařízení se systémem iOS můžete také zálohovat přihlašovací údaje účtu a související nastavení aplikace, jako je například pořadí účtů, do cloudu. Další informace najdete v tématu [zálohování a obnovení pomocí aplikace Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
