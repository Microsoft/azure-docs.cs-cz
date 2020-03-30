---
title: Resetování hesla pomocí bezpečnostních údajů – Azure Active Directory | Dokumenty společnosti Microsoft
description: Jak obnovit vlastní heslo, pokud ho zapomenete, pomocí bezpečnostních údajů a dvoustupňového ověření.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 3ed79be318319009aabb1b1ef0c42c4021bbbabe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062655"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>Resetování pracovního nebo školního hesla pomocí bezpečnostních údajů

Pokud jste zapomněli pracovní nebo školní heslo, nikdy jste nedostali heslo od vaší organizace nebo jste z účtu nezapomněli, můžete pomocí bezpečnostních údajů a mobilního zařízení resetovat pracovní nebo školní heslo. Správce musí tuto funkci zapnout, abyste mohli nastavit informace a resetovat vlastní heslo.

Pokud heslo znáte, ale chcete ho změnit, [přečtěte si](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password#how-to-change-your-password) část kroky ke změně hesla v tomto článku.

>[!Important]
>Tento článek je určen pro uživatele, kteří se pokoušejí použít resetování zapomenutého nebo neznámého hesla pracovního nebo školního účtu. Pokud jste správce, který hledá informace o tom, jak zapnout samoobslužné resetování hesla pro zaměstnance nebo jiné uživatele, přečtěte [si článek Nasazení samoobslužného hesla Azure AD a další články](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Jak resetovat nebo odemknout heslo pro pracovní nebo školní účet

Pokud nemáte přístup ke svému účtu Azure Active Directory (Azure AD), může to být buď proto, že:

- Heslo nefunguje a chcete ho resetovat, nebo

- Znáte své heslo, ale váš účet je uzamčen a musíte ho odemknout.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>Resetování hesla a návrat do účtu

1. Na obrazovce **Zadejte heslo** vyberte **Zapomněli jste heslo**.

2. Na obrazovce **Vrátit se zpět do účtu** zadejte své pracovní nebo školní **ID uživatele** (například e-mailovou adresu), dokažte, že nejste robot, zadáním znaků, které vidíte na obrazovce, a pak vyberte **Další**.

   ![Návrat na obrazovku svého účtu](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Pokud správce nezapnul možnost resetovat vlastní heslo, zobrazí se vám odkaz **Kontaktovat správce** místo na obrazovce Vrátit se zpět **do účtu.** Tento odkaz umožňuje kontaktovat správce ohledně obnovení hesla prostřednictvím e-mailu nebo webového portálu.

3. Zvolte jednu z následujících metod, abyste ověřili svou identitu a změnili heslo. V závislosti na tom, jak správce nastavil vaši organizaci, může být nutné projít tímto procesem podruhé a přidat informace pro druhý krok ověření.

    ![Vraťte se ke svému účtu, krok ověření #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >V závislosti na tom, jak správce nastavil vaši organizaci, nemusí být některé z těchto možností ověření k dispozici. Musíte mít dříve nastavené mobilní zařízení pro ověření alespoň jednou z těchto metod.<br><br>Navíc nové heslo může být nutné splnit určité požadavky na sílu. Silná hesla mají obvykle 8 až 16 znaků, včetně velkých a malých písmen, alespoň jedno číslo a alespoň jeden speciální znak.

- **Resetujte heslo pomocí e-mailové adresy.** Odešle e-mail na e-mailovou adresu, kterou jste dříve nastavili ve dvoukrocích ověření nebo bezpečnostních údajích. Pokud správce zapnul prostředí bezpečnostních údajů, najdete další informace o nastavení e-mailové adresy v článku [Nastavit bezpečnostní údaje pro používání e-mailu (ve verzi Preview).](security-info-setup-email.md) Pokud ještě nepoužíváte bezpečnostní údaje, další informace o nastavení e-mailové adresy najdete v článku [Nastavení účtu pro dvoustupňové ověření.](multi-factor-authentication-end-user-first-time.md) 

    1. Vyberte **E-mail s alternativním e-mailem**a pak vyberte **E-mail**.

    2. Do pole zadejte ověřovací kód z e-mailu a vyberte **Další**.

    3. Zadejte a potvrďte nové heslo a pak vyberte **Dokončit**.

- **Obnovte heslo pomocí textové zprávy.** Odešle textovou zprávu na telefonní číslo, které jste dříve nastavili v bezpečnostních údajích. Pokud správce zapnul prostředí bezpečnostních údajů, najdete další informace o nastavení textových zpráv v článku [Nastavit bezpečnostní údaje tak, aby používaly textové zprávy (preview).](security-info-setup-text-msg.md) Pokud ještě nepoužíváte bezpečnostní údaje, další informace o nastavení textových zpráv najdete v článku [Nastavení účtu pro dvoustupňové ověření.](multi-factor-authentication-end-user-first-time.md)

    1. Vyberte **Text na můj mobilní telefon**, zadejte telefonní číslo a pak vyberte **Text**.

    2. Do pole zadejte ověřovací kód z textové zprávy a pak vyberte **Další**.

    3. Zadejte a potvrďte nové heslo a pak vyberte **Dokončit**.

- **Resetujte heslo pomocí telefonního čísla.** Odešle textovou zprávu na telefonní číslo, které jste dříve nastavili v bezpečnostních údajích. Pokud správce zapnul prostředí bezpečnostních údajů, najdete další informace o nastavení telefonního čísla v článku [Nastavení bezpečnostních údajů pro použití telefonního hovoru (preview).](security-info-setup-phone-number.md) Pokud ještě nepoužíváte bezpečnostní údaje, další informace o nastavení telefonního čísla najdete v článku [Nastavení účtu pro dvoustupňové ověření.](multi-factor-authentication-end-user-first-time.md)

    1. Vyberte **Volat na můj mobilní telefon**, zadejte své telefonní číslo a pak vyberte **Volat**.

    2. Přijměte telefonní hovor a podle pokynů ověřte svou identitu a pak vyberte **Další**.

    3. Zadejte a potvrďte nové heslo a pak vyberte **Dokončit**.

- **Resetujte heslo pomocí bezpečnostních otázek.** Zobrazí seznam bezpečnostních otázek, které jste nastavili v bezpečnostních údajích. Pokud správce zapnul prostředí bezpečnostních údajů, najdete další informace o nastavení bezpečnostních otázek v článku [Nastavení bezpečnostních údajů pro použití předdefinovaných otázek zabezpečení (preview).](security-info-setup-questions.md) Pokud ještě nepoužíváte bezpečnostní údaje, další informace o nastavení bezpečnostních otázek najdete v článku [Nastavení účtu pro dvoustupňové ověření.](multi-factor-authentication-end-user-first-time.md)

    1. Vyberte **Odpovědět na mé bezpečnostní otázky**, odpovězte na otázky a pak vyberte **Další**.

    2. Zadejte a potvrďte nové heslo a pak vyberte **Dokončit**.

- **Obnovte heslo pomocí oznámení z ověřovací aplikace.** Odešle oznámení o schválení do ověřovací aplikace. Pokud správce zapnul prostředí bezpečnostních údajů, najdete další informace o nastavení ověřovací aplikace pro odeslání oznámení v článku [Nastavit bezpečnostní údaje pro použití ověřovací aplikace (preview).](security-info-setup-auth-app.md) Pokud ještě nepoužíváte bezpečnostní údaje, najdete další informace o nastavení ověřovací aplikace pro odeslání oznámení v článku [Nastavení účtu pro dvoustupňové ověření.](multi-factor-authentication-end-user-first-time.md)

    1. Vyberte **Schválit oznámení v aplikaci ověřovatel**a pak vyberte **Odeslat oznámení**.

    2. Schválení přihlášení z ověřovací aplikace.

    3. Zadejte a potvrďte nové heslo a pak vyberte **Dokončit**.

- **Obnovte heslo pomocí kódu z ověřovací aplikace.** Přijme náhodný kód poskytnutý vaší ověřovací aplikací. Pokud správce zapnul prostředí bezpečnostních údajů, najdete další informace o nastavení ověřovací aplikace, která vám poskytne kód v článku [Nastavit bezpečnostní údaje pro použití ověřovací aplikace (preview).](security-info-setup-auth-app.md) Pokud ještě nepoužíváte bezpečnostní údaje, najdete další informace o nastavení ověřovací aplikace, která vám poskytne kód v článku [Nastavení účtu pro dvoustupňové ověření.](multi-factor-authentication-end-user-first-time.md)

  1. Vyberte **Zadat kód z aplikace ověřovatel**a pak vyberte **Odeslat oznámení**.

  2. Otevřete ověřovací aplikaci, do pole zadejte ověřovací kód svého účtu a pak vyberte **Další**.

  3. Zadejte a potvrďte nové heslo a pak vyberte **Dokončit**.

  4. Po zobrazení zprávy, že bylo heslo resetováno, se můžete ke svému účtu přihlásit pomocí nového hesla.

     Pokud stále nemáte přístup ke svému účtu, obraťte se na správce organizace a požádejte o další pomoc.

Po resetování hesla můžete dostat potvrzovací e-mail, který pochází \<z účtu, například "Microsoft jménem *your_organization*>.". Pokud se vám dostane podobný e-mail, ale nedávno jste heslo neresetovali, musíte okamžitě kontaktovat správce organizace.

## <a name="how-to-change-your-password"></a>Jak si změnit heslo

Pokud chcete jen změnit heslo, můžete to udělat prostřednictvím portálu Office 365, přístupového panelu Azure nebo přihlašovací stránky Windows 10.

### <a name="to-change-your-password-using-the-office-365-portal"></a>Změna hesla pomocí portálu Office 365

Tuto metodu použijte, pokud obvykle přistupujete k aplikacím prostřednictvím portálu Office:

1. Přihlaste se ke svému [účtu Office 365](https://portal.office.com)pomocí stávajícího hesla.

2. Vyberte svůj profil na pravé horní straně a pak vyberte **Zobrazit účet**.

3. Vyberte **možnost Zabezpečení & heslo pro ochranu osobních údajů** > **Password**.

4. Zadejte staré heslo, vytvořte a potvrďte nové heslo a pak vyberte **Odeslat**.

### <a name="to-change-your-password-from-the-azure-access-panel"></a>Změna hesla z přístupového panelu Azure

Tuto metodu použijte, pokud obvykle přistupujete ke svým aplikacím z Přístupového panelu Azure (MyApps):

1. Přihlaste se k [přístupovému panelu Azure](https://myapps.microsoft.com/)pomocí stávajícího hesla.

2. Vyberte svůj profil na pravé horní straně a pak vyberte **Profil**.

3. Vyberte **možnost Změnit heslo**.

4. Zadejte staré heslo, vytvořte a potvrďte nové heslo a pak vyberte **Odeslat**.

### <a name="to-change-your-password-at-windows-sign-in"></a>Změna hesla při přihlášení k Systému Windows

Pokud správce tuto funkci zapnul, zobrazí se na přihlašovací obrazovce Windows 7, Windows 8.1 nebo Windows 10 odkaz na **Resetování hesla.**

1. Chcete-li spustit proces obnovení hesla, aniž byste museli používat běžné webové prostředí, vyberte odkaz **Obnovit heslo.**

2. Potvrďte id uživatele a vyberte **další**.

3. Vyberte a potvrďte metodu kontaktu pro ověření. V případě potřeby zvolte druhou možnost ověření, která se liší od předchozí možnosti, a vyplňte potřebné informace.

4. Na stránce **Vytvořit nové heslo** zadejte a potvrďte nové heslo a pak vyberte **Další**.

    Silná hesla mají obvykle 8 až 16 znaků, včetně velkých a malých písmen, alespoň jedno číslo a alespoň jeden speciální znak.

5. Po zobrazení zprávy, že heslo bylo resetováno, můžete vybrat **možnost Dokončit**.

    Pokud stále nemáte přístup ke svému účtu, obraťte se na správce organizace a požádejte o další pomoc.

## <a name="common-problems-and-their-solutions"></a>Běžné problémy a jejich řešení

Zde jsou některé běžné chybové případy a jejich řešení:

|Problém|Popis|Řešení|
| --- | --- | --- |
|Při pokusu o změnu hesla se zobrazí chyba. |Heslo má slovo, frázi nebo vzor, který umožňuje heslo snadno uhodnout.| Zkuste to znovu pomocí silnějšího hesla.|
|Po zadání id uživatele přejdu na stránku s nápisem "Obraťte se na správce"|Společnost Microsoft zjistila, že heslo vašeho uživatelského účtu spravuje správce v místním prostředí. V důsledku toho nelze obnovit heslo z odkazu "Nelze získat přístup ke svému účtu". |Další pomoc získáte od správce.|
|Po zadání id uživatele se zobrazí chyba, která říká, že váš účet není povolen pro resetování hesla.|Správce nenastavil váš účet, abyste mohli obnovit vlastní heslo.|Správce nezapnul pro vaši organizaci resetování hesla z odkazu "Nelze získat přístup k účtu" nebo vám nepovolil používat tuto funkci.<br><br> Chcete-li obnovit heslo, musíte vybrat odkaz "Kontaktovat správce", abyste odeslali e-mail správci vaší společnosti a dali mu vědět, že chcete heslo obnovit.|
|Po zadání id uživatele se zobrazí chyba s nápisem "Nepodařilo se ověřit váš účet".|Proces přihlášení nemohl ověřit informace o vašem účtu.|Existují dva důvody, proč se vám tato zpráva může zoavka vidět.<br><br>1. Správce zapnul pro vaši organizaci resetování hesla, ale nezaregistroval jste službu. Pokud chcete zaregistrovat pro obnovení hesla, podívejte se na jeden z následujících článků na základě způsobu ověření: [Nastavte bezpečnostní údaje tak, aby používaly ověřovací aplikaci (náhled),](security-info-setup-auth-app.md) [Nastavit bezpečnostní údaje tak, aby používaly telefonní hovor (náhled),](security-info-setup-phone-number.md) [Nastavit bezpečnostní údaje tak, aby používaly textové zprávy (preview),](security-info-setup-text-msg.md) [Nastavení bezpečnostních údajů pro používání e-mailu (náhled)](security-info-setup-email.md)nebo [Nastavení bezpečnostních údajů pro použití bezpečnostních otázek (preview).](security-info-setup-questions.md)<br><br>2. Správce nezapnul resetování hesla pro vaši organizaci. V takovém případě je nutné vybrat odkaz "Kontaktovat správce" a odeslat e-mail správci s žádostí o obnovení hesla.|

## <a name="next-steps"></a>Další kroky

- Informace o bezpečnostních údajích najdete v článku [Přehled bezpečnostních údajů (preview).](user-help-security-info-overview.md)

- Pokud se pokoušíte vrátit k osobnímu účtu, jako je Xbox, hotmail.com nebo outlook.com, vyzkoušejte návrhy v [článku Když se nemůžete přihlásit k účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
