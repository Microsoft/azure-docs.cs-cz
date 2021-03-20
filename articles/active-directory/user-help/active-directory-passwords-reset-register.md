---
title: Registrace ověřovacích informací pro resetování vlastního hesla – Azure AD
description: Zaregistrujte informace o metodách ověřování pro Samoobslužné resetování hesla služby Azure AD, abyste mohli resetovat vlastní heslo, aniž byste museli nápovědu pro správce.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e460f3fd383286b1e61a979b87a9dfa09f272313
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88799022"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registrace informací o metodě ověřování pro resetování vlastního hesla

Pokud jste zapomněli své pracovní nebo školní heslo, nikdy nebudete mít heslo z vaší organizace nebo jste jeho uzamčením nedostali svůj účet, můžete k resetování svého pracovního nebo školního hesla použít své bezpečnostní údaje a mobilní zařízení.

Správce musí tuto funkci zapnout, aby bylo možné zaregistrovat vaše informace a resetovat vlastní heslo. Pokud nevidíte možnost **zapomenuté heslo** , znamená to, že správce tuto funkci pro vaši organizaci nezapnul. Pokud se domníváte, že to není správné, obraťte se na helpdesk, kde najdete pomoc.

>[!Important]
>Tento článek je určený pro uživatele, kteří se pokoušejí použít k registraci samoobslužného resetování hesla. To znamená, že budete moct resetovat svoje vlastní pracovní nebo školní heslo (například alain@contoso.com ), aniž byste museli pomáhat správce. Pokud jste správcem a hledáte informace o tom, jak zapnout Samoobslužné resetování hesla pro vaše zaměstnance nebo jiné uživatele, přečtěte si téma [nasazení samoobslužného resetování hesla Azure AD a dalších článků](../authentication/howto-sspr-deployment.md).

## <a name="set-up-your-password-reset-verification-method"></a>Nastavení metody ověřování pro resetování hesla

1. Na zařízení otevřete webový prohlížeč a potom na [stránce informace o zabezpečení](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. V závislosti na tom, jak váš správce nastavil vaši organizaci, budete mít k dispozici jednu nebo více následujících možností, které můžete nastavit jako metodu ověření zabezpečení. Pokud je k dispozici více možností, důrazně doporučujeme použít více než jeden jako metodu ověření zabezpečení, v případě, že jedna z vašich metod nebude k dispozici.

    - **Ověřovací aplikace** Vyberte, aby jako metoda ověření zabezpečení používala aplikaci Microsoft Authenticator nebo jinou ověřovací aplikaci. Další informace o nastavení aplikace najdete v tématu [nastavení aplikace Microsoft Authenticator jako metody ověřování](security-info-setup-auth-app.md).

    - **Textové zprávy.** Vyberte, chcete-li odesílat textové zprávy do mobilního zařízení. Další informace o nastavení zasílání zpráv pro text najdete v tématu [Nastavení způsobu ověřování textovým zasíláním zpráv](security-info-setup-text-msg.md).

    - **Telefonní hovory.** Vyberte, pokud chcete získat telefonní hovor na vaše registrované telefonní číslo. Další informace o nastavení telefonních hovorů najdete v tématu [Nastavení telefonního čísla jako metody ověřování](security-info-setup-phone-number.md).

    - **Bezpečnostní klíč** Vyberte, že chcete použít bezpečnostní klíč kompatibilní se společností Microsoft. Další informace najdete v tématu [nastavení bezpečnostního klíče jako metody ověřování](security-info-setup-security-key.md).

    - **E-mailová adresa** Použijte alternativní e-mailovou adresu, kterou můžete použít, aniž byste museli zapomenuté nebo chybějící heslo. To funguje jenom pro resetování hesla, ne jako metodu ověření zabezpečení. Další informace o nastavení e-mailové adresy najdete v tématu [Nastavení e-mailové adresy jako metody ověřování](security-info-setup-email.md).

    - **Bezpečnostní otázky.** Vyberte možnost nastavit a odpovědět předem definované bezpečnostní otázky nastavené vaším správcem. To funguje jenom pro resetování hesla, ne jako metodu ověření zabezpečení. Další informace o bezpečnostních otázkách najdete v tématu [Nastavení bezpečnostních otázek jako metody ověřování](security-info-setup-questions.md).

3. Po výběru a nastavení vašich metod zvolte **Dokončit** a proces dokončete.

    > [!Note]
    > Informace přidané pro telefonní číslo nebo e-mailovou adresu se nesdílí s globálním adresářem vaší organizace. Pouze lidé, kteří vidí tyto informace, jsou vy a váš správce. Odpovědi na vaše bezpečnostní otázky uvidíte jenom vy.

## <a name="common-problems-and-their-solutions"></a>Běžné problémy a jejich řešení

 Tady jsou některé běžné chybové případy a jejich řešení:

| Chybová zpráva |  Možné řešení |
| --- | --- | --- |
| Obraťte se prosím na správce.<br>Zjistili jsme, že heslo ke svému uživatelskému účtu nespravuje Microsoft. V důsledku toho nemůžeme automaticky resetovat vaše heslo.<br>Další pomoc vám poskytne váš personál oddělení IT.| Pokud se tato chybová zpráva zobrazí po zadání ID uživatele, znamená to, že vaše organizace interně spravuje vaše heslo a nechce, abyste heslo obnovili z připojení k **vašemu účtu** . Pokud chcete v této situaci resetovat heslo, musíte se obrátit na helpdesk nebo správce vaší organizace, který vám pomůže. |
| Váš účet není pro resetování hesla povolený.<br>Je nám líto, ale vaši zaměstnanci IT nevytvořili účet pro použití s touto službou.<br>Pokud byste chtěli, můžeme kontaktovat správce ve vaší organizaci, aby vám heslo resetoval. | Pokud se tato chybová zpráva zobrazí po zadání ID uživatele, znamená to, že vaše organizace nemá zapnutou funkci resetování hesla, nebo ji nemůžete používat. Pokud chcete v této situaci resetovat heslo, musíte vybrat odkaz **kontaktujte správce** . Po kliknutí na odkaz se na helpdesk nebo správce vaší organizace pošle e-mail s oznámením, že si chcete resetovat heslo. |
| Nepovedlo se nám ověřit váš účet.<br>Pokud byste chtěli, můžeme kontaktovat správce ve vaší organizaci, aby vám heslo resetoval. | Pokud se tato chybová zpráva zobrazí po zadání ID uživatele, znamená to, že vaše organizace zapnula resetování hesla a že ji můžete použít, ale nezaregistrovali jste ji pro službu. V takovém případě musíte požádat o resetování hesla oddělení technické podpory nebo správce vaší organizace. Informace o tom, jak se zaregistrovat pro resetování hesla po návratu do zařízení, najdete v části v tomto článku postup. |

## <a name="next-steps"></a>Další kroky

- [Změna hesla pomocí samoobslužného resetování hesla](active-directory-passwords-update-your-own-password.md)

- [Stránka informace o zabezpečení](https://mysignins.microsoft.com/security-info)

- [Portál pro resetování hesla](https://passwordreset.microsoftonline.com/)

- [Když se nemůžete přihlásit k účet Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)