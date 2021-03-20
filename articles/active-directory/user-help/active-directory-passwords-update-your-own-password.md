---
title: Resetování hesla pomocí informací o zabezpečení – Azure Active Directory | Microsoft Docs
description: Postup resetování vlastního hesla, pokud ho zapomenete, pomocí vašich bezpečnostních údajů a dvoustupňového ověřování.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/10/2020
ms.author: curtand
ms.openlocfilehash: c47d573dab3e72a956f6f0b9d09a1dafadf0225c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91827763"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>Resetujte své pracovní nebo školní heslo pomocí bezpečnostních údajů

Pokud jste zapomněli své pracovní nebo školní heslo, nikdy nebudete mít heslo z vaší organizace nebo jste jeho uzamčením nedostali svůj účet, můžete k resetování svého pracovního nebo školního hesla použít své bezpečnostní údaje a mobilní zařízení. Správce musí tuto funkci zapnout, abyste mohli nastavit vaše informace a [resetovat svoje vlastní heslo](./active-directory-passwords-reset-register.md).

Pokud znáte heslo a chcete ho změnit, přečtěte si část [Změna kroků hesla](#how-to-change-your-password) v tomto článku.

>[!Important]
>Tento článek je určený pro uživatele, kteří se pokoušejí obnovit zapomenuté nebo neznámé heslo pracovního nebo školního účtu. Pokud jste správcem a hledáte informace o tom, jak zapnout Samoobslužné resetování hesla pro vaše zaměstnance nebo jiné uživatele, přečtěte si téma [nasazení samoobslužného resetování hesla Azure AD a dalších článků](../authentication/howto-sspr-deployment.md).

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Resetování nebo odemknutí hesla pro pracovní nebo školní účet

Pokud nemůžete získat přístup k účtu Azure Active Directory (Azure AD), může to být způsobeno těmito možnostmi:

- Heslo nefunguje a chcete ho resetovat.

- Vaše heslo znáte, ale váš účet je uzamčený a je potřeba ho odemknout.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Resetování hesla a návrat k účtu

1. Na obrazovce **zadání hesla** vyberte **zapomenuté heslo**.

2. Na obrazovce **Přejít zpět na účet** zadejte své ID pracovního nebo školního **uživatele** (například vaši e-mailovou adresu), prokázání, že nejste robot, zadáním znaků, které vidíte na obrazovce, a pak vyberte **Další**.

   ![Vrátit se zpátky na obrazovku účtu](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Pokud váš správce nezapnul možnost resetovat si vlastní heslo, zobrazí se vám odkaz s odkazem na **správce** , místo aby se **vrátil zpátky na obrazovku účtu** . Tento odkaz vám umožní kontaktovat správce o resetování hesla, a to prostřednictvím e-mailu nebo webového portálu.

3. Vyberte jednu z následujících metod pro ověření identity a změnu hesla. V závislosti na tom, jak váš správce nastavil vaši organizaci, možná budete muset projít tento proces podruhé a přidat informace pro druhý ověřovací krok.

    ![Vraťte se k účtu, #1 kroku ověření](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >V závislosti na tom, jak váš správce nastavil vaši organizaci, nemusí být některé z těchto možností ověřování k dispozici. Aby bylo možné ověřit mobilní zařízení pomocí alespoň jedné z těchto metod, je nutné, abyste měli nastavení mobilního zařízení.<br><br>Kromě toho může vaše nové heslo vyžadovat splnění určitých požadavků na sílu. Silná hesla mají obvykle 8 až 16 znaků, včetně velkých a malých písmen, aspoň jedno číslo a aspoň jeden speciální znak.

- **Resetujte heslo pomocí e-mailové adresy.** Pošle e-mail na e-mailovou adresu, kterou jste předtím nastavili v rámci dvoustupňového ověřování, nebo bezpečnostní údaje. Pokud správce zapnul prostředí informací o zabezpečení, najdete další informace o nastavení e-mailové adresy v článku [Nastavení informací o zabezpečení pro použití e-mailu (Preview)](security-info-setup-email.md) . Pokud ještě nepoužíváte bezpečnostní údaje, najdete další informace o nastavení e-mailové adresy v článku [Nastavení účtu pro dvoustupňové ověřování](multi-factor-authentication-end-user-first-time.md) . 

    1. Vyberte **E-mail e**-mailem alternativní e-mail a pak vyberte **e-mail**.

    2. Do pole zadejte ověřovací kód z e-mailu a pak vyberte **Další**.

    3. Zadejte a potvrďte nové heslo a pak vyberte **Dokončit**.

- **Resetujte heslo pomocí textové zprávy.** Pošle textovou zprávu na telefonní číslo, které jste předtím nastavili v části informace o zabezpečení. Pokud váš správce zapnul prostředí informací o zabezpečení, najdete další informace o nastavení zasílání zpráv SMS v článku [Nastavení informací zabezpečení pro použití zasílání zpráv SMS (Preview)](security-info-setup-text-msg.md) . Pokud ještě nepoužíváte bezpečnostní údaje, najdete další informace o nastavení zasílání zpráv SMS v článku [Nastavení účtu pro dvoustupňové ověřování](multi-factor-authentication-end-user-first-time.md) .

    1. Vyberte **text můj mobilní telefon**, zadejte své telefonní číslo a pak vyberte **text**.

    2. Zadejte ověřovací kód z textové zprávy do pole a pak vyberte **Další**.

    3. Zadejte a potvrďte nové heslo a pak vyberte **Dokončit**.

- **Resetujte heslo pomocí telefonního čísla.** Vloží automatizované hlasové volání do telefonního čísla, které jste dříve nastavili v informacích o zabezpečení. Pokud váš správce zapnul prostředí informací o zabezpečení, můžete získat další informace o nastavení telefonního čísla v článku [Nastavení bezpečnostních údajů pro použití telefonního hovoru (Preview)](security-info-setup-phone-number.md) . Pokud ještě nepoužíváte bezpečnostní údaje, najdete další informace o nastavení telefonního čísla v článku [Nastavení mého účtu pro dvoustupňové ověřování](multi-factor-authentication-end-user-first-time.md) .

    1. Vyberte **zavolat mi na mobilní telefon**, zadejte své telefonní číslo a pak vyberte **zavolat**.

    2. Odpovězte na telefonní hovor a podle pokynů ověřte svoji identitu a pak vyberte **Další**.

    3. Zadejte a potvrďte nové heslo a pak vyberte **Dokončit**.

- **Resetujte heslo pomocí bezpečnostních otázek.** Zobrazuje seznam bezpečnostních otázek, které jste nastavili v informacích o zabezpečení. Pokud správce zapnul prostředí informací o zabezpečení, můžete najít další informace o nastavení bezpečnostních otázek v článku [Nastavení bezpečnostních otázek pro použití předem definovaných bezpečnostních otázek (Preview)](security-info-setup-questions.md) . Pokud ještě nepoužíváte bezpečnostní údaje, najdete další informace o nastavení bezpečnostních otázek v článku [Nastavení účtu pro dvoustupňové ověřování](multi-factor-authentication-end-user-first-time.md) .

    1. Vyberte **odpovědět na otázky zabezpečení**, odpovězte na otázky a pak vyberte **Další**.

    2. Zadejte a potvrďte nové heslo a pak vyberte **Dokončit**.

- **Resetujte heslo pomocí oznámení z ověřovací aplikace.** Pošle oznámení o schválení do aplikace ověřovatele. Pokud správce zapnul prostředí informací o zabezpečení, můžete najít další informace o nastavení ověřovací aplikace k odeslání oznámení v článku [Nastavení bezpečnostních údajů pro použití ověřovací aplikace (Preview)](security-info-setup-auth-app.md) . Pokud ještě nepoužíváte bezpečnostní údaje, můžete najít další informace o nastavení aplikace ověřovatele k odeslání oznámení v článku [Nastavení účtu pro dvoustupňové ověřování](multi-factor-authentication-end-user-first-time.md) .

    1. Vyberte **schválit oznámení v aplikaci Authenticator** a pak vyberte **Odeslat oznámení**.

    2. Schvalte přihlášení z aplikace ověřovatele.

    3. Zadejte a potvrďte nové heslo a pak vyberte **Dokončit**.

- **Resetujte heslo pomocí kódu z ověřovací aplikace.** Přijímá náhodný kód poskytnutý vaší ověřovací aplikací. Pokud váš správce zapnul prostředí informací o zabezpečení, můžete najít další informace o nastavení ověřovací aplikace pro poskytnutí kódu v článku [Nastavení bezpečnostních údajů pro použití ověřovací aplikace (Preview)](security-info-setup-auth-app.md) . Pokud ještě nepoužíváte bezpečnostní údaje, můžete najít další informace o nastavení ověřovací aplikace pro poskytnutí kódu v článku [Nastavení účtu pro dvoustupňové ověřování](multi-factor-authentication-end-user-first-time.md) .

  1. Vyberte možnost **zadat kód z aplikace Authenticator** a pak vyberte **Odeslat oznámení**.

  2. Otevřete ověřovací aplikaci, do pole zadejte ověřovací kód pro svůj účet a pak vyberte **Další**.

  3. Zadejte a potvrďte nové heslo a pak vyberte **Dokončit**.

  4. Jakmile se zobrazí zpráva s oznámením, že vaše heslo bylo resetováno, můžete se přihlásit ke svému účtu pomocí nového hesla.

     Pokud stále nemáte přístup k vašemu účtu, měli byste požádat o další nápovědu správce vaší organizace.

Po resetování hesla se může zobrazit potvrzovací e-mail, který pochází z účtu, jako je Microsoft jménem \<*your_organization*> . Pokud obdržíte podobný e-mail, ale nedávno jste neobnovili heslo, musíte se hned obrátit na správce vaší organizace.

## <a name="how-to-change-your-password"></a>Jak si změnit heslo

Pokud chcete změnit jenom heslo, můžete to udělat na portálu Office 365, na portálu moje aplikace nebo na přihlašovací stránce Windows 10.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Změna hesla pomocí portálu Office 365

Tuto metodu použijte, pokud obvykle k vašim aplikacím přistupujete prostřednictvím portálu Office:

1. Přihlaste se ke svému [účtu Office 365](https://portal.office.com)pomocí stávajícího hesla.

2. V pravé horní části vyberte svůj profil a pak vyberte **Zobrazit účet**.

3. Vyberte **zabezpečení & heslo ochrany osobních údajů**  >  .

4. Zadejte staré heslo, vytvořte a potvrďte nové heslo a pak vyberte **Odeslat**.

### <a name="to-change-your-password-from-the-my-apps-portal"></a>Změna hesla z portálu moje aplikace

Tuto metodu použijte, pokud obvykle přistupujete k aplikacím z portálu moje aplikace:

1. Přihlaste se k [portálu moje aplikace](https://myapps.microsoft.com/)pomocí stávajícího hesla.

2. V pravém horním rohu vyberte svůj profil a pak vyberte **profil**.

3. Vyberte **změnit heslo**.

4. Zadejte staré heslo, vytvořte a potvrďte nové heslo a pak vyberte **Odeslat**.

### <a name="to-change-your-password-at-windows-sign-in"></a>Změna hesla při přihlášení Windows

Pokud správce zapnul funkci, uvidíte odkaz na **resetování hesla** na přihlašovací obrazovce Windows 7, Windows 8, Windows 8.1 nebo Windows 10.

1. Kliknutím na odkaz **resetovat heslo** spusťte proces resetování hesla, aniž byste museli používat normální webové prostředí.

2. Potvrďte své ID uživatele a vyberte **Další**.

3. Vyberte a potvrďte způsob ověření metodou kontaktu. V případě potřeby vyberte druhou možnost ověření, která se liší od předchozí a vyplňte potřebné informace.

4. Na stránce **vytvořit nové heslo** zadejte a potvrďte nové heslo a pak vyberte **Další**.

    Silná hesla mají obvykle 8 až 16 znaků, včetně velkých a malých písmen, aspoň jedno číslo a aspoň jeden speciální znak.

5. Po zobrazení zprávy s oznámením, že vaše heslo bylo resetováno, můžete vybrat **Dokončit**.

    Pokud stále nemáte přístup k vašemu účtu, měli byste požádat o další nápovědu správce vaší organizace.

## <a name="common-problems-and-their-solutions"></a>Běžné problémy a jejich řešení

Tady jsou některé běžné chybové případy a jejich řešení:

|Problém|Popis|Řešení|
| --- | --- | --- |
|Při pokusu o změnu mého hesla se zobrazí chyba. |Heslo obsahuje slovo, frázi nebo vzor, který umožňuje snadnou možnost uhodnout heslo.| Zkuste to znovu s silnějším heslem.|
|Když zadáte moje ID uživatele, přejdete na stránku s oznámením "kontaktujte správce."|Společnost Microsoft určila, že vaše heslo účtu uživatele spravuje správce v místním prostředí. V důsledku toho nemůžete resetovat heslo z odkazu "nepřístup k vašemu účtu". |Další nápovědu získáte od správce.|
|Po zadání mého ID uživatele se zobrazí chyba s informacemi o tom, že váš účet není pro resetování hesla povolený.|Správce nevytvořil váš účet, takže můžete resetovat svoje vlastní heslo.|Správce nepovolil resetování hesla ve vaší organizaci z odkazu "nemáte přístup k vašemu účtu" nebo nemáte licenci k použití této funkce.<br><br> Pokud chcete resetovat heslo, musíte vybrat odkaz kontaktujte správce a poslat mu e-mail správci vaší společnosti a dát jim jistotu, že chcete heslo resetovat.|
|Když zadáte moje ID uživatele, zobrazí se chyba oznamující, že se nám nepovedlo ověřit váš účet.|Procesu přihlašování se nepovedlo ověřit informace o účtu.|Tuto zprávu můžete zobrazit dvěma důvody.<br><br>1. váš správce vypnul resetování hesla ve vaší organizaci, ale nezaregistrovali jste ho k používání služby. Pokud se chcete zaregistrovat k resetování hesla, podívejte se na některé z následujících článků na základě metody ověřování: [nastavte bezpečnostní údaje tak, aby používaly ověřovací aplikaci (Preview)](security-info-setup-auth-app.md), [nastavte bezpečnostní údaje pro použití telefonního hovoru (Preview)](security-info-setup-phone-number.md), nastavte bezpečnostní údaje pro použití [zasílání zpráv SMS](security-info-setup-text-msg.md)(Preview), nastavte bezpečnostní údaje pro použití [e-mailu](security-info-setup-email.md)(Preview), nebo nastavte bezpečnostní údaje, abyste [mohli používat bezpečnostní otázky (](security-info-setup-questions.md)Preview).<br><br>2. váš správce nepovolil resetování hesla ve vaší organizaci. V takovém případě musíte vybrat odkaz "kontaktujte správce" a poslat mu e-mail s žádostí o resetování hesla.|

## <a name="next-steps"></a>Další kroky

- Informace o zabezpečení najdete v článku Přehled informací o zabezpečení [(Preview)](./security-info-setup-signin.md) .

- Pokud se pokoušíte vrátit se k osobnímu účtu, jako je Xbox, hotmail.com nebo outlook.com, zkuste návrhy v části [když se nemůžete přihlásit k vašemu účet Microsoft článku](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
