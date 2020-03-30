---
title: Přidání účtů jiných než Microsoft do aplikace Microsoft Authenticator – Azure AD
description: Přidejte účty jiných společností než Microsoft, například pro Google nebo Facebook, do aplikace Microsoft Authenticator, abyste ověřili svou identitu při použití dvoufaktorového ověření.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 8650d0170e8ff910140e2b432dd1c998d19e72d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063947"
---
# <a name="add-non-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Přidání účtů jiných než Microsoft do aplikace Microsoft Authenticator

Přidejte své účty, které nejsou od Microsoftu, například pro Google, Facebook nebo GitHub, do aplikace Microsoft Authenticator pro dvoufaktorové ověření. Aplikace Microsoft Authenticator funguje s jakoukoli aplikací, která používá dvoufaktorové ověření, a s jakýmkoli účtem, který podporuje standardy jednorázového hesla (TOTP) založené na čase.

>[!Important]
>Před přidáním účtu je nutné stáhnout a nainstalovat aplikaci Microsoft Authenticator. Pokud jste to ještě neudělali, postupujte podle pokynů v článku [Ke stažení a instalaci aplikace.](user-help-auth-app-download-install.md)

## <a name="add-personal-accounts"></a>Přidání osobních účtů

Obecně platí, že pro všechny vaše osobní účty, musíte:

1. Přihlaste se ke svému účtu a zapněte dvoufaktorové ověřování pomocí zařízení nebo počítače.

2. Přidejte účet do aplikace Microsoft Authenticator. V rámci tohoto procesu můžete být požádáni o naskenování QR kódu.

    >[!Note]
    >Pokud nastavujete aplikaci Microsoft Authenticator poprvé, může se zobrazit výzva s dotazem, zda má aplikaci povolit přístup ke kameře (iOS) nebo zda aplikaci pořizovat snímky a nahrávat video (Android). Musíte vybrat **Povolit,** aby ověřovací aplikace mohla přistupovat k fotoaparátu a pořit qr kód v dalším kroku. Pokud fotoaparát nepovolíte, můžete aplikaci pro ověřování nastavit, ale budete muset přidat informace o kódu ručně. Informace o ručním přidání kódu najdete v [tématu Ruční přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

Tento proces zde poskytujeme pro vaše účty Na Facebooku, Google, GitHub a Amazon, ale tento proces je stejný pro všechny ostatní aplikace, jako je Instagram, Netflix nebo Adobe.

## <a name="add-your-google-account"></a>Přidání účtu Google

Přidejte svůj účet Google tak, že zapnete dvoufaktorové ověření a pak účet přidáte do aplikace.

### <a name="turn-on-two-factor-verification"></a>Zapnutí dvoufaktorového ověření

1. Na počítači přejděte na https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcomepoložku , vyberte Možnost **Začínáme**a ověřte svou identitu.

2. Podle pokynů na stránce zapněte dvoustupňové ověření svého osobního účtu Google.

### <a name="add-your-google-account-to-the-app"></a>Přidání účtu Google do aplikace

1. Na stránce Google v počítači přejděte do části **Nastavit alternativní druhý krok** a zvolte **Nastavit** v části **Aplikace Authenticator.**

2. Na stránce Získat kódy ze stránky **aplikace Authenticator** vyberte **Android** nebo **iPhone** na základě typu telefonu a pak vyberte **Další**.

    Dostanete QR kód, který můžete použít k automatickému přidružení účtu k aplikaci Microsoft Authenticator. Nezavírejte toto okno.

3. Otevřete aplikaci Microsoft Authenticator, vyberte **Přidat účet** z ikony **Přizpůsobit a ovládat** v pravém horním setu a pak vyberte Jiný **účet (Google, Facebook atd.).**

4. Pomocí fotoaparátu zařízení naskenujte QR kód ze stránky **Nastavit pravost** v počítači.

    >[!Note]
    >Pokud fotoaparát nefunguje správně, můžete qr kód a adresu URL zadat ručně.

5. Zkontrolujte stránku **Účty** aplikace Microsoft Authenticator na vašem zařízení, abyste se ujistili, že jsou informace o vašem účtu správné a že existuje přidružený šestimístný ověřovací kód.

    Z důvodu dodatečného zabezpečení se ověřovací kód mění každých 30 sekund, aby někdo mohl kód používat vícekrát.

6. Na stránce **Nastavit ověřování** v počítači vyberte **Další,** zadejte šestimístný ověřovací kód uvedený v aplikaci pro váš účet Google a pak vyberte **Ověřit**.

7. Váš účet je ověřený a můžete vybrat **Hotovo** a zavřít stránku **Nastavit pravost.**

    >[!NOTE]
    >Další informace o dvoufaktorovém ověření a účtu Google najdete v [tématu Zapnutí dvoufázového ověření](https://support.google.com/accounts/answer/185839) a [další informace o dvoufázovém ověření](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Přidání facebookového účtu

Přidejte svůj facebookový účet tak, že zapnete dvoufaktorové ověření a pak ho přidáte do aplikace.

### <a name="turn-on-two-factor-verification"></a>Zapnutí dvoufaktorového ověření

1. Na počítači otevřete Facebook, vyberte rozevírací nabídku v pravém horním rohu a přejděte na **Nastavení** > **zabezpečení a přihlášení**.

    Zobrazí se stránka **Zabezpečení a přihlášení.**

2. V části **Dvoufaktorové ověřování** přejděte na možnost **Použít dvoufaktorové ověřování** a pak vyberte **Upravit**.

    Zobrazí se stránka **Dvoufaktorové ověřování.**

3. Vyberte **Zapnout**.

### <a name="add-your-facebook-account-to-the-app"></a>Přidání facebookového účtu do aplikace

1. Na facebookové stránce v počítači přejděte do části **Přidat zálohu** a pak zvolte **Nastavit** v oblasti **aplikace Ověřování.**

    Dostanete QR kód, který můžete použít k automatickému přidružení účtu k aplikaci Microsoft Authenticator. Nezavírejte toto okno.

2. Otevřete aplikaci Microsoft Authenticator, vyberte **Přidat účet** z ikony **Přizpůsobit a ovládat** v pravém horním setu a pak vyberte Jiný **účet (Google, Facebook atd.).**

3. Pomocí fotoaparátu zařízení naskenujte QR kód ze stránky **Dvoufaktorové ověřování** v počítači.

    >[!Note]
    >Pokud fotoaparát nefunguje správně, můžete qr kód a adresu URL zadat ručně.

4. Zkontrolujte stránku **Účty** aplikace Microsoft Authenticator na vašem zařízení, abyste se ujistili, že jsou informace o vašem účtu správné a že existuje přidružený šestimístný ověřovací kód.

    Z důvodu dodatečného zabezpečení se ověřovací kód mění každých 30 sekund, aby někdo mohl kód používat vícekrát.

5. Na stránce **Dvoufaktorové ověřování** v počítači vyberte **Další** a zadejte šestimístný ověřovací kód, který je k dispozici v aplikaci pro váš účet na Facebooku.

    Váš účet je ověřena a nyní můžete použít aplikaci k ověření účtu.

    >[!NOTE]
    >Další informace o dvoufaktorovém ověřování a facebookovém účtu najdete v tématu [Co je dvoufaktorové ověřování a jak funguje?](https://www.facebook.com/help/148233965247823)

## <a name="add-your-github-account"></a>Přidejte svůj účet GitHub

Přidejte svůj účet GitHub zapnutím dvoufaktorového ověření a přidáním účtu do aplikace.

### <a name="turn-on-two-factor-verification"></a>Zapnutí dvoufaktorového ověření

1. Na počítači otevřete GitHub, vyberte obrázek v pravém horním rohu a pak vyberte **Nastavení**.

    Zobrazí se stránka **dvoufaktorového ověřování.**

2. V postranním panelu **Osobní nastavení** vyberte **Zabezpečení** a pak v oblasti **dvoufaktorového ověřování** vyberte **Povolit dvoufaktorové ověřování.**

### <a name="add-your-github-account-to-the-app"></a>Přidejte svůj účet GitHub do aplikace

1. Na stránce **Dvoufaktorové ověřování** v počítači vyberte **Nastavit pomocí aplikace**.

2. Uložte si kódy pro obnovení, abyste se mohli vrátit ke svému účtu, pokud ztratíte přístup, a pak vyberte **Další**. 

    Kódy můžete uložit tak, že je stáhnete do zařízení, vytisknete tištěnou kopii nebo zkopírujete je do nástroje správce hesel.

3. Na stránce **Dvoufaktorové ověřování** vyberte **Nastavit pomocí aplikace**.

    Stránka se změní tak, aby zobrazovala QR kód. Nezavírejte tuto stránku.

4. Otevřete aplikaci Microsoft Authenticator, vyberte **Přidat účet** z ikony **Přizpůsobit a ovládat** v pravém horním rohu, vyberte Jiný účet **(Google, Facebook atd.)** a pak **vyberte tento textový kód** z textu v horní části stránky.

    Aplikace Microsoft Authenticator nemůže naskenovat QR kód, takže je nutné kód zadat ručně.

5. Zadejte **název účtu** (například GitHub) a zadejte klíč Tajný **klíč** z kroku 4 a pak vyberte **Dokončit**.

6. Na stránce **Dvoufaktorový ověřovatel** v počítači zadejte šestimístný ověřovací kód uvedený v aplikaci pro váš účet GitHub a pak vyberte **Povolit**.

    Na stránce **Účty** v aplikaci se zobrazí název vašeho účtu a šestimístný ověřovací kód. Z důvodu dodatečného zabezpečení se ověřovací kód mění každých 30 sekund, aby někdo mohl kód používat vícekrát.

    >[!NOTE]
    >Další informace o dvoufaktorovém ověřování a účtu GitHub najdete [v tématu O dvoufaktorovém ověřování](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Přidejte svůj účet Amazon

Přidejte svůj účet Amazon zapnutím dvoufaktorového ověření a přidáním účtu do aplikace.

### <a name="turn-on-two-factor-verification"></a>Zapnutí dvoufaktorového ověření

1. Na počítači otevřete amazonskou, vyberte rozevírací nabídku **Seznamy & účtů** a pak vyberte **Váš účet**.

2. Vyberte **Přihlásit & zabezpečení**, přihlaste se ke svému účtu Amazon a pak vyberte **Upravit** v oblasti **Upřesnit nastavení zabezpečení.**

    Zobrazí se stránka **Upřesnit nastavení zabezpečení.**

3. Vyberte **Možnost Začínáme**.

4. Na stránce Zvolte **Možnost Ověřovací aplikace** na stránce **Zvolte, jak budete přijímat kódy.**

    Stránka se změní tak, aby zobrazovala QR kód. Nezavírejte tuto stránku.

5. Otevřete aplikaci Microsoft Authenticator, vyberte **Přidat účet** z ikony **Přizpůsobit a ovládat** v pravém horním setu a pak vyberte Jiný **účet (Google, Facebook atd.).**

6. Pomocí fotoaparátu zařízení naskenujte QR kód na stránce Zvolte, jak budete v počítači **dostávat kódy.**

    >[!Note]
    >Pokud fotoaparát nefunguje správně, můžete qr kód a adresu URL zadat ručně.

7. Zkontrolujte stránku **Účty** aplikace Microsoft Authenticator na vašem zařízení, abyste se ujistili, že jsou informace o vašem účtu správné a že existuje přidružený šestimístný ověřovací kód.

    Z důvodu dodatečného zabezpečení se ověřovací kód mění každých 30 sekund, aby někdo mohl kód používat vícekrát.

8. Na stránce Zvolit, jak budete v počítači **dostávat kódy,** zadejte šestimístný ověřovací kód uvedený v aplikaci pro váš účet Amazon a pak vyberte **Ověřit kód a pokračujte**.

9. Dokončete zbytek procesu registrace, včetně přidání metody ověření zálohy, například textové zprávy, a pak vyberte **Odeslat kód**.

10. Na stránce **Přidat metodu ověření zálohy** v počítači zadejte šestimístný ověřovací kód poskytnutý metodou ověření zálohování pro váš účet Amazon a pak vyberte **Ověřit kód a pokračujte**.

11. Na stránce **Téměř hotovo** se rozhodněte, zda chcete počítač vytvořit důvěryhodné zařízení, a pak vyberte **Možnost Mám. Zapněte dvoustupňové ověření**.

    Zobrazí se stránka **Upřesnit nastavení zabezpečení** s aktualizovanými dvoufaktorovémi ověřovacími údaji.

    >[!NOTE]
    >Další informace o dvoufaktorovém ověření a vašem účtu Amazon najdete v [tématu O dvoufázovém ověření](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) a [přihlášení pomocí dvoufázového ověření](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).

## <a name="next-steps"></a>Další kroky

- Po přidání účtů do aplikace se můžete přihlásit pomocí aplikace Authenticator v zařízení. Další informace najdete [v tématu Přihlášení pomocí aplikace](user-help-auth-app-sign-in.md).

- U zařízení se systémem iOS můžete také zálohovat přihlašovací údaje k účtu a související nastavení aplikací, jako je pořadí vašich účtů, do cloudu. Další informace naleznete v [tématu Zálohování a obnovení pomocí aplikace Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
