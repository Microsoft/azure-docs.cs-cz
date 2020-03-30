---
title: Nastavení e-mailové adresy jako metody ověření – Azure AD
description: Jak nastavit stránku Bezpečnostní údaje (náhled) pro ověření vaší identity pomocí e-mailové adresy jako metody ověření.
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
ms.openlocfilehash: 9488b96dda15b0ffb794f83826dc299a5f397b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063998"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>Nastavení e-mailové adresy jako metody ověření

Pomocí těchto kroků můžete přidat metodu obnovení hesla. Po prvním nastavení se můžete vrátit na stránku **Bezpečnostní údaje** a přidat, aktualizovat nebo odstranit bezpečnostní údaje.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Pokud možnost e-mailu nevidíte, je možné, že vaše organizace neumožňuje použít tuto možnost pro metodu resetování hesla. V takovém případě budete muset zvolit jinou metodu nebo se obrátit na technickou podporu vaší organizace a požádat o další pomoc.

## <a name="security-vs-password-reset-verification"></a>Zabezpečení vs ověření obnovení hesla

Metody bezpečnostních informací se používají jak pro dvoufaktorové ověření zabezpečení, tak pro resetování hesla. Ne všechny metody však lze použít pro obě metody.

| Metoda | Použití |
| ------ | -------- |
| Aplikace Authenticator | Dvoufaktorové ověření a ověřování heslem. |
| Textové zprávy | Dvoufaktorové ověření a ověřování heslem. |
| Telefonní hovory | Dvoufaktorové ověření a ověřování heslem. |
| Klíč zabezpečení | Dvoufaktorové ověření a ověřování heslem. |
| E-mailový účet | Pouze ověřování pro resetování hesla. Budete muset zvolit jinou metodu pro dvoufaktorové ověření. |
| Bezpečnostní otázky | Pouze ověřování pro resetování hesla. Budete muset zvolit jinou metodu pro dvoufaktorové ověření. |

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Nastavení e-mailové adresy na stránce Bezpečnostní údaje

V závislosti na nastavení vaší organizace můžete použít e-mailovou adresu jako jednu z metod bezpečnostních údajů.

>[!Note]
>Doporučujeme použít e-mailovou adresu, která nevyžaduje přístup k vašemu síťovému heslu. Pokud možnost e-mailu nevidíte, je možné, že vám vaše organizace neumožňuje používat e-mail k ověření. V takovém případě budete muset zvolit jinou metodu nebo požádat správce o další pomoc.

### <a name="to-set-up-your-email-address"></a>Nastavení e-mailové adresy

1. Přihlaste se ke svému pracovnímu https://myprofile.microsoft.com/ nebo školnímu účtu a přejděte na stránku.

    ![Stránka Můj profil se zvýrazněnými odkazy Na informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. V levém navigačním podokně nebo z odkazu v bloku **Informace o zabezpečení** vyberte Bezpečnostní **údaje** a na stránce Informace **o zabezpečení** vyberte **Přidat metodu.**

    ![Stránka S informacemi o zabezpečení se zvýrazněnou možností Přidat metodu](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stránce **Přidat metodu** vyberte v rozevíracím seznamu **E-mail** a pak vyberte **Přidat**.

    ![Pole Přidat metodu s vybraným e-mailem](media/security-info/securityinfo-myprofile-addemail.png)

4. Na stránce **E-mail** zadejte svou alain@gmail.come-mailovou adresu (například) a pak vyberte **Další**.

    ![Přidání telefonního čísla a výběr telefonních hovorů](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Tato e-mailová adresa nemůže být vaším pracovním nebo školním e-mailem.

5. Zadejte kód odeslaný na zadanou e-mailovou adresu a vyberte **další**.

    ![Přidání telefonního čísla a výběr textových zpráv](media/security-info/securityinfo-myprofile-emailcode.png)

    Vaše bezpečnostní údaje se aktualizují a můžete použít svou e-mailovou adresu k ověření vaší identity při použití resetování hesla.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Odstranění e-mailové adresy z metod bezpečnostních údajů

Pokud už nechcete používat svou e-mailovou adresu jako metodu bezpečnostních údajů, můžete ji odebrat ze stránky **Informace o zabezpečení.**

>[!Important]
>Pokud e-mailovou adresu smažete omylem, neexistuje žádný způsob, jak ji vrátit. Budete muset přidat metodu znovu, postupujte podle kroků v [části Nastavit e-mailovou adresu](#set-up-your-email-address-from-the-security-info-page) v tomto článku.

### <a name="to-delete-your-email-address"></a>Odstranění e-mailové adresy

1. Na stránce **Informace o zabezpečení** vyberte odkaz **Odstranit** vedle možnosti **E-mail.**

    ![Odkaz na odstranění metody telefonu z bezpečnostních informací](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Chcete-li odstranit **e-mailový** účet, vyberte v potvrzovacím poli možnost **Ano.** Po odstranění e-mailového účtu se z bezpečnostních údajů odebere a zmizí ze stránky **Bezpečnostní údaje.**

## <a name="additional-security-info-methods"></a>Další metody bezpečnostních informací

Máte další možnosti, jak vás vaše organizace kontaktuje za účelem ověření vaší identity, a to na základě toho, o co se pokoušíte. Mezi možnosti patří:

- **Ověřovací aplikace.** Stáhněte si a použijte ověřovací aplikaci, abyste získali oznámení o schválení nebo náhodně generovaný kód schválení pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k nastavení a používání aplikace Microsoft Authenticator najdete v tématu [Nastavení bezpečnostních údajů pro použití ověřovací aplikace](security-info-setup-auth-app.md).

- **Text mobilního zařízení.** Zadejte číslo svého mobilního zařízení a získejte text, který použijete pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k ověření identity pomocí textové zprávy (SMS) najdete v tématu [Nastavení bezpečnostních údajů pro používání textových zpráv (SMS).](security-info-setup-text-msg.md)

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo svého mobilního zařízení a získejte telefonní hovor pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k ověření identity pomocí telefonního čísla najdete v tématu [Nastavení bezpečnostních údajů pro používání telefonních hovorů](security-info-setup-phone-number.md).

- **Bezpečnostní klíč.** Zaregistrujte svůj bezpečnostní klíč kompatibilní se společností Microsoft a použijte jej spolu s kódem PIN pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k ověření identity pomocí bezpečnostního klíče naleznete v tématu [Nastavení bezpečnostních údajů pro použití bezpečnostního klíče](security-info-setup-security-key.md).

- **Bezpečnostní otázky.** Odpovězte na některé bezpečnostní otázky vytvořené správcem pro vaši organizaci. Tato možnost je k dispozici pouze pro resetování hesla a ne pro dvoustupňové ověření. Podrobné pokyny k nastavení bezpečnostních otázek najdete v článku [Nastavení bezpečnostních údajů pro použití bezpečnostních otázek.](security-info-setup-questions.md)

    >[!Note]
    >Pokud některé z těchto možností chybí, je to s největší pravděpodobností proto, že vaše organizace tyto metody nepovoluje. V takovém případě budete muset zvolit dostupnou metodu nebo požádat správce o další pomoc.

## <a name="next-steps"></a>Další kroky

- Pokud jste heslo ztratili nebo zapomněli, resetujte ho na [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) nebo postupujte podle pokynů v článku [Obnovit pracovní nebo školní heslo.](active-directory-passwords-update-your-own-password.md)

- V článku Nelze se [přihlásit k účtu Microsoft,](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) najdete tipy pro řešení potíží a nápovědu k problémům s přihlášením.
