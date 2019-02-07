---
title: Přidat své účty jiných společností než Microsoft k aplikaci Microsoft Authenticator – Azure Active Directory | Dokumentace Microsoftu
description: Postup přidání vaše účty jiných společností než Microsoft, jako pro Google, Facebook nebo Githubu do aplikace Microsoft Authenticator pro dvoufaktorové ověřování.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: afcac73af84010f37eccd018f39852712e495b84
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773216"
---
# <a name="add-your-non-microsoft-accounts"></a>Přidat své účty jiných společností než Microsoft
Přidáte vaše účty jiných společností než Microsoft, jako pro Google, Facebook nebo Githubu do aplikace Microsoft Authenticator pro dvoufaktorové ověřování. Aplikace Microsoft Authenticator funguje s každou aplikaci, která používá dvojúrovňového ověřování a jakýkoli účet, který podporuje podle času jednorázového hesla (TOTP) normami.

>[!Important]
>Než budete moct přidat svůj účet, musíte stáhnout a nainstalovat aplikaci Microsoft Authenticator. Pokud jste tak ještě neučinili, postupujte podle kroků v [stáhnout a nainstalovat aplikaci](user-help-auth-app-download-install.md) článku.

## <a name="add-personal-accounts"></a>Přidejte osobní účty
Obecně platí pro všechny vaše osobní účty, musíte mít:

1. Přihlaste se ke svému účtu a zapněte dvojúrovňového ověřování pomocí zařízení nebo počítače.

2. Přidáte účet do aplikace Microsoft Authenticator. Může se zobrazit výzva k naskenovat kód QR jako součást tohoto procesu.

Proces zde poskytujeme pro vaše účty služby Facebook, Google, Githubu a Amazon, ale tento postup je stejný pro jakoukoli jinou aplikaci, jako je například Instagramu a Netflix, Adobe.

## <a name="add-your-google-account"></a>Přidání účtu Google
Zapnutí dvojúrovňového ověřování a následným přidáním účtu do aplikace přidáte váš účet Google.

### <a name="turn-on-two-factor-verification"></a>Zapnutí dvojúrovňového ověřování

1. Na vašem počítači, přejděte na https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcomevyberte **Začínáme**a poté ověřit vaši identitu.

2. Postupujte podle kroků na stránku k zapnutí dvoustupňového ověřování pro váš osobní účet Google.

### <a name="add-your-google-account-to-the-app"></a>Přidání účtu Google do aplikace

1. Na stránce Google ve vašem počítači přejděte na **nastavit alternativní druhý krok** zvolte **nastavení** z **aplikace Authenticator** oddílu.

2. Na **získat kódy aplikace Authenticator** stránky, vyberte buď možnost **Android** nebo **iPhone** podle typu vašeho telefonu a potom vyberte **Další**.

    Už daný kód QR, který vám umožní automaticky postupu přidružte svůj účet v aplikaci Microsoft Authenticator. Nezavírejte toto okno.

3. Otevřete aplikaci Microsoft Authenticator, vyberte **přidat účet** z **přizpůsobení a řízení** ikonu pravém horním rohu a vyberte **jiný účet (Google, Facebook atd.)** .

4. Naskenování kódu QR pomocí fotoaparátu v zařízení **nastavit ověřovací** stránky ve vašem počítači.

    >[!Note]
    >Pokud vaše kamera nepracuje správně, můžete si [QR kód a adresu URL zadat ručně](#add-an-account-to-the-app-manually).

5. Zkontrolujte **účty** stránka aplikace Microsoft Authenticator na vašem zařízení, abyste měli jistotu, informace o vašem účtu je práva a je k přidružené ověřovací šestimístný kód.

    Za účelem zvýšení zabezpečení ověřovací kód, který změní každých 30 sekund brání pomocí kódu více než jednou.

6. Vyberte **Další** na **nastavit ověřovací** stránky ve vašem počítači, zadejte šestičíselný ověřovací kód v aplikaci k dispozici pro váš účet Google a vyberte **ověřte**.

7. Váš účet je ověřený, a můžete vybrat **provádí** zavřete **nastavit ověřovací** stránky.

    >[!NOTE]
    >Další informace o dvojúrovňového ověřování a váš účet Google, najdete v části [zapnout ověřování kroku 2](https://support.google.com/accounts/answer/185839) a [Další informace o ověřovací krok 2](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Přidání vašeho účtu na Facebooku
Zapnutí dvojúrovňového ověřování a následným přidáním účtu do aplikace přidáte Facebookový účet.

### <a name="turn-on-two-factor-verification"></a>Zapnutí dvojúrovňového ověřování

1. Ve vašem počítači, otevřete Facebook, vyberte rozevírací nabídky v pravém horním rohu a potom přejděte ke **nastavení** > **zabezpečení a přihlášení**.

    **Zabezpečení a přihlášení** se zobrazí stránka.

2. Přejděte dolů na **použít dvojúrovňové ověřování** možnost **Dvojúrovňového ověřování** a potom vyberte **upravit**.

    **Dvojúrovňového ověřování** zobrazí se stránka.

3. Vyberte **zapnout**.

### <a name="add-your-facebook-account-to-the-app"></a>Přidání vašeho účtu na Facebooku do aplikace

1. Přejděte na stránku Facebooku ve vašem počítači, **přidat zálohy** části a klikněte na tlačítko **nastavení** z **ověřování aplikace** oblasti.

    Už daný kód QR, který vám umožní automaticky postupu přidružte svůj účet v aplikaci Microsoft Authenticator. Nezavírejte toto okno.

2. Otevřete aplikaci Microsoft Authenticator, vyberte **přidat účet** z **přizpůsobení a řízení** ikonu pravém horním rohu a vyberte **jiný účet (Google, Facebook atd.)** .

3. Naskenování kódu QR pomocí fotoaparátu v zařízení **dvoufaktorové ověřování** stránky ve vašem počítači.

    >[!Note]
    >Pokud vaše kamera nepracuje správně, můžete si [QR kód a adresu URL zadat ručně](#add-an-account-to-the-app-manually).

4. Zkontrolujte **účty** stránka aplikace Microsoft Authenticator na vašem zařízení, abyste měli jistotu, informace o vašem účtu je práva a je k přidružené ověřovací šestimístný kód.

    Za účelem zvýšení zabezpečení ověřovací kód, který změní každých 30 sekund brání pomocí kódu více než jednou.

5. Vyberte **Další** na **dvoufaktorové ověřování** stránky ve vašem počítači a pak zadejte šestičíselný ověřovací kód v aplikaci k dispozici pro váš účet služby Facebook.

    Váš účet je ověřený a nyní můžete aplikaci k ověření svého účtu.

    >[!NOTE]
    >Další informace o dvojúrovňového ověřování a vašeho účtu na Facebooku, najdete v části [novinky dvojúrovňového ověřování a jak to funguje?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>Přidání účtu GitHub
Zapnutí dvojúrovňového ověřování a následným přidáním účtu do aplikace přidáte účet GitHub.

### <a name="turn-on-two-factor-verification"></a>Zapnutí dvojúrovňového ověřování

1. Na vašem počítači, otevřete GitHub, vyberte image v pravém horním rohu a pak vyberte **nastavení**.

    **Dvojúrovňového ověřování** zobrazí se stránka.

2. Vyberte **zabezpečení** z **osobní nastavení** bočním panelu a pak vyberte **povolení dvoufaktorového ověřování** z **dvoufaktorového ověřování**  oblasti.

### <a name="add-your-github-account-to-the-app"></a>Přidání účtu GitHub do aplikace

1. Na **dvojúrovňového ověřování** stránky ve vašem počítači, vyberte **aplikace s využitím**.

2. Uložit kódy pro obnovení, takže se můžete vrátit ke svému účtu pokud ztratíte přístup a pak vyberte **Další**. 

    Stáhnout do svého zařízení, pomocí kopie pevný tisku nebo jejich zkopírováním do nástroje Správce hesel můžete uložit vaše kódy.

3. Na **dvojúrovňového ověřování** stránce **aplikace s využitím**.

    Změny stránky, chcete-li zobrazit kód QR. Nezavírejte tuto stránku.

4. Otevřete aplikaci Microsoft Authenticator, vyberte **přidat účet** z **přizpůsobení a řízení** ikonu vpravo, nahoře vyberte **jiný účet (Google, Facebook atd.)** a pak vyberte **zadejte tento text kód** z textu v horní části stránky.

    Aplikace Microsoft Authenticator nelze naskenovat kód QR, takže je nutné ručně zadat kód.

5. Zadejte **název účtu** (například GitHub) a zadejte **tajný klíč** z kroku 4 a pak vyberte **Dokončit**.

4. Na **dvojúrovňového authenticator** stránky ve vašem počítači, zadejte šestičíselný ověřovací kód v aplikaci k dispozici pro váš účet GitHub a pak vyberte **povolit**.

    **Účty** stránky aplikace ukazuje název účtu a ověřovací šestimístný kód. Za účelem zvýšení zabezpečení ověřovací kód, který změní každých 30 sekund brání pomocí kódu více než jednou.

    >[!NOTE]
    >Další informace o dvojúrovňového ověřování a účtu GitHub, najdete v části [přibližně dvojúrovňového ověřování](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Přidání účtu Amazon
Zapnutí dvojúrovňového ověřování a následným přidáním účtu do aplikace přidáte účtu Amazon.

### <a name="turn-on-two-factor-verification"></a>Zapnutí dvojúrovňového ověřování

1. Ve vašem počítači, otevřete Amazon, vyberte **účet a seznamy** rozevírací nabídky a pak vyberte **účtu**.

2. Vyberte **při & Hlašovací zabezpečení**, přihlaste se ke svému účtu Amazon a pak vyberte **upravit** v **Upřesnit nastavení zabezpečení** oblasti.

    **Upřesnit nastavení zabezpečení** se zobrazí stránka.

3. Vyberte **Začínáme**.

4. Vyberte **aplikace Authenticator** z **zvolte, jak budete dostávat kódy** stránky.

    Změny stránky, chcete-li zobrazit kód QR. Nezavírejte tuto stránku.

5. Otevřete aplikaci Microsoft Authenticator, vyberte **přidat účet** z **přizpůsobení a řízení** ikonu pravém horním rohu a vyberte **jiný účet (Google, Facebook atd.)** .

6. Naskenování kódu QR pomocí fotoaparátu v zařízení **zvolte, jak budete dostávat kódy** stránky ve vašem počítači.

    >[!Note]
    >Pokud vaše kamera nepracuje správně, můžete si [QR kód a adresu URL zadat ručně](#add-an-account-to-the-app-manually).

5. Zkontrolujte **účty** stránka aplikace Microsoft Authenticator na vašem zařízení, abyste měli jistotu, informace o vašem účtu je práva a je k přidružené ověřovací šestimístný kód.

    Za účelem zvýšení zabezpečení ověřovací kód, který změní každých 30 sekund brání pomocí kódu více než jednou.

6. Na **zvolte, jak budete dostávat kódy** stránky ve vašem počítači, zadejte šestičíselný ověřovací kód účtu Amazon k dispozici v aplikaci a pak vyberte **ověřit kód a pokračovat**.

7. Dokončení procesu registrace, včetně přidání metody ověření zálohy například textovou zprávu a pak vyberte **poslat kód**.

8. Na **přidejte metodu ověření zálohy** stránky ve vašem počítači, zadejte šestičíselný ověřovací kód, který poskytuje metodu ověření zálohy účtu Amazon a pak vyberte **ověřit kód a pokračovat**.

9. Na Almost stránky, rozhodněte, jestli se má provádět počítače důvěryhodné zařízení a pak vyberte **rozumím. Zapnutí dvoustupňového ověřování**.

    **Upřesnit nastavení zabezpečení** se zobrazí stránka zobrazující podrobnosti o vašem aktualizované dvojúrovňového ověřování.

    >[!NOTE]
    >Další informace o dvojúrovňového ověřování a účtu Amazon, najdete v části [o dvoustupňové ověření](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) a [přihlášení pomocí dvoustupňového ověření](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).


## <a name="next-steps"></a>Další postup

- Poté, co vaše účty přidáte do aplikace, můžete se přihlásit pomocí aplikace Authenticator na vašem zařízení. Další informace najdete v tématu [Přihlaste se pomocí aplikace](user-help-auth-app-sign-in.md).

- Pro zařízení s Iosem můžete také zálohovat přihlašovací údaje účtu a související nastavení aplikace, jako například pořadí z vašich účtů s cloudem. Další informace najdete v tématu [zálohování a obnovení pomocí aplikace Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
