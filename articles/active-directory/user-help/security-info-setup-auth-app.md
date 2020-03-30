---
title: Nastavení aplikace Microsoft Authenticator jako metody ověření – Azure AD
description: Jak nastavit stránku Bezpečnostní údaje (náhled) pro ověření vaší identity pomocí aplikace Microsoft Authenticator jako metody ověření.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 16cb512d3f8f8afcc199cb52c13e09b12107576f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062417"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>Nastavení aplikace Microsoft Authenticator jako metody ověření

Pomocí těchto kroků můžete přidat dvoufaktorové ověření a metody resetování hesla. Po prvním nastavení se můžete vrátit na stránku **Bezpečnostní údaje** a přidat, aktualizovat nebo odstranit bezpečnostní údaje.

Pokud se zobrazí výzva k nastavení ihned po přihlášení k pracovnímu nebo školnímu účtu, přečtěte si podrobné kroky v článku Nastavení bezpečnostních údajů v článku [výzvy k přihlášení.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Pokud možnost ověřovací aplikace nevidíte, je možné, že vaše organizace neumožňuje použít tuto možnost k ověření. V takovém případě budete muset zvolit jinou metodu nebo se obrátit na technickou podporu vaší organizace s žádostí o další pomoc.

## <a name="security-vs-password-reset-verification"></a>Zabezpečení vs ověření obnovení hesla

Metody bezpečnostních informací se používají jak pro dvoufaktorové ověření zabezpečení, tak pro resetování hesla. Ne všechny metody však lze použít pro obě metody.

| Metoda | Použití |
| ------ | -------- |
| Aplikace Authenticator | Dvoufaktorové ověření a ověřování heslem. |
| Textové zprávy | Dvoufaktorové ověření a ověřování heslem. |
| Telefonní hovory | Dvoufaktorové ověření a ověřování heslem. |
| Klíč zabezpečení | Dvoufaktorové ověření a ověřování heslem. |
| E-mailový účet | Pouze ověřování pro resetování hesla. Budete muset zvolit jinou metodu pro dvoufaktorové ověření. |
| Bezpečnostní otázky | Pouze ověřování pro resetování hesla. Budete muset zvolit jinou metodu pro dvoufaktorové ověření. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Nastavení aplikace Microsoft Authenticator na stránce Informace o zabezpečení

V závislosti na nastavení vaší organizace můžete jako jednu z metod bezpečnostních informací použít ověřovací aplikaci. Nemusíte používat aplikaci Microsoft Authenticator a během procesu nastavení si můžete vybrat jinou aplikaci. Tento článek však používá aplikaci Microsoft Authenticator.

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Nastavení aplikace Microsoft Authenticator

1. Přihlaste se ke svému pracovnímu https://myprofile.microsoft.com/ nebo školnímu účtu a přejděte na stránku.

    ![Stránka Můj profil se zvýrazněnými odkazy Na informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. V levém navigačním podokně nebo z odkazu v bloku **Informace o zabezpečení** vyberte Bezpečnostní **údaje** a na stránce Informace **o zabezpečení** vyberte **Přidat metodu.**

    ![Stránka S informacemi o zabezpečení se zvýrazněnou možností Přidat metodu](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stránce **Přidat metodu** vyberte v rozevíracím seznamu **aplikaci Authenticator** a pak vyberte **Přidat**.

    ![Pole Přidat metodu s vybranou aplikací Authenticator](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Na **úvodní obrazovce zobrazením** stránky aplikace vyberte **Stáhnout a** stáhněte a nainstalujte aplikaci Microsoft Authenticator do mobilního zařízení a pak vyberte **Další**.

    Další informace o stažení a instalaci aplikace naleznete v [tématu Stažení a instalace aplikace Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Začněte zobrazením stránky aplikace](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Pokud chcete použít jinou ověřovací aplikaci než aplikaci Microsoft Authenticator, vyberte odkaz **Na aplikaci I Want to use a different authenticator app.**
   >
   > Pokud vaše organizace umožňuje zvolit jinou metodu kromě ověřovací aplikace, můžete vybrat **odkaz Na požadovanou metodu**.

5. Při nastavování aplikace Microsoft Authenticator na mobilním zařízení zůstanete na stránce **Nastavit účet.**

    ![Nastavení stránky aplikace pro ověřování pravosti](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Otevřete aplikaci Microsoft Authenticator, vyberte povolit oznámení (pokud se zobrazí výzva), vyberte **Přidat účet** z ikony Přizpůsobit **a řídit** v pravém horním bodě a pak vyberte Pracovní nebo **školní účet**.

    >[!Note]
    >Pokud nastavujete aplikaci Microsoft Authenticator poprvé, může se zobrazit výzva s dotazem, zda má aplikaci povolit přístup ke kameře (iOS) nebo zda aplikaci pořizovat snímky a nahrávat video (Android). Musíte vybrat **Povolit,** aby ověřovací aplikace mohla přistupovat k fotoaparátu a pořit qr kód v dalším kroku. Pokud fotoaparát nepovolíte, můžete aplikaci pro ověřování nastavit, ale budete muset přidat informace o kódu ručně. Informace o ručním přidání kódu najdete v [tématu Ruční přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

7. Vraťte se na stránku **Nastavení účtu** v počítači a pak vyberte **Další**.

    Zobrazí **se obrazovka Prohledává qr kódovou** stránku.

    ![Skenování QR kódu pomocí aplikace Authenticator](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Naskenujte poskytnutý kód pomocí čtečky QR kódů aplikace Microsoft Authenticator, která se na vašem mobilním zařízení objevila po vytvoření pracovního nebo školního účtu v kroku 6.

    Ověřovací aplikace by měla úspěšně přidat váš pracovní nebo školní účet, aniž by od vás vyžadovala další informace. Pokud však čtečka QR kódů nemůže kód přečíst, můžete vybrat **odkaz Nelze naskenovat kód QR** a ručně zadat kód a adresu URL do aplikace Microsoft Authenticator. Další informace o ručním přidání kódu najdete v [tématu Ruční přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

9. Na stránce **Scan em qr kód v** počítači vyberte **Další.**

    Oznámení se odešle do aplikace Microsoft Authenticator na vašem mobilním zařízení, aby se váš účet otestoval.

    ![Otestujte svůj účet pomocí ověřovací aplikace](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Schválení oznámení v aplikaci Microsoft Authenticator a pak vyberte **Další**.

     ![Oznámení o úspěchu, připojení aplikace a vašeho účtu](media/security-info/securityinfo-myprofile-successauthapp.png)

     Vaše bezpečnostní údaje se aktualizují tak, aby při použití dvoustupňového ověření nebo resetování hesla používaly aplikaci Microsoft Authenticator k ověření vaší identity.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Odstranění ověřovací aplikace z metod bezpečnostních informací

Pokud už nechcete aplikaci ověřovatele používat jako metodu bezpečnostních informací, můžete ji odebrat ze stránky **Informace o zabezpečení.** To funguje pro všechny ověřovací aplikace, nejen pro aplikaci Microsoft Authenticator. Po odstranění aplikace budete muset přejít do ověřovací aplikace na mobilním zařízení a odstranit účet.

>[!Important]
>Pokud aplikaci authenticator omylem smažete, neexistuje žádný způsob, jak ji vrátit. Budete muset znovu přidat ověřovací aplikaci podle kroků v části [Nastavení aplikace pro ověřování](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) v tomto článku.

### <a name="to-delete-the-authenticator-app"></a>Odstranění ověřovací aplikace

1. Na stránce **Bezpečnostní informace** vyberte odkaz **Odstranit** vedle aplikace Authenticator.

    ![Odkaz na odstranění ověřovací aplikace z bezpečnostních informací](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. **Chcete-li** odstranit ověřovací aplikaci, vyberte v potvrzovacím poli ano. Po odstranění ověřovací aplikace se aplikace odebere z bezpečnostních údajů a zmizí ze stránky **Bezpečnostní údaje.** Pokud ověřovací aplikace je výchozí metoda, výchozí se změní na jinou dostupnou metodu.

3. Otevřete ověřovací aplikaci na mobilním zařízení, vyberte **Upravit účty**a pak odstraňte pracovní nebo školní účet z ověřovací aplikace.

    Váš účet je zcela odebrán z ověřovací aplikace pro dvoufaktorové ověření a resetování hesla.

## <a name="change-your-default-security-info-method"></a>Změna výchozí metody informací o zabezpečení

Pokud chcete, aby ověřovací aplikace byla výchozí metodou používanou při přihlášení k pracovnímu nebo školnímu účtu pomocí dvoufaktorového ověření nebo pro žádosti o resetování hesla, můžete ji nastavit na stránce **Bezpečnostní údaje.**

### <a name="to-change-your-default-security-info-method"></a>Změna výchozí metody informací o zabezpečení

1. Na stránce **Informace o zabezpečení** vyberte odkaz **Změnit** vedle **výchozíinformace o metodě přihlášení.**

    ![Změnit odkaz pro výchozí metodu přihlášení](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Zvolte **Microsoft Authenticator - oznámení** z rozevíracího seznamu dostupných metod. Pokud nepoužíváte aplikaci Microsoft Authenticator, vyberte možnost **ověřovací aplikace nebo hardwarového tokenu.**

    ![Zvolit metodu pro výchozí přihlášení](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Vyberte **Potvrdit**.

    Výchozí metoda používaná pro změny přihlášení do aplikace Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Další metody bezpečnostních informací

Máte další možnosti, jak vás vaše organizace kontaktuje za účelem ověření vaší identity, a to na základě toho, o co se pokoušíte. Mezi možnosti patří:

- **Text mobilního zařízení.** Zadejte číslo svého mobilního zařízení a získejte text, který použijete pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k ověření identity pomocí textové zprávy (SMS) najdete v tématu [Nastavení bezpečnostních údajů pro používání textových zpráv (SMS).](security-info-setup-text-msg.md)

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo svého mobilního zařízení a získejte telefonní hovor pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k ověření identity pomocí telefonního čísla najdete v tématu [Nastavení bezpečnostních údajů pro používání telefonních hovorů](security-info-setup-phone-number.md).

- **Bezpečnostní klíč.** Zaregistrujte svůj bezpečnostní klíč kompatibilní se společností Microsoft a použijte jej spolu s kódem PIN pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k ověření identity pomocí bezpečnostního klíče naleznete v tématu [Nastavení bezpečnostních údajů pro použití bezpečnostního klíče](security-info-setup-security-key.md).

- **E-mailová adresa.** Zadejte svou pracovní nebo školní e-mailovou adresu a získejte e-mail s žádostí o resetování hesla. Tato možnost není k dispozici pro dvoustupňové ověření. Podrobné pokyny k nastavení e-mailu najdete v tématu [Nastavení bezpečnostních údajů pro používání e-mailu](security-info-setup-email.md).

- **Bezpečnostní otázky.** Odpovězte na některé bezpečnostní otázky vytvořené správcem pro vaši organizaci. Tato možnost je k dispozici pouze pro resetování hesla a ne pro dvoustupňové ověření. Podrobné pokyny k nastavení bezpečnostních otázek najdete v článku [Nastavení bezpečnostních údajů pro použití bezpečnostních otázek.](security-info-setup-questions.md)

    >[!Note]
    >Pokud některé z těchto možností chybí, je to s největší pravděpodobností proto, že vaše organizace tyto metody nepovoluje. V takovém případě budete muset zvolit dostupnou metodu nebo požádat správce o další pomoc.

## <a name="next-steps"></a>Další kroky

- Přihlaste se pomocí aplikace Microsoft Authenticator podle kroků v článku [Přihlášení pomocí dvoustupňového ověření nebo informací o zabezpečení.](security-info-setup-signin.md)

- Pokud jste heslo ztratili nebo zapomněli, resetujte ho na [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) nebo postupujte podle pokynů v článku [Obnovit pracovní nebo školní heslo.](active-directory-passwords-update-your-own-password.md)

- V článku Nelze se [přihlásit k účtu Microsoft,](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) najdete tipy pro řešení potíží a nápovědu k problémům s přihlášením.
