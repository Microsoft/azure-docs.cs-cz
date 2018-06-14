---
title: 'Azure AD: Resetování hesla | Microsoft Docs'
description: Použít samoobslužné resetování hesel k znovu získat přístup ke své práci nebo škole uživatelský účet
services: active-directory
keywords: ''
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 5c850fe9eaf78ab5ee6a175e5433998e1f15ab2e
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2018
ms.locfileid: "29463774"
---
# <a name="reset-your-work-or-school-password"></a>Resetovat heslo pracovního nebo školního účtu

Pokud zapomněli jste heslo nikdy přijaté z podpory vaší společnosti, byl uzamčen mimo váš účet nebo ji chcete změnit, pomůžeme vám. Pokud znáte heslo a jednoduše ho můžete změnit, i nadále [změnu hesla](#change-my-password) části.

   > [!NOTE]
   > Pokud se pokoušíte získat zpátky ke svému osobnímu účtu, jako je outlook.com, Xbox a hotmail.com, zkuste návrhů v [při nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) článku.
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>Resetování nebo odemknutí hesla pro pracovní nebo školní účet

Možná nebude možné získat přístup k účtu Azure Active Directory (Azure AD) z jednoho z následujících důvodů:

* Heslo není funkční a chcete ho resetovat.
* Znát heslo, ale váš účet uzamčen a chcete ho odemknout.

Pomocí následujících kroků pro přístup k Azure AD samoobslužné resetování hesla (SSPR) a zase dostat k vašemu účtu.

1. Z libovolného pracovní nebo školní **přihlášení** vyberte **nelze získat přístup k účtu?** propojit a potom vyberte **pracovní nebo školní účet** nebo přejít přímo na [ Stránka pro vytvoření nového hesla](https://passwordreset.microsoftonline.com/).

    ![Nejde se dostat na váš účet][Login]

2. Zadejte své pracovní nebo školní **ID uživatele**, prokázat nejste robot zadáním znaky zobrazte na obrazovce a potom vyberte **Další**.

   > [!NOTE]
   > Pokud je tato funkce není povoleno pracovníci IT, se zobrazí, pracovníci IT pomůžou e-mailem nebo webový portál vlastní odkaz "Obraťte se na správce".
   >
   > Pokud potřebujete k odemknutí účtu, vyberte možnost v tomto okamžiku **I vím svoje heslo, ale stále nemůžete se přihlásit.**
   >

3. V závislosti na konfiguraci SSPR pracovníci IT by měl zobrazit jedna nebo více z následujících metod ověřování. Vy nebo pracovníci IT by měl mít vyplní některé z těchto informací podle kroků v [registrace pro resetování hesla pomocí samoobslužné služby](active-directory-passwords-reset-register.md) článku.

   * **Zaslat e-mail na mou alternativní e-mailovou adresu**
   * **Poslat mi SMS na mobil**
   * **Zavolat mi na mobilní telefon**
   * **Zavolat mi na telefon do kanceláře**
   * **Odpověď na mé bezpečnostní otázky**

   Zvolte možnost, zadejte správné odpovědi a pak vyberte **Další**.

   ![Ověření ověřovacích dat][Verification]

4. Pracovníci IT může být třeba další ověření a možná budete muset opakujte krok 3 s jinou možnost.
5. Na **zvolit nové heslo** stránky, zadejte nové heslo, potvrďte heslo a potom vyberte **Dokončit**. Heslo pracovního nebo školního může mít některé požadavky, které je potřeba splnit. Doporučujeme, zvolte heslo, které je 8 až 16 znaků a zahrnuje velká a malá písmena, čísla a speciální znak.
6. Až se zobrazí zpráva **vaše heslo bylo resetováno**, se můžete přihlásit pomocí nového hesla.

    ![Vaše heslo bylo resetováno][Complete]

Teď by měla být moct získat přístup k účtu. Pokud máte přístup k účtu, měli byste požádat vaší organizace pracovníky IT o další pomoc.

Můžete obdržet e-mail s potvrzením, který jste dostali z účtu, jako je "Microsoft jménem \<vaší organizace >." Pokud dojde k e-mailu tohoto typu a jste nepoužili samoobslužné služby heslo resetovat znovu získat přístup ke svému účtu, obraťte se na vaší organizace IT oddělení.

## <a name="change-my-password"></a>Změna hesla

Pokud jste již znát heslo a chcete ho změnit, použijte následující kroky.

### <a name="change-your-password-from-the-office-365-portal"></a>Změna hesla z portálu Office 365

Tuto metodu použijte, pokud obvykle přístup aplikace prostřednictvím portálu Office:

1. Přihlaste se k vaší [účtu Office 365](https://www.office.com) s vaší stávající heslo.
2. Vyberte váš profil na horní pravé straně a pak vyberte **zobrazit účet**.
3. Vyberte **zabezpečení a ochrana osobních údajů** > **heslo**.
4. Zadejte svoje staré heslo, nastavení a potvrďte nové heslo a potom vyberte **odeslání**.

### <a name="change-your-password-from-the-azure-access-panel"></a>Změna hesla na panelu Azure pro přístup

Tuto metodu použijte, pokud normálně aplikace z přístupový Panel Azure (MyApps):

1. Přihlaste se k [přístupový Panel Azure](https://myapps.microsoft.com/) s vaší stávající heslo.
2. Vyberte váš profil na horní pravé straně a pak vyberte **profil**.
3. Vyberte **změnit heslo**.
4. Zadejte svoje staré heslo, nastavení a potvrďte nové heslo a potom vyberte **odeslání**.

## <a name="reset-password-at-sign-in"></a>Resetovat heslo při přihlášení

Pokud správce povolil funkci, se zobrazí odkaz na **resetovat heslo** na přihlašovací obrazovky Windows 10 patří Creators Update.

![Přihlašovací obrazovky][LoginScreen]

Vyberte **resetovat heslo** odkaz otevře SSPR prostředí na obrazovce přihlášení tak, aby mohli resetovat heslo bez nutnosti se přihlásit k normální prostředí založené na webu.

1. Potvrďte své ID uživatele a vyberte **Další**.
2. Vyberte a potvrďte kontaktní metodu pro ověření. Pracovníci IT může být třeba další ověření a možná budete muset opakovat tento krok s jinou možnost.

   ![Způsob kontaktu][ContactMethod]

3. Na **vytvořit nové heslo** stránky, zadejte nové heslo, potvrďte heslo a potom vyberte **Další**. Doporučujeme, aby vaše heslo 8 až 16 znaků dlouhé a skládá se z velká a malá písmena, číslice a speciální znaky.

   ![Resetování hesla][ResetPassword]

4. Až se zobrazí zpráva **vaše heslo bylo resetováno**, vyberte **Dokončit**.

Teď by měla být moct získat přístup k účtu. Pokud ne, obraťte se na vaší organizace pracovníky IT o další pomoc.

## <a name="common-problems-and-their-solutions"></a>Běžné problémy a jejich řešení

 Zde jsou některé běžné případy chyba a jejich řešení:

| Chyb| Jaké chyby se zobrazí?| Řešení |
| --- | --- | --- |
| Zobrazuje, chybu při pokusu o změnu hesla. | Bohužel heslo obsahuje slova, fráze nebo vzor, který umožňuje snadno uhodnutelných heslo. Zkuste to prosím znovu s jiným heslem. | Zvolte heslo, které je obtížné uhodnout. |
| Zobrazí na stránce ", kontaktujte prosím správce" po zadání ID uživatele | Kontaktujte svého správce. <br> <br> Zjistili jsme, že heslo vašeho uživatelského účtu nespravuje společnost Microsoft. Snažíme se v důsledku toho nelze automaticky resetovat heslo. <br> <br> Potřebujete další pomoc požádejte pracovníci IT. | Tato zpráva se zobrazuje, protože pracovníci oddělení IT spravuje vaše heslo v místním prostředí. Nelze obnovit heslo na odkaz "Nemůže získat přístup k účtu". <br> <br> Pokud chcete resetovat heslo, požádejte pracovníci IT přímo o pomoc a dát jim vědět, že chcete resetovat heslo, takže se můžete povolit tuto funkci můžete.|
| Zobrazí chybu "váš účet není povolen pro resetování hesla" po zadání ID uživatele | Váš účet není povolen pro resetování hesla. <br> <br> Je nám líto, ale pracovníci IT nenastavil váš účet k používání této služby. <br> <br> Pokud chcete, můžeme kontaktovat správce ve vaší organizaci k resetování hesla. | Tato zpráva se zobrazuje, protože pracovníci IT nepovolil resetování hesla pro vaše organizace na odkaz "Nemůže získat přístup k účtu", nebo nebyl licenci, můžete použít funkci. <br> <br> Chcete-li resetovat heslo, vyberte "kontaktovat správce odkaz" Odeslat e-mail pro vaši společnost je pracovníky IT a dát jim vědět, které chcete resetovat heslo, takže se můžete povolit tuto funkci můžete. |
| Zobrazí chybu "Jsme nemohl ověřit váš účet" po zadání ID uživatele | Nemohli jsme ověřit váš účet. <br> <br> Pokud chcete, můžeme kontaktovat správce ve vaší organizaci k resetování hesla. | Tato zpráva se zobrazuje, protože jste povolen pro resetování hesla, ale nejsou zaregistrované pro používání služby. Chcete-li zaregistrovat pro resetování hesla, přejděte na https://aka.ms/ssprsetup po jste znovu získali přístup ke svému účtu. <br> <br> Pokud chcete resetovat heslo, vyberte odkaz ", kontaktujte správce" Odeslat e-mail pro vaši společnost zaměstnanců IT. |

## <a name="next-steps"></a>Další postup

* [Registrace pro použití samoobslužného resetování hesla](active-directory-passwords-reset-register.md)
* [Registrační stránka pro resetování hesla](https://aka.ms/ssprsetup)
* [Portál pro resetování hesla](https://passwordreset.microsoftonline.com/)
* [Nejde se přihlásit k vašemu účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "Přihlašovací stránka Nejde se dostat na váš účet"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "Ověření ověřovacích dat"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "Změna hesla"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "Vaše heslo bylo resetováno"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Windows 10 patří Creators Update přihlašovací obrazovky resetování hesla odkaz"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "Ověření ověřovacích dat"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "Změna hesla"
