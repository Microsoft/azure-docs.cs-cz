---
title: Nastavení textových zpráv jako metody ověření – Azure AD
description: Jak nastavit stránku Bezpečnostní údaje (náhled) pro ověření identity pomocí textových zpráv jako metody ověření.
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
ms.openlocfilehash: 0525ee6d0ac5df61113e5a97c9812394323ffb3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062349"
---
# <a name="set-up-text-messaging-as-your-verification-method"></a>Nastavení textových zpráv jako ověřovací metody

Pomocí těchto kroků můžete přidat dvoufaktorové ověření a metody resetování hesla. Po prvním nastavení se můžete vrátit na stránku **Bezpečnostní údaje** a přidat, aktualizovat nebo odstranit bezpečnostní údaje.

Pokud se zobrazí výzva k nastavení ihned po přihlášení k pracovnímu nebo školnímu účtu, přečtěte si podrobné kroky v článku Nastavení bezpečnostních údajů v článku [výzvy k přihlášení.](security-info-setup-signin.md)

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Pokud možnost telefonu nevidíte, je možné, že vaše organizace neumožňuje použít tuto možnost k ověření. V takovém případě budete muset zvolit jinou metodu nebo se obrátit na technickou podporu vaší organizace s žádostí o další pomoc.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Nastavení textových zpráv ze stránky Informace o zabezpečení

V závislosti na nastavení vaší organizace můžete používat textové zprávy jako jednu z metod bezpečnostních informací. Možnost textové zprávy je součástí možnosti telefonu, takže vše nastavíte stejným způsobem jako pro své telefonní číslo, ale místo toho, abyste měli společnost Microsoft zavolat, zvolíte textovou zprávu.

>[!Note]
>Pokud chcete místo textové zprávy přijímat telefonní hovor, postupujte podle pokynů v článku [Nastavení bezpečnostních údajů pro používání telefonních hovorů.](security-info-setup-phone-number.md)

### <a name="to-set-up-text-messages"></a>Nastavení textových zpráv

