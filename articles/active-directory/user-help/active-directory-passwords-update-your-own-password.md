---
title: Resetování hesla – Azure Active Directory | Dokumentace Microsoftu
description: Použijte samoobslužné resetování hesla a jak opět získat přístup k vašim pracovním nebo školním účtem uživatele
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.custom: end-user
ms.openlocfilehash: b4e784e7d249976b95c5f70986ba007351d2abb4
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55076960"
---
# <a name="reset-your-work-or-school-password"></a>Resetovat heslo pracovního nebo školního

Pokud zapomněli jste heslo nikdy nedostali některou z svou firemní podporu, byla uzamčena mimo váš účet nebo ji chcete změnit, můžeme pomoct. Pokud své heslo znáte a potřebujete ho změnit, pokračujte [změnu hesla](#change-my-password) oddílu.

   > [!NOTE]
   > Pokud se pokoušíte získat zpátky ke svému osobnímu účtu, jako jsou Xbox, hotmail.com nebo outlook.com, vyzkoušejte návrhy v [když nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) článku.
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Resetování nebo odemknutí hesla pro pracovní nebo školní účet

Možná nemá přístup k účtu Azure Active Directory (Azure AD) z jednoho z následujících důvodů:

* Heslo, ale nefunguje, a chcete ho resetovat.
* Znát heslo, ale váš účet je uzamčen a chcete ho odemknout.

Pomocí následujících kroků pro přístup k Azure AD samoobslužné resetování hesla (SSPR) a znova dostali do svého účtu.

1. Z libovolného pracovního nebo školního **přihlášení** stránky, vyberte **nemá přístup k účtu?** propojit a pak vyberte **pracovní nebo školní účet** nebo přejděte přímo na [ Stránku pro resetování hesla](https://passwordreset.microsoftonline.com/).

    ![Nejde se dostat na váš účet][Login]

2. Zadejte svůj pracovní nebo školní **ID uživatele**, prokázat, že nejste robot, zadáním znaků zobrazí na obrazovce a pak vyberte **Další**.

   > [!NOTE]
   > Pokud vaši pracovníci IT tuto funkci nepovolil, zobrazí se odkaz "Kontaktujte správce" vaši pracovníci IT mohli pomoct e-mailem nebo webový portál s vlastními.
   >
   > Pokud chcete odemknout účet, v tuto chvíli vyberte možnost **můžu vím svoje heslo, ale stále nemůže přihlásit.**
   >

3. V závislosti na konfiguraci SSPR vaši pracovníci IT by měl zobrazit jedna nebo více z následujících metod ověřování. Vy nebo vaši pracovníci IT by jste vyplnili některé z těchto informací pomocí následujících kroků v [registrace pro resetování hesla pomocí samoobslužné služby](active-directory-passwords-reset-register.md) článku.

   * **Zaslat e-mail na mou alternativní e-mailovou adresu**
   * **Poslat mi SMS na mobil**
   * **Zavolat mi na mobilní telefon**
   * **Zavolat mi na telefon do kanceláře**
   * **Odpověď na mé bezpečnostní otázky**

   Zvolte možnost, zadejte správné odpovědi a pak vyberte **Další**.

   ![Ověření ověřovacích dat][Verification]

4. Pracovníci IT můžou potřebovat další ověření a může být nutné opakovat krok 3 a zvolit jinou možnost.
5. Na **zvolit nové heslo** stránky, zadejte nové heslo, potvrďte heslo a pak vyberte **Dokončit**. Heslo pracovního nebo školního může mít některé požadavky, které musí dodržovat. Doporučujeme, zvolte si heslo, je 8 až 16 znaků a obsahuje velká a malá písmena, čísla a speciální znak.
6. Když se zobrazí zpráva **vaše heslo bylo resetováno**, se můžete přihlásit pomocí nového hesla.

    ![Vaše heslo bylo resetováno][Complete]

Teď by měl být mít přístup ke svému účtu. Pokud nelze získat přístup k účtu, měli byste požádat vaší organizaci o pomoc pracovníky IT.

Možná obdržíte potvrzovací e-mail odeslaný z účtu jako "Microsoft jménem společnosti \<vaší organizace >." Pokud vám přijde e-mail, jako je ten a nepoužili jste samoobslužné resetování hesla a jak opět získat přístup ke svému účtu, obraťte se na vaše organizace IT zaměstnance.

## <a name="change-my-password"></a>Změna hesla

Pokud své heslo již znáte a chcete ho změnit, použijte následující postup.

### <a name="change-your-password-from-the-office-365-portal"></a>Změna hesla z portálu Office 365

Tuto metodu použijte, pokud obvykle přistupujete k aplikacím na portálu Office:

1. Přihlaste se k vaší [účet Office 365](https://www.office.com) s vaší stávající heslo.
2. Vyberte svůj profil vpravo nahoře a pak vyberte **zobrazit účet**.
3. Vyberte **zabezpečení a ochrana osobních údajů** > **heslo**.
4. Zadejte svoje staré heslo, nastavte a potvrďte nové heslo a pak vyberte **odeslat**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Změna hesla na panelu Azure pro přístup

Tuto metodu použijte, pokud obvykle přistupujete k aplikacím z přístupového panelu Azure (MyApps):

1. Přihlaste se k [přístupového panelu Azure](https://myapps.microsoft.com/) s vaší stávající heslo.
2. Vyberte svůj profil vpravo nahoře a pak vyberte **profilu**.
3. Vyberte **změnit heslo**.
4. Zadejte svoje staré heslo, nastavte a potvrďte nové heslo a pak vyberte **odeslat**.

## <a name="reset-password-at-sign-in"></a>Resetování hesla při přihlášení

Pokud správce povolil funkci, zobrazí se odkaz na **resetovat heslo** na přihlašovací obrazovce Windows 10 Fall Creators Update.

![Přihlašovací obrazovka][LoginScreen]

Vyberte **resetovat heslo** odkaz otevře prostředí samoobslužného resetování HESLA na přihlašovací obrazovce tak, aby mohli resetovat heslo, aniž byste museli přihlásit se a získat běžné webové prostředí.

1. Potvrďte své ID uživatele a vyberte **Další**.
2. Vyberte a zkontrolujte způsob kontaktu k ověření. Pracovníci IT můžou potřebovat další ověření a může být potřeba ji opakovat tento krok a zvolit jinou možnost.

   ![Způsob kontaktu][ContactMethod]

3. Na **vytvoření nového hesla** stránky, zadejte nové heslo, potvrďte heslo a pak vyberte **Další**. Doporučujeme, že heslo je 8 – 16 znaků dlouhé a skládá se z velká a malá písmena, číslice a speciální znaky.

   ![Resetování hesla][ResetPassword]

4. Když se zobrazí zpráva **vaše heslo bylo resetováno**vyberte **Dokončit**.

Teď by měl být mít přístup ke svému účtu. Pokud ne, obraťte se na vaše organizace o pomoc pracovníky IT.

## <a name="common-problems-and-their-solutions"></a>Běžné problémy a jejich řešení

 Tady jsou některé běžné případy chyb a jejich řešení:

| Případ chyby| Jaká chyba se zobrazí?| Řešení |
| --- | --- | --- |
| Zobrazila se chyba při pokusu o změnu hesla. | Vaše heslo bohužel obsahuje slovo, frázi nebo vzor, který umožňuje snadno uhodnutelných heslo. Zkuste to prosím znovu s jiným heslem. | Zvolte si heslo, je obtížnější uhádnout. |
| Můžu získat stránky "Kontaktujte správce" po zadání ID uživatele | Kontaktujte svého správce. <br> <br> Zjistili jsme, že heslo vašeho uživatelského účtu nespravuje Microsoft. V důsledku toho nemůžeme automaticky resetovat své heslo. <br> <br> Potřebujete další pomoc požádejte pracovníky IT. | Tato zpráva se zobrazuje, protože pracovníci IT spravuje vaše heslo v místním prostředí. Nelze resetovat heslo na odkaz "Nedaří se vašeho účtu". <br> <br> Resetovat své heslo, požádejte přímo o pomoc pracovníky IT a dát jim vědět, že chcete resetovat heslo, jsou-li povolit tuto funkci můžete.|
| Získat chybu "váš účet není povolen pro resetování hesla" po zadání ID uživatele | Váš účet není povolen pro resetování hesla. <br> <br> Je nám líto, ale vaši pracovníci IT nenastavila váš účet k používání této služby. <br> <br> Pokud chcete, můžeme kontaktovat správce ve vaší organizaci k vám heslo resetoval. | Tato zpráva se zobrazuje, protože pracovníci IT nepovolil resetování hesla pro vaši organizaci na odkaz "Nedaří se váš účet", nebo nebyla licenci, abyste použili funkci. <br> <br> K resetování hesla, vyberte "kontaktujte správce propojení" k odesílání e-mailu pro vaši společnost uživatele pracovníky IT a dát jim vědět, kterou chcete resetování hesla tak, že tuto funkci povolit pro vás. |
| Získat chybu "Máme nelze ověřit váš účet" po zadání ID uživatele | Nemohli jsme ověřit váš účet. <br> <br> Pokud chcete, můžeme kontaktovat správce ve vaší organizaci k vám heslo resetoval. | Zobrazuje se vám tato zpráva je povolená pro resetování hesla, protože jste se ještě nezaregistrovali pro používání služby. Chcete-li zaregistrovat pro resetování hesla, přejděte na https://aka.ms/ssprsetup po jste znovu získali přístup ke svému účtu. <br> <br> Pokud chcete resetovat heslo, vyberte odkaz "kontaktujte správce" Odeslat e-mail pro vaši společnost pracovníků IT. |

## <a name="next-steps"></a>Další postup

* [Registrace pro použití samoobslužného resetování hesla](active-directory-passwords-reset-register.md)
* [Registrační stránka pro resetování hesla](https://aka.ms/ssprsetup)
* [Portál pro resetování hesla](https://passwordreset.microsoftonline.com/)
* [Nejde se přihlásit k vašemu účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Přihlašovací stránka Nejde se dostat na váš účet"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Ověření ověřovacích dat"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Změna hesla"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Vaše heslo bylo resetováno"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Odkaz na Windows 10 Fall Creators Update přihlašovací obrazovky resetování hesla"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "Ověření ověřovacích dat"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Změna hesla"
