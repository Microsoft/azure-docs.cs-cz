---
title: Registrace ověřovacích informací pro resetování vlastního hesla – Azure AD
description: Zaregistrujte informace o metodě ověření pro samoobslužné resetování hesla Azure AD, abyste mohli resetovat vlastní heslo bez pomoci správce.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 397e1fd7695fd7e74e1f22959d0f9f24af7d1ea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062638"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Zaregistrujte informace o metodě ověření a resetujte si vlastní heslo

Pokud jste zapomněli pracovní nebo školní heslo, nikdy jste nedostali heslo od vaší organizace nebo jste z účtu nezapomněli, můžete pomocí bezpečnostních údajů a mobilního zařízení resetovat pracovní nebo školní heslo.

Správce musí tuto funkci zapnout, abyste mohli zaregistrovat informace a resetovat vlastní heslo. Pokud možnost **Zapomněli jste heslo** nevidíte, znamená to, že správce nezapnul funkci pro vaši organizaci. Pokud se domníváte, že je to nesprávné, požádejte o pomoc technickou podporu.

>[!Important]
>Tento článek je určen pro uživatele, kteří se pokoušejí použít registraci pro samoobslužné resetování hesla. To znamená, že budete moci resetovat své vlastní pracovní alain@contoso.comnebo školní heslo (například ), aniž byste museli potřebovat pomoc správce. Pokud jste správce, který hledá informace o tom, jak zapnout samoobslužné resetování hesla pro zaměstnance nebo jiné uživatele, přečtěte [si článek Nasazení samoobslužného hesla Azure AD a další články](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment).

## <a name="set-up-your-password-reset-verification-method"></a>Nastavení metody ověření resetování hesla

1. Otevřete webový prohlížeč v zařízení a přejděte na [stránku s bezpečnostními údaji](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. V závislosti na tom, jak správce nastavil vaši organizaci, bude k dispozici jedna nebo více následujících možností, které můžete nastavit jako metodu ověření zabezpečení. Pokud je k dispozici více možností, důrazně doporučujeme použít více než jednu jako metodu ověření zabezpečení v případě, že jedna z vašich metod nebude k dispozici.

    - **Ověřovací aplikace.** Zvolte, zda chcete jako metodu ověření zabezpečení použít aplikaci Microsoft Authenticator nebo jinou ověřovací aplikaci. Další informace o nastavení aplikace najdete v [tématu Nastavení aplikace Microsoft Authenticator jako metody ověření](security-info-setup-auth-app.md).

    - **Textové zprávy.** Zvolte, zda chcete posílat textové zprávy do svého mobilního zařízení. Další informace o nastavení textových zpráv naleznete v tématu [Nastavení textových zpráv jako metody ověření](security-info-setup-text-msg.md).

    - **Telefonáty.** Zvolte, zda chcete telefonovat na registrované telefonní číslo. Další informace o nastavení telefonních hovorů naleznete [v tématu Nastavení telefonního čísla jako metody ověření](security-info-setup-phone-number.md).

    - **Bezpečnostní klíč.** Zvolte použití zabezpečovacího klíče kompatibilního se společností Microsoft. Další informace naleznete [v tématu Nastavení klíče zabezpečení jako metody ověření](security-info-setup-security-key.md).

    - **E-mailová adresa.** Zvolte alternativní e-mailovou adresu, kterou lze použít bez nutnosti zapomenutého nebo chybějícího hesla. To funguje pouze pro resetování hesla, nikoli jako metodu ověření zabezpečení. Další informace o nastavení e-mailové adresy najdete v [tématu Nastavení e-mailové adresy jako metody ověření](security-info-setup-email.md).

    - **Bezpečnostní otázky.** Zvolte nastavení a zodpovězení předdefinovaných bezpečnostních otázek nastavených správcem. To funguje pouze pro resetování hesla, nikoli jako metodu ověření zabezpečení. Další informace o bezpečnostních otázkách naleznete v [tématu Nastavení bezpečnostních otázek jako metody ověření](security-info-setup-questions.md).

3. Po výběru a nastavení metod zvolte **Dokončit** a dokončit proces.

    > [!Note]
    > Informace přidané pro vaše telefonní číslo nebo e-mailovou adresu nejsou sdíleny s globálním adresářem vaší organizace. Tyto informace můžete zobrazit pouze vy a váš správce. Odpovědi na bezpečnostní otázky můžete zobrazit pouze vy.

## <a name="common-problems-and-their-solutions"></a>Běžné problémy a jejich řešení

 Zde jsou některé běžné chybové případy a jejich řešení:

| Chybová zpráva |  Možné řešení |
| --- | --- | --- |
| Kontaktujte správce.<br>Zjistili jsme, že heslo k vašemu uživatelskému účtu není spravováno společností Microsoft. V důsledku toho nemůžeme automaticky obnovit heslo.<br>Obraťte se na pracovníky IT pro další pomoc.| Pokud se tato chybová zpráva zobrazí po zadání id uživatele, znamená to, že vaše organizace interně spravuje vaše heslo a nechce, abyste resetovali heslo z odkazu **Nelze získat přístup k účtu.** Chcete-li v takovém případě obnovit heslo, obraťte se na technickou podporu vaší organizace nebo na správce. |
| Váš účet není povolen pro resetování hesla.<br>Je nám líto, ale pracovníci IT nenastavili váš účet pro použití s touto službou.<br>Pokud chcete, můžeme kontaktovat správce ve vaší organizaci a resetovat vám heslo. | Pokud se tato chybová zpráva zobrazí po zadání id uživatele, znamená to, že buď vaše organizace nezapnula funkci pro resetování hesla, nebo ji nemůžete používat. Chcete-li v této situaci obnovit heslo, musíte vybrat odkaz **Kontaktovat správce.** Po kliknutí na odkaz se na technickou podporu nebo správce vaší organizace odešle e-mail s upozorněním, že chcete heslo resetovat. |
| Váš účet jsme nemohli ověřit.<br>Pokud chcete, můžeme kontaktovat správce ve vaší organizaci a resetovat vám heslo. | Pokud se tato chybová zpráva zobrazí po zadání id uživatele, znamená to, že vaše organizace zapnula resetování hesla a že ji můžete použít, ale nezaregistrovali jste se pro službu. V takovém případě je nutné kontaktovat technickou podporu vaší organizace nebo správce obnovit heslo. Informace o registraci pro obnovení hesla po vrácení do zařízení naleznete v procesu výše v tomto článku. |

## <a name="next-steps"></a>Další kroky

- [Změna hesla pomocí samoobslužného resetování hesla](active-directory-passwords-update-your-own-password.md)

- [Stránka s bezpečnostními údaji](https://mysignins.microsoft.com/security-info)

- [Portál pro resetování hesla](https://passwordreset.microsoftonline.com/)

- [Když se nemůžete přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