1. Přihlaste se ke svému pracovnímu https://myprofile.microsoft.com/ nebo školnímu účtu a přejděte na stránku.

    ![Stránka Můj profil se zvýrazněnými odkazy Na informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. V levém navigačním podokně nebo z odkazu v bloku **Informace o zabezpečení** vyberte Bezpečnostní **údaje** a na stránce Informace **o zabezpečení** vyberte **Přidat metodu.**

    ![Stránka S informacemi o zabezpečení se zvýrazněnou možností Přidat metodu](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stránce **Přidat metodu** vrozeněte **telefon** z rozevíracího seznamu a pak vyberte **Přidat**.

    ![Pole Přidat metodu s vybranou možností Telefon](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na stránce **Telefon** zadejte telefonní číslo mobilního zařízení, zvolte **Nastolit kód**a pak vyberte **Další**.

    ![Přidání telefonního čísla a výběr textových zpráv](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Zadejte kód, který vám byl odeslán prostřednictvím textové zprávy do mobilního zařízení, a pak vyberte **Další**.

    ![Přidání telefonního čísla a výběr textových zpráv](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    Stránka se změní, aby se zobrazil váš úspěch.

    ![Oznámení o úspěchu, připojení telefonního čísla, možnost přijímat textové zprávy a váš účet](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Vaše bezpečnostní údaje se aktualizují a pomocí textových zpráv můžete ověřit svou identitu při použití dvoustupňového ověření nebo resetování hesla. Pokud chcete, aby textových zpráv výchozí metody, naleznete [v části Změnit výchozí způsob zabezpečení informace](#change-your-default-security-info-method) v tomto článku.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Odstranění textových zpráv z metod bezpečnostních informací

Pokud již nechcete používat textové zprávy jako metodu bezpečnostních informací, můžete je odebrat ze stránky **Informace o zabezpečení.**

>[!Important]
>Pokud textové zprávy odstraníte omylem, neexistuje žádný způsob, jak je vrátit. Budete muset přidat metodu znovu, postupujte podle kroků v [části Nastavit textové zprávy](#set-up-text-messages-from-the-security-info-page) v tomto článku.

### <a name="to-delete-text-messaging"></a>Odstranění textových zpráv

1. Na stránce **Bezpečnostní informace** vyberte odkaz **Odstranit** vedle možnosti **Telefon.**

    ![Odkaz na odstranění telefonu a metody textových zpráv z bezpečnostních informací](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Chcete-li telefonní číslo odstranit, vyberte v potvrzovacím poli **možnost** **Ano.** Po odstranění telefonního čísla se z bezpečnostních údajů odebere a zmizí ze stránky **Bezpečnostní údaje.** Pokud je výchozí metodou **telefon,** výchozí hodnota se změní na jinou dostupnou metodu.

## <a name="change-your-default-security-info-method"></a>Změna výchozí metody informací o zabezpečení

Pokud chcete, aby textové zprávy byly výchozí metodou používanou při přihlášení k pracovnímu nebo školnímu účtu pomocí dvoufaktorového ověření nebo pro žádosti o obnovení hesla, můžete ji nastavit na stránce **Informace o zabezpečení.**

### <a name="to-change-your-default-security-info-method"></a>Změna výchozí metody informací o zabezpečení

1. Na stránce **Informace o zabezpečení** vyberte odkaz **Změnit** vedle **výchozíinformace o metodě přihlášení.**

    ![Změnit odkaz pro výchozí metodu přihlášení](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. V rozevíracím seznamu dostupných metod vyberte **Telefon - text (*_your_phone_number_*)** a pak vyberte **Potvrdit**.

    ![Zvolit metodu pro výchozí přihlášení](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    Výchozí metoda použitá pro změny přihlášení k **telefonu - text (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Další metody bezpečnostních informací

Máte další možnosti, jak vás vaše organizace kontaktuje za účelem ověření vaší identity, a to na základě toho, o co se pokoušíte. Mezi možnosti patří:

- **Ověřovací aplikace.** Stáhněte si a použijte ověřovací aplikaci, abyste získali oznámení o schválení nebo náhodně generovaný kód schválení pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k nastavení a používání aplikace Microsoft Authenticator najdete v tématu [Nastavení bezpečnostních údajů pro použití ověřovací aplikace](security-info-setup-auth-app.md).

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo svého mobilního zařízení a získejte telefonní hovor pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k ověření identity pomocí telefonního čísla najdete v tématu [Nastavení bezpečnostních údajů pro používání telefonních hovorů](security-info-setup-phone-number.md).

- **Bezpečnostní klíč.** Zaregistrujte svůj bezpečnostní klíč kompatibilní se společností Microsoft a použijte jej spolu s kódem PIN pro dvoustupňové ověření nebo resetování hesla. Podrobné pokyny k ověření identity pomocí bezpečnostního klíče naleznete v tématu [Nastavení bezpečnostních údajů pro použití bezpečnostního klíče](security-info-setup-security-key.md).

- **E-mailová adresa.** Zadejte svou pracovní nebo školní e-mailovou adresu a získejte e-mail s žádostí o resetování hesla. Tato možnost není k dispozici pro dvoustupňové ověření. Podrobné pokyny k nastavení e-mailu najdete v tématu [Nastavení bezpečnostních údajů pro používání e-mailu](security-info-setup-email.md).

- **Bezpečnostní otázky.** Odpovězte na některé bezpečnostní otázky vytvořené správcem pro vaši organizaci. Tato možnost je k dispozici pouze pro resetování hesla a ne pro dvoustupňové ověření. Podrobné pokyny k nastavení bezpečnostních otázek najdete v článku [Nastavení bezpečnostních údajů pro použití bezpečnostních otázek.](security-info-setup-questions.md)

    >[!Note]
    >Pokud některé z těchto možností chybí, je to s největší pravděpodobností proto, že vaše organizace tyto metody nepovoluje. V takovém případě budete muset zvolit dostupnou metodu nebo požádat správce o další pomoc.

## <a name="next-steps"></a>Další kroky

- Pokud jste heslo ztratili nebo zapomněli, resetujte ho na [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) nebo postupujte podle pokynů v článku [Obnovit pracovní nebo školní heslo.](active-directory-passwords-update-your-own-password.md)

- V článku Nelze se [přihlásit k účtu Microsoft,](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) najdete tipy pro řešení potíží a nápovědu k problémům s přihlášením.
