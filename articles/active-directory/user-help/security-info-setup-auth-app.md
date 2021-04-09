---
title: Nastavení aplikace Microsoft Authenticator jako metody ověřování – Azure AD
description: Jak nastavit stránku informací o zabezpečení (Preview) k ověření vaší identity pomocí aplikace Microsoft Authenticator jako metody ověřování.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: c947bee0b702797a86d1e038f74c6c10e2b23eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103197477"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>Nastavení aplikace Microsoft Authenticator jako metody ověřování

Pomocí těchto kroků můžete přidat metody dvou faktorů ověřování a resetování hesla. Po prvním nastavení se můžete vrátit na stránku **bezpečnostní údaje** a přidat, aktualizovat nebo odstranit informace o zabezpečení.

Pokud se zobrazí výzva k nastavení hned po přihlášení k pracovnímu nebo školnímu účtu, přečtěte si podrobný postup v článku [Nastavení bezpečnostních údajů z výzvy k zadání přihlašovací stránky](security-info-setup-signin.md) .

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Pokud nevidíte možnost aplikace ověřovatele, je možné, že vaše organizace neumožňuje použít tuto možnost k ověření. V takovém případě budete muset zvolit jinou metodu nebo požádat o další pomoc oddělení technické podpory vaší organizace.

## <a name="security-vs-password-reset-verification"></a>Ověření zabezpečení a resetování hesla

Metody bezpečnostních údajů se používají pro ověřování dvou faktorů zabezpečení i pro resetování hesla. Nicméně ne všechny metody lze použít pro obojí.

| Metoda | Použití |
| ------ | -------- |
| Ověřovací aplikace | Dvojúrovňové ověřování a ověřování nového hesla. |
| Textové zprávy | Dvojúrovňové ověřování a ověřování nového hesla. |
| Telefonní hovory | Dvojúrovňové ověřování a ověřování nového hesla. |
| Bezpečnostní klíč | Dvojúrovňové ověřování a ověřování nového hesla. |
| E-mailový účet | Jenom ověřování heslem. Budete muset zvolit jinou metodu pro dvojúrovňové ověřování. |
| Bezpečnostní otázky | Jenom ověřování heslem. Budete muset zvolit jinou metodu pro dvojúrovňové ověřování. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Nastavení aplikace Microsoft Authenticator ze stránky bezpečnostní údaje

V závislosti na nastaveních vaší organizace možná budete moct použít ověřovací aplikaci jako jednu z vašich metod bezpečnostních údajů. Nemusíte používat aplikaci Microsoft Authenticator a během procesu nastavování můžete zvolit jinou aplikaci. Tento článek však používá aplikaci Microsoft Authenticator.

