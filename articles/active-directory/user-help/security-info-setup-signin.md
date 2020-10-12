---
title: Nastavení bezpečnostních údajů z výzvy k přihlášení – Azure AD
description: Nastavení bezpečnostních údajů pro svůj pracovní nebo školní účet po zobrazení výzvy na přihlašovací stránce vaší organizace.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/07/2020
ms.author: curtand
ms.openlocfilehash: 016e0de26bf3a9513940be161cfecf6d1c2b9d02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88798495"
---
# <a name="set-up-your-security-info-from-a-sign-in-prompt"></a>Nastavení bezpečnostních údajů z výzvy k přihlášení

Pokud se zobrazí výzva k nastavení bezpečnostních údajů hned po přihlášení ke svému pracovnímu nebo školnímu účtu, můžete postupovat podle těchto kroků.

Tato výzva se zobrazí jenom v případě, že jste nenastavili bezpečnostní údaje vyžadované vaší organizací. Pokud jste si dříve nastavili bezpečnostní údaje, ale chcete provést změny, můžete postupovat podle kroků v různých článcích o postupech založených na metodách. Další informace najdete v tématu věnovaném [Přidání nebo aktualizaci informací o zabezpečení](./security-info-setup-auth-app.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="security-verification-versus-password-reset-authentication"></a>Ověření zabezpečení oproti ověřování hesla pro resetování hesla

Metody bezpečnostních údajů se používají pro ověřování dvou faktorů zabezpečení i pro resetování hesla. Nicméně ne všechny metody lze použít pro obojí.

| Metoda | Použití |
| ------ | -------- |
| Ověřovací aplikace | Dvojúrovňové ověřování a ověřování nového hesla. |
| Textové zprávy | Dvojúrovňové ověřování a ověřování nového hesla. |
| Telefonní hovory | Dvojúrovňové ověřování a ověřování nového hesla. |
| Bezpečnostní klíč | Dvojúrovňové ověřování a ověřování nového hesla. |
| E-mailový účet | Jenom ověřování heslem. Budete muset zvolit jinou metodu pro dvojúrovňové ověřování. |
| Bezpečnostní otázky | Jenom ověřování heslem. Budete muset zvolit jinou metodu pro dvojúrovňové ověřování. |

## <a name="sign-in-to-your-work-or-school-account"></a>Přihlaste se ke svému pracovnímu nebo školnímu účtu.

Po přihlášení ke svému pracovnímu nebo školnímu účtu se zobrazí výzva s výzvou k zadání dalších informací, než vám umožní přístup k vašemu účtu.

![Dotaz na Další informace](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Nastavení bezpečnostních údajů pomocí Průvodce

Postupujte podle těchto kroků a nastavte bezpečnostní údaje pro svůj pracovní nebo školní účet z příkazového řádku.

>[!Important]
>Toto je pouze příklad procesu. V závislosti na požadavcích vaší organizace může správce nastavit různé metody ověřování, které budete muset během tohoto procesu nastavit. V tomto příkladu vyžadujeme dvě metody, Microsoft Authenticator aplikace a mobilní telefonní číslo pro ověřovací hovory nebo textové zprávy.

1. Až na příkazovém řádku vyberete **Další** , zobrazí se **Průvodce zabezpečením vašeho účtu** , ve kterém se zobrazí první metoda, kterou váš správce a organizace vyžaduje, abyste si je nastavili. V tomto příkladu je to aplikace Microsoft Authenticator.

   > [!Note]
   > Pokud chcete použít jinou ověřovací aplikaci než aplikaci Microsoft Authenticator, vyberte možnost **Chci použít jiný ověřovací odkaz aplikace** .
   >
   > Pokud vaše organizace umožňuje vybrat jinou metodu než ověřovací aplikace, můžete vybrat, že **chcete nastavit jiný odkaz na metodu**.

    ![Ponechte si Průvodce zabezpečením účtu a zobrazí se stránka pro stažení aplikace ověřování.](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Vyberte **Stáhnout** a stáhněte a nainstalujte aplikaci Microsoft Authenticator do svého mobilního zařízení a pak vyberte **Další**. Další informace o tom, jak stáhnout a nainstalovat aplikaci, najdete v tématu [Stažení a instalace aplikace Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Ponechte si Průvodce zabezpečením účtu a zobrazí se stránka s informacemi o nastavení účtu.](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Při nastavování aplikace Microsoft Authenticator na mobilním zařízení zůstat na stránce **Nastavení účtu** .

4. Otevřete aplikaci Microsoft Authenticator, vyberte, jestli se mají zobrazovat oznámení (Pokud se zobrazí výzva), vyberte **Přidat účet** z ikony **přizpůsobení a řízení** v pravém horním rohu a pak vyberte **pracovní nebo školní účet**.

    >[!Note]
    >Pokud Microsoft Authenticator aplikaci nakonfigurujete poprvé, může se zobrazit výzva s dotazem, jestli chcete, aby aplikace měla přístup k vaší kameře (iOS), nebo aby aplikaci mohla pořizovat snímky a nahrávat video (Android). Je nutné vybrat možnost **umožnit** , aby mohla aplikace ověřovatele získat přístup k fotoaparátu, aby pomohlo vytvořit obrázek kódu QR v dalším kroku. Pokud fotoaparát nepovolíte, můžete přesto nastavit ověřovací aplikaci, ale budete muset informace o kódu přidat ručně. Informace o tom, jak kód přidat ručně, najdete v tématu věnovaném [ručnímu přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

5. Vraťte se na váš počítač na stránku **Nastavení účtu** a pak vyberte **Další**.

    Zobrazí se stránka **Kontrola kódu QR** .

    ![Naskenování kódu QR pomocí ověřovací aplikace](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Vyhledejte zadaný kód pomocí čtečky kódu QR Microsoft Authenticator App, která se objevila na vašem mobilním zařízení po vytvoření pracovního nebo školního účtu v kroku 5.

    Aplikace ověřovatele by měla úspěšně přidat svůj pracovní nebo školní účet bez nutnosti jakýchkoli dalších informací. Pokud však čtecí modul pro kód QR nemůže přečíst kód, můžete vybrat **bitovou kopii QR nelze vyhledat** a ručně zadat kód a adresu URL do aplikace Microsoft Authenticator. Další informace o ručním přidání kódu najdete v tématu [Ruční přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

7. V počítači vyberte **Další** na stránce **Kontrola kódu QR** .

    Do aplikace Microsoft Authenticator na vašem mobilním zařízení se pošle oznámení, aby se Váš účet otestoval.

    ![Testování účtu pomocí ověřovací aplikace](media/security-info/securityinfo-prompt-test-app.png)

8. Schvalte oznámení v aplikaci Microsoft Authenticator a pak vyberte **Další**.

    ![Oznámení o úspěšnosti, připojení aplikace a účtu](media/security-info/securityinfo-prompt-auth-app-success.png)

    Vaše bezpečnostní údaje se aktualizují tak, aby ve výchozím nastavení používaly aplikaci Microsoft Authenticator k ověření vaší identity při použití dvoustupňového ověřování nebo resetování hesla.

9. Na stránce nastavení **telefonu** zvolte, jestli chcete dostávat textovou zprávu nebo telefonní hovor, a pak vyberte **Další**. Pro účely tohoto příkladu používáme textové zprávy, takže musíte použít telefonní číslo zařízení, které může přijímat textové zprávy.

    ![Zahájení nastavování telefonního čísla pro zasílání textových zpráv](media/security-info/securityinfo-prompt-text-msg.png)

    Do vašeho telefonního čísla se pošle textová zpráva. Pokud by to chtělo získat telefonní hovor, proces je stejný. Místo textové zprávy se ale zobrazí telefonní hovor s pokyny.

10. Zadejte kód poskytnutý textovou zprávou odeslanou do mobilního zařízení a pak vyberte **Další**.

    ![Testování účtu pomocí textové zprávy](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Přečtěte si oznámení o úspěšnosti a potom vyberte **Hotovo**.

    ![Oznámení o úspěchu](media/security-info/securityinfo-prompt-call-answered-success.png)

    Vaše bezpečnostní údaje se aktualizují, aby používaly textové zasílání zpráv jako metodu zálohování k ověření vaší identity při použití dvoustupňového ověřování nebo resetování hesla.

12. Zkontrolujte stránku **úspěch** a ověřte, že jste úspěšně nastavili aplikaci Microsoft Authenticator a telefon (buď textovou zprávu nebo telefonní hovor) pro vaše bezpečnostní údaje, a pak vyberte **Hotovo**.

    ![Stránka průvodce byla úspěšně dokončena.](media/security-info/securityinfo-prompt-setup-success.png)

    >[!Note]
    >Pokud vaše organizace vyžaduje, abyste používali hesla aplikací, může se v tomto průvodci zobrazit další část, kde ji můžete nastavit. Pokud se zobrazí třetí část s názvem **hesla aplikací**, je nutné ji vyplnit předtím, než budete moci průvodce Dokončit. Postup přidání hesla aplikace najdete v části [Správa hesel aplikací](#manage-your-app-passwords) v tomto článku.

### <a name="manage-your-app-passwords"></a>Správa hesel aplikací

Některé aplikace, jako je například Outlook 2010, nepodporují dvoustupňové ověřování. Tato nedostatečná podpora znamená, že pokud ve vaší organizaci používáte dvoustupňové ověřování, aplikace nebude fungovat. Pokud se chcete tomuto problému vyhnout, můžete vytvořit automaticky vygenerované heslo, které se použije u každé neprohlížečové aplikace, oddělené od normálního hesla.

>[!Note]
>Pokud tuto možnost nevidíte v průvodci, znamená to, že ji správce nevytvořil. Pokud tato možnost není nastavená, ale víte, že je potřeba použít hesla aplikací, můžete postupovat podle kroků uvedených v části [Nastavení hesel aplikací na stránce informace o zabezpečení](security-info-app-passwords.md).

Při používání hesel aplikací je důležité pamatovat:

- Hesla aplikací se generují automaticky a zadávají se jenom jednou pro každou aplikaci.

- Pro každého uživatele je povolený limit 40 hesel. Pokud se po uplynutí tohoto limitu pokusíte jeden vytvořit, budete vyzváni k odstranění existujícího hesla, než bude povoleno vytvoření nového.

- Použijte jedno heslo aplikace na zařízení, ne na aplikaci. Můžete například vytvořit jedno heslo pro všechny aplikace na přenosném počítači a další jedno heslo pro všechny aplikace na ploše.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>Přidání hesel aplikací v průvodci přihlášením

1. Po dokončení předchozích částí průvodce vyberte **Další** a dokončete oddíl **heslo aplikace** .

2. Zadejte název aplikace, která potřebuje heslo, například `Outlook 2010` a potom vyberte **Další**.

    ![Přidání názvu hesla aplikace v Průvodci](media/security-info/app-password-app-password.png)

3. Zkopírujte kód pro heslo z obrazovky pro **heslo aplikace** a vložte ho do oblasti **hesla** aplikace (v tomto příkladu je to Outlook 2010).

    ![Stránka hesla aplikace s heslem pro kopírování](media/security-info/app-password-copy-password.png)

4. Po zkopírování hesla a jeho vložení do aplikace se vraťte k tomuto průvodci a ujistěte se, že všechny informace o způsobu přihlašování jsou přesné a potom vyberte **Hotovo**.

    ![Stránka hesla aplikace s upozorněním na dokončení](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak změnit, odstranit nebo aktualizovat výchozí bezpečnostní údaje, najdete v těchto tématech:

    - [Nastavte bezpečnostní údaje pro ověřovací aplikaci](security-info-setup-auth-app.md).

    - [Nastavte bezpečnostní údaje pro zasílání textových zpráv](security-info-setup-text-msg.md).

    - [Nastavte bezpečnostní údaje pro použití telefonních hovorů](security-info-setup-phone-number.md).

    - [Nastavte bezpečnostní údaje pro použití e-mailu](security-info-setup-email.md).

    - [Nastavte bezpečnostní údaje pro použití předem definovaných bezpečnostních otázek](security-info-setup-questions.md).

- Informace o tom, jak se pomocí zadané metody přihlásit, najdete v tématu [jak se přihlásit](user-help-sign-in.md).

- Resetujte heslo, pokud jste ho ztratili nebo zapomněli z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) , nebo postupujte podle pokynů v článku [resetování svého pracovního nebo školního hesla](active-directory-passwords-update-your-own-password.md) .

- Získejte tipy pro řešení potíží a nápovědu k problémům s přihlášením v tématu se [nemůžete přihlásit k vašemu účet Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .