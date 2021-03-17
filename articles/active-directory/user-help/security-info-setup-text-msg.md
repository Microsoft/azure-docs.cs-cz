---
title: Nastavení zasílání textových zpráv jako metody ověřování – Azure AD
description: Jak nastavit stránku informací o zabezpečení (Preview) k ověření vaší identity pomocí textových zpráv jako metody ověřování.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: c68e01e0eb7c926f47c99b16efa87d23a10b6711
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537029"
---
# <a name="set-up-text-messaging-as-your-verification-method"></a>Nastavení zasílání textových zpráv jako metody ověřování

Pomocí těchto kroků můžete přidat metody dvou faktorů ověřování a resetování hesla. Po prvním nastavení se můžete vrátit na stránku **bezpečnostní údaje** a přidat, aktualizovat nebo odstranit informace o zabezpečení.

Pokud se zobrazí výzva k nastavení hned po přihlášení k pracovnímu nebo školnímu účtu, přečtěte si podrobný postup v článku [Nastavení bezpečnostních údajů z výzvy k zadání přihlašovací stránky](security-info-setup-signin.md) .

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Pokud nevidíte možnost telefonu, je možné, že vám vaše organizace neumožní tuto možnost použít k ověření. V takovém případě budete muset zvolit jinou metodu nebo požádat o další pomoc oddělení technické podpory vaší organizace.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Nastavení textových zpráv ze stránky bezpečnostní údaje

V závislosti na nastaveních vaší organizace můžete používat textové zasílání zpráv jako jednu z vašich metod bezpečnostních údajů. Možnost textová zpráva je součástí možnosti telefon, takže nastavili jste všechno tak, jak chcete, aby se vaše telefonní číslo vyvolalo, ale místo toho, abyste vás vyvolali od Microsoftu, se rozhodnete použít textovou zprávu.

>[!Note]
>Pokud chcete místo textové zprávy přijmout telefonní hovor, postupujte podle kroků v článku [Nastavení informací o zabezpečení pro použití telefonních hovorů](security-info-setup-phone-number.md) .

