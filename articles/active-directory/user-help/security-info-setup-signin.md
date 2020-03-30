---
title: Nastavení bezpečnostních údajů (preview) z výzvy k přihlášení – Azure AD
description: Jak nastavit bezpečnostní údaje (náhled) pro pracovní nebo školní účet, až se zobrazí výzva z přihlašovací stránky vaší organizace.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: overview
ms.date: 08/05/2019
ms.author: curtand
ms.openlocfilehash: c216dbfef99422fc49fde774dc57d5cbcc9f879a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77063981"
---
# <a name="set-up-your-security-info-preview-from-a-sign-in-prompt"></a>Nastavení bezpečnostních údajů (preview) z výzvy k přihlášení

Pokud se zobrazí výzva k nastavení bezpečnostních údajů ihned po přihlášení k pracovnímu nebo školnímu účtu, můžete postupovat podle těchto kroků.

Tuto výzvu zobrazíte pouze v případě, že jste nenastavili bezpečnostní údaje požadované vaší organizací. Pokud jste dříve nastavili bezpečnostní údaje, ale chcete provést změny, můžete postupovat podle pokynů v různých článcích s postupy založenými na metodách. Další informace naleznete v [tématu Přidání nebo aktualizace přehledu bezpečnostních údajů](security-info-add-update-methods-overview.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="security-verification-versus-password-reset-authentication"></a>Ověření zabezpečení versus ověřování při resetování hesla

Metody bezpečnostních informací se používají jak pro dvoufaktorové ověření zabezpečení, tak pro resetování hesla. Ne všechny metody však lze použít pro obě metody.

| Metoda | Použití |
| ------ | -------- |
| Aplikace Authenticator | Dvoufaktorové ověření a ověřování heslem. |
| Textové zprávy | Dvoufaktorové ověření a ověřování heslem. |
| Telefonní hovory | Dvoufaktorové ověření a ověřování heslem. |
| Klíč zabezpečení | Dvoufaktorové ověření a ověřování heslem. |
| E-mailový účet | Pouze ověřování pro resetování hesla. Budete muset zvolit jinou metodu pro dvoufaktorové ověření. |
| Bezpečnostní otázky | Pouze ověřování pro resetování hesla. Budete muset zvolit jinou metodu pro dvoufaktorové ověření. |

## <a name="sign-in-to-your-work-or-school-account"></a>Přihlášení k pracovnímu nebo školnímu účtu

Po přihlášení k pracovnímu nebo školnímu účtu se zobrazí výzva s výzvou k poskytnutí dalších informací, než vám umožní přístup k vašemu účtu.

![Výzva s žádostí o další informace](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Nastavení bezpečnostních údajů pomocí průvodce

Podle těchto kroků nastavte bezpečnostní údaje pracovního nebo školního účtu na výzvu.

>[!Important]
>Toto je pouze příklad procesu. V závislosti na požadavcích vaší organizace je možné, že správce nastavil různé metody ověřování, které budete muset během tohoto procesu nastavit. V tomto příkladu požadujeme dvě metody, aplikaci Microsoft Authenticator a číslo mobilního telefonu pro ověřovací hovory nebo textové zprávy.

1. Po výběru **možnosti Další** z výzvy se zobrazí **Průvodce zabezpečením účtu,** který zobrazí první metodu, kterou správce a organizace vyžadují, abyste nastavili. V tomto příkladu je aplikace Microsoft Authenticator.

   > [!Note]
   > Pokud chcete použít jinou ověřovací aplikaci než aplikaci Microsoft Authenticator, vyberte odkaz **Na aplikaci I Want to use a different authenticator app.**
   >
   > Pokud vaše organizace umožňuje zvolit jinou metodu kromě ověřovací aplikace, můžete vybrat **odkaz Na požadovanou metodu**.

    ![Uchovávejte svůj účet v bezpečí průvodce, zobrazující stránku pro stažení aplikace auth](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Výběrem **možnosti Stáhnout,** chcete-li stáhnout a nainstalovat aplikaci Microsoft Authenticator do mobilního zařízení, a pak vyberte **Další**. Další informace o stažení a instalaci aplikace naleznete v [tématu Stažení a instalace aplikace Microsoft Authenticator](user-help-auth-app-download-install.md).

    ![Průvodce zabezpečením účtu, který zobrazuje ověřovací zařízení Nastavit stránku účtu](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Při nastavování aplikace Microsoft Authenticator na mobilním zařízení zůstanete na stránce **Nastavit účet.**

4. Otevřete aplikaci Microsoft Authenticator, vyberte povolit oznámení (pokud se zobrazí výzva), vyberte **Přidat účet** z ikony Přizpůsobit **a řídit** v pravém horním bodě a pak vyberte Pracovní nebo **školní účet**.

    >[!Note]
    >Pokud nastavujete aplikaci Microsoft Authenticator poprvé, může se zobrazit výzva s dotazem, zda má aplikaci povolit přístup ke kameře (iOS) nebo zda aplikaci pořizovat snímky a nahrávat video (Android). Musíte vybrat **Povolit,** aby ověřovací aplikace mohla přistupovat k fotoaparátu a pořit qr kód v dalším kroku. Pokud fotoaparát nepovolíte, můžete aplikaci pro ověřování nastavit, ale budete muset přidat informace o kódu ručně. Informace o ručním přidání kódu najdete v [tématu Ruční přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

5. Vraťte se na stránku **Nastavení účtu** v počítači a pak vyberte **Další**.

    Zobrazí **se obrazovka Prohledává qr kódovou** stránku.

    ![Skenování QR kódu pomocí aplikace Authenticator](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Naskenujte poskytnutý kód pomocí čtečky QR kódů aplikace Microsoft Authenticator, která se na vašem mobilním zařízení objevila po vytvoření pracovního nebo školního účtu v kroku 5.

    Ověřovací aplikace by měla úspěšně přidat váš pracovní nebo školní účet, aniž by od vás vyžadovala další informace. Pokud však čtečka QR kódů nemůže kód přečíst, můžete vybrat **možnost Nelze naskenovat bitovou kopii QR** a ručně zadat kód a adresu URL do aplikace Microsoft Authenticator. Další informace o ručním přidání kódu najdete v [tématu Ruční přidání účtu do aplikace](user-help-auth-app-add-account-manual.md).

7. Na stránce **Scan em qr kód v** počítači vyberte **Další.**

    Oznámení se odešle do aplikace Microsoft Authenticator na vašem mobilním zařízení, aby se váš účet otestoval.

    ![Otestujte svůj účet pomocí ověřovací aplikace](media/security-info/securityinfo-prompt-test-app.png)

8. Schválení oznámení v aplikaci Microsoft Authenticator a pak vyberte **Další**.

    ![Oznámení o úspěchu, připojení aplikace a vašeho účtu](media/security-info/securityinfo-prompt-auth-app-success.png)

    Vaše bezpečnostní údaje se aktualizují tak, aby při použití dvoustupňového ověření nebo resetování hesla používaly aplikaci Microsoft Authenticator k ověření vaší identity.

9. Na stránce **Nastavení telefonu** zvolte, jestli chcete dostávat textové zprávy nebo telefonní hovor, a pak vyberte **Další**. Pro účely tohoto příkladu používáme textové zprávy, takže je nutné použít telefonní číslo pro zařízení, které může přijímat textové zprávy.

    ![Zahájení nastavení telefonního čísla pro textové zprávy](media/security-info/securityinfo-prompt-text-msg.png)

    Na vaše telefonní číslo je odeslána textová zpráva. Pokud by raději dostat telefonní hovor, proces je stejný. Místo textové zprávy však obdržíte telefonní hovor s pokyny.

10. Zadejte kód poskytnutý textovou zprávou odeslanou do mobilního zařízení a pak vyberte **Další**.

    ![Testování účtu pomocí textové zprávy](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Zkontrolujte oznámení o úspěchu a pak vyberte **Hotovo**.

    ![Oznámení o úspěchu](media/security-info/securityinfo-prompt-call-answered-success.png)

    Vaše bezpečnostní údaje jsou aktualizovány tak, aby používaly textové zprávy jako metodu zálohování k ověření vaší identity při použití dvoustupňového ověření nebo resetování hesla.

12. Projděte si stránku **Úspěch** a ověřte, zda jste úspěšně nastavili aplikaci Microsoft Authenticator i metodu telefonu (textovou zprávu nebo telefonní hovor) pro vaše bezpečnostní údaje, a pak vyberte **Hotovo**.

    ![Průvodce úspěšně dokončil stránku](media/security-info/securityinfo-prompt-setup-success.png)

    >[!Note]
    >Pokud vaše organizace vyžaduje, abyste používali hesla aplikací, může se vám v tomto průvodci zobrazit další oddíl, ve kterém ji můžete nastavit. Pokud se zobrazí třetí část s názvem **Hesla aplikací**, musíte ji vyplnit, než budete moci průvodce dokončit. Postup přidání hesla aplikace najdete v tomto článku v části [Správa hesel aplikací.](#manage-your-app-passwords)

### <a name="manage-your-app-passwords"></a>Správa hesel aplikací

Některé aplikace, například Outlook 2010, nepodporují dvoustupňové ověření. Tento nedostatek podpory znamená, že pokud ve vaší organizaci používáte dvoustupňové ověření, aplikace nebude fungovat. Chcete-li tento problém vyřešit, můžete vytvořit automaticky generované heslo pro použití s každou aplikací bez prohlížeče, odděleně od normálního hesla.

>[!Note]
>Pokud tuto možnost v průvodci nevidíte, znamená to, že ji správce nenastavil. Pokud to není nastavené, ale víte, že potřebujete používat hesla aplikací, můžete postupovat podle pokynů v části [Nastavení hesel aplikací na stránce Bezpečnostní údaje (náhled).](security-info-app-passwords.md)

Při používání hesel aplikací je důležité mít na paměti:

- Hesla aplikací se automaticky generují a zadávají se pouze jednou za aplikaci.

- Je zde limit 40 hesel na uživatele. Pokud se pokusíte vytvořit po tomto limitu, budete vyzváni k odstranění existujícího hesla před povolením k vytvoření nového.

- Použijte jedno heslo aplikace na zařízení, ne na aplikaci. Můžete například vytvořit jediné heslo pro všechny aplikace v přenosném počítači a pak další jediné heslo pro všechny aplikace na ploše.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>Přidání hesel aplikací v průvodci přihlášením

1. Po dokončení předchozích částí průvodce vyberte **Další** a vyplňte oddíl **Heslo aplikace.**

2. Zadejte název aplikace, která potřebuje heslo, například `Outlook 2010`a pak vyberte **Další**.

    ![Přidání názvu hesla aplikace do průvodce](media/security-info/app-password-app-password.png)

3. Zkopírujte kód hesla z obrazovky **s heslem aplikace** a vložte ho do oblasti **Heslo** aplikace (v tomto příkladu Outlook 2010).

    ![Stránka s heslem aplikace s heslem ke kopírování](media/security-info/app-password-copy-password.png)

4. Po zkopírování hesla a jeho vložení do aplikace se vraťte k tomuto průvodci, abyste se ujistili, že jsou všechny informace o metodě přihlášení přesné, a pak vyberte **Hotovo**.

    ![Stránka s heslem aplikace s oznámením o dokončení](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>Další kroky

- Informace o změně, odstranění nebo aktualizaci výchozích metod informací o zabezpečení najdete v tématu:

    - [Nastavte bezpečnostní údaje pro ověřovací aplikaci](security-info-setup-auth-app.md).

    - [Nastavte bezpečnostní údaje pro textové zprávy](security-info-setup-text-msg.md).

    - [Nastavte bezpečnostní údaje pro používání telefonních hovorů](security-info-setup-phone-number.md).

    - [Nastavte bezpečnostní údaje pro použití e-mailu](security-info-setup-email.md).

    - [Nastavte bezpečnostní údaje tak, aby používaly předdefinované bezpečnostní otázky](security-info-setup-questions.md).

- Informace o tom, jak se přihlásit pomocí zadané metody, naleznete [v tématu Jak se přihlásit](user-help-sign-in.md).

- Pokud jste heslo ztratili nebo zapomněli, resetujte ho na [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) nebo postupujte podle pokynů v článku [Obnovit pracovní nebo školní heslo.](active-directory-passwords-update-your-own-password.md)

- V článku Nelze se [přihlásit k účtu Microsoft,](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) najdete tipy pro řešení potíží a nápovědu k problémům s přihlášením.
