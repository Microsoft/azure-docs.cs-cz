---
title: Správa hesel aplikací – Azure Active Directory | Dokumenty společnosti Microsoft
description: Přečtěte si o heslech aplikací a o tom, k čemu se používají, pokud jde o dvoustupňové ověřování.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 5f81181ac3107307a352cdbcd0b5cc4a555deacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253218"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>Správa hesel aplikací pro dvoustupňové ověření

>[!Important]
>Správce vám nemusí povolit používání hesel aplikací. Pokud **hesla aplikací** jako možnost nevidíte, nejsou ve vaší organizaci dostupná.

Při používání hesel aplikací je důležité mít na paměti:

- Hesla aplikací se generují automaticky a měla by být vytvořena a zadána jednou pro aplikaci.

- Je zde limit 40 hesel na uživatele. Pokud se pokusíte vytvořit po tomto limitu, budete vyzváni k odstranění existujícího hesla před povolením k vytvoření nového.

    >[!Note]
    >Klienti Office 2013 (včetně Outlooku) podporují nové ověřovací protokoly a můžou je používat s dvoustupňovým ověřováním. Tato podpora znamená, že po zapnutí dvoustupňového ověření už nebudete pro klienty Office 2013 potřebovat hesla aplikací. Další informace najdete v článku [Jak funguje moderní ověřování pro klientské aplikace Office 2013 a Office 2016.](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)

## <a name="create-new-app-passwords"></a>Vytváření nových hesel aplikací

Během počátečního dvoufaktorového procesu registrace ověření máte k dispozici jediné heslo aplikace. Pokud budete potřebovat více než jeden, budete muset vytvořit sami. Hesla aplikací můžete vytvářet z více oblastí v závislosti na nastavení dvoufaktorového ověřování ve vaší organizaci. Další informace o registraci k používání dvoufaktorového ověření u pracovního nebo školního účtu najdete v [tématu Přehled dvoufaktorového ověření a pracovního nebo školního účtu](multi-factor-authentication-end-user-first-time.md) a souvisejících článků.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Kde vytvořit a odstranit hesla aplikací

Hesla aplikací můžete vytvářet a odstraňovat podle toho, jak používáte dvoufaktorové ověření:

- **Vaše organizace používá dvoufaktorové ověření a stránku Další ověření zabezpečení.** Pokud používáte pracovní nebo školní účet (například alain@contoso.com) s dvoufaktorovým ověřením ve vaší organizaci, můžete hesla aplikací spravovat na stránce Další ověření [zabezpečení](https://account.activedirectory.windowsazure.com/Proofup.aspx). Podrobné pokyny najdete v [tématu Vytvoření a odstranění hesel aplikací pomocí stránky Další ověření zabezpečení](#create-and-delete-app-passwords-from-the-additional-security-verification-page) v tomto článku.

- **Vaše organizace používá dvoufaktorové ověření a portál Office 365.** Pokud ve vaší organizaci používáte pracovní nebo alain@contoso.comškolní účet (například ), dvoufaktorové ověření a aplikace Office 365, můžete hesla aplikací spravovat na [stránce portálu Office 365](https://www.office.com). Podrobné pokyny najdete v [tématu Vytváření a odstraňování hesel aplikací pomocí portálu Office 365](#create-and-delete-app-passwords-using-the-office-365-portal) v tomto článku.

- **Používáte dvoufaktorové ověření s osobním účtem Microsoft.** Pokud používáte osobní účet Microsoft alain@outlook.com(například) s dvoufaktorovým ověřením, můžete hesla aplikací spravovat na stránce [Základy zabezpečení](https://account.microsoft.com/security/). Podrobné pokyny najdete v tématu [Používání hesel aplikací s aplikacemi, které nepodporují dvoustupňové ověření](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification).

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>Vytvoření a odstranění hesel aplikací ze stránky Další ověření zabezpečení

Hesla aplikací můžete vytvářet a odstraňovat na stránce **Další ověření zabezpečení** pro svůj pracovní nebo školní účet.

1. Přihlaste se na [stránku Další ověření zabezpečení](https://account.activedirectory.windowsazure.com/Proofup.aspx)a vyberte **hesla aplikací**.

    ![Stránka Hesla aplikací se zvýrazněnou kartou Hesla aplikací](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Vyberte **Vytvořit**, zadejte název aplikace, která vyžaduje heslo aplikace, a pak vyberte **Další**.

    ![Vytvoření stránky hesel aplikací s názvem aplikace, která potřebuje heslo](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Zkopírujte heslo ze stránky **Heslo aplikace** a pak vyberte **Zavřít**.

    ![Stránka s heslem aplikace s heslem pro zadanou aplikaci](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Na stránce **Hesla aplikací** zkontrolujte, jestli je vaše aplikace v seznamu.

     ![Stránka hesel aplikací s novou aplikací zobrazenou v seznamu](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Otevřete aplikaci, pro kterou jste vytvořili heslo aplikace (například Outlook 2010), a na požádání vložte heslo aplikace. Měli byste to udělat pouze jednou za aplikaci.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Odstranění hesla aplikace pomocí stránky Hesla aplikací

1. Na stránce **Hesla aplikací** vyberte **Odstranit** vedle hesla aplikace, které chcete odstranit.

   ![Odstranění hesla aplikace](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Výběrem **možnosti Ano** potvrďte, že chcete heslo odstranit, a pak vyberte **Zavřít**.

    Heslo aplikace se úspěšně odstraní.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Vytváření a odstraňování hesel aplikací pomocí portálu Office 365

Pokud používáte dvoustupňové ověření s pracovním nebo školním účtem a aplikacemi Office 365, můžete vytvořit a odstranit hesla aplikací pomocí portálu Office 365.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Vytváření hesel aplikací pomocí portálu Office 365

1. Přihlaste se k Office 365 a přejděte na [stránku Můj účet](https://portal.office.com), vyberte **Zabezpečení & soukromí**a pak rozbalte Další ověření **zabezpečení**.

    ![Portál Office zobrazující rozšířenou oblast dodatečného ověřování zabezpečení](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Vyberte text, který říká, **Vytvořit a spravovat hesla aplikací** otevřete stránku Hesla **aplikací.**

    ![Stránka Hesla aplikací se zvýrazněnou kartou Hesla aplikací](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. Vyberte **Vytvořit**, zadejte název aplikace, která vyžaduje heslo aplikace, a pak vyberte **Další**.

    ![Vytvoření stránky hesel aplikací s názvem aplikace, která potřebuje heslo](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Zkopírujte heslo ze stránky **Heslo aplikace** a pak vyberte **Zavřít**.

    ![Stránka s heslem aplikace s heslem pro zadanou aplikaci](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. Na stránce **Hesla aplikací** zkontrolujte, jestli je vaše aplikace v seznamu.

     ![Stránka hesel aplikací s novou aplikací zobrazenou v seznamu](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Otevřete aplikaci, pro kterou jste vytvořili heslo aplikace (například Outlook 2010), a na požádání vložte heslo aplikace. Měli byste to udělat pouze jednou za aplikaci.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>Odstranění hesel aplikací pomocí stránky Hesla aplikací

1. Na stránce **Hesla aplikací** vyberte **Odstranit** vedle hesla aplikace, které chcete odstranit.

   ![Odstranění hesla aplikace](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. V potvrzovacím poli vyberte **Ano** a pak vyberte **Zavřít**.

    Heslo aplikace se úspěšně odstraní.

## <a name="if-your-app-passwords-arent-working-properly"></a>Pokud hesla aplikací nefungují správně

Zkontrolujte, zda jste heslo zadali správně. Pokud jste si jisti, že jste heslo zadali správně, můžete se pokusit znovu přihlásit a vytvořit nové heslo aplikace. Pokud ani jedna z těchto možností problém nevyřeší, obraťte se na oddělení technické podpory vaší organizace, aby mohla odstranit vaše stávající hesla aplikací a vytvořit tak zcela nová.

## <a name="next-steps"></a>Další kroky

- [Správa nastavení dvoustupňového ověření](multi-factor-authentication-end-user-manage-settings.md)

- Vyzkoušejte [aplikaci Microsoft Authenticator](user-help-auth-app-download-install.md) a ověřte si vaše přihlášení pomocí oznámení aplikací, místo toho, abyste přijímali textové zprávy nebo hovory.
