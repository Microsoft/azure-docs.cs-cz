---
title: Nastavení telefonního čísla jako metody ověření – Azure AD
description: Jak nastavit stránku Bezpečnostní údaje (náhled) pro ověření vaší identity pomocí telefonního čísla a mobilního zařízení jako metody ověření.
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
ms.openlocfilehash: e85be9140aabe5b66e63d3ccdd4a3ea907a1d6fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062400"
---
# <a name="set-up-a-phone-number-as-your-verification-method"></a>Nastavení telefonního čísla jako metody ověření

Pomocí těchto kroků můžete přidat dvoufaktorové ověření a metody resetování hesla. Po prvním nastavení se můžete vrátit na stránku **Bezpečnostní údaje** a přidat, aktualizovat nebo odstranit bezpečnostní údaje.

Pokud se zobrazí výzva k nastavení ihned po přihlášení k pracovnímu nebo školnímu účtu, přečtěte si podrobné kroky v článku Nastavení bezpečnostních údajů v článku [výzvy k přihlášení.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> Bezpečnostní údaje nepodporují použití telefonních rozšíření. I když přidáte správný formát +1 4255551234X12345, rozšíření jsou odebrány před voláním.
>
> Pokud možnost telefonu nevidíte, je možné, že vaše organizace neumožňuje použít tuto možnost k ověření. V takovém případě budete muset zvolit jinou metodu nebo se obrátit na technickou podporu vaší organizace s žádostí o další pomoc.

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

## <a name="set-up-phone-calls-from-the-security-info-page"></a>Nastavení telefonních hovorů na stránce Bezpečnostní údaje

V závislosti na nastavení vaší organizace můžete telefonní hovory použít jako jednu z metod bezpečnostních údajů.

>[!Note]
>Pokud chcete místo telefonního hovoru dostávat textovou zprávu, postupujte podle pokynů v článku [Nastavení bezpečnostních údajů, abyste použili textovou zprávu.](security-info-setup-text-msg.md)

### <a name="to-set-up-phone-calls"></a>Nastavení telefonních hovorů

1. Přihlaste se ke svému pracovnímu https://myprofile.microsoft.com/ nebo školnímu účtu a přejděte na stránku.

    ![Stránka Můj profil se zvýrazněnými odkazy Na informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. V levém navigačním podokně nebo z odkazu v bloku **Informace o zabezpečení** vyberte Bezpečnostní **údaje** a na stránce Informace **o zabezpečení** vyberte **Přidat metodu.**

    ![Stránka S informacemi o zabezpečení se zvýrazněnou možností Přidat metodu](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stránce **Přidat metodu** vrozeněte **telefon** z rozevíracího seznamu a pak vyberte **Přidat**.

    ![Pole Přidat metodu s vybranou možností Telefon](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na stránce **Telefon** zadejte telefonní číslo svého mobilního zařízení, zvolte **Volat mi**a pak vyberte **Další**.

    ![Přidání telefonního čísla a výběr telefonních hovorů](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. Přijměte ověřovací telefonní hovor, odešlete na zadané telefonní číslo a postupujte podle pokynů.

    Stránka se změní, aby se zobrazil váš úspěch.

    ![Oznámení o úspěchu, připojení telefonního čísla, možnost přijímat telefonní hovory a váš účet](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Vaše bezpečnostní údaje jsou aktualizovány a telefonní hovory můžete použít k ověření vaší identity při použití dvoustupňového ověření nebo resetování hesla. Pokud chcete, aby volání telefonních hovorů výchozí metodu, naleznete [v části Změnit výchozí způsob zabezpečení informace](#change-your-default-security-info-method) v tomto článku.

## <a name="delete-phone-calls-from-your-security-info-methods"></a>Odstranění telefonních hovorů z metod bezpečnostních informací

Pokud již nechcete používat telefonní hovory jako metodu bezpečnostních informací, můžete je odebrat ze stránky **Informace o zabezpečení.**

>[!Important]
>Pokud omylem smažete telefonní hovory, neexistuje žádný způsob, jak to vrátit. Budete muset přidat metodu znovu, postupujte podle kroků v části [Nastavit telefonní hovory](#set-up-phone-calls-from-the-security-info-page) v tomto článku.

### <a name="to-delete-phone-calls"></a>Odstranění telefonních hovorů

1. Na stránce **Bezpečnostní informace** vyberte odkaz **Odstranit** vedle možnosti **Telefon.**

    ![Odkaz na odstranění metody telefonu z bezpečnostních informací](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Chcete-li telefonní číslo odstranit, vyberte v potvrzovacím poli **možnost** **Ano.** Po odstranění telefonního čísla se z bezpečnostních údajů odebere a zmizí ze stránky **Bezpečnostní údaje.** Pokud je výchozí metodou **telefon,** výchozí hodnota se změní na jinou dostupnou metodu.

## <a name="change-your-default-security-info-method"></a>Změna výchozí metody informací o zabezpečení

Pokud chcete, aby telefonní hovory byly výchozí metodou používanou při přihlášení k pracovnímu nebo školnímu účtu pomocí dvoufaktorového ověření nebo pro žádosti o obnovení hesla, můžete je nastavit na stránce **Bezpečnostní údaje.**

### <a name="to-change-your-default-security-info-method"></a>Změna výchozí metody informací o zabezpečení

1. Na stránce **Informace o zabezpečení** vyberte odkaz **Změnit** vedle **výchozíinformace o metodě přihlášení.**

    ![Změnit odkaz pro výchozí metodu přihlášení](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. V rozevíracím seznamu dostupných metod vyberte **Telefon - volání (*_your_phone_number_*)** a pak vyberte **Potvrdit**.

    ![Zvolit metodu pro výchozí přihlášení](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    Výchozí metoda používaná pro změny přihlášení do **phone - call (*_your_phone_number_*).**

## <a name="additional-security-info-methods"></a>Další metody bezpečnostních informací

Máte další možnosti, jak vás vaše organizace kontaktuje za účelem ověření vaší identity, a to na základě toho, o co se pokoušíte. Mezi možnosti patří:

- **Ověřovací aplikace.** Stáhněte si a použijte ověřovací aplikaci, abyste získali oznámení o schválení nebo náhodně generovaný kód schválení pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k nastavení a používání aplikace Microsoft Authenticator najdete v tématu [Nastavení bezpečnostních údajů pro použití ověřovací aplikace](security-info-setup-auth-app.md).

- **Text mobilního zařízení.** Zadejte číslo svého mobilního zařízení a získejte text, který použijete pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k ověření identity pomocí textové zprávy (SMS) najdete v tématu [Nastavení bezpečnostních údajů pro používání textových zpráv (SMS).](security-info-setup-text-msg.md)

- **Bezpečnostní klíč.** Zaregistrujte svůj bezpečnostní klíč kompatibilní se společností Microsoft a použijte jej spolu s kódem PIN pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k ověření identity pomocí bezpečnostního klíče naleznete v tématu [Nastavení bezpečnostních údajů pro použití bezpečnostního klíče](security-info-setup-security-key.md).

- **E-mailová adresa.** Zadejte svou pracovní nebo školní e-mailovou adresu a získejte e-mail s žádostí o resetování hesla. Tato možnost není k dispozici pro dvoustupňové ověření. Podrobné pokyny k nastavení e-mailu najdete v tématu [Nastavení bezpečnostních údajů pro používání e-mailu](security-info-setup-email.md).

- **Bezpečnostní otázky.** Odpovězte na některé bezpečnostní otázky vytvořené správcem pro vaši organizaci. Tato možnost je k dispozici pouze pro resetování hesla a ne pro dvoustupňové ověření. Podrobné pokyny k nastavení bezpečnostních otázek najdete v článku [Nastavení bezpečnostních údajů pro použití bezpečnostních otázek.](security-info-setup-questions.md)

    >[!Note]
    >Pokud některé z těchto možností chybí, je to s největší pravděpodobností proto, že vaše organizace tyto metody nepovoluje. V takovém případě budete muset zvolit dostupnou metodu nebo požádat správce o další pomoc.

## <a name="next-steps"></a>Další kroky

- Pokud jste heslo ztratili nebo zapomněli, resetujte ho na [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) nebo postupujte podle pokynů v článku [Obnovit pracovní nebo školní heslo.](active-directory-passwords-update-your-own-password.md)

- V článku Nelze se [přihlásit k účtu Microsoft,](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) najdete tipy pro řešení potíží a nápovědu k problémům s přihlášením.