> [!IMPORTANT]
> Pokud jste nastavili Microsoft Authenticator aplikaci na pět různých zařízení nebo pokud jste použili pět hardwarových tokenů, nebudete moct nastavit šestou a může se zobrazit tato chybová zpráva:
> 
> **Nemůžete nastavit Microsoft Authenticator, protože už máte pět ověřovacích aplikací nebo hardwarových tokenů. Pokud chcete odstranit jednu z vašich ověřovacích aplikací nebo hardwarových tokenů, kontaktujte prosím správce.**

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Nastavení aplikace Microsoft Authenticator

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu a pak přejít na https://myaccount.microsoft.com/ stránku.

    ![Stránka můj profil zobrazující odkazy na zvýrazněné informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. V nabídce vlevo vyberte **informace o zabezpečení** nebo použijte odkaz v podokně **bezpečnostní údaje** . Pokud jste už zaregistrovali, budete vyzváni k ověření dvou faktorů. Pak v podokně **bezpečnostní údaje** vyberte **Přidat metodu** .

    ![Stránka informace o zabezpečení se zvýrazněnou možností přidat metodu](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stránce **Přidat metodu** vyberte v rozevíracím seznamu možnost **aplikace Authenticator** a pak vyberte **Přidat**.

    ![Dialogové okno Přidat metodu se zvolenou ověřovací aplikací](media/security-info/securityinfo-myprofile-addauthapp.png)

4. Na stránce **Začněte získáním aplikace** vyberte **Stáhnout** a stáhněte a nainstalujte aplikaci Microsoft Authenticator do mobilního zařízení a pak vyberte **Další**.

    Další informace o tom, jak stáhnout a nainstalovat aplikaci, najdete v tématu [Stažení a instalace aplikace Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Začněte získáním stránky aplikace](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Pokud chcete použít jinou ověřovací aplikaci než aplikaci Microsoft Authenticator, vyberte možnost **Chci použít jiný ověřovací odkaz aplikace** .
   >
   > Pokud vaše organizace umožňuje vybrat jinou metodu než ověřovací aplikace, můžete vybrat, že **chcete nastavit jiný odkaz na metodu**.

5. Při nastavování aplikace Microsoft Authenticator na mobilním zařízení zůstat na stránce **Nastavení účtu** .

    ![Nastavení stránky ověřovací aplikace](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Otevřete aplikaci Microsoft Authenticator, vyberte, jestli se mají zobrazovat oznámení (Pokud se zobrazí výzva), vyberte **Přidat účet** z ikony **přizpůsobení a řízení** v pravém horním rohu a pak vyberte **pracovní nebo školní účet**.

    >[!Note]
    >Pokud Microsoft Authenticator aplikaci nakonfigurujete poprvé, může se zobrazit výzva s dotazem, jestli chcete, aby aplikace měla přístup k vaší kameře (iOS), nebo aby aplikaci mohla pořizovat snímky a nahrávat video (Android). Je nutné vybrat možnost **umožnit** , aby mohla aplikace ověřovatele získat přístup k fotoaparátu, aby pomohlo vytvořit obrázek kódu QR v dalším kroku. Pokud fotoaparát nepovolíte, můžete přesto nastavit ověřovací aplikaci, ale budete muset informace o kódu přidat ručně. Informace o tom, jak kód přidat ručně, najdete v tématu věnovaném [ručnímu přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

7. Vraťte se na váš počítač na stránku **Nastavení účtu** a pak vyberte **Další**.

    Zobrazí se stránka **Kontrola kódu QR** .

    ![Naskenování kódu QR pomocí ověřovací aplikace](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Vyhledejte zadaný kód pomocí čtečky kódu QR Microsoft Authenticator App, která se objevila na vašem mobilním zařízení po vytvoření pracovního nebo školního účtu v kroku 6.

    Aplikace ověřovatele by měla úspěšně přidat svůj pracovní nebo školní účet bez nutnosti jakýchkoli dalších informací. Nicméně pokud čtecí modul QR Code nemůže přečíst kód, můžete vybrat **odkaz Nekontrolovat kód QR** a ručně zadat kód a adresu URL do aplikace Microsoft Authenticator. Další informace o ručním přidání kódu najdete v tématu [Ruční přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

9. V počítači vyberte **Další** na stránce **Kontrola kódu QR** .

    Do aplikace Microsoft Authenticator na vašem mobilním zařízení se pošle oznámení, aby se Váš účet otestoval.

    ![Testování účtu pomocí ověřovací aplikace](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Schvalte oznámení v aplikaci Microsoft Authenticator a pak vyberte **Další**.

     ![Oznámení o úspěšnosti, připojení aplikace a účtu](media/security-info/securityinfo-myprofile-successauthapp.png)

     Vaše bezpečnostní údaje se aktualizují tak, aby ve výchozím nastavení používaly aplikaci Microsoft Authenticator k ověření vaší identity při použití dvoustupňového ověřování nebo resetování hesla.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Odstranění aplikace ověřovatele z metod bezpečnostních údajů

Pokud už nechcete, aby se aplikace ověřovatele používala jako metoda bezpečnostního údaje, můžete ji odebrat ze stránky **bezpečnostní údaje** . Tato funkce funguje pro všechny aplikace ověřovatele, nikoli jenom pro aplikaci Microsoft Authenticator. Po odstranění aplikace budete muset přejít do ověřovací aplikace na mobilním zařízení a účet odstranit.

>[!Important]
>Pokud aplikaci ověřovatele odstraníte omylem, neexistuje žádný způsob, jak ji vrátit. Pak budete muset znovu přidat ověřovací aplikaci podle postupu v části [nastavení ověřovací aplikace](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) v tomto článku.

### <a name="to-delete-the-authenticator-app"></a>Postup odstranění ověřovací aplikace

1. Na stránce **informace o zabezpečení** vyberte odkaz **Odstranit** vedle ověřovací aplikace.

    ![Odkaz pro odstranění aplikace ověřovatele z bezpečnostních údajů](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Pokud chcete ověřovací aplikaci odstranit, vyberte v potvrzovacím poli možnost **Ano** . Po odstranění ověřovací aplikace se odstraní z bezpečnostních údajů a zmizí ze stránky **bezpečnostní údaje** . Pokud je aplikace ověřovatele výchozí metodou, výchozí nastavení se změní na jinou dostupnou metodu.

3. Otevřete ověřovací aplikaci na mobilním zařízení, vyberte **Upravit účty** a pak z ověřovací aplikace odstraňte svůj pracovní nebo školní účet.

    Váš účet je zcela odebrán z ověřovací aplikace pro dvojúrovňové ověřování a žádosti o resetování hesla.

## <a name="change-your-default-security-info-method"></a>Změna výchozí metody zabezpečení údajů

Pokud chcete, aby byla aplikace ověřovatele výchozí metodou použitou při přihlášení ke svému pracovnímu nebo školnímu účtu Pomocí dvojúrovňového ověřování nebo pro žádosti o resetování hesla, můžete ji nastavit na stránce **informace o** zabezpečení.

### <a name="to-change-your-default-security-info-method"></a>Změna výchozí metody zabezpečení údajů

1. Na stránce **informace o zabezpečení** vyberte odkaz **změnit** vedle informací o **výchozím způsobu přihlašování** .

    ![Odkaz pro změnu výchozí metody přihlašování](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. V rozevíracím seznamu dostupných metod vyberte **Microsoft Authenticator-Notification** . Pokud nepoužíváte aplikaci Microsoft Authenticator, vyberte možnost **ověřovací aplikace nebo hardwarového tokenu** .

    ![Zvolit metodu pro výchozí přihlášení](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Vyberte **Potvrdit**.

    Výchozí metoda použitá pro změny přihlášení do aplikace Microsoft Authenticator.

## <a name="additional-security-info-methods"></a>Další metody zabezpečení

Máte k dispozici další možnosti, jak vaše organizace kontaktuje vaši identitu na základě toho, co se you're snaží. Mezi možnosti patří:

- **Text mobilního zařízení** Zadejte číslo mobilního zařízení a získejte text jako kód, který budete používat pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí textové zprávy (SMS), najdete v tématu [Nastavení bezpečnostních údajů pro použití zasílání textových zpráv (SMS)](security-info-setup-text-msg.md).

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo mobilního zařízení a získejte telefonní hovor pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí telefonního čísla, najdete v tématu [Nastavení bezpečnostních údajů pro použití telefonních hovorů](security-info-setup-phone-number.md).

- **Bezpečnostní klíč** Zaregistrujte svůj bezpečnostní klíč kompatibilní s Microsoftem a použijte ho spolu s kódem PIN pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí bezpečnostního klíče, najdete v tématu [Nastavení bezpečnostních údajů pro použití bezpečnostního klíče](security-info-setup-security-key.md).

- **E-mailová adresa** Zadejte svou pracovní nebo školní e-mailovou adresu, abyste získali e-mail pro resetování hesla. Tato možnost není k dispozici pro dvoustupňové ověřování. Podrobné pokyny k nastavení e-mailu najdete v tématu [Nastavení bezpečnostních údajů pro použití e-mailu](security-info-setup-email.md).

- **Bezpečnostní otázky.** Odpovězte na některé bezpečnostní otázky, které vytvořil správce vaší organizace. Tato možnost je k dispozici pouze pro resetování hesla a nikoli pro dvoustupňové ověřování. Podrobné pokyny k nastavení bezpečnostních otázek najdete v článku [Nastavení bezpečnostních informací pro použití bezpečnostních otázek](security-info-setup-questions.md) .

    >[!Note]
    >Pokud některé z těchto možností chybí, je to pravděpodobně proto, že vaše organizace tyto metody nepovoluje. Pokud se jedná o tento případ, budete muset zvolit dostupnou metodu nebo požádat správce o další nápovědu.

## <a name="next-steps"></a>Další kroky

- Přihlaste se pomocí aplikace Microsoft Authenticator, a to podle kroků v článku [přihlášení pomocí dvou kroků ověřování nebo informací o zabezpečení](security-info-setup-signin.md) .

- Resetujte heslo, pokud jste ho ztratili nebo zapomněli z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) , nebo postupujte podle pokynů v článku [resetování svého pracovního nebo školního hesla](active-directory-passwords-update-your-own-password.md) .

- Získejte tipy pro řešení potíží a nápovědu k problémům s přihlášením v tématu se [nemůžete přihlásit k vašemu účet Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .
