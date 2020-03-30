---
title: Nastavení bezpečnostních otázek jako metody ověření – Azure AD
description: Jak nastavit stránku Bezpečnostní údaje (náhled) pro ověření vaší identity pomocí předdefinovaných bezpečnostních otázek jako metody ověření.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 236f1e4e41488b926d9fc2e5e717e68090a0ed7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063964"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Nastavení bezpečnostních otázek jako metody ověření

Pomocí těchto kroků můžete přidat metodu obnovení hesla. Po prvním nastavení se můžete vrátit na stránku **Bezpečnostní údaje** a přidat, aktualizovat nebo odstranit bezpečnostní údaje.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Pokud možnost bezpečnostních otázek nevidíte, je možné, že vaše organizace neumožňuje použít tuto možnost pro metodu resetování hesla. V takovém případě budete muset zvolit jinou metodu nebo se obrátit na technickou podporu vaší organizace a požádat o další pomoc.

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

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Nastavení bezpečnostních otázek na stránce Bezpečnostní údaje

V závislosti na nastavení vaší organizace můžete jako jednu z metod bezpečnostních údajů vybrat několik bezpečnostních otázek a zodpovědět je. Správce nastaví počet bezpečnostních otázek, které musíte zvolit a zodpovědět.

Pokud používáte bezpečnostní otázky, doporučujeme je použít ve spojení s jinou metodou. Bezpečnostní otázky mohou být méně bezpečné než jiné metody, protože někteří lidé mohou znát odpovědi na vaše otázky.

> [!Note]
> Bezpečnostní otázky jsou uloženy soukromě a bezpečně na objekt uživatele v adresáři a mohou být zodpovězeny pouze během registrace. Neexistuje žádný způsob, jak by správce mohl číst nebo upravovat vaše otázky nebo odpovědi.
>
> Pokud možnost bezpečnostních otázek nevidíte, je možné, že vaše organizace neumožňuje používat bezpečnostní otázky k ověření. V takovém případě budete muset zvolit jinou metodu nebo požádat správce o další pomoc.
>
> Účty správce nesmějí používat bezpečnostní otázky jako metodu resetování hesla. Pokud jste přihlášeni jako účet na úrovni správce, tyto možnosti neuvidíte.

### <a name="to-set-up-your-security-questions"></a>Nastavení bezpečnostních otázek

1. Přihlaste se ke svému pracovnímu https://myprofile.microsoft.com/ nebo školnímu účtu a přejděte na stránku.

    ![Stránka Můj profil se zvýrazněnými odkazy Na informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. V levém navigačním podokně nebo z odkazu v bloku **Informace o zabezpečení** vyberte Bezpečnostní **údaje** a na stránce Informace **o zabezpečení** vyberte **Přidat metodu.**

    ![Stránka S informacemi o zabezpečení se zvýrazněnou možností Přidat metodu](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stránce **Přidat metodu** vyberte v rozevíracím seznamu **bezpečnostní otázky** a pak vyberte **Přidat**.

    ![Pole Přidat metodu s vybranými bezpečnostními otázkami](media/security-info/securityinfo-myprofile-addquestions.png)

4. Na stránce **Bezpečnostní otázky** zvolte a odpovězte na bezpečnostní otázky a pak vyberte **Uložit**.

    ![Přidání telefonního čísla a výběr telefonních hovorů](media/security-info/securityinfo-myprofile-securityquestions.png)

    Vaše bezpečnostní údaje jsou aktualizovány a bezpečnostní otázky můžete použít k ověření vaší identity při použití resetování hesla.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Odstranění bezpečnostních otázek z metod bezpečnostních údajů

Pokud již nechcete používat bezpečnostní otázky jako metodu bezpečnostních údajů, můžete je odebrat ze stránky **Informace o zabezpečení.**

>[!Important]
>Pokud bezpečnostní otázky omylem smažete, neexistuje žádný způsob, jak je vrátit. Budete muset přidat metodu znovu, postupujte podle kroků v [části Nastavení bezpečnostních otázek](#set-up-your-security-questions-from-the-security-info-page) v tomto článku.

### <a name="to-delete-your-security-questions"></a>Odstranění bezpečnostních otázek

1. Na stránce **Informace o zabezpečení** vyberte odkaz **Odstranit** vedle možnosti **Bezpečnostní otázky.**

    ![Odkaz na odstranění metody telefonu z bezpečnostních informací](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Chcete-li odstranit **bezpečnostní otázky**, vyberte v potvrzovacím poli možnost **Ano** . Po odstranění bezpečnostních otázek se metoda odebere z bezpečnostních údajů a zmizí ze stránky **Bezpečnostní údaje.**

## <a name="additional-security-info-methods"></a>Další metody bezpečnostních informací

Máte další možnosti, jak vás vaše organizace kontaktuje za účelem ověření vaší identity, a to na základě toho, o co se pokoušíte. Mezi možnosti patří:

- **Ověřovací aplikace.** Stáhněte si a použijte ověřovací aplikaci, abyste získali oznámení o schválení nebo náhodně generovaný kód schválení pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k nastavení a používání aplikace Microsoft Authenticator najdete v tématu [Nastavení bezpečnostních údajů pro použití ověřovací aplikace](security-info-setup-auth-app.md).

- **Text mobilního zařízení.** Zadejte číslo svého mobilního zařízení a získejte text, který použijete pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k ověření identity pomocí textové zprávy (SMS) najdete v tématu [Nastavení bezpečnostních údajů pro používání textových zpráv (SMS).](security-info-setup-text-msg.md)

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo svého mobilního zařízení a získejte telefonní hovor pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k ověření identity pomocí telefonního čísla najdete v tématu [Nastavení bezpečnostních údajů pro používání telefonních hovorů](security-info-setup-phone-number.md).

- **Bezpečnostní klíč.** Zaregistrujte svůj bezpečnostní klíč kompatibilní se společností Microsoft a použijte jej spolu s kódem PIN pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k ověření identity pomocí bezpečnostního klíče naleznete v tématu [Nastavení bezpečnostních údajů pro použití bezpečnostního klíče](security-info-setup-security-key.md).

- **E-mailová adresa.** Zadejte svou pracovní nebo školní e-mailovou adresu a získejte e-mail s žádostí o resetování hesla. Tato možnost není k dispozici pro dvoustupňové ověření. Podrobné pokyny k nastavení e-mailu najdete v tématu [Nastavení bezpečnostních údajů pro používání e-mailu](security-info-setup-email.md).

    >[!Note]
    >Pokud některé z těchto možností chybí, je to s největší pravděpodobností proto, že vaše organizace tyto metody nepovoluje. V takovém případě budete muset zvolit dostupnou metodu nebo požádat správce o další pomoc.

## <a name="next-steps"></a>Další kroky

- Pokud jste heslo ztratili nebo zapomněli, resetujte ho na [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) nebo postupujte podle pokynů v článku [Obnovit pracovní nebo školní heslo.](active-directory-passwords-update-your-own-password.md)

- V článku Nelze se [přihlásit k účtu Microsoft,](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) najdete tipy pro řešení potíží a nápovědu k problémům s přihlášením.