### <a name="to-set-up-text-messages"></a>Nastavení textových zpráv

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu a pak přejít na https://myaccount.microsoft.com/ stránku.

    ![Stránka můj profil zobrazující odkazy na zvýrazněné informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. V levém navigačním podokně nebo na odkaz v bloku **informací o** zabezpečení vyberte možnost **informace o zabezpečení** a pak vyberte **Přidat metodu** ze stránky **bezpečnostní údaje** .

    ![Stránka informace o zabezpečení se zvýrazněnou možností přidat metodu](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stránce **Přidat metodu** vyberte v rozevíracím seznamu možnost **telefon** a pak vyberte **Přidat**.

    ![Přidat pole metody s vybraným telefonem](media/security-info/securityinfo-myprofile-addphonetext.png)

4. Na stránce **telefon** zadejte telefonní číslo mobilního zařízení, zvolte **text já a kód**a potom vyberte **Další**.

    ![Snímek obrazovky, který zobrazuje stránku "Phone" s vybraným textem "text-a kód".](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Do mobilního zařízení zadejte kód, který vám pošle textovou zprávu, a pak vyberte **Další**.

    ![Přidat telefonní číslo a zvolit textové zprávy](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    Stránka se změní tak, aby zobrazovala úspěch.

    ![Oznámení o úspěšnosti, připojení telefonního čísla, volba pro příjem textových zpráv a váš účet](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Vaše bezpečnostní údaje se aktualizují a k ověření vaší identity při použití dvoustupňového ověřování nebo resetování hesla můžete použít textové zasílání zpráv. Pokud chcete nastavit jako výchozí metodu text zasílání zpráv, přečtěte si část [Změna výchozích informací o zabezpečení](#change-your-default-security-info-method) v tomto článku.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Odstranění zasílání textových zpráv z metod zabezpečení

Pokud již nechcete používat textové zprávy jako metodu bezpečnostního údaje, můžete ji odebrat ze stránky **bezpečnostní údaje** .

>[!Important]
>Pokud odstraníte textové zasílání zpráv omylem, neexistuje žádný způsob, jak ho vrátit zpátky. Tuto metodu budete muset přidat znovu podle kroků v části [Nastavení textových zpráv](#set-up-text-messages-from-the-security-info-page) v tomto článku.

### <a name="to-delete-text-messaging"></a>Odstranění zasílání textových zpráv

1. Na stránce **informace o zabezpečení** vyberte odkaz **Odstranit** vedle možnosti **telefon** .

    ![Odkaz pro odstranění metody Phone a text Messaging z bezpečnostních údajů](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Chcete-li odstranit **telefonní** číslo, vyberte možnost **Ano** v potvrzovacím poli. Po odstranění telefonního čísla se z bezpečnostních údajů odstraní a zmizí ze stránky **bezpečnostní údaje** . Pokud je **telefon** výchozí metodou, výchozí nastavení se změní na jinou dostupnou metodu.

## <a name="change-your-default-security-info-method"></a>Změna výchozí metody zabezpečení údajů

Pokud chcete, aby se textové zprávy používaly jako výchozí, když se přihlašujete ke svému pracovnímu nebo školnímu účtu Pomocí dvojúrovňového ověřování nebo pro žádosti o resetování hesla, můžete ho nastavit na stránce **informace o zabezpečení** .

### <a name="to-change-your-default-security-info-method"></a>Změna výchozí metody zabezpečení údajů

1. Na stránce **informace o zabezpečení** vyberte odkaz **změnit** vedle informací o **výchozím způsobu přihlašování** .

    ![Odkaz pro změnu výchozí metody přihlašování](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. V rozevíracím seznamu dostupných metod vyberte **telefonní text (*_your_phone_number_*)** a pak vyberte **Potvrdit**.

    ![Zvolit metodu pro výchozí přihlášení](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    Výchozí metoda, která se používá pro změny přihlášení k **telefonnímu textu (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Další metody zabezpečení

Máte k dispozici další možnosti, jak vaše organizace kontaktuje vaši identitu na základě toho, co se you're snaží. Mezi možnosti patří:

- **Ověřovací aplikace** Stáhněte a použijte ověřovací aplikaci k získání oznámení o schválení nebo náhodně generovaného kódu schválení pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny k nastavení a používání aplikace Microsoft Authenticator najdete v tématu [Nastavení bezpečnostních údajů pro použití ověřovací aplikace](security-info-setup-auth-app.md).

- **Mobilní zařízení nebo pracovní telefonní hovor.** Zadejte číslo mobilního zařízení a získejte telefonní hovor pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí telefonního čísla, najdete v tématu [Nastavení bezpečnostních údajů pro použití telefonních hovorů](security-info-setup-phone-number.md).

- **Bezpečnostní klíč** Zaregistrujte svůj bezpečnostní klíč kompatibilní s Microsoftem a použijte ho spolu s kódem PIN pro dvoustupňové ověřování nebo resetování hesla. Podrobné pokyny, jak ověřit identitu pomocí bezpečnostního klíče, najdete v tématu [Nastavení bezpečnostních údajů pro použití bezpečnostního klíče](security-info-setup-security-key.md).

- **E-mailová adresa** Zadejte svou pracovní nebo školní e-mailovou adresu, abyste získali e-mail pro resetování hesla. Tato možnost není k dispozici pro dvoustupňové ověřování. Podrobné pokyny k nastavení e-mailu najdete v tématu [Nastavení bezpečnostních údajů pro použití e-mailu](security-info-setup-email.md).

- **Bezpečnostní otázky.** Odpovězte na některé bezpečnostní otázky, které vytvořil správce vaší organizace. Tato možnost je k dispozici pouze pro resetování hesla a nikoli pro dvoustupňové ověřování. Podrobné pokyny k nastavení bezpečnostních otázek najdete v článku [Nastavení bezpečnostních informací pro použití bezpečnostních otázek](security-info-setup-questions.md) .

    >[!Note]
    >Pokud některé z těchto možností chybí, je to pravděpodobně proto, že vaše organizace tyto metody nepovoluje. Pokud se jedná o tento případ, budete muset zvolit dostupnou metodu nebo požádat správce o další nápovědu.

## <a name="next-steps"></a>Další kroky

- Resetujte heslo, pokud jste ho ztratili nebo zapomněli z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com/) , nebo postupujte podle pokynů v článku [resetování svého pracovního nebo školního hesla](active-directory-passwords-update-your-own-password.md) .

- Získejte tipy pro řešení potíží a nápovědu k problémům s přihlášením v tématu se [nemůžete přihlásit k vašemu účet Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .
